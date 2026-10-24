# Memória e Armazenamento - Glossário.

- **RAM (Random Access Memory)**: Memória de acesso aleatório, volátil, usada para armazenar dados temporários enquanto o computador está ligado.

- **ROM (Read-Only Memory)**: Memória apenas de leitura, não volátil, contém instruções permanentes que não podem ser modificadas.

- **SRAM (Static RAM)**: RAM estática que mantém os dados enquanto estiver energizada, sem necessidade de atualização.

- **DRAM (Dynamic RAM)**: RAM dinâmica que precisa ser constantemente atualizada para manter os dados.

- **CPU caches**: Memória de alta velocidade situada entre a CPU e a RAM principal para acelerar o acesso a dados frequentemente usados.

- **SDRAM (Synchronous DRAM)**: DRAM síncrona que opera em sincronia com o clock do sistema.

- **DDR SDRAM (Double Data Rate SDRAM)**: SDRAM que transfere dados tanto na borda de subida quanto na de descida do sinal de clock.

- **GDDR SDRAM (Graphics DDR SDRAM)**: Versão de DDR SDRAM otimizada para placas gráficas.

- **DDR4/DDR5**: Gerações sucessivas de memória DDR com velocidades e eficiências crescentes.

- **Firmware**: Software permanente instalado em dispositivos de hardware para controlar sua operação básica.

- **BIOS (Basic Input/Output System)**: Firmware que inicializa o hardware durante o processo de boot.

- **HDD (Hard Disk Drive)**: Dispositivo de armazenamento não volátil que usa discos magnéticos rotativos.

- **SSD (Solid State Drive)**: Dispositivo de armazenamento não volátil que usa memória flash, sem partes móveis.

- **USB Drive**: Dispositivo de armazenamento portátil que se conecta via porta USB.

- **SD Card (Secure Digital Card)**: Cartão de memória usado em dispositivos portáteis como câmeras e smartphones.

<br>

```mermaid
flowchart TD
   A[Tipos de Memória e Armazenamento] --> B[Memória]
   A --> C[Armazenamento]

   B --> D[RAM]
   B --> E[ROM]

   D --> F[SRAM]
   D --> G[DRAM]

   F --> H[CPU caches]
   F --> I["Mais rápido que DRAM"]

   G --> J[SDRAM]
   G --> K[DDR SDRAM]
   G --> L[GDDR SDRAM]

   K --> M[DDR4]
   K --> N[DDR5]

   E --> O[Firmware]
   E --> P[BIOS]

   C --> Q[HDD]
   C --> R[SSD]
   C --> S[USB Drive]
   C --> T[SD Card]
```
