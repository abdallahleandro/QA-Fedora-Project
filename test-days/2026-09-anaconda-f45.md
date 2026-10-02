# Test Day: Anaconda F45 — Stratis

**Data:** 28 de setembro a 02 de outubro de 2026
**Página oficial:** https://testdays.fedoraproject.org/testday/28
**Wiki:** https://fedoraproject.org/wiki/Test_Day:2026-09-28_Anaconda_F45_features

---

## 🖥️ Ambiente de teste

- **Imagem:** Fedora-Workstation-Live-45-20260928.n.0.x86_64
- **VM:** KVM/QEMU via virt-manager
- **Recursos:** 4 vCPU, 8 GB RAM, 64 GB disco
- **Firmware:** BIOS (com biosboot partition)

---

## 🧪 Testes realizados

### ✅ Stratis - basic version

- Pool Stratis com filesystems `root` (`/`) e `home` (`/home`)
- Instalação concluída e boot funcionando
- Verificado com `findmnt`, `stratis pool list`, `stratis filesystem list` e `lsblk -f`
- **Resultado:** PASS

### ❌ Stratis - encrypted pool

- Pool criptografado com senha via Anaconda WebUI
- Plymouth pede a senha corretamente
- Após digitar a senha, sistema entra em **emergency mode**
- Erro: `Warning: /dev/stratis/fedorapool/root does not exist`
- **Bug reportado:** [Bugzilla 2543511](https://bugzilla.redhat.com/show_bug.cgi?id=2543511)
- **Resultado:** FAIL

### ✅ Stratis - multiple filesystems

- 3 filesystems: `root` (`/`), `home` (`/home`), `var` (`/var`)
- Todos montados corretamente a partir do mesmo pool
- **Resultado:** PASS

### ✅ Stratis - multiple devices

- Pool usando 2 discos (`vda` + `vdb`)
- Verificado com `stratis blockdev list`
- **Resultado:** PASS

### ✅ Stratis - whole disk

- Pool ocupando quase todo o disco
- Apenas `/boot/efi` e `/boot` fora do pool
- **Resultado:** PASS

### ✅ Stratis - multiple pools

- 2 pools separados: `pool_root` (`/`) e `pool_home` (`/home`)
- Verificado com `stratis pool list`, `stratis filesystem list` e `lsblk -f`
- **Resultado:** PASS

---

## 📊 Resultado final

| Teste | Status |
|-------|--------|
| Basic version | ✅ PASS |
| Encrypted pool | ❌ FAIL |
| Multiple filesystems | ✅ PASS |
| Multiple devices | ✅ PASS |
| Whole disk | ✅ PASS |
| Multiple pools | ✅ PASS |

**5 PASS, 1 FAIL** — bug reportado ao Bugzilla.

---

## 📸 Evidências

- `images/stratis-emergency-mode.png` — emergency mode após desbloqueio
- `images/plymouth-password.png` — prompt de senha do pool
- `images/stratis-pools-list.png` — pools ativos após o boot

---

## 🔗 Links úteis

- [Resultado no Test Day](https://testdays.fedoraproject.org/testday/28)
- [Bug 2543511](https://bugzilla.redhat.com/show_bug.cgi?id=2543511)
- [Wiki do Test Day](https://fedoraproject.org/wiki/Test_Day:2026-09-28_Anaconda_F45_features)
