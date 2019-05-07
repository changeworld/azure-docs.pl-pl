---
title: Usługa Azure Data Box dotyczące rozwiązywania problemów przy użyciu interfejsu REST | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób rozwiązywania problemów występujących w usłudze Azure Data Box, kiedy kopiowanie danych jest za pośrednictwem interfejsu REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: c5ceeb2e6419cab7945454087edd4c821db28343
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204217"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Rozwiązywanie problemów związanych z usługą Azure Data Box Blob storage

Informacje dotyczące tego artykułu szczegółowe informacje na temat rozwiązywania problemów może zostać wyświetlony w przypadku używania magazynu obiektów Blob pole danych za pośrednictwem interfejsu REST na Twoje urządzenie Data Box do kopiowania danych. Te problemy powierzchni przy zastosowaniu pole danych w usłudze Blob storage z innymi aplikacjami lub bibliotek klienckich, takich jak biblioteki Azure Storage Explorer, narzędzia AzCopy lub usługi Azure Storage dla języka Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Błędy w Eksploratorze usługi Azure Storage

W tej sekcji przedstawiono niektórych problemów sterowaną za pomocą Eksploratora usługi Azure Storage z magazynem obiektów Blob pola danych.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Nie można pobrać zasoby podrzędne. Wartość dla jednego z nagłówków HTTP nie jest w poprawnym formacie.|Z **Edytuj** menu, wybierz opcję **interfejsów API programu docelowej usługi Azure Stack**. <br>Ponownie uruchom Eksploratora usługi Azure Storage.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` jest dodawany do pliku hosts na tej ścieżce: <li>`C:\Windows\System32\drivers\etc\hosts` w Windows lub </li><li> `/etc/hosts` w systemie Linux.</li>|
|Nie można pobrać zasoby podrzędne. <br>Szczegóły: certyfikat z podpisem własnym |Zaimportuj certyfikat SSL dla Twojego urządzenia w Eksploratorze usługi Azure Storage: <li>Pobierz certyfikat w witrynie Azure portal. Aby uzyskać więcej informacji, przejdź do [Pobierz certyfikat](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Z **Edytuj** menu, wybierz opcję **certyfikaty SSL** , a następnie wybierz **Importuj certyfikaty**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Błędy w narzędzia AzCopy dla Windows

W tej sekcji przedstawiono niektórych problemów sterowaną za pomocą narzędzia AzCopy dla Windows przy użyciu magazynu obiektów Blob pola danych.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Zawieszenie minutę przed wyświetleniem tego błędu pojawia się polecenia narzędzia AzCopy: <br>Nie można wyliczyć https:// katalogu... Nie można rozpoznać nazwy zdalnej `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` jest dodawany do pliku hosts na: `C:\Windows\System32\drivers\etc\hosts`.|
|Zawieszenie minutę przed wyświetleniem tego błędu pojawia się polecenia narzędzia AzCopy: <br>Błąd podczas analizowania lokalizacja źródłowa. Połączenie podstawowe zostało zamknięte: Nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS.|Certyfikat SSL dla Twojego urządzenia należy zaimportować do magazynu certyfikatów systemowych. Aby uzyskać więcej informacji, przejdź do [Pobierz certyfikat](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Błędy widoczne w narzędzia AzCopy dla systemu Linux

W tej sekcji przedstawiono niektórych problemów sterowaną za pomocą narzędzia AzCopy dla systemu Linux z magazynem obiektów Blob pola danych.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Zawieszenie przez 20 minut przed wyświetleniem tego błędu pojawia się polecenia narzędzia AzCopy: <br>Wystąpił błąd podczas analizowania lokalizacja źródłowa `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Nie ma takiego urządzenia lub adresu|Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` jest dodawany do pliku hosts na: `/etc/hosts`.|
|Zawieszenie przez 20 minut przed wyświetleniem tego błędu pojawia się polecenia narzędzia AzCopy: <br>Błąd podczas analizowania lokalizacji źródła... Nie można ustanowić połączenia SSL.|Certyfikat SSL dla Twojego urządzenia należy zaimportować do magazynu certyfikatów systemowych. Aby uzyskać więcej informacji, przejdź do [Pobierz certyfikat](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Błędy widoczne w bibliotece usługi Azure Storage dla języka Python

W tej sekcji przedstawiono niektóre najważniejsze problemy sterowaną podczas wdrażania dysku Data Box za pomocą klienta systemu Linux do kopiowania danych.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Wartość dla jednego z nagłówków HTTP nie jest w poprawnym formacie. |Zainstalowana wersja biblioteki usługi Microsoft Azure Storage dla języka Python nie jest obsługiwana przez urządzenia Data Box. Zobacz wymagania dotyczące magazynu usługi Azure Blob pole danych dla obsługiwanych wersji.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …|Przed uruchomieniem języka Python, należy ustawić zmiennej środowiskowej REQUESTS_CA_BUNDLE ścieżkę do pliku certyfikatu SSL z algorytmem Base64 (zobacz instrukcje [Pobierz certyfikat](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Na przykład:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Możesz również dodać certyfikat do magazynu certyfikatów systemu, a następnie ustaw tej zmiennej środowiskowej ścieżka do tego magazynu. <br> Na przykład na platformie Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Typowe błędy

Te błędy nie są specyficzne dla dowolnej aplikacji.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Upłynął limit czasu połączenia. |Zaloguj się do urządzenia Data Box i sprawdź, czy jest odblokowane. Dowolny ponownym uruchomieniu urządzenia, pozostanie zablokowana do momentu indywidualnego zarejestrowania się.|

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [wymagania systemowe magazynu obiektów Blob pole danych](data-box-system-requirements-rest.md).
