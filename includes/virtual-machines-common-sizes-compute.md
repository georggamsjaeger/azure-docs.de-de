<!-- F-series, Fs-series* -->

Compute-optimierte VM-Größen weisen ein großes Verhältnis von CPU zu Arbeitsspeicher auf und eignen sich gut für Webserver mit mittlerem Durchsatz, Netzwerkappliances, Batchprozesse und Anwendungsserver. Dieser Artikel enthält Informationen zur Anzahl von vCPUs, Datenträgern und NICs sowie zum Speicherdurchsatz und zur Netzwerkbandbreite der einzelnen Größen in dieser Gruppe.

Die Fsv2-Serie basiert auf dem Prozessor Intel® Xeon® Platinum 8168 mit einer Basiskernfrequenz von 2,7 GHz und einer maximalen Einzelkern-Turbofrequenz von 3,7 GHz. Intel® AVX-512-Anweisungen, die auf Intel Scalable Processors neu sind, können bei Gleitkommaoperationen mit einfacher und doppelter Genauigkeit mit Vektorverarbeitungeworkloads die Leistung verdoppeln. D.h., sie bewältigen alle Rechenworkloads wirklich schnell. 

Die Fsv2-Serie hat einen niedrigeren Listenpreis pro Stunde und bietet auf Basis der Azure-Compute-Einheit (Azure Compute Unit, ACU) das beste Preis-Leistungs-Verhältnis pro vCPU im Azure-Portfolio. 

Die F-Serie basiert auf dem Intel Xeon® E5-2673 v3-Prozessor (Haswell) mit 2,4 GHz, der mit der Intel Turbo Boost Technology 2.0 Taktfrequenzen von 3,1 GHz erreichen kann. Dies ist die gleiche CPU-Leistung wie bei virtuellen Computern (VMs) der Dv2-Serie.  

Virtuelle Computer der F-Serie sind eine hervorragende Wahl für Workloads, die schnellere CPUs erfordern, aber nicht so viel Arbeitsspeicher oder temporären Speicher pro vCPU benötigen.  Bei Arbeitslasten wie Analysen, Gamingservern, Webservern und Batchverarbeitung kommen die Vorteile und der Nutzen der F-Serie besonders gut zum Tragen.

Die Fs-Serie verfügt zusätzlich zum Premium-Speicher über alle Vorteile der F-Serie.

## <a name="fsv2-series"></a>Fsv2-Serie*

ACU: 195 – 210

| Größe             | vCPUs | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s (Cachegröße in GiB) | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4.000 (32)                                                             | Moderat                                       |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8.000 (64)                                                             | Moderat                                       |
| Standard_F8s_v2   | 8      | 16          | 64             | 16             | 16.000 (128)                                                           | Hoch                                           |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32.000 (256)                                                           | Hoch                                           |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64.000 (512)                                                           | Äußerst hoch                                 |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128.000 (1024)                                                         | Äußerst hoch                                 |
| Standard_F72s_v2 | 72     | 144         | 576            | 32             | 144.000 (1520)                                                         | Äußerst hoch                                 |
* Virtuelle Computer der Fsv2-Serie verfügen über Hyper-Threading-Technologie von Intel®

## <a name="fs-series"></a>Fs-Serie*

ACU: 210 - 250

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s (Cachegröße in GiB) | Maximaler Datenträgerdurchsatz ohne Cache: IOPS / MB/s | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4.000/32 (12) |3.200/48 |2/750 |
| Standard_F2s |2 |4 |8 |8 |8.000/64 (24) |6.400/96 |2/1500 |
| Standard_F4s |4 |8 |16 |16 |16.000/128 (48) |12.800/192 |4/3000 |
| Standard_F8s |8 |16 |32 |32 |32.000/256 (96) |25.600/384 |8/6000 |
| Standard_F16s |16 |32 |64 |64 |64.000/512 (192) |51.200/768 |8/6000–12000 &#8224; |

MB/s = 10^6 Bytes pro Sekunde und GB = 1.024^3 Bytes.

* Der mit einer VM der Fs-Serie maximal mögliche Datenträgerdurchsatz (IOPS oder MB/s) kann durch Anzahl, Größe und Striping der angefügten Datenträger beschränkt werden.  Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](../articles/virtual-machines/windows/premium-storage.md).


<br>

## <a name="f-series"></a>F-Serie

ACU: 210 - 250

| Größe         | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/Lesen (MBit/s)/Schreiben (MBit/s) | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000/46/23                                           | 4/4 x 500                         | 2/750                 |
| Standard_F2  | 2         | 4           | 32             | 6000/93/46                                           | 8/8 x 500                         | 2/1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000/187/93                                         | 16/16 x 500                         | 4/3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000/375/187                                        | 32/32 x 500                       | 8/6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000/750/375                                        | 64/64 x 500                       | 8/6000–12000 &#8224;           |


<br>


