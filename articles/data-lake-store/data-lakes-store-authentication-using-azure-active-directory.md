---
title: Uwierzytelnianie w Data Lake Store przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i uwierzytelniania w usłudze Data Lake Store przy użyciu usługi Azure Active Directory
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
ms.openlocfilehash: 7db48b03a6f34655917c82702c12dbc9bc4c987a
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494181"
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Authentication with Data Lake Store przy użyciu usługi Azure Active Directory uwierzytelnianie

Azure Data Lake Store używa usługi Azure Active Directory do uwierzytelniania. Przed tworzenia aplikacji, która współdziała z usługi Azure Data Lake Store, musisz zdecydować, jak do uwierzytelniania aplikacji z usługą Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Opcje uwierzytelniania

* **Uwierzytelnianie użytkowników końcowych** — użytkownik końcowy poświadczeń platformy Azure są używane do uwierzytelniania w usłudze Data Lake Store. Tworzonych do pracy z Data Lake Store aplikacji wyświetli monit o poświadczenia użytkownika. W rezultacie, ten mechanizm uwierzytelniania jest *interaktywne* i aplikacja zostanie uruchomiona w kontekście zalogowanego użytkownika. Aby uzyskać więcej informacji oraz instrukcje, zobacz [uwierzytelnianie użytkowników końcowych dla Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Service-to-service authentication** — Użyj tej opcji, jeśli chcesz, aby uwierzytelniać Data Lake Store aplikacji. W takiej sytuacji należy utworzyć aplikację usługi Azure Active Directory (AD) i użyć klucza z aplikacji usługi Azure AD do uwierzytelniania w usłudze Data Lake Store. W rezultacie, ten mechanizm uwierzytelniania jest *nieinterakcyjnych*. Aby uzyskać więcej informacji oraz instrukcje, zobacz [uwierzytelniania do usługi Data Lake Store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

W poniższej tabeli przedstawiono, jak i mechanizmów uwierzytelniania service to service dla użytkowników końcowych są obsługiwane w przypadku Data Lake Store. Oto, jak odczytać tabeli.

* Symbol ✔ * oznacza, że opcja uwierzytelniania jest obsługiwana i łączy do artykułu, który demonstruje sposób użycia opcji uwierzytelniania. 
* ✔ symbol oznacza, że opcja uwierzytelniania jest obsługiwany. 
* Puste komórki oznaczają, że opcja uwierzytelniania nie jest obsługiwany.


|Użyj tej opcji uwierzytelniania za pomocą...                   |.NET         |Java     |PowerShell |Interfejs wiersza polecenia 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Użytkownik końcowy (bez uwierzytelniania Wieloskładnikowego **)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(przestarzałe)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Użytkownik końcowy (za pomocą usługi MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Usługa service (za pomocą kluczy klienta)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Usługa service (przy użyciu certyfikatu klienta) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Kliknij <b>✔\* </b> symboli. Jest to łącze.</i><br>
<i>** Uwierzytelnianie wieloskładnikowe oznacza uwierzytelniania Wieloskładnikowego</i>

Zobacz [scenariusze uwierzytelniania dla usługi Azure Active Directory](../active-directory/develop/authentication-scenarios.md) Aby uzyskać więcej informacji na temat korzystania z usługi Azure Active Directory do uwierzytelniania.

## <a name="next-steps"></a>Kolejne kroki

* [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)


