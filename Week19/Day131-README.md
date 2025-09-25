
> **"Session closed – Status: display_server_not_supported"**

This confirms that the problem is on the **Ubuntu side** — the Ubuntu system you're trying to connect to is likely running **Wayland**, which AnyDesk does **not support** for remote control (only viewing in some cases).

---

### ✅ Solution: Change Ubuntu's display server to Xorg

You’ll need **physical or alternative remote access** to the Ubuntu machine (keyboard, monitor, or SSH) to fix this.

---

## 🛠️ Steps (on the Ubuntu machine)

### 🔁 Temporary Fix: Log in with Xorg instead of Wayland

1. **Log out** of the Ubuntu session (click top-right corner > Power icon > Log Out).

2. On the **login screen**:

   * Select the **username**.
   * Before entering your password, click the **gear icon ⚙️** in the bottom-right.
   * Choose **“Ubuntu on Xorg”**.
   * Then log in.

3. Now try connecting from Windows using AnyDesk. It should work.

> This change is temporary — after reboot, Ubuntu might use Wayland again.

---

### 🔧 Permanent Fix: Make Xorg the default display server

1. On the Ubuntu machine, open a terminal.

2. Run the following command to edit the display manager config:

   ```bash
   sudo nano /etc/gdm3/custom.conf
   ```

3. Look for the line:

   ```ini
   #WaylandEnable=false
   ```

4. **Uncomment it** by removing the `#`:

   ```ini
   WaylandEnable=false
   ```

5. Save and exit:

   * Press `Ctrl + O` to save
   * Press `Enter`
   * Press `Ctrl + X` to exit

6. Reboot Ubuntu:

   ```bash
   sudo reboot
   ```

7. After reboot, Ubuntu will use **Xorg** by default, and **AnyDesk will be fully compatible**.

---

### 🧪 How to confirm it's fixed

On Ubuntu, after logging in:

```bash
echo $XDG_SESSION_TYPE
```

* `x11` = ✅ Xorg (AnyDesk will work)
* `wayland` = ❌ Still using Wayland (AnyDesk won't work)

---

Let me know if you can access the Ubuntu machine physically or via SSH, and I can guide you through those steps.

