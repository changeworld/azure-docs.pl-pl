---
title: 'Service-to-service authentication: Interfejs API REST przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak osiągnąć usługa Usługa uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory przy użyciu interfejsu API REST
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c48f7d7608b2b70f4ae41e2af5792cff72bb0dd2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60195786"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Usługa Usługa uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

W tym artykule dowiesz się o tym, jak używać interfejsu API REST w celu usługi do uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1. Aby uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu interfejsu API REST, zobacz [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Web"**. Zostały wykonane kroki opisane w [Service to service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami
W tym scenariuszu aplikacja udostępnia własne poświadczenia, aby wykonywać operacje. W tym celu należy wygenerować żądanie POST, tak jak pokazano w poniższym fragmencie kodu: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Dane wyjściowe w żądaniu zawiera token autoryzacji (wskazywane przez `access-token` w danych wyjściowych poniżej), aby były następnie przekazywane z wywołaniami interfejsu API REST. Zapisz token uwierzytelniania w pliku tekstowym. będą potrzebne podczas wykonywania wywołania REST do Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

W tym artykule wykorzystano podejście **nieinterakcyjne**. Więcej informacji na temat podejścia nieinterakcyjnego (wywołań między usługami) zawiera temat [Service to service calls using credentials](https://msdn.microsoft.com/library/azure/dn645543.aspx) (Wywołania między usługami przy użyciu poświadczeń). 

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania w usłudze Data Lake Storage Gen1 za pomocą usługi do uwierzytelniania przy użyciu interfejsu API REST. Możesz teraz przejrzeć następujące artykuły, które omówiono sposób użycia interfejsu API REST do pracy z Data Lake Storage Gen1.

* [Operacje zarządzania kontem w Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-get-started-rest-api.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-data-operations-rest-api.md)

