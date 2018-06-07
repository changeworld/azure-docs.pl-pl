---
title: 'Do usługi uwierzytelniania: interfejsu API REST z usługą Data Lake Store za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie uzyskania do usługi uwierzytelniania przy użyciu usługi Azure Active Directory przy użyciu interfejsu API REST usługi Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: ffa9b7408475820735e35a82edc0b1751abeb08a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624853"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>Usługa do uwierzytelniania przy użyciu interfejsu API REST usługi Data Lake Store
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

W tym artykule informacje o sposobie używania interfejsu API REST w celu usługi do uwierzytelniania za pomocą usługi Azure Data Lake Store. Do uwierzytelnienia użytkownika końcowego przy użyciu interfejsu API REST usługi Data Lake Store, zobacz [uwierzytelniania użytkownika końcowego przy użyciu interfejsu API REST usługi Data Lake Store](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Web"**. Należy wykonać czynności opisane w [do usługi uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami
W tym scenariuszu aplikacja udostępnia własne poświadczenia w celu wykonania operacji. W tym celu należy wygenerować żądanie POST, tak jak pokazano w poniższy fragment kodu: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Dane wyjściowe żądania zawiera token autoryzacji (wskazywane przez `access-token` w danych wyjściowych poniżej) czy następnie przekazujesz o wywołaniach interfejsu API REST. Zapisz token uwierzytelniania w pliku tekstowym. będzie on potrzebny w wywołaniach REST usługi Data Lake Store.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

W tym artykule wykorzystano podejście **nieinterakcyjne**. Więcej informacji na temat podejścia nieinterakcyjnego (wywołań między usługami) zawiera temat [Service to service calls using credentials](https://msdn.microsoft.com/library/azure/dn645543.aspx) (Wywołania między usługami przy użyciu poświadczeń). 

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania za pomocą usługi Azure Data Lake Store za pomocą usługi do uwierzytelniania przy użyciu interfejsu API REST. Teraz można przeglądać następujące artykuły, które opisano sposób użycia interfejsu API REST do pracy z usługą Azure Data Lake Store.

* [Operacji zarządzania kontem w usłudze Data Lake Store za pomocą interfejsu API REST](data-lake-store-get-started-rest-api.md)
* [Operacje na danych w usłudze Data Lake Store za pomocą interfejsu API REST](data-lake-store-data-operations-rest-api.md)

