---
title: Uaktualnij urządzenie modułu zbierającego w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera informacje dotyczące uaktualnień dla urządzenia w usłudze Azure Migrate Collector.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: f9f2ef25a2054d92cdcc3ab33ea9234883e352d6
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484512"
---
# <a name="collector-appliance-updates"></a>Aktualizacje urządzenia modułu zbierającego

## <a name="how-to-upgrade-the-appliance"></a>Jak uaktualnić urządzenie

Moduł zbierający można uaktualnić do najnowszej wersji, bez pobierania OVA ponownie.

1. Zamknij wszystkie okna przeglądarki i wszystkie otwarte pliki i foldery w urządzeniu.
2. Pobierz najnowszy pakiet aktualizacji na liście aktualizacji wymienionych poniżej w tym artykule.
3. Aby upewnić się, że pobrany pakiet jest bezpieczne, Otwórz okno wiersza polecenia administratora i uruchom następujące polecenie, aby wygenerować wartość skrótu dla pliku ZIP. Wygenerowany skrót powinien odpowiadać za pomocą skrótu wymienionych dla określonej wersji:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Przykład: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. Skopiuj plik zip do urządzenia modułu zbierającego maszyny Wirtualnej.
5. Kliknij prawym przyciskiem myszy plik zip > **Wyodrębnij wszystkie**.
6. Kliknij prawym przyciskiem myszy **Setup.ps1** > **Uruchom przy użyciu programu PowerShell**i postępuj zgodnie z instrukcjami instalacji.

## <a name="collector-update-release-history"></a>Historia wersji aktualizacji modułu zbierającego

Ten artykuł zawiera podsumowanie uaktualnienia informacji dla urządzenia modułu zbierającego w [usługi Azure Migrate](migrate-overview.md).

Azure Migrate Collector to urządzenie uproszczone, które służy do odnajdywania w lokalnym środowisku vCenter do celów oceny przed migracją na platformę Azure. [Dowiedz się więcej](concepts-collector.md).

### <a name="continuous-discovery-upgrade-versions"></a>Ciągłe odnajdywania: Uaktualnienie wersji

#### <a name="version-101012-released-on-03132019"></a>Wersja 1.0.10.12 (wydane 03/13/2019 r)

Rozwiązuje problemy dotyczące doboru platformy Azure w chmurze w urządzeniu.

Wartości dla uaktualnienie mieszania [pakietu 1.0.10.12](https://aka.ms/migrate/col/upgrade_10_12)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Jednorazowe odnajdywania (przestarzałe teraz): Poprzednie wersje uaktualnienia

> [!NOTE]
> Urządzenie jednorazowego odnajdywania jest już przestarzałe, ponieważ ta metoda opierała się na ustawieniach statystyk programu vCenter Server w zakresie dostępności punktów danych wydajności i zbierała średnią liczników wydajności, co powodowało określanie zbyt małego rozmiaru maszyn wirtualnych na potrzeby migracji na platformę Azure.

#### <a name="version-10916-released-on-10292018"></a>Wersja 1.0.9.16 (wydane 2018-10/29)

Zawiera poprawki dotyczące problemów PowerCLI zmierzyła się z zespołem podczas konfigurowania urządzenia.

Wartości dla uaktualnienie mieszania [pakietu 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>Wersja 1.0.9.14

Wartości dla uaktualnienie mieszania [pakietu 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>Wersja 1.0.9.13

Wartości dla uaktualnienie mieszania [pakietu 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej](concepts-collector.md) o urządzenia modułu zbierającego.
- [Uruchom ocenę](tutorial-assessment-vmware.md) dla maszyn wirtualnych VMware.
