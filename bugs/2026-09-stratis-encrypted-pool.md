# Bug: Stratis encrypted pool fails to boot

**Bugzilla:** https://bugzilla.redhat.com/show_bug.cgi?id=2543511

**Componente:** anaconda

**Versão:** Fedora 45

**Severidade:** High

**Status:** NEW

---

## 📝 Descrição

Ao instalar o Fedora 45 com **pool Stratis criptografado** via Anaconda WebUI, o sistema entra em **emergency mode** após o desbloqueio do pool com a senha no Plymouth.

O prompt de senha aparece corretamente, a senha é aceita, mas o `dracut` não encontra o filesystem raiz (`/dev/stratis/fedorapool/root`), impedindo o boot.

---

## 🔄 Passos para reproduzir

1. Iniciar o Fedora 45 Workstation Live
2. Abrir o instalador e escolher **custom partitioning** no Cockpit Storage
3. Criar as partições:
   - `biosboot` (1 MiB)
   - `/boot/efi` (VFAT, 500 MB)
   - `/boot` (ext4, 1 GB)
4. Criar um **pool Stratis com criptografia** e definir uma senha
5. Dentro do pool, criar os filesystems:
   - `root` → `/`
   - `home` → `/home`
6. Completar a instalação e reiniciar
7. Digitar a senha do pool no prompt do Plymouth

---

## ❌ Resultado real

Após digitar a senha corretamente, o sistema entra em emergency mode com:

---

O pool é desbloqueado (o prompt de senha funciona), mas o `dracut` não encontra o filesystem raiz.

---

## ✅ Resultado esperado

O sistema deveria desbloquear o pool Stratis criptografado e montar `/` e `/home` normalmente, permitindo o boot completo.

---

## 🖥️ Ambiente

- **Imagem:** Fedora-Workstation-Live-45-20260928.n.0.x86_64
- **VM:** KVM/QEMU via virt-manager
- **Recursos:** 4 vCPU, 8 GB RAM, 64 GB disco
- **Firmware:** BIOS (com biosboot partition)
- **Pool UUID:** `0fb7306e-a1d7-4f88-9aea-cc2e405462fb`

---
