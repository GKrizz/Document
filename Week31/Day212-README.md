

> **Your FHIR server is a Spring Boot application that exposes standard FHIR URLs, forwards those requests to an internal EMR backend, parses the backend response into FHIR R4 objects using HAPI, and returns them to the caller.**

Now let’s prove this line by line.

---

## 1️⃣ Application startup – “FHIR server comes alive”

### File

`FhirPlainBootApplication.java`

```java
@SpringBootApplication
public class FhirPlainBootApplication {
    public static void main(String[] args) {
        SpringApplication.run(FhirPlainBootApplication.class, args);
    }
}
```

### What happens here

1. JVM starts
2. Spring Boot starts
3. Spring scans package `com.glenwood.fhir`
4. Beans are created:

   * `FhirServerConfig`
   * `PatientResourceProvider`
   * `BackendClient`
5. Embedded Tomcat starts on **port 9080**

📌 Why port 9080?

From:

```properties
server.port=9080
```

So your server is now listening at:

```
http://localhost:9080
```

---

## 2️⃣ FHIR engine setup – “FHIR exists now”

### File

`FhirServerConfig.java`

This file is **where FHIR is born**.

---

### Step 2.1 – FHIR version

```java
@Bean
public FhirContext fhirContext() {
    return FhirContext.forR4();
}
```

This means:

* All parsing
* All validation
* All resource models

➡ are **FHIR R4**

---

### Step 2.2 – HAPI RestfulServer

```java
@Bean
public RestfulServer fhirRestfulServer(
        FhirContext fhirContext,
        List<IResourceProvider> resourceProviders)
```

Important things happening here:

* `RestfulServer` = **FHIR HTTP engine**
* Spring injects **all classes implementing `IResourceProvider`**

  * `PatientResourceProvider`
  * `EncounterResourceProvider`
  * etc.

```java
server.setResourceProviders(resourceProviders);
```

📌 This is how HAPI knows:

> “If someone calls `/Patient`, use PatientResourceProvider”

---

### Step 2.3 – Servlet mapping

```java
new ServletRegistrationBean<>(restfulServer, "/fhir/*");
```

This line is **critical**.

It means:

```
/fhir/*  →  HAPI FHIR server
```

So your **FHIR base URL** becomes:

```
http://localhost:9080/fhir
```

Without this line → no FHIR endpoints exist.

---

## 3️⃣ Client calls FHIR – “How FHIR is called”

### Example request

```
GET http://localhost:9080/fhir/Patient/376483
```

Let’s break this URL:

| Part             | Meaning            |
| ---------------- | ------------------ |
| `localhost:9080` | Spring Boot server |
| `/fhir`          | FHIR servlet       |
| `/Patient`       | FHIR resource      |
| `/376483`        | Logical ID         |

This URL is **defined by the FHIR standard**, not by you.

---

## 4️⃣ How request reaches your code

### Internally, this happens:

```
HTTP request
 → Tomcat
 → /fhir/*
 → RestfulServer (HAPI)
 → Match resource = Patient
 → Match operation = @Read
 → Call PatientResourceProvider.read()
```

You didn’t write routing code — **HAPI does this automatically**.

---

## 5️⃣ PatientResourceProvider – “FHIR → Java”

### File

`PatientResourceProvider.java`

```java
@Read
public Patient read(@IdParam IdType theId) {
    String id = theId.getIdPart();
    return backendClient.getPatientById(id);
}
```

What happens:

* HAPI extracts `376483` from URL
* Converts it to `IdType`
* Calls this method
* `id = "376483"`

📌 At this point:

* FHIR logic ends
* Business logic begins

---

## 6️⃣ BackendClient – “Where it goes next”

### File

`BackendClient.java`

This class is **the bridge** between:

* FHIR world
* EMR backend world

---

### Step 6.1 – Backend base URL

From `application.properties`:

```properties
backend.base-url=http://192.168.2.241:4080/glaceemr_backend/api/emr/PlainFHIR/R4
```

Spring injects this into:

```java
@Value("${backend.base-url}")
private String backendBaseUrl;
```

So now the Java variable holds:

```
http://192.168.2.241:4080/glaceemr_backend/api/emr/PlainFHIR/R4
```

---

### Step 6.2 – Build backend URL

```java
public Patient getPatientById(String id) {
    String url = buildUrlWithParams(
        "Patient",
        Map.of("dbname", "glace", "patientId", id)
    );
    return fetchSingleResource(url, Patient.class);
}
```

This creates:

```
http://192.168.2.241:4080/glaceemr_backend/api/emr/PlainFHIR/R4/Patient
    ?dbname=glace
    &patientId=376483
```

📌 This is **NOT standard FHIR**
📌 This is **your internal EMR API**

---

## 7️⃣ HTTP call to backend – “How data is fetched”

```java
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create(url))
    .GET()
    .build();
```

Then:

```java
HttpResponse<String> response =
    httpClient.send(request, HttpResponse.BodyHandlers.ofString());
```

At this moment:

➡ Your FHIR server is acting as an **HTTP client**
➡ It calls the EMR backend
➡ Gets a JSON response

---

## 8️⃣ Backend response format – “Important assumption”

Your backend returns something like:

```json
{
  "status": "success",
  "data": "[ { FHIR Patient JSON }, { ... } ]"
}
```

Notice:

* `data` is a **STRING**
* Inside it is a **FHIR JSON array**

That’s why you do:

```java
BackendEnvelope envelope =
    objectMapper.readValue(response.body(), BackendEnvelope.class);
```

---

## 9️⃣ Parsing backend data into FHIR objects

### Step 9.1 – Extract array

```java
JsonNode arrayNode = objectMapper.readTree(envelope.getData());
```

### Step 9.2 – Take first patient (READ)

```java
JsonNode node = arrayNode.get(0);
```

### Step 9.3 – Convert JSON → FHIR Patient

```java
IParser parser = newLenientJsonParser();
return parser.parseResource(Patient.class, Json);
```

This is where **HAPI FHIR shines**:

* Validates structure
* Converts JSON → `org.hl7.fhir.r4.model.Patient`
* Ignores unknown fields (lenient mode)

---

## 🔁 For SEARCH – same flow, list instead of single

```
GET /fhir/Patient?family=TEST
```

Flow:

```
FHIR search
 → PatientResourceProvider.search()
 → backendClient.searchPatients()
 → fetchResourceList()
 → parse each JSON → Patient
 → return List<Patient>
```

HAPI automatically wraps it into a **FHIR Bundle**.

---

## 🔄 Full flow diagram (mental)

```
Client
  |
  | GET /fhir/Patient/376483
  |
Spring Boot (9080)
  |
HAPI RestfulServer
  |
PatientResourceProvider
  |
BackendClient
  |
HTTP call to EMR backend (4080)
  |
EMR returns FHIR JSON
  |
HAPI parses JSON → Patient
  |
FHIR XML/JSON response
```

---

## ✅ Key things you should now be clear about

✔ FHIR URLs are **standard**
✔ You never hardcode `/Patient/{id}`
✔ HAPI routes requests automatically
✔ Backend URL is **internal**
✔ Your server is a **proxy / façade**
✔ Data returned is already **FHIR R4**
✔ HAPI only parses & serializes

---

## 🧠 Final one-line understanding

> **FHIR doesn’t store data here. It only standardizes access to data that already lives in your EMR backend.**

---

```
GET http://localhost:9080/fhir/Patient/376483
```

No guessing. Every part comes **directly from your code + FHIR rules**.

---

# 🔹 Step 1: Where did `http://localhost:9080` come from?

From your **application.properties**:

```properties
server.port=9080
```

Spring Boot rule:

* If no host is specified → defaults to `localhost`
* Port = `9080`

So base server URL is:

```
http://localhost:9080
```

---

# 🔹 Step 2: Where did `/fhir` come from?

From **FhirServerConfig.java**:

```java
new ServletRegistrationBean<>(restfulServer, "/fhir/*");
```

This line means:

> “Mount the HAPI FHIR server at `/fhir`”

So every FHIR endpoint starts with:

```
/fhir
```

---

# 🔹 Step 3: Where did `/Patient` come from?

From **FHIR specification + your provider**.

### In `PatientResourceProvider.java`:

```java
public class PatientResourceProvider implements IResourceProvider {

    @Override
    public Class<Patient> getResourceType() {
        return Patient.class;
    }
}
```

FHIR rule:

* Resource name = class name
* `Patient.class` → `"Patient"`

So HAPI automatically exposes:

```
/fhir/Patient
```

You did **not** hardcode `/Patient` anywhere — HAPI did it.

---

# 🔹 Step 4: Where did `/376483` come from?

From this method in your provider:

```java
@Read
public Patient read(@IdParam IdType theId)
```

FHIR rule:

* `@Read` → **read-by-id**
* `{id}` comes from the URL

So FHIR defines:

```
GET /{resourceType}/{id}
```

Which becomes:

```
GET /fhir/Patient/{id}
```

When you call:

```
GET /fhir/Patient/376483
```

HAPI extracts:

```java
theId.getIdPart() → "376483"
```

---

# 🔹 Step 5: Putting ALL parts together

Now assemble everything logically:

| Part               | Source             |
| ------------------ | ------------------ |
| `http://localhost` | Default host       |
| `:9080`            | `server.port=9080` |
| `/fhir`            | Servlet mapping    |
| `/Patient`         | `Patient.class`    |
| `/376483`          | `@Read` + `{id}`   |

➡ Final API:

```
GET http://localhost:9080/fhir/Patient/376483
```

---

# 🔹 Step 6: Why NOT this? (Common mistakes)

❌ **Wrong**

```
GET /Patient/376483
```

Reason: No servlet mapped there.

❌ **Wrong**

```
GET /fhir/patient/376483
```

Reason: FHIR resource names are **case-sensitive**

❌ **Wrong**

```
POST /fhir/Patient/376483
```

Reason: `@Read` only responds to **GET**

---

# 🔹 Step 7: FHIR Rule (Memorize This)

> **FHIR Read API format is always:**
>
> ```
> GET {base}/{ResourceType}/{id}
> ```

Your base is:

```
http://localhost:9080/fhir
```

---

# 🔹 Step 8: How to frame ANY FHIR API yourself

Use this mental template:

```
http://{host}:{port}/{fhir-base}/{ResourceType}/{id}
```

Example:

* Encounter:

```
GET http://localhost:9080/fhir/Encounter/12345
```

* Search:

```
GET http://localhost:9080/fhir/Patient?family=Smith
```

---

# ✅ One-line answer (what you asked)

> **I framed `GET http://localhost:9080/fhir/Patient/376483` by combining your server port, servlet mapping, FHIR resource type, and the `@Read` rule defined by HAPI FHIR.**

---
