# Despliegue en EC2 (AWS Academy Learner Lab)

El Learner Lab entrega credenciales temporales, por lo que el despliegue se hace
a mano con el artefacto que genera GitHub Actions.

## 1. Descargar el artefacto

En GitHub: **Actions → CI → (ejecución) → Artifacts → `dsy1104-backend-<sha>`**,
o con la CLI:

```bash
gh run download --repo <usuario>/dsy1104-backend --dir artefacto
```

## 2. Preparar la EC2 (una sola vez)

- AMI: Amazon Linux 2023, tipo `t2.micro`/`t3.micro`, key pair `vockey` (o la tuya).
- Security group: entrada TCP **22** (tu IP) y TCP **8080** (0.0.0.0/0 o el origen del frontend).

```bash
sudo dnf install -y java-17-amazon-corretto-headless
sudo mkdir -p /opt/dsy1104-backend && sudo chown ec2-user: /opt/dsy1104-backend
```

## 3. Copiar y configurar

Desde tu equipo (con `labsuser.pem` descargado de *AWS Details*):

```bash
cd artefacto/dsy1104-backend-*/
sha256sum -c app.jar.sha256
scp -i labsuser.pem app.jar app.env.example dsy1104-backend.service \
    ec2-user@<IP-PUBLICA>:/opt/dsy1104-backend/
```

En la EC2:

```bash
cd /opt/dsy1104-backend
cp app.env.example app.env && nano app.env          # completar variables
chmod 600 app.env
sudo cp dsy1104-backend.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now dsy1104-backend
curl http://localhost:8080/actuator/health          # {"status":"UP"}
```

## 4. Actualizar a una nueva versión

```bash
scp -i labsuser.pem app.jar ec2-user@<IP-PUBLICA>:/opt/dsy1104-backend/
ssh -i labsuser.pem ec2-user@<IP-PUBLICA> 'sudo systemctl restart dsy1104-backend'
```

> Al reiniciar el laboratorio la IP pública de la EC2 cambia: actualiza
> `VITE_API_URL` en el frontend (y recompila) o asigna una Elastic IP.
> Logs: `journalctl -u dsy1104-backend -f`.
