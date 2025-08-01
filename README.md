# Termux-Client-Server-Architecture-for-Sensor-Data-Collection
Este projeto propõe uma arquitetura cliente-servidor utilizando o aplicativo Termux em dispositivos Android para coleta e envio de dados sensoriais. O cliente, baseado em shell scripts, coleta informações do acelerômetro, sensor de luz e localização, e realiza uploads automáticos para um servidor remoto (Google Drive).

⚙️ Objetivos

Utilizar o Termux como ambiente de execução de scripts no Android

Coletar dados de sensores do próprio dispositivo (acelerômetro, luz, GPS)

Enviar os dados para um servidor remoto (Google Drive) de forma automatizada

Criar um sistema de monitoramento portátil e de baixo custo



---

📊 Tecnologias utilizadas

Tecnologia	Função

Termux	Terminal Linux no Android
Shell Script	Scripts de automação
termux-sensor	Leitura de sensores
termux-location	Acesso à localização GPS/rede
rclone	Upload para Google Drive
Google Drive	Armazenamento remoto
(Opcional) Termux:Boot	Execução automática ao ligar o dispositivo



---

🛠️ Arquitetura do sistema

+-------------+        captura.sh         +---------------------+
| Dispositivo |-------------------------->| Google Drive (nuvem) |
|   Android   |                           +---------------------+
| (com Termux)|                             (Servidor remoto)
+-------------+
      |
      | sensores: acelerômetro, luz, GPS
      v
   coleta + upload


---

▶️ Funcionamento do sistema

Um script chamado sensores_drive.sh é executado a cada 30 segundos

Ele coleta:

Acelerômetro (via termux-sensor)

Luz ambiente (via termux-sensor)

Localização (via termux-location)


Os dados são salvos em um arquivo .json

O arquivo é automaticamente enviado para o Google Drive usando rclone



---

📃 Exemplo de saída JSON

{
  "timestamp": "2025-08-01 15:35:22",
  "acelerometro": [
    {
      "name": "accelerometer",
      "values": [-0.1, 9.81, 0.05]
    }
  ],
  "luz": [
    {
      "name": "light",
      "values": [217.5]
    }
  ],
  "localizacao": {
    "latitude": -23.5611,
    "longitude": -46.6562,
    "provider": "gps",
    "accuracy": 5.2
  }
}


---

🌍 Aplicações possíveis

Monitoramento ambiental em áreas remotas

Coleta de dados de mobilidade urbana

Experimentos científicos em campo com celulares

Prototipagem de sistemas IoT sem hardware adicional



---

⚠️ Limitações e melhorias futuras

Necessita permissão manual para localização e sensores

Pode ser limitado em segundo plano sem Termux:Boot

Pode ser estendido com:

API REST própria

Banco de dados online

Painel de visualização em tempo real




---

📂 Código-fonte exemplo

#!/data/data/com.termux/files/usr/bin/bash

TIMESTAMP=$(date '+%Y-%m-%d_%H-%M-%S')
ARQUIVO="/data/data/com.termux/files/home/storage/downloads/sensores_$TIMESTAMP.json"

ACELEROMETRO=$(termux-sensor -s accelerometer -n 1 2>/dev/null)
LUZ=$(termux-sensor -s light -n 1 2>/dev/null)
LOCALIZACAO=$(termux-location -p gps -r once 2>/dev/null)
[[ -z "$LOCALIZACAO" || "$LOCALIZACAO" == *"error"* ]] && \
  LOCALIZACAO=$(termux-location -p network -r once 2>/dev/null)

{
echo "{"
echo "  \"timestamp\": \"$TIMESTAMP\","  
echo "  \"acelerometro\": $ACELEROMETRO,"
echo "  \"luz\": $LUZ,"
echo "  \"localizacao\": $LOCALIZACAO"
echo "}"
} > "$ARQUIVO"

rclone copy "$ARQUIVO" sensores2:sensores_termux


---

> Criado por André Luiz Cunha, 2025
