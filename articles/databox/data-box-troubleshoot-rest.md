---
title: Rozwiązywanie problemów z usługą Azure Data Box w przypadku korzystania z interfejsu REST| Dokumenty firmy Microsoft
description: W tym artykule opisano, jak rozwiązywać problemy widoczne w usłudze Azure Data Box, gdy kopiowanie danych odbywa się za pośrednictwem interfejsu REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297125"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Rozwiązywanie problemów związanych z magazynem obiektów blob usługi Azure Data Box

W tym artykule opisano informacje dotyczące sposobu rozwiązywania problemów, które mogą pojawić się podczas korzystania z magazynu obiektów Blob pola danych za pośrednictwem interfejsu REST w polu danych w celu skopiowania danych. Te problemy pojawiają się podczas korzystania z magazynu obiektów blob pola danych z innymi aplikacjami lub bibliotekami klienckimi, takimi jak Azure Storage Explorer, AzCopy lub biblioteka usługi Azure Storage dla języka Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Błędy widoczne w Eksploratorze usługi Azure Storage

W tej sekcji opisano niektóre problemy napotykane podczas korzystania z usługi Azure Storage Explorer z magazynem obiektów blob pola danych.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Nie można pobrać zasobów podrzędnych. Wartość jednego z nagłówków HTTP nie jest w poprawnym formacie.|Z menu **Edycja** wybierz pozycję **Target Azure Stack APIs**. <br>Uruchom ponownie Eksploratora usługi Azure Storage.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Sprawdź, czy nazwa `<accountname>.blob.<serialnumber>.microsoftdatabox.com` punktu końcowego jest dodawana do pliku hosts w tej ścieżce: <li>`C:\Windows\System32\drivers\etc\hosts`w systemie Windows, lub </li><li> `/etc/hosts`na Linuksie.</li>|
|Nie można pobrać zasobów podrzędnych. <br>Szczegóły: certyfikat z podpisem własnym |Zaimportowanie certyfikatu TLS/SSL dla urządzenia do Eksploratora usługi Azure Storage: <li>Pobierz certyfikat z witryny Azure portal. Aby uzyskać więcej informacji, przejdź do [strony Pobieranie certyfikatu](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Z menu **Edycja** wybierz pozycję **Certyfikaty SSL,** a następnie wybierz polecenie **Importuj certyfikaty**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Błędy widoczne w AzCopy dla systemu Windows

W tej sekcji szczegółowo niektóre problemy napotykane podczas korzystania z AzCopy dla systemu Windows z magazynu obiektów Blob pola danych.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Polecenie AzCopy wydaje się zawieszać na minutę przed wyświetleniem tego błędu: <br>Nie można wyliczyć https:// katalogu... Nie można rozpoznać nazwy zdalnej`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Sprawdź, czy nazwa `<accountname>.blob.<serialnumber>.microsoftdatabox.com` punktu końcowego jest `C:\Windows\System32\drivers\etc\hosts`dodawana do pliku hosts pod adresem: .|
|Polecenie AzCopy wydaje się zawieszać na minutę przed wyświetleniem tego błędu: <br>Błąd podczas analizowania lokalizacji źródłej. Podstawowe połączenie zostało zamknięte: nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS.|Zaimportuj certyfikat TLS/SSL dla swojego urządzenia do magazynu certyfikatów systemu. Aby uzyskać więcej informacji, przejdź do [strony Pobieranie certyfikatu](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Błędy widoczne w AzCopy dla Linuksa

W tej sekcji szczegółowo niektóre problemy napotykane podczas korzystania z AzCopy dla systemu Linux z magazynu obiektów Blob data box.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Polecenie AzCopy wydaje się zawieszać przez 20 minut przed wyświetleniem tego błędu: <br>Błąd podczas analizowania `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`lokalizacji źródłej . Nie ma takiego urządzenia lub adresu|Sprawdź, czy nazwa `<accountname>.blob.<serialnumber>.microsoftdatabox.com` punktu końcowego jest `/etc/hosts`dodawana do pliku hosts pod adresem: .|
|Polecenie AzCopy wydaje się zawieszać przez 20 minut przed wyświetleniem tego błędu: <br>Wystąpił błąd podczas analizowania lokalizacji źródłej... Nie można ustanowić połączenia SSL.|Zaimportuj certyfikat TLS/SSL dla swojego urządzenia do magazynu certyfikatów systemu. Aby uzyskać więcej informacji, przejdź do [strony Pobieranie certyfikatu](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Błędy widoczne w bibliotece usługi Azure Storage dla języka Python

W tej sekcji opisano niektóre z najważniejszych problemów napotykanych podczas wdrażania dysku data box podczas korzystania z klienta systemu Linux do kopiowania danych.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Wartość jednego z nagłówków HTTP nie jest w poprawnym formacie. |Zainstalowana wersja biblioteki magazynu microsoft azure dla języka Python nie jest obsługiwana przez pole danych. Zobacz wymagania dotyczące magazynu obiektów blob usługi Azure Data Box dla obsługiwanych wersji.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|Przed uruchomieniem języka Python ustaw zmienną środowiskową REQUESTS_CA_BUNDLE na ścieżkę pliku certyfikatu TLS zakodowanego w systemie Base64 (zobacz, jak [pobrać certyfikat](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Przykład:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternatywnie dodaj certyfikat do magazynu certyfikatów systemu, a następnie ustaw tę zmienną środowiskową do ścieżki tego magazynu. <br> Na przykład na platformie Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Typowe błędy

Te błędy nie są specyficzne dla żadnej aplikacji.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Limit czasu połączenia. |Zaloguj się do urządzenia Data Box i sprawdź, czy jest odblokowane. Za każdym razem, gdy urządzenie zostanie ponownie uruchomione, pozostanie zablokowane, dopóki ktoś się nie zaloguje.|

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wymaganiach systemowych przechowywania obiektów Blob.](data-box-system-requirements-rest.md)
