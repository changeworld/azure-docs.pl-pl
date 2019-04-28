---
title: 'INTERFEJS API REST: Operacje zarządzania kontem w usłudze Azure Data Lake magazynu Gen1 | Dokumentacja firmy Microsoft'
description: Użyj usługi Azure Data Lake Storage Gen1 i interfejsu API REST WebHDFS do wykonywania operacji zarządzania kontem w ramach konta Data Lake Storage Gen1
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877110"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operacje zarządzania kontem w usłudze Azure Data Lake magazynu Gen1 przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
> * [Interfejs API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

W tym artykule dowiesz się, jak wykonywać operacje zarządzania kontem w usłudze Azure Data Lake magazynu Gen1 przy użyciu interfejsu API REST. Operacje zarządzania kontem obejmują tworzenie konta Data Lake Storage Gen1, usuwanie konta Data Lake Storage Gen1 itp. Aby uzyskać instrukcje na temat sposobu wykonywania operacji systemu plików na Data Lake Storage Gen1 przy użyciu interfejsu API REST, zobacz [operacje systemu plików w Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[Adres cURL](https://curl.haxx.se/)**. W tym artykule używa programu cURL w celu zademonstrowania sposobu wykonywania wywołań interfejsu API REST względem konta Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>W jaki sposób uwierzytelniać za pomocą usługi Azure Active Directory?
Dostępne są dwa podejścia do uwierzytelniania za pomocą usługi Azure Active Directory.

* Aby uwierzytelnianie użytkowników końcowych (interakcyjne) aplikacji, zobacz [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Do uwierzytelniania service to service dla aplikacji (nieinterakcyjne), zobacz [Service to service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta Data Lake Storage Gen1
Ta operacja jest oparta na wywołaniu interfejsu API REST zdefiniowanym [tutaj](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Użyj następującego polecenia cURL. Zastąp  **\<yourstoragegen1name >** nazwą usługi Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

W poleceniu powyżej zastąp ciąg \<`REDACTED`\> tokenem autoryzacji pobranym wcześniej. Ładunek żądania dla tego polecenia jest zawarty w pliku **input.json**, który jest udostępniany dla parametru `-d` powyżej. Zawartość pliku input.json przypomina następujący fragment kodu:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Usuwanie konta Data Lake Storage Gen1
Ta operacja jest oparta na wywołaniu interfejsu API REST zdefiniowanym [tutaj](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Użyj następującego polecenia cURL, można usunąć konta Data Lake Storage Gen1. Zastąp  **\<yourstoragegen1name >** nazwą konta usługi Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Kolejne kroki
* [Operacje systemu plików w Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Zobacz także
* [Dokumentacja interfejsu API REST programu usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Otwórz źródło danych big Data aplikacji zgodnych z usługi Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

