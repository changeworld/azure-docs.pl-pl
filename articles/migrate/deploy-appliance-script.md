---
title: Konfigurowanie urządzenia migracji platformy Azure za pomocą skryptu
description: Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure za pomocą skryptu
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337690"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurowanie urządzenia za pomocą skryptu

W tym [artykule](deploy-appliance.md) opisano sposób konfigurowania urządzenia migracji platformy Azure przy użyciu skryptu instalatora programu PowerShell.

Skrypt zawiera:
- Alternatywa dla konfigurowania urządzenia przy użyciu szablonu OVA, do oceny i bezagentowej migracji maszyn wirtualnych VMware.
- Alternatywa dla konfigurowania urządzenia przy użyciu szablonu VHD do oceny i migracji maszyn wirtualnych funkcji Hyper-V.
- Do oceny serwerów fizycznych (lub maszyn wirtualnych, które mają być migrowane jako serwery fizyczne), skrypt jest jedyną metodą konfigurowania urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Skrypt konfiguruje urządzenie migracji platformy Azure na istniejącym komputerze fizycznym lub maszynie wirtualnej.

- Komputer, który będzie działać jako urządzenie, musi być uruchomiony w systemie Windows Server 2016, z 32 GB pamięci, ośmioma procesorami wirtualnymi, około 80 GB pamięci masowej na dysku i zewnętrznym przełącznikiem wirtualnym. Wymaga statycznego lub dynamicznego adresu IP i dostępu do Internetu.
- Przed wdrożeniem urządzenia należy zapoznać się ze szczegółowymi wymaganiami dotyczącymi urządzeń [VMware vmware,](migrate-appliance.md#appliance---vmware) [maszyn wirtualnych hyper-V](migrate-appliance.md#appliance---hyper-v)i [serwerów fizycznych.](migrate-appliance.md#appliance---physical)
- Nie uruchamiaj skryptu na istniejącym urządzeniu migracji platformy Azure.


## <a name="download-the-script"></a>Pobierz skrypt

1. Zlokalizuj maszynę/maszynę wirtualną, która będzie działać jako urządzenie migracji platformy Azure.
2. Na urządzeniu wykonaj następujące czynności:

    - Aby używać urządzenia z maszynami wirtualnymi VMware lub maszynami wirtualnymi funkcji Hyper-V, [pobierz](https://go.microsoft.com/fwlink/?linkid=2105112) spakowany folder zawierający skrypt instalatora i msi.
    - Aby korzystać z urządzenia z serwerami fizycznymi, pobierz skrypt z portalu Azure Migrate, zgodnie z opisem w tym [samouczku](tutorial-assess-physical.md#set-up-the-appliance).

## <a name="verify-file-security"></a>Weryfikowanie zabezpieczeń plików

Przed wdrożeniem pliku jest bezpieczny, zanim go wdrożysz.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla spakowany plik
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Sprawdź, czy wygenerowane wartości skrótu są zgodne z tymi ustawieniami (w przypadku najnowszej wersji urządzenia):

    **Algorytm** | **Wartość skrótu**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>Uruchamianie skryptu

Oto, co robi skrypt:

- Instaluje agentów i aplikację sieci web.
- Instaluje role systemu Windows, w tym usługę aktywacji systemu Windows, usługi IIS i program PowerShell ISE.
- Pobiera i instaluje moduł wielokrotnego zapisu usługi IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM), z trwałymi ustawieniami dla migracji platformy Azure.
- Tworzy pliki dziennika i konfiguracji w następujący sposób:
    - **Pliki konfiguracyjne:**%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika:**%ProgramData%\Microsoft Azure\Logs

Aby uruchomić skrypt:

1. Wyodrębnij spakowany plik do folderu na komputerze, w który będzie obsługiwał urządzenie.
2. Uruchom program PowerShell na komputerze z uprawnieniami administratora (podwyższone).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnione z pobranego pliku spakowane.
4. Uruchom skrypt **AzureMigrateInstaller.ps1** w następujący sposób:

    - Dla VMware: 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Dla funkcji Hyper-V:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - W przypadku serwerów fizycznych:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. Po pomyślnym uruchomieniu skryptu uruchamia aplikację internetową urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, możesz wyświetlić dzienniki skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu urządzenia przy użyciu skryptu postępuj zgodnie z poniższymi instrukcjami:

- Skonfiguruj urządzenie dla [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Skonfiguruj urządzenie dla [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Skonfiguruj urządzenie dla [serwerów fizycznych](how-to-set-up-appliance-physical.md).