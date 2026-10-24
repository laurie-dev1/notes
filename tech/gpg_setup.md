# GPG Setup, Key Management, and File Encryption Guide (Using Modern ECC Encryption)

This guide covers installing GPG on various operating systems (Windows, macOS, and Linux), generating a new key pair using modern Elliptic Curve Cryptography (ECC), exporting your public key, encrypting and decrypting files using users’ public keys, and encryption best practices.

> **Note:** Newer versions of GnuPG (2.2 and later) support ECC keys. It is recommended to use ECC (such as Curve25519 for encryption and EdDSA/Ed25519 for signing) instead of RSA for improved security and performance.

---

## 1. Installing GPG

### Windows (Using Gpg4win for Easiest Setup with GUI)
1. Download the latest Gpg4win installer from the [official website](https://www.gpg4win.org/).
2. Run the installer, accept the license agreement, and select the default components. This includes **Kleopatra**, a graphical user interface (GUI) for easy key management, encryption, and decryption tasks. Gpg4win is the recommended method for Windows as it provides a user-friendly experience without requiring command-line expertise.

### macOS (Using Homebrew for Easiest CLI Setup)
1. If Homebrew is not installed, install it from [https://brew.sh](https://brew.sh) by opening Terminal and running the installation command provided on the site.
2. In Terminal, run:

       brew install gnupg

   This installs the latest GnuPG version, optimized for macOS.

### Linux (Using Package Managers for Easiest Setup)
- **Debian/Ubuntu-based distributions**: Open Terminal and run:

      sudo apt update && sudo apt install gnupg

- **Fedora-based distributions**: Open Terminal and run:

      sudo dnf install gnupg2

- **Arch Linux-based distributions**: Open Terminal and run:

      sudo pacman -S gnupg

These methods ensure you get the most up-to-date and secure version from your distribution's repositories.

---

## 2. Generating a New ECC Key Pair

Modern GnuPG supports ECC keys, which are recommended over RSA for better security, smaller key sizes, and faster performance. We'll use curves like Curve25519 for encryption and Ed25519 for signing.

### Windows (Using Kleopatra GUI)
1. Launch **Kleopatra** from the Start menu.
2. Click **File → New Certificate…**.
3. Select **Create a personal OpenPGP key pair** and click **Next**.
4. Enter your name, email address, and optionally a comment, then click **Next**.
5. In **Advanced Settings**:
   - Choose **ECC** for the key type.
   - Select **Curve25519** for encryption and **Ed25519** for signing.
6. Set a key expiration date (recommended: 2-3 years) or choose no expiration, then click **Next**.
7. Enter a strong passphrase (at least 12 characters, mixing letters, numbers, and symbols) to protect your private key.
8. Click **Create** to generate the key pair.
9. Your new key will appear in the **My Certificates** tab.

### macOS / Linux (Using CLI)
1. Open Terminal.
2. Run:

       gpg --expert --full-generate-key

3. Follow the interactive prompts:
   - Select **(9) ECC and ECC** for key types.
   - Choose **(1) Curve25519** for encryption and **Ed25519** for signing (it may be the default).
   - Enter a key expiration (e.g., `2y` for 2 years, or `0` for no expiration).
   - Provide your user ID: real name, email, and optional comment.
   - Enter a strong passphrase to secure your private key.

Your key pair is now generated and added to your GnuPG keyring.

---

## 3. Setting Up and Adding Keys to the Keyring

The GnuPG keyring is a secure storage for your public and private keys. Your own key pair is automatically added during generation. For others' keys:

### Importing Public Keys to the Keyring
- **Windows (Kleopatra GUI)**: In Kleopatra, click **File → Import Certificates…**, select the public key file (e.g., `.asc`), and import it. It will appear under **Other Certificates**.
- **macOS / Linux (CLI)**: Run:

      gpg --import recipient_public_key.asc

This adds the key to your keyring for use in encryption.

### Handling Trust Relationships
GnuPG uses a "web of trust" model. By default, imported keys are untrusted. To establish trust:
1. Verify the key's fingerprint with the owner via a secure channel (e.g., in-person or encrypted video call).
2. **Set Trust Level**:
   - **Windows (Kleopatra)**: Right-click the imported key, select **Certify…**, confirm the fingerprint, and choose a certification level (e.g., "I have checked the fingerprint").
   - **macOS / Linux (CLI)**: Run `gpg --edit-key recipient@example.com`, then type `trust` and select a level (e.g., 5 for ultimate trust, but use cautiously—reserve ultimate for your own key).
3. For your own key, it should already have ultimate trust. This helps validate chains of trust for secure communication.

---

## 4. Verifying and Testing Your Keys

### Verifying Key Details
- **Windows (Kleopatra)**: In Kleopatra, double-click your key under **My Certificates** to view details like fingerprint, algorithm (should show ECC), and expiration.
- **macOS / Linux (CLI)**: Run:

      gpg --list-keys
      gpg --list-secret-keys

Look for your key ID, user info, and ECC indicators (e.g., `ed25519` or `cv25519`).

### Testing Key Functionality
1. Create a test file: In a text editor, save "This is a test." as `test.txt`.
2. Encrypt it to yourself (using your own public key):
   - **Windows (Kleopatra)**: Right-click `test.txt` in File Explorer, select **More GpgEX options → Encrypt**, choose your key, and encrypt.
   - **macOS / Linux (CLI)**: Run `gpg --encrypt --recipient your.email@example.com test.txt` (produces `test.txt.gpg`).
3. Decrypt it:
   - **Windows (Kleopatra)**: Right-click `test.txt.gpg`, select **More GpgEX options → Decrypt/Verify**, enter your passphrase.
   - **macOS / Linux (CLI)**: Run `gpg --decrypt test.txt.gpg > decrypted_test.txt`.
4. Verify the decrypted file matches the original. If successful, your keys work.

---

## 5. Exporting Your Public Key

Share your public key (not private!) for others to encrypt files to you.

- **Windows (Kleopatra)**: Right-click your key in **My Certificates**, select **Export…**, save as `my_public_key.asc` (ASCII-armored format).
- **macOS / Linux (CLI)**: Run:

      gpg --armor --export your.email@example.com > my_public_key.asc

---

## 6. Encrypting Files Using Public Keys

First, import and trust the recipient's public key (see Section 3).

### Encrypting a Single File
- **Windows (Kleopatra GUI)**: Right-click the file in File Explorer, select **More GpgEX options → Encrypt**, choose the recipient's key, and confirm.
- **macOS / Linux (CLI)**: Run:

      gpg --encrypt --recipient recipient@example.com filename.txt

Produces `filename.txt.gpg`.

### Encrypting Multiple Files
1. Compress into an archive first (recommended for efficiency):
   - Use built-in tools: On Windows/macOS, right-click files and zip; on Linux, run `zip archive.zip file1.txt file2.txt`.
2. Encrypt the archive as above.

Test by decrypting (see next section) to ensure functionality.

---

## 7. Decrypting Files

Files encrypted to your public key require your private key and passphrase.

- **Windows (Kleopatra GUI)**: Right-click the `.gpg` file, select **More GpgEX options → Decrypt/Verify**, enter your passphrase. The decrypted file is saved automatically.
- **macOS / Linux (CLI)**: Run:

      gpg --decrypt filename.txt.gpg > decrypted_filename.txt

Or to view without saving: `gpg --decrypt filename.txt.gpg`.

Test by comparing to the original if possible.

---

## 8. Decrypting & Re-Encrypting a file
Sometimes, we need to re-encrypt a file with new or different recipients' public keys.

Below shows how to take an existing `foo.txt.gpg`, decrypt it to the stdout, pipe it back to gpg, and encrypt for the same/new/different recipients:

```
gpg --output - --decrypt foo.txt.gpg | 
  gpg -e -r recip1@bar.com 
  -r recip2@bar.com 
  -r recip3@bar.com 
  -o newfoo.txt.gpg
```

---

## 9. Importance of Keeping Private Keys Safe

Your private key is the only way to decrypt files sent to you—losing it means permanent data loss; compromising it allows others to decrypt your data or impersonate you.

- **What "Safe" Means**: Store privately on encrypted drives or hardware security modules (e.g., YubiKey). Never share it. Use a strong passphrase.
- **Best Practices**:
  - Backup your private key: Export it (e.g., `gpg --export-secret-keys --armor > private_key.asc`) and store encrypted backups offline (e.g., on USB in a safe).
  - Avoid cloud storage unless encrypted.
  - Revoke and replace if compromised (use `gpg --edit-key` to set revocation).
  - Use hardware tokens for extra security if advanced.

---

## 10. Encryption Best Practices

- **Use Strong Passphrases:** Complex, unique, and managed with a password manager.
- **Regular Key Backups:** Export and store securely as above.
- **Set Key Expiration Dates:** Renew before expiry to maintain access.
- **Verify Public Keys:** Always check fingerprints via trusted channels.
- **Keep Software Updated:** Run updates for GnuPG/Gpg4win to patch vulnerabilities.
- **Secure Key Distribution:** Use key servers (e.g., `gpg --send-keys keyID`) or direct sharing.
- **Encrypt Locally:** Never send unencrypted sensitive data; encrypt before transmission.
- **Handle Trust Carefully:** Only certify keys after verification to build a secure web of trust.

---

This guide provides a comprehensive overview for setting up GPG on Windows (using the Gpg4win UI tool), macOS, and Linux with modern ECC encryption, managing keys, and securely encrypting/decrypting files while following best practices for encryption security.
