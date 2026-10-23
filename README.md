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
- No se realizó el reinicio opcional. Se comprobó una conexión SSH nueva después de optimizar.

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
