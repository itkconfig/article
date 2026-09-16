# 🛠️ ติดตั้ง EPEL และ htop

## 🛠️ Step 1: Import the EPEL GPG Key

Run this command to import the key from the Fedora project:

    sudo rpm --import https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-9

---

## 🛠️ Step 2: Retry the Installation

Now try installing the EPEL release package again:

    sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

---

## ✅ Step 3: Install htop

Once EPEL is successfully installed, you can install htop:

    sudo dnf install htop
