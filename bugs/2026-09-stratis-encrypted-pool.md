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

## 💬 Discussão

### Cronologia

| Data | Evento |
|------|--------|
| 29/09/2026 | Bug reportado por mim (abdallahleandrodev) |
| 30/09/2026 | Vojtech Trefny (dev) tentou reproduzir e **não conseguiu** |
| 30/09/2026 | Em uma segunda tentativa minha, o problema **também não se reproduziu** |
| 15/10/2026 | **Petr Sklenar** (QA) reproduziu o bug em um ambiente com 5 discos, confirmando que é um problema real |
| 15/10/2026 | **John Baublitz** (dev) confirmou que é um bug e está trabalhando em uma correção |

### Análise

- Inicialmente, o bug parecia **intermitente** — não se reproduzia em todas as tentativas
- O QA **Petr Sklenar** confirmou que o problema **é real** e está relacionado ao `stratisd`
- A investigação revelou que, em emergency mode, o `stratis pool list` falha porque o **D-Bus não está disponível**
- O desenvolvedor **John Baublitz** identificou a causa e vai propor uma correção
- O componente correto foi alterado de `anaconda` para **`stratisd`**

### Status atual

- **Status:** ASSIGNED (correção em andamento)
- **Assignee:** mulhern
- **Componente:** stratisd
- **Severidade:** High

---

- [Comentário 10 do John Baublitz](https://bugzilla.redhat.com/show_bug.cgi?id=2543511#c10) — confirmação do bug e proposta de correção.
