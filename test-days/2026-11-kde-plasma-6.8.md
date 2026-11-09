# Test Day: KDE Plasma 6.8 — Fedora 45

**Data:** 9 a 15 de novembro de 2026
**Página oficial:** https://testdays.fedoraproject.org/testday/32 (ou o número correspondente)
**Wiki:** https://fedoraproject.org/wiki/Test_Day:2026-11-09_KDE_Plasma_6.8

---

## 🖥️ Ambiente de teste

- **Imagem:** Fedora-KDE-Desktop-Live-45-20260912.n.0.x86_64
- **VM:** KVM/QEMU via virt-manager
- **Recursos:** 4 vCPU, 8 GB RAM, 60 GB disco
- **Plasma:** 6.8
- **Kernel:** 7.3.1-200.fc45.x86_64 (ou o kernel atual da VM)

---

<img width="1057" height="475" alt="Teste_KDE" src="https://github.com/user-attachments/assets/f77b404f-16ff-4459-a2e7-07f4e0982a17" />


## 🧪 Testes realizados

### ✅ Exploratory Testing

- Uso geral do sistema: Dolphin, Konsole, System Settings, Discover
- Nenhum crash, freeze ou glitch visual
- **Resultado:** PASS

### ✅ Window Manager

- Movimentação, redimensionamento, maximização e restauração de janelas
- Múltiplas janelas, workspaces (criados e testados), fullscreen
- **Resultado:** PASS

### ✅ Login

- Login/logout com dois usuários
- Tratamento de senha inválida e retry após falha de autenticação
- Reboot e shutdown
- **Resultado:** PASS

### ✅ Panel

- Componentes do painel, lançamento e troca de aplicativos
- Menu de aplicativos, system tray, relógio/calendário e notificações
- **Resultado:** PASS

### ✅ Graphical Update

- Atualização via KDE Discover (sistema já atualizado, mas Discover funcionou)
- **Resultado:** PASS

### ✅ Automount

- Dispositivo removível virtual conectado
- Montagem automática habilitada nas Configurações do Sistema
- Dispositivo montado em `/run/media/leandro/ISOIMAGE`
- Verificado com `lsblk -f` e `mount`
- **Resultado:** PASS

### ✅ Audio

- Dispositivo de saída detectado corretamente
- Sons de teste reproduzidos
- Controle de volume funcionando pelo mixer do Plasma
- **Resultado:** PASS

---

## 📊 Resultado final

| Teste | Status |
|-------|--------|
| Exploratory Testing | ✅ PASS |
| Window Manager | ✅ PASS |
| Login | ✅ PASS |
| Panel | ✅ PASS |
| Graphical Update | ✅ PASS |
| Automount | ✅ PASS |
| Audio | ✅ PASS |

**7 PASS, 0 FAIL** — nenhum bug encontrado.

---

## 🔗 Links úteis

- [Resultado no Test Day](https://testdays.fedoraproject.org/testday/32)
- [Wiki do Test Day](https://fedoraproject.org/wiki/Test_Day:2026-11-09_KDE_Plasma_6.8)
