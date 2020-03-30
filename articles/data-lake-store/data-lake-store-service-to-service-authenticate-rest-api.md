---
title: Uwierzytelnianie usługi do usługi — Pamięć masowa Data Lake Gen1 — INTERFEJS API REST
description: Dowiedz się, jak osiągnąć uwierzytelnianie między usługami za pomocą usługi Azure Data Lake Storage Gen1 i usługi Azure Active Directory przy użyciu interfejsu API REST.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 59d0bf20b16beda47d76e6a9940ac9fa4436da3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904528"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Uwierzytelnianie usługi do usługi przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

W tym artykule dowiesz się, jak używać interfejsu API REST do uwierzytelniania usługi do usługi za pomocą usługi Azure Data Lake Storage Gen1. Aby uzyskać uwierzytelnianie użytkownika końcowego za pomocą interfejsu Data Lake Storage Gen1 przy użyciu interfejsu API REST, zobacz [Uwierzytelnianie użytkownika końcowego za pomocą usługi Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji sieci Web usługi Azure Active Directory**. Kroki w [uwierzytelnianiu usługi do usługi za pomocą usługi Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami

W tym scenariuszu aplikacja udostępnia własne poświadczenia do wykonywania operacji. W tym celu należy wysłać żądanie POST, takie jak żądanie pokazane w poniższym urywek:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Dane wyjściowe żądania zawiera token autoryzacji `access-token` (oznaczone przez w danych wyjściowych poniżej), które następnie przekazać z wywołania interfejsu API REST. Zapisz token uwierzytelniania w pliku tekstowym; będzie potrzebny podczas wykonywania połączeń REST do Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

W tym artykule wykorzystano podejście **nieinterakcyjne**. Więcej informacji na temat podejścia nieinterakcyjnego (wywołań między usługami) zawiera temat [Service to service calls using credentials](https://msdn.microsoft.com/library/azure/dn645543.aspx) (Wywołania między usługami przy użyciu poświadczeń).

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano, jak używać uwierzytelniania usługi do usługi do uwierzytelniania przy użyciu usługi Usługi Magazyn Gen1 przy użyciu interfejsu API REST. Teraz można przeglądać następujące artykuły, które mówią o tym, jak używać interfejsu API REST do pracy z gen1 magazynu usługi Data Lake.

* [Operacje zarządzania kontami w usłudze Data Lake Storage Gen1 przy użyciu interfejsu REST API](data-lake-store-get-started-rest-api.md)
* [Operacje danych w usłudze Data Lake Storage Gen1 przy użyciu interfejsu REST API](data-lake-store-data-operations-rest-api.md)
