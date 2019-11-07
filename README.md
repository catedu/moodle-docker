# Moodle de Aularagón Dockerizado

Para crear el entorno de pruebas, en esta carpeta hacer:

```bash
rsync -azvh --exclude-from 'exclude-list.txt' -P -e "ssh" moodle@____________:/home/moodle/moodle-docker/ .
```
