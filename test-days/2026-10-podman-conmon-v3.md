# Test Day: Podman Conmon-v3 — Fedora 44


<img width="738" height="369" alt="podman" src="https://github.com/user-attachments/assets/71868581-4da2-4925-b2cb-4436bcd77c80" />



**Data:** 12 a 18 de outubro de 2026

**Página oficial:** https://testdays.fedoraproject.org/testday/29

**Wiki:** https://fedoraproject.org/wiki/Test_Day:2026-10-12_Podman_Conmon-v3

**Test case:** https://fedoraproject.org/wiki/QA:Testcase_podman_conmon_v3

---

## 🖥️ Ambiente de teste

- **Imagem:** Fedora 44 Workstation
- **VM:** KVM/QEMU via virt-manager
- **Recursos:** 4 vCPU, 8 GB RAM, 60 GB disco
- **Podman:** 6.1.3
- **conmon-v3:** 3.0.0-dev (commit `a0e1fd02cec26a8c6771cec5de2503bb60ec677d`)

---

## 🔧 Configuração

O Podman foi configurado para usar o conmon-v3 através do drop-in:

```ini
# /etc/containers/containers.conf.d/99-conmon-v3.conf
[engine]
conmon_path = ["/usr/bin/conmon-v3"]
```

Verificação:

```bash
$ podman info --format '{{.Host.Conmon}}'
conmon-v3-3.0.0~dev-5.20261007092403828293.main.69.g3f6f404.fc44.x86_64
/usr/bin/conmon-v3
conmon version 3.0.0-dev
```

---

## 🧪 Testes realizados

### ✅ common sanity

- Container iniciado com nome único
- Logs mostraram `START` e `STDERR`
- Exec imprimiu `EXEC_OK`
- Estado após stop: `exited`
- Exit code propagado: `42`
- TTY interativo: `TTY_OK`
- **Resultado:** PASS

### ✅ common exit

- `podman run --rm ... sh -c 'exit 42'`
- Exit code propagado corretamente: `42`
- **Resultado:** PASS

### ✅ common log file

- Container com stdout e stderr
- `podman logs` capturou `START` (stdout) e `STDERR` (stderr)
- **Resultado:** PASS

### ✅ common interactive

- `podman run --rm -it ... sh`
- Shell interativo aceitou input, imprimiu `TTY_OK`, retornou ao host após `exit`
- **Resultado:** PASS

### ✅ common exec_stop

- Container em background
- `podman exec` imprimiu `EXEC_OK`
- `podman stop -t 2` encerrou (aviso de SIGKILL aceitável)
- `podman inspect` mostrou `exited`
- `podman rm` removeu o container
- **Resultado:** PASS

### ✅ common stdin

- Pipe direto: `echo "hello from stdin" | podman run -i ... cat`
- Interativo: entrada via `Ctrl+D` corretamente terminada
- **Resultado:** PASS

### ✅ common journald

- `podman run --log-driver=journald ...`
- Mensagem capturada por `journalctl`
- **Resultado:** PASS

---

## 📊 Resultado final

| Teste | Status |
|-------|--------|
| common sanity | ✅ PASS |
| common exit | ✅ PASS |
| common log file | ✅ PASS |
| common interactive | ✅ PASS |
| common exec_stop | ✅ PASS |
| common stdin | ✅ PASS |
| common journald | ✅ PASS |

**7 PASS, 0 FAIL** — nenhum bug encontrado.

---

## 💡 Observações

- O aviso de SIGKILL durante o `podman stop` é esperado para containers que não respondem a SIGTERM em 2 segundos (como `sleep 300`). Não é falha do conmon-v3.
- Todos os testes foram executados em modo rootless, no mesmo terminal.
- Nenhum comportamento inesperado foi observado durante os testes.

---

## 🔗 Links úteis

- [Resultado no Test Day](https://testdays.fedoraproject.org/testday/29)
- [Wiki do Test Day](https://fedoraproject.org/wiki/Test_Day:2026-10-12_Podman_Conmon-v3)
- [Test case oficial](https://fedoraproject.org/wiki/QA:Testcase_podman_conmon_v3)
