---
title: Pobieranie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny Wirtualnej z usługami firmy Microsoft oparte na platformie Azure | Portal Azure Marketplace
description: Opis sposobu uzyskania sygnatury dostępu współdzielonego (SAS) identyfikator URI obrazu maszyny Wirtualnej.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 4da82b2f6aaa3fc664d2e91b80722329533b0cd0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938659"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Pobieranie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny Wirtualnej

W procesie publikowania musisz podać identyfikator uniform resource identifier (URI) dla każdego wirtualnego dysku twardego (VHD) skojarzony z od jednostek SKU. Dostęp do tych wirtualnych dysków twardych jest potrzebny firmie Microsoft w procesie certyfikacji. W tym artykule opisano sposób generowania sygnatury dostępu współdzielonego (SAS) identyfikator URI dla poszczególnych wirtualnych dysków Twardych. Wprowadź ten identyfikator URI w **jednostki SKU** karta w portalu Cloud Partner. 

Podczas generowania identyfikatorów URI sygnatury dostępu Współdzielonego dla generowanych, należy spełnić następujące wymagania:

- Obsługiwane są tylko niezarządzanych dysków VHD.
- `List` i `Read` uprawnienia są wystarczające. Czy *nie* zapewniają `Write` lub `Delete` dostępu.
- Czas trwania dla dostępu (*datę wygaśnięcia*) powinien mieć co najmniej trzech tygodni od utworzenia identyfikatora URI sygnatury dostępu Współdzielonego.
- Aby zabezpieczyć się przed różnice czasu UTC, ustaw datę początkową na jeden dzień przed bieżącą datą. Na przykład jeśli bieżąca data to 6 października 2014 roku, wybierz 2014-10-5.

## <a name="generate-the-sas-url"></a>Generowanie adresu URL sygnatury dostępu Współdzielonego

Adres URL sygnatury dostępu Współdzielonego można wygenerować na dwa sposoby wspólnego przy użyciu następujących narzędzi:

-   Eksplorator usługi Microsoft Storage — graficznego narzędzia dostępne dla Windows, macOS i Linux
-   Interfejs wiersza polecenia Microsoft Azure — zalecana dla środowisk zautomatyzowane lub ciągłej integracji i OSs innych niż Windows


### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Wykonaj następujące kroki, aby wygenerować identyfikator URI sygnatury dostępu Współdzielonego przy użyciu wiersza polecenia platformy Azure.

1. Pobierz i zainstaluj [wiersza polecenia platformy Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  Wersje są dostępne dla Windows, macOS i różne dystrybucje systemu Linux. 
2. Utwórz plik programu PowerShell (`.ps1` rozszerzenia pliku), skopiuj poniższy kod, a następnie zapisać je lokalnie.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```
    
3. Edytuj plik, aby podać następujące wartości parametrów.  Daty powinny być podane w formacie daty/godziny UTC, na przykład `10-25-2016T00:00:00Z`.
   - `<account-name>` -Nazwa konta magazynu platformy Azure
   - `<account-key>` -Klucz konta magazynu platformy Azure
   - `<vhd-name>` -Nazwa wirtualnego dysku twardego
   - `<start-date>` — Uprawnienia Data rozpoczęcia dla dostępu do dysku VHD. Podaj datę jeden dzień przed bieżącą datą. 
   - `<expiry-date>` — Data wygaśnięcia uprawnienia dostępu do dysku VHD.  Podaj datę co najmniej trzy tygodnie po dacie bieżącej. 
 
   Poniższy przykład przedstawia wartości odpowiednich parametrów (w momencie pisania tego dokumentu).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```
 
4. Zapisz zmiany, aby ten skrypt programu PowerShell.
5. Uruchom ten skrypt, korzystając z uprawnień administracyjnych, aby wygenerować *parametrów połączenia SAS* dla poziomu dostępu do kontenera.  Możesz użyć dwa podstawowe podejścia:
   - Uruchom skrypt z konsoli.  Na przykład w Windows, zapisu, kliknij przycisk skrypt, a następnie wybierz pozycję **Uruchom jako administrator**.
   - Uruchom skrypt z edytora skryptów programu PowerShell, takie jak [środowiska Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise), korzystając z uprawnień administracyjnych. 
     Poniżej przedstawiono parametry połączenia sygnatury dostępu Współdzielonego generowanych w ramach tego edytora. 

     ![Generowanie identyfikatora URI połączenia SAS w środowisku PowerShell ISE](./media/publishvm_032.png)

6. Skopiuj ciąg wynikowy połączenia SAS i zapisz go do pliku tekstowego w bezpiecznej lokalizacji.  Możesz edytować ten ciąg, aby dodać do niego do utworzenia końcowej identyfikatora URI połączenia SAS skojarzone informacje wirtualnego dysku twardego w lokalizacji. 
7. W witrynie Azure portal przejdź do magazynu obiektów blob, który zawiera skojarzony z nowo wygenerowanego identyfikatora URI wirtualnego dysku twardego.
8. Skopiuj wartość adresu URL **punkt końcowy usługi Blob**, jak pokazano poniżej.

    ![Punkt końcowy usługi BLOB w witrynie Azure portal](./media/publishvm_033.png)

9. Edytuj plik tekstowy z parametrami połączenia SAS z kroku 6.  Będzie konstruowania pełny identyfikator URI sygnatury dostępu Współdzielonego w następującym formacie:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Na przykład, jeśli nazwa wirtualnego dysku twardego jest `TestRGVM2.vhd`, wyniósłby wynikowy identyfikatora URI sygnatury dostępu Współdzielonego:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Powtórz te kroki dla każdego wirtualnego dysku twardego w ramach jednostek SKU, planowanie publikowania.


### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

Wykonaj następujące kroki, aby wygenerować identyfikator URI sygnatury dostępu Współdzielonego za pomocą Eksploratora usługi Microsoft Azure Storage.

1. Pobrać i zainstalować program [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Otwórz Eksploratora, a następnie na pasku menu po lewej stronie, kliknij pozycję **Dodaj konto** ikony.  **Łączenie z usługą Azure Storage** zostanie wyświetlone okno dialogowe.
3. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie kliknij pozycję **Zaloguj się...**.  Kontynuuj czynności wymagane do logowania się do konta platformy Azure.
4. W lewym **Explorer** okienko, przejdź do swojej **kont magazynu** i rozwiń ten węzeł.
5. Kliknij prawym przyciskiem myszy na wirtualny dysk twardy i wybierz **Uzyskaj sygnaturę dostępu udziału** z menu kontekstowego. 

    ![Uzyskiwanie sygnatury dostępu Współdzielonego elementu w Eksploratorze usługi Azure](./media/publishvm_034.png)

6. **Sygnatura dostępu współdzielonego** zostanie wyświetlone okno dialogowe. Wprowadź wartości dla następujących pól:
   - **Czas rozpoczęcia** -uprawnienie Data rozpoczęcia dla dostępu do dysku VHD. Podaj datę, która jest jeden dzień przed bieżącą datą.
   - **Czas wygaśnięcia** -datę wygaśnięcia uprawnienia dostępu do dysku VHD.  Podaj datę co najmniej trzy tygodnie po dacie bieżącej.
   - **Uprawnienia** — wybierz tę opcję `Read` i `List` uprawnienia. 

     ![Okno dialogowe sygnatury dostępu Współdzielonego, w Eksploratorze usługi Azure](./media/publishvm_035.png)

7. Kliknij przycisk **Utwórz** utworzyć skojarzony identyfikator URI sygnatury dostępu Współdzielonego dla tego wirtualnego dysku twardego.  Okno dialogowe zostaną wyświetlone szczegółowe informacje o tej operacji. 
8. Kopiuj **adresu URL** wartość i zapisz go do pliku tekstowego w bezpiecznej lokalizacji. 

    ![Tworzenie identyfikatora URI połączenia SAS, w Eksploratorze usługi Azure](./media/publishvm_036.png)

    Ten wygenerowany adres URL sygnatury dostępu Współdzielonego dotyczy dostęp na poziomie kontenera.  Aby utworzyć określone, skojarzone nazwy wirtualnego dysku twardego należy dodać do niego.

9. Edytuj plik tekstowy. Wstaw nazwę wirtualnego dysku twardego po `vhds` ciągu w adresie URL sygnatury dostępu Współdzielonego (w tym wiodącego ukośnika).  Końcowy identyfikatora URI sygnatury dostępu Współdzielonego powinien mieć format:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Na przykład, jeśli nazwa wirtualnego dysku twardego jest `TestRGVM2.vhd`, wyniósłby wynikowy identyfikatora URI sygnatury dostępu Współdzielonego:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Powtórz te kroki dla każdego wirtualnego dysku twardego w ramach jednostek SKU, planowanie publikowania.


## <a name="verify-the-sas-uri"></a>Sprawdź identyfikator URI sygnatury dostępu Współdzielonego

Przejrzyj i upewnij się, że każda wygenerowana identyfikatora URI sygnatury dostępu Współdzielonego przy użyciu poniższej listy kontrolnej.  Upewnij się, że:
- Identyfikator URI jest następujący:       `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- Identyfikator URI zawiera Twoje filename obrazu wirtualnego dysku twardego, łącznie z rozszerzeniem ".vhd".
- Kierunku środka identyfikatora URI `sp=rl` pojawia się. Ten ciąg wskazuje, że `Read` i `List` dostępu jest określony.
- Od tego momentu `sr=c` pojawia się również. Ten ciąg wskazuje, że określono dostęp na poziomie kontenera.
- Skopiuj i wklej identyfikator URI do przeglądarki, aby rozpocząć pobieranie skojarzonego obiektu blob.  (Możesz anulować operację przed zakończeniem pobierania.)


## <a name="next-steps"></a>Kolejne kroki

Jeśli występują trudności, generowania identyfikatora URI sygnatury dostępu Współdzielonego, zobacz [generuje typowych URL sygnatury dostępu Współdzielonego](./cpp-common-sas-url-issues.md).  W przeciwnym razie należy zapisać URI(s) sygnatury dostępu Współdzielonego do bezpiecznej lokalizacji w celu późniejszego użycia. Będą musieli [publikowanie oferty maszyny Wirtualnej](./cpp-publish-offer.md) w portalu Cloud Partner.
