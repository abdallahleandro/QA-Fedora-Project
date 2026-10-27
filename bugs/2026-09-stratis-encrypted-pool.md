# Bug: Stratis encrypted pool fails to boot

**Bugzilla:** https://bugzilla.redhat.com/show_bug.cgi?id=2543511

**Componente:** stratisd

**Versão:** Fedora 45

**Severidade:** High

**Status:** ASSIGNED

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
Warning: /dev/stratis/fedorapool/root does not exist
Generating "/run/initramfs/rdsosreport.txt"
Entering emergency mode.

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
| 15/10/2026 | **John Baublitz** (dev) confirmou que é um bug e propôs uma correção |
| 16/10/2026 | **Petr Sklenar** testou a correção do COPR e o problema **persistiu** |
| 16/10/2026 | **mulhern** (dev) explicou que o `initramfs` precisa ser regenerado para incluir a correção |
| 16/10/2026 | Após regenerar o `initramfs` (`dracut --force --regenerate-all`), o cenário mudou: a raiz `/` monta, mas o pool inteiro ainda falha em ~34% dos reboots (20 testes) |
| 20/10/2026 | **Adam Williamson** (Fedora QA) propôs o bug como **Final Blocker** do Fedora 45, citando a violação do critério de boot com partições criptografadas |
| 22/10/2026 | **Adam Williamson** (Fedora QA) anunciou que o bug foi **aceito como bloqueador oficial (AcceptedBlocker)** na reunião F45 Go/No-Go #2, impedindo o lançamento do Fedora 45 até ser corrigido ou dispensado. |
| 23/10/2026 | **Adam Williamson** tentou reproduzir com 1 disco e **não conseguiu** (6 boots com sucesso) |
| 23/10/2026 | **John Baublitz** (dev) **reproduziu o bug** e descobriu a causa: uma *race condition* entre a montagem do pool e o registro dos dispositivos pelo `udev`, que só acontece quando o pool usa **múltiplos discos** |
| 23/10/2026 | **John Baublitz** anunciou que já tem uma correção em andamento para o problema |
| 26/10/2026 | **Adam Williamson** cobrou urgência na correção para não atrasar o lançamento do Fedora 45 |
| 26/10/2026 | **John Baublitz** publicou a correção final (PR #4113) e testou com sucesso em 5 reboots usando um pool com 5 discos |

### Análise

- Inicialmente, o bug parecia **intermitente** — não se reproduzia em todas as tentativas
- O QA **Petr Sklenar** confirmou que o problema **é real** e está relacionado ao `stratisd`
- O desenvolvedor **John Baublitz** propôs uma correção e o componente foi alterado para `stratisd`
- A primeira correção resolveu a falha onde o `/` não montava, mas ainda há um problema intermitente (pool quebra em ~34% dos boots)
- A equipe de desenvolvimento continua investigando a causa raiz da falha restante
- O bug foi escalado como **candidato a bloqueador do lançamento** do Fedora 45, devido ao impacto na experiência do usuário e ao fato de o Stratis ser um recurso anunciado
- O bug foi **aceito como bloqueador oficial** do Fedora 45 (Final Blocker). Isso significa que a equipe de desenvolvimento tem prioridade máxima para corrigi-lo antes do lançamento.
- A causa raiz foi identificada: o bug ocorre apenas em pools com **múltiplos discos**, devido a uma *race condition* na inicialização (o sistema tenta montar o pool antes do `udev` registrar todos os discos). Uma correção já está sendo desenvolvida.
- O desenvolvedor **John Baublitz** publicou a correção final (PR #4113) e validou com sucesso em um ambiente de múltiplos discos. A correção está aguardando o build do pacote e a publicação no repositório de testes.

### Status atual

- **Status:** ASSIGNED (AcceptedBlocker)
- **Assignee:** mulhern
- **Componente:** stratisd
- **QA Contact:** Petr Sklenar
- **Severidade:** High
- **Última atualização:** 23/10/2026 — causa raiz identificada (race condition com múltiplos discos) e correção em desenvolvimento
- **Status:** ASSIGNED (AcceptedBlocker) - Correção publicada e aguardando build
- **Assignee:** mulhern
- **Componente:** stratisd
- **QA Contact:** Petr Sklenar
- **Severidade:** High
- **Última atualização:** 26/10/2026 — correção final publicada (PR #4113) e aguardando build

### Links úteis

- [Logs do teste do Petr Sklenar (inicial)](https://psklenar.fedorapeople.org/bz2543511/)
- [Logs após regeneração do dracut](https://psklenar.fedorapeople.org/bz2543511/after-dracut-regenerate/)
- [F45 Go/No-Go Meeting #2 (22/10/2026)](https://meetbot-raw.fedoraproject.org/meeting_matrix_fedoraproject-org/2026-10-22/f45-final-go-no-go-meeting.2026-10-22-17.00.html)
- [PR #4113 - Correção do bug](https://github.com/stratis-storage/stratisd/pull/4113)
