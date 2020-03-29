---
title: 'INTERFEJS API REST: operacje zarządzania kontami w usłudze Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft'
description: Użyj interfejsu Api usługi Azure Data Lake Storage Gen1 i WebHDFS REST do wykonywania operacji zarządzania kontami na koncie Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877110"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operacje zarządzania kontami w usłudze Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
> * [INTERFEJS API ODPOCZYNKU](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

W tym artykule dowiesz się, jak wykonywać operacje zarządzania kontami w usłudze Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST. Operacje zarządzania kontami obejmują utworzenie konta Data Lake Storage Gen1, usunięcie konta Data Lake Storage Gen1 itp. Aby uzyskać instrukcje dotyczące wykonywania operacji systemu plików w usłudze Data Lake Storage Gen1 przy użyciu interfejsu API REST, zobacz [Operacje systemu plików w usłudze Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. W tym artykule użyto cURL, aby zademonstrować, jak wykonać wywołania interfejsu API REST na koncie Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Jak wykonać uwierzytelnianie przy użyciu usługi Azure Active Directory?
Dostępne są dwa podejścia do uwierzytelniania za pomocą usługi Azure Active Directory.

* Aby uzyskać informacje o uwierzytelnianiu użytkowników końcowych dla aplikacji (interaktywnej), zobacz [Uwierzytelnianie użytkowników końcowych za pomocą usługi Data Lake Storage Gen1 przy użyciu pliku .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Aby uzyskać uwierzytelnianie między usługami dla aplikacji (nieinterakcyjne), zobacz [Uwierzytelnianie usługi do usługi przy użyciu usługi Lake Storage Gen1 przy użyciu .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1
Ta operacja jest oparta na wywołaniu interfejsu API REST zdefiniowanym [tutaj](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Użyj następującego polecenia cURL. Wymień ** \<nazwę>** data lake storage gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

W poleceniu powyżej zastąp ciąg \<`REDACTED`\> tokenem autoryzacji pobranym wcześniej. Ładunek żądania dla tego polecenia jest zawarty w pliku **input.json**, który jest udostępniany dla parametru `-d` powyżej. Zawartość pliku input.json przypomina następujący fragment kodu:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Usuwanie konta Gen1 magazynu usługi Data Lake
Ta operacja jest oparta na wywołaniu interfejsu API REST zdefiniowanym [tutaj](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Użyj następującego polecenia cURL, aby usunąć konto Gen1 magazynu usługi Data Lake. Zastąp nazwę ** \<>yourstoragegen1name** nazwą konta Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Następne kroki
* [Operacje systemu plików w pamięci masowej danych Gen1 przy użyciu interfejsu REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Zobacz też
* [Odwołanie interfejsu API interfejsu REST usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aplikacje Open Source Big Data zgodne z usługą Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

