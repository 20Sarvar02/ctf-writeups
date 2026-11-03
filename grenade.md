
# HackMyVM: Grenade — Write-up

## Mashina haqida
- Qiyinchilik: Oson
- OS: Linux (AlmaLinux 10)
- IP: 192.168.56.104

## 1. Recon (Tarmoqni tekshirish)
Nmap orqali ikkita ochiq port topildi: 22 (SSH) va 8080 (HTTP).

nmap -sC -sV -p- 192.168.56.104


8080-portda WordPress sayti ishlayotgani va unda **GiveWP** (xayriya) plagini o'rnatilganligi aniqlandi (versiya 4.16.5.1).

## 2. Initial Access (Serverga birinchi kirish)
GiveWP 4.16.5.1 versiyasida **CVE-2026-82222** nomli kritik (CVSS 10.0) RCE zaifligi borligi aniqlandi. Bu zaiflik orqali autentifikatsiyasiz serverda buyruq bajarish mumkin.

Ochiq PoC (github.com/dinosn/givewp-cve-2026-82222-rce-lab) yordamida reverse shell olindi, natijada `www-data` foydalanuvchisi huquqida shell qo'lga kiritildi.

## 3. Privilege Escalation (Imtiyozni oshirish)
`wp-config.php` faylidan MySQL login ma'lumotlari topildi. Kernel tekshiruvi (linpeas) natijasida **CVE-2026-31431 ("Copy Fail")** — kernel darajasidagi LPE zaifligi topildi va "VULNERABLE" deb tasdiqlandi.

Rasmiy PoC (github.com/theori-io/copy-fail-CVE-2026-31431) yordamida `/usr/bin/su` faylini page-cache darajasida vaqtincha "yamab", to'g'ridan-to'g'ri **root** shell olindi.

## 4. Flags
- User: `HMV{user-a1ec23e9b9ab43a88222d9949ee26499}`
- Root: `HMV{root-2582609dcdfd475beea5ab54442a532d}`

## Xulosa
Bu mashina ikkita zamonaviy real-dunyo CVE'sini (WordPress plugin RCE va Linux kernel LPE) zanjirlashtirib yechishni o'rgatadi.
