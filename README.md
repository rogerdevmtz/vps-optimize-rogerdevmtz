# VPS: acceso SSH y optimización de RAM

Entrega del proyecto de 4Geeks. Informe principal: [resources-after.txt](resources-after.txt).

## Resultado

Ubuntu 22.04.5 LTS, kernel 5.15.0-191-generic. Acceso real por SSH; segunda sesión autenticada después de los cambios.

| Medición | Antes | Después |
|---|---:|---:|
| RAM total | 957 MiB | 957 MiB |
| RAM usada | 174 MiB | 158 MiB |
| RAM disponible | 628 MiB | 653 MiB |
| Disco raíz total / disponible | 25G / 23G | 25G / 23G |
| Swap | 0 | 0 |

La mejora observada es de unos 25 MiB disponibles; son mediciones puntuales, no una garantía ni una prueba aislada del efecto de cada servicio. La imagen ya era ligera.

## Cambios y decisiones

- `multipath -ll` no mostró rutas; discos virtuales ordinarios. Desactivados `multipathd.service` y su socket para impedir la reactivación.
- `apport.service` deshabilitado mediante systemctl, que delegó en systemd-sysv-install. Se muestra `active (exited)` con `MainPID=0`: no tiene un proceso residente. No se afirma que is-active devuelva inactive.
- cups, avahi-daemon, ModemManager, bluetooth y popularity-contest no estaban instalados; se documentaron y omitieron.
- Snap conservado porque LXD está instalado; no se asumió que se pudiera retirar su infraestructura.
- Actualizaciones automáticas de seguridad conservadas. SSH, red, DNS, logs, cron y agente del proveedor permanecen activos.
- Reinicio opcional completado. Boot ID distinto y nueva conexión SSH comprobados; sistema running, sin unidades fallidas, DNS operativo y servicios esenciales activos. Multipath permanece disabled/inactive; apport inactive/dead.

## Evidencia

- `resources-before.txt`: SO, RAM, disco y servicios antes.
- `resources-after.txt`: salida real después y comunicación de disponibilidad para despliegues.
- `optimization-log.txt`: comandos y estados de servicios.
- `dependency-check.txt`: comprobación de Snap/LXD y multipath.
- `ssh-verification.txt`: segunda conexión, hostname, usuario y uptime.

VPS preparado como base para los próximos despliegues. No se ha desplegado ni validado una aplicación. Sin contraseñas, claves privadas ni tokens en esta entrega.

## Referencia y reversión

Se siguió la lección del curso: https://github.com/4GeeksAcademy/ai-engineering-syllabus/blob/main/content/lessons/optimize-ubuntu-vps-ram-efficiency/optimize-ubuntu-vps-ram-efficiency.es.md

Si se necesita multipath posteriormente: `sudo systemctl enable --now multipathd.socket multipathd.service`.
Para volver a habilitar apport: `sudo systemctl enable --now apport.service`.

## Verificación después del reinicio

El informe principal `resources-after.txt` contiene la medición más reciente tras reiniciar: RAM total 957 MiB, usada 141 MiB, disponible 675 MiB; disco raíz 25G total, 23G disponibles. La tabla anterior corresponde a la medición inmediatamente después de optimizar, conservada en `resources-after-optimization.txt`. Los cambios entre mediciones también dependen del arranque, cachés y servicios transitorios.

`boot-before.txt` y `reboot-verification.txt` registran identificadores de arranque diferentes y la reconexión SSH. Sistema `running`, cero unidades fallidas, DNS resuelve archive.ubuntu.com, SSH/red/logs/cron/actualizaciones/Snap activos. Multipath y su socket permanecen disabled/inactive. Apport queda inactive/dead tras arrancar; su unit generada no equivale a enabled.

## Correspondencia con la rúbrica

| Requisito | Evidencia |
|---|---|
| Credenciales del VPS del curso y acceso SSH real | resources-before.txt, ssh-verification.txt, reboot-verification.txt |
| Prompt remoto, hostname y usuario | Los mismos archivos, hostname bc-vps-465 y usuario root |
| Inventario previo: SO, RAM, disco y servicios | resources-before.txt |
| Aplicación de la guía y decisiones seguras | optimization-log.txt, dependency-check.txt y decisiones del README |
| SSH funcional después de optimizar y reiniciar | ssh-verification.txt, reboot-verification.txt |
| Reinicio y persistencia de servicios desactivados | boot-before.txt, reboot-verification.txt |
| TXT con salida real posterior de free -h y df -h, SO y aviso de VPS listo | resources-after.txt |
| Repositorio accesible al instructor | https://github.com/rogerdevmtz/vps-optimize-rogerdevmtz |
| Sin credenciales, claves privadas ni tokens | Solo se publican mediciones y estados de servicios; archivos revisados |

Todos los requisitos obligatorios y el reinicio opcional están cubiertos. El informe declara disponibilidad del VPS como base de despliegue; no certifica una aplicación que todavía no se ha instalado.
