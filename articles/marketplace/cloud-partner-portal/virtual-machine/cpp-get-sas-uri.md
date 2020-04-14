---
title: Uzyskiwanie identyfikatora URI podpisu dostępu współdzielonego dla obrazu maszyny wirtualnej opartej na platformie Microsoft Azure | Azure Marketplace
description: W tym artykule wyjaśniono, jak uzyskać identyfikator URI podpisu dostępu współdzielonego (SAS) dla obrazu maszyny Wirtualnej.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 2fdbc2a11bd963057b465a629757f2be51ae4061
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273855"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Uzyskiwanie identyfikatora URI podpisu dostępu współdzielonego dla obrazu maszyny Wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [Pobierz identyfikator URI podpisu dostępu współdzielonego dla obrazu maszyny Wirtualnej,](https://aka.ms/GetSASURI) aby zarządzać zmigrowanymi ofertami.

Podczas procesu publikowania należy podać jednolity identyfikator zasobu (URI) dla każdego wirtualnego dysku twardego (VHD) skojarzonego z jednostkami SKU. Firma Microsoft potrzebuje dostępu do tych identyfikatorów VHD podczas procesu certyfikacji. W tym artykule wyjaśniono, jak wygenerować identyfikator URI podpisu dostępu udostępnionego (SAS) dla każdego dysku wirtualnego. Ten identyfikator URI zostanie wprowadzony na karcie **Jednostki SKU** w portalu cloud partnerów.

Podczas generowania identyfikatorów URI sygnatury dostępu Współdzielonego dla identyfikatorów VHD należy przestrzegać następujących wymagań:

- Obsługiwane są tylko niezarządzane wirtualne identyfikatory wirtualne.
- `List`i `Read` uprawnienia są wystarczające. *Nie udzielaj* dostępu `Write` ani `Delete` nie należy do nich udzielać dostępu.
- Czas trwania dostępu *(data wygaśnięcia)* powinien wynosić co najmniej trzy tygodnie od momentu utworzenia identyfikatora URI sygnatury dostępu współdzielonego.
- Aby zabezpieczyć się przed wahaniami czasu UTC, należy ustawić datę rozpoczęcia na jeden dzień przed bieżącą datą. Jeśli na przykład bieżącą datą jest 6 października 2014 r., wybierz 10/5/2014.

## <a name="generate-the-sas-url"></a>Generowanie adresu URL sygnatury dostępu Współdzielonego

Adres URL sygnatury dostępu Współdzielonego można wygenerować na dwa typowe sposoby przy użyciu następujących narzędzi:

- Microsoft Storage Explorer — narzędzie graficzne dostępne dla systemów Windows, macOS i Linux
- Interfejs wiersza polecenia platformy Microsoft Azure — zalecane dla systemów operacyjnych innych niż Windows oraz zautomatyzowanych lub ciągłych środowisk integracji

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wygenerować identyfikator URI sygnatury dostępu Współdzielonego za pomocą interfejsu wiersza polecenia platformy Azure, należy wykonać następujące kroki.

1. Pobierz i zainstaluj plik [CLI platformy Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Wersje są dostępne dla systemu Windows, macOS i różnych dystrybucji Linuksa.
2. Utwórz plik programu`.ps1` PowerShell (rozszerzenie pliku), skopiuj w poniższym kodzie, a następnie zapisz go lokalnie.

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Edytuj plik, aby podać następujące wartości parametrów.  Daty powinny być podane w formacie `2016-10-25T00:00:00Z`datatime UTC, na przykład .
   - `<account-name>`- Nazwa konta magazynu platformy Azure
   - `<account-key>`- Klucz konta usługi Azure storage
   - `<vhd-name>`- Twoja nazwa VHD
   - `<start-date>`- Data rozpoczęcia uprawnień dla dostępu VHD. Podaj datę na jeden dzień przed bieżącą datą.
   - `<expiry-date>`- Data wygaśnięcia uprawnień dla dostępu VHD.  Podaj datę co najmniej trzy tygodnie po bieżącej dacie.

   Poniższy przykład pokazuje prawidłowe wartości parametrów (w momencie pisania tego tekstu).

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Zapisz zmiany w tym skrypcie programu PowerShell.
5. Uruchom ten skrypt, przy użyciu uprawnień administracyjnych, aby wygenerować *ciąg połączenia sygnatury dostępu* do kontenera.  Można użyć dwóch podstawowych podejść:
   - Uruchom skrypt z konsoli.  Na przykład w systemie Windows, write-click na skrypcie i wybierz polecenie **Uruchom jako administrator**.
   - Uruchom skrypt z edytora skryptów programu PowerShell, takiego jak [program Windows PowerShell ISE,](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)przy użyciu uprawnień administracyjnych.
     Poniżej przedstawiono ciąg połączenia sygnatury dostępu Współdzielonego generowane w tym edytorze.

     ![Generowanie identyfikatora URI sas w programie PowerShell ISE](./media/publishvm_032.png)

6. Skopiuj wynikowy ciąg połączenia SAS i zapisz go w pliku tekstowym w bezpiecznej lokalizacji.  Ten ciąg zostanie edytowany, aby dodać do niego skojarzone informacje o lokalizacji VHD, aby utworzyć końcowy identyfikator URI sygnatury dostępu Współdzielonego.
7. W witrynie Azure portal przejdź do magazynu obiektów blob, który zawiera dysk VHD skojarzony z nowo wygenerowanym identyfikatorem URI.
8. Skopiuj wartość adresu URL **punktu końcowego usługi obiektów Blob,** jak pokazano poniżej.

    ![Punkt końcowy usługi obiektów Blob w witrynie Azure portal](./media/publishvm_033.png)

9. Edytuj plik tekstowy za pomocą ciągu połączenia sygnatury dostępu Współdzielonego z kroku 6.  Zostanie skonstruowanie pełnego identyfikatora URI sygnatury dostępu Współdzielonego przy użyciu następującego formatu:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Na przykład jeśli nazwa VDH `TestRGVM2.vhd`jest , a następnie wynikowy identyfikator URI sygnatury dostępu Współdzielonego będzie:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Powtórz te kroki dla każdego dysku VHD w jednostkach SKU, które zamierzasz opublikować.

### <a name="microsoft-storage-explorer"></a>Eksplorator magazynu firmy Microsoft

Aby wygenerować identyfikator URI sygnatury dostępu Współdzielonego za pomocą Eksploratora magazynu platformy Microsoft Azure.

1. Pobrać i zainstalować program [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Otwórz eksploratora i w lewym pasku menu kliknij ikonę **Dodaj konto.**  Zostanie wyświetlone okno dialogowe **Łączenie się z usługą Azure Storage.**
3. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie kliknij pozycję **Zaloguj się...**.  Kontynuuj wymagane kroki, aby zalogować się do konta platformy Azure.
4. W lewym okienku **Eksploratora** przejdź do **kont magazynu** i rozwiń ten węzeł.
5. Kliknij prawym przyciskiem myszy dysk VHD i wybierz polecenie **Pobierz podpis dostępu do udostępniania** z menu kontekstowego.

    ![Pobierz element sygnatury dostępu Współdzielonego w Eksploratorze platformy Azure](./media/publishvm_034.png)

6. Zostanie wyświetlone okno dialogowe **Podpis dostępu udostępnionego.** Wprowadź wartości dla następujących pól:
   - **Godzina rozpoczęcia** — data rozpoczęcia uprawnień dla dostępu VHD. Podaj datę, która jest jeden dzień przed bieżącą datą.
   - **Czas wygaśnięcia** — data wygaśnięcia uprawnień dla dostępu VHD.  Podaj datę co najmniej trzy tygodnie po bieżącej dacie.
   - **Uprawnienia** — wybierz `Read` `List` i uprawnienia.

     ![Okno dialogowe Sygnatury dostępu Współdzielonego w Eksplorator](./media/publishvm_035.png)

7. Kliknij **przycisk Utwórz,** aby utworzyć skojarzony identyfikator URI sygnatury dostępu Współdzielonego dla tej wirtualnej sieci wirtualnej.  W oknie dialogowym są teraz wyświetlane szczegółowe informacje o tej operacji.
8. Skopiuj wartość **adresu URL** i zapisz ją w pliku tekstowym w bezpiecznej lokalizacji.

    ![Tworzenie identyfikatora URI sygnatury dostępu Współdzielonego w Eksploratorze platformy Azure](./media/publishvm_036.png)

    Ten wygenerowany adres URL sygnatury dostępu współdzielonego jest przeznaczony do dostępu na poziomie kontenera.  Aby uczynić go specyficznym, należy dodać do niej skojarzoną nazwę VHD.

9. Edytuj plik tekstowy. Wstaw nazwę VHD `vhds` po ciągu w adresie URL sygnatury dostępu Współdzielonego (w tym wiodący ukośnik do przodu).  Ostateczny identyfikator URI sygnatury dostępu Współdzielonego powinien mieć format:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Na przykład jeśli nazwa VDH `TestRGVM2.vhd`jest , a następnie wynikowy identyfikator URI sygnatury dostępu Współdzielonego będzie:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Powtórz te kroki dla każdego dysku VHD w jednostkach SKU, które zamierzasz opublikować.

## <a name="verify-the-sas-uri"></a>Sprawdź identyfikator URI sygnatury dostępu Współdzielonego

Przejrzyj i sprawdź każdy wygenerowany identyfikator URI sygnatury dostępu Współdzielonego przy użyciu poniższej listy kontrolnej.  Sprawdź, czy:

- Identyfikator URI ma formę: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` +`<sas-connection-string>`
- Identyfikator URI zawiera nazwę pliku obrazu VHD, w tym rozszerzenie nazwy pliku ".vhd".
- W kierunku środka identyfikatora `sp=rl` URI, pojawia się. Ten ciąg wskazuje, że `Read` i `List` dostęp jest określony.
- Po tym `sr=c` punkcie pojawia się również. Ten ciąg wskazuje, że określono dostęp na poziomie kontenera.
- Skopiuj i wklej identyfikator URI do przeglądarki, aby rozpocząć pobieranie skojarzonego obiektu blob.  (Możesz anulować operację przed zakończeniem pobierania).

## <a name="next-steps"></a>Następne kroki

Jeśli masz trudności z generowaniem identyfikatora URI sygnatury dostępu Współdzielonego, zobacz [Typowe problemy z adresem URL sygnatury dostępu Współdzielonego](./cpp-common-sas-url-issues.md).  W przeciwnym razie zapisz identyfikatory URI sygnatury dostępu Współdzielonego w bezpiecznej lokalizacji do późniejszego użycia. Będzie musiał [opublikować ofertę maszyny Wirtualnej](./cpp-publish-offer.md) w portalu cloud partner.
