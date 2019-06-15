---
title: Śledzenie i rejestrować urządzenia Azure Data Box, Azure Data Box mocno zdarzeń | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak śledzenie i rejestrowanie zdarzeń na różnych etapach Twoje zamówienie urządzenia Azure Data Box i Azure Data Box duże.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 108d17d3e0ca5f32648f9d4f6cf4b5f9a2984d0c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495821"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Śledzenie i rejestrowanie zdarzeń dla usługi Azure Data Box i Azure Data Box duże

Zamówienie urządzenia Data Box lub duże pole danych przechodzi przez następujące kroki: kolejność, konfigurowanie, dane skopiuj wróć, przekazać na platformę Azure i sprawdzić i usuwania danych. Odpowiadający każdego kroku w kolejności, można wykonywać wiele akcji do kontrolowania dostępu do zamówienia, przeprowadź inspekcję zdarzeń, śledzenie kolejności i interpretowanie różnych dzienników, które są generowane.

W poniższej tabeli przedstawiono podsumowanie kroki zamówienie urządzenia Data Box lub duże pole danych i narzędzia umożliwiające śledzenie i inspekcję kolejności podczas każdego kroku.

| Etap zlecenia pole danych       | Narzędzie do śledzenia i inspekcji                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Utwórz zamówienie               | [Konfigurowanie kontroli dostępu w kolejności przy użyciu RBAC](#set-up-access-control-on-the-order)                                                    |
| Kolejność przetwarzania            | [Śledzenie kolejność](#track-the-order) za pośrednictwem <ul><li> Azure Portal </li><li> Wysyłanie operatora witryny sieci Web </li><li>Powiadomienia e-mail</ul> |
| Konfigurowanie urządzenia              | Urządzenie poświadczeń dostępu zalogowany [dzienników aktywności](#query-activity-logs-during-setup)                                              |
| Kopiowanie danych do urządzenia        | [Widok *error.xml* pliki](#view-error-log-during-data-copy) do kopiowania danych                                                             |
| Przygotowanie do wysłania            | [Przeglądanie plików BOM](#inspect-bom-during-prepare-to-ship) lub pliki manifestu na urządzeniu                                      |
| Przekazywanie danych do platformy Azure       | [Przegląd *copylogs* ](#review-copy-log-during-upload-to-azure) błędów w trakcie dane należy przekazać w centrum danych platformy Azure                         |
| Usunięcie danych z urządzenia   | [Wyświetlanie łańcucha dowodowego dzienniki](#get-chain-of-custody-logs-after-data-erasure) tym dzienniki inspekcji i kolejność historii                                                   |

W tym artykule opisano szczegółowo w różnych mechanizmów lub narzędzia dostępne do śledzenia i inspekcji zamówienie urządzenia Data Box lub duże pole danych. Informacje przedstawione w tym artykule dotyczą zarówno Data Box, jak i duże pole danych. W kolejnych sekcjach wszelkie odwołania do urządzenia Data Box dotyczą również duże pole danych.

## <a name="set-up-access-control-on-the-order"></a>Konfigurowanie kontroli dostępu w kolejności

Można kontrolować, kto ma dostęp do zamówienia, podczas tworzenia zamówienia. Konfigurowanie ról kontroli dostępu opartej na rolach (RBAC) w różnych zakresach do kontrolowania dostępu do zamówienie urządzenia Data Box. Rola RBAC Określa typ dostępu — odczytu i zapisu, tylko do odczytu, odczytu i zapisu do podzbioru operacji.

Dostępne są dwie role, które mogą być definiowane dla usługi Azure Data Box:

- **Czytnik pole danych** -mają dostęp tylko do odczytu do zamówień, zgodnie z definicją w zakresie. Mogą jedynie wyświetlać szczegóły wybranego zamówienia. Nie można ich dostęp inne szczegóły związane z kontami magazynu lub edytować szczegóły zamówienia, takie jak adres i tak dalej.
- **Współautora usługi Data Box** — można tworzyć tylko zamówienie na przesyłanie danych do danego konta magazynu *Jeśli już mają dostęp do zapisu do konta magazynu*. Jeśli nie masz dostępu do konta magazynu, nie można nawet utworzyć zamówienie urządzenia Data Box w celu kopiowania danych do konta. Ta rola nie definiuje wszystkich kont magazynu związane z uprawnieniami, ani nie udziela dostępu do kont magazynu.  

Aby ograniczyć dostęp do zamówienia, możesz wykonywać następujące czynności:

- Przypisywanie roli na poziomie zamówienia. Użytkownik ma tylko uprawnienia określone przez role do interakcji z tylko tej określonej kolejności urządzenia Data Box i od niczego więcej.
- Przypisywanie roli na poziomie grupy zasobów, użytkownik ma dostęp do wszystkich zamówień urządzenia Data Box w grupie zasobów.

Aby uzyskać więcej informacji na sugerowane Użyj RBAC, zobacz [najlepsze rozwiązania dotyczące kontroli RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Śledzenie zamówienia

Możliwe jest śledzenie zamówienia w witrynie Azure portal i za pośrednictwem witryny sieci Web operatora wysyłki. W celu śledzenia zamówienie urządzenia Data Box w dowolnym momencie są następujące mechanizmy:

- Aby śledzić kolejności, gdy urządzenie jest w centrum danych platformy Azure lub w środowisku lokalnym, przejdź do swojej **zamówienie urządzenia Data Box > Przegląd** w witrynie Azure portal.

    ![Wyświetl stan zamówienia i śledzenia nie](media/data-box-logs/overview-view-status-1.png)

- Aby śledzić kolejności, gdy urządzenie jest w drodze, przejdź do operatora regionalnych witryny sieci Web, na przykład tworzenia witryny sieci Web w Stanach Zjednoczonych. Podaj numer śledzenia skojarzony z zamówienia.
- Urządzenie Data Box również wysyła powiadomienia e-mail, w dowolnym czasie zmiany stanu zamówienia oparte na wiadomości e-mail, podany przy wywołaniu metody zamówienie zostało utworzone. Aby uzyskać listę wszystkich stanów zamówienie urządzenia Data Box, zobacz [wyświetlić stan zamówienia](data-box-portal-admin.md#view-order-status). Aby zmienić ustawienia powiadomień skojarzonego z zamówieniem, zobacz [edytowanie szczegółów powiadomienia](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Dzienniki aktywności zapytania podczas instalacji

- Twoje urządzenie Data Box nadejściu lokalną w stanie zablokowanym. Poświadczenia urządzenia dostępne w witrynie Azure portal można użyć w ramach zamówienia.  

    Po skonfigurowaniu urządzenia Data Box trzeba wiedzieć, kto wszystkich dostęp do poświadczeń urządzenia. Aby ustalić, kto dostęp do **poświadczenia urządzenia** bloku można tworzyć zapytania dzienników aktywności.  Dowolną akcję, która obejmuje dostęp do **szczegóły urządzenia > poświadczenia** bloku jest rejestrowane w dziennikach działań jako `ListCredentials` akcji.

    ![Wykonywanie zapytań dotyczących dzienników aktywności](media/data-box-logs/query-activity-log-1.png)

- Każdy Zaloguj się do urządzenia Data Box jest rejestrowane czasu rzeczywistego. Jednakże, informacja ta jest dostępna tylko w [dzienniki inspekcji](#audit-logs) po pomyślnym zakończeniu kolejności.

## <a name="view-error-log-during-data-copy"></a>Wyświetl dziennik błędów podczas kopiowania danych

Podczas kopiowania danych na urządzenie Data Box lub duże pole danych generowany jest plik błędu w przypadku jakichkolwiek problemów z danych, w której są kopiowane.

### <a name="errorxml-file"></a>Plik Error.XML

Upewnij się, że zadania kopiowania została zakończona bez błędów. Jeśli występują błędy podczas procesu kopiowania, Pobierz dzienniki z **Połącz i skopiuj** strony.

- Jeśli plik, który jest wyrównany do folderu dysku zarządzanego na Twoje urządzenie Data Box nie 512 bajtów jest kopiowany, plik nie jest przekazywany jako stronicowy obiekt blob na koncie magazynu przejściowego. Zostanie wyświetlony błąd w dziennikach. Usuń plik, a następnie skopiuj plik, który wynosi 512 bajtów wyrównane.
- Jeśli został skopiowany plik VHDX, dynamicznego wirtualnego dysku twardego i/lub różnicowy wirtualny dysk twardy (te pliki nie są obsługiwane), zobaczysz błąd w dziennikach.

Poniżej przedstawiono przykładowe *error.xml* różnych błędów podczas kopiowania do usługi managed disks.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Poniżej przedstawiono przykładowe *error.xml* różnych błędów podczas kopiowania do stronicowych obiektów blob.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Poniżej przedstawiono przykładowe *error.xml* różnych błędów podczas kopiowania do blokowych obiektów blob.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Poniżej przedstawiono przykładowe *error.xml* różnych błędów podczas kopiowania do usługi Azure Files.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

We wszystkich powyższych przypadkach napraw błędy, zanim przejdziesz do kolejnego kroku. Aby uzyskać więcej informacji na temat błędów odebranych podczas kopiowania danych do urządzenia Data Box za pomocą protokołów SMB lub NFS, przejdź do [problemy rozwiązywanie problemów z urządzenia Data Box i duże pole danych](data-box-troubleshoot.md). Aby uzyskać informacje na temat błędów odebranych podczas kopiowania danych na urządzenie Data Box za pośrednictwem interfejsu REST, przejdź do [problemów z magazynowaniem Rozwiązywanie problemów z pola obiektu Blob danych](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Sprawdzanie BOM podczas przygotowania do wydania

Podczas przygotowania do wysłania listy plików, znana jako zestawienie materiałów (BOM) lub pliku manifestu jest tworzony.

- Użyj tego pliku, aby sprawdzić przed rzeczywistymi nazwami i liczby plików, które zostały skopiowane do urządzenia Data Box.
- Aby sprawdzić względem rzeczywiste rozmiary plików, należy użyć tego pliku.
- Upewnij się, że *crc64* odpowiada ciągowi różna od zera. <!--A null value for crc64 indicates that there was a reparse point error)-->

Aby uzyskać więcej informacji na temat błędów odebranych podczas przygotowania do wysłania, przejdź do [problemy rozwiązywanie problemów z urządzenia Data Box i duże pole danych](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Znak BOM lub manifest pliku

BOM lub plik manifestu zawiera listę wszystkich plików, które są kopiowane do urządzenia Data Box. Plik BOM ma nazw plików i rozmiary odpowiednie, a także sumy kontrolnej. Dla blokowych obiektów blob, stronicowe obiekty BLOB, usługi Azure Files, kopiowanie za pośrednictwem interfejsów API REST i kopiowanie do usługi managed disks na urządzenie Data Box tworzony jest oddzielny plik BOM. Możesz pobrać pliki BOM z poziomu lokalnego internetowego interfejsu użytkownika urządzenia podczas przygotowywania do wysłania.

Te pliki również znajdują się na urządzenie Data Box i są przekazywane do skojarzonego konta magazynu w centrum danych platformy Azure.

### <a name="bom-file-format"></a>Format pliku BOM

Plik manifest lub znak BOM ma następujący format Ogólne:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Poniżej przedstawiono przykładowe manifestu generowane, jeśli dane zostały skopiowane do udziału blokowych obiektów blob na urządzenie Data Box.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

BOM lub pliki manifestu również zostaną skopiowane do konta magazynu platformy Azure. Można użyć znaku BOM lub manifest plików, aby sprawdzić, czy przekazane do platformy Azure są zgodne dane, które zostały skopiowane do urządzenia Data Box.

## <a name="review-copy-log-during-upload-to-azure"></a>Przejrzyj dziennik kopiowania podczas przekazywania na platformie Azure

Podczas przekazywania danych na platformę Azure *copylog* zostanie utworzony.

### <a name="copylog"></a>Copylog

Dla każdego zamówienia, który jest przetwarzany, tworzy usługi Data Box *copylog* w skojarzonego konta magazynu. *Copylog* całkowita liczba plików, które zostały przekazane i liczbę plików, który dotyczącego podczas danych skopiować z urządzenia Data Box do konta usługi Azure storage.

Obliczenia cykliczne Sprawdź nadmiarowości (CRC) odbywa się w trakcie przekazywania na platformie Azure. CRC kopiowania danych i po przekazywania danych są porównywane. Niezgodność sumy kontrolnej wskazuje, że odpowiadające im pliki nie można przekazać.

Domyślnie dzienniki są zapisywane w kontenerze o nazwie copylog. Dzienniki są przechowywane przy użyciu następującej konwencji nazewnictwa:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Ścieżka copylog jest również wyświetlany na **Przegląd** bloku portalu.

![Ścieżka do copylog w bloku przeglądu po zakończeniu](media/data-box-logs/copy-log-path-1.png)

Poniższy przykład opisano ogólny format pliku copylog dla urządzenia Data Box przekazywania, które zakończone powodzeniem:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Przekaż do platformy Azure również może zostać zakończone z błędami.

![Ścieżka do copylog w bloku przeglądu, gdy została ukończona z błędami](media/data-box-logs/copy-log-path-2.png)

Oto przykład copylog gdzie przekazywanie zostało zakończone z błędami:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Pobierz łańcuch nadzoru dzienników po usunięcie danych

Po dane są usuwane z dysków Data Box, zgodnie z wytycznymi poprawka 1 SP NIST 800-88, łańcuch nadzoru dzienniki będą dostępne. Dzienniki te obejmują dzienniki inspekcji i historii zamówień. BOM lub pliki manifestu, również są kopiowane z dziennikami inspekcji.

### <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji zawierają informacji na temat włączania zasilania i udostępnić dostęp do urządzenia Data Box lub duże pole danych, gdy znajduje się poza centrum danych platformy Azure. Te dzienniki znajdują się w lokalizacji: `storage-account/azuredatabox-chainofcustodylogs`

Poniżej przedstawiono przykładowy dziennik inspekcji z urządzenia Data Box:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Pobieranie historii zamówienia

Historia zamówień jest dostępna w witrynie Azure portal. Jeśli kolejność jest kompletny i zakończeniu czyszczenia urządzenia (usunięcie danych z dysków), przejdź do Twoje zamówienie urządzenia i przejdź do **szczegóły zamówienia**. ** Pobieranie historii zamówień** opcja jest dostępna. Aby uzyskać więcej informacji, zobacz [pobrać historii zamówień](data-box-portal-admin.md#download-order-history).

Możesz przewijać historii zamówień, zobacz:

- Operatora śledzi informacje dla Twojego urządzenia.
- Zdarzenia przy użyciu *SecureErase* działania. Zdarzenia te odpowiadają skasowanie danych na dysku.
- Łącza do dziennika pól danych. Ścieżki dla *dzienniki inspekcji*, *copylogs*, i *BOM* pliki są uporządkowane.

Poniżej przedstawiono przykładowy dziennik historii kolejności z witryny Azure portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Rozwiązywanie problemów na urządzenie Data Box i duże pole danych](data-box-troubleshoot.md).
