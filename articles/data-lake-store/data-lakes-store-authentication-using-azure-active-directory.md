---
title: Uwierzytelnianie w usłudze Data Lake Store za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uwierzytelniania w usłudze Data Lake Store za pomocą usługi Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 061a44540cb618074764ae02fecb3f43d47fe5dd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Uwierzytelnianie za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory

Azure Data Lake Store używa usługi Azure Active Directory do uwierzytelniania. Przed tworzenia aplikacji, która współdziała z usługi Azure Data Lake Store, należy określić sposób uwierzytelniania aplikacji z usługą Azure Active Directory (Azure AD). 

## <a name="authentication-options"></a>Opcje uwierzytelniania

* **Uwierzytelnianie użytkownika końcowego** — użytkownik końcowy Azure poświadczenia są używane do uwierzytelniania w usłudze Data Lake Store. Tworzonych do pracy z usługą Data Lake Store aplikacji monituje o podanie poświadczeń użytkownika. W związku z tym jest to mechanizm uwierzytelniania *interakcyjne* i aplikacja zostanie uruchomiona w kontekście zalogowanego użytkownika. Aby uzyskać więcej informacji oraz instrukcje, zobacz [uwierzytelniania użytkowników końcowych dla usługi Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Uwierzytelnianie usługi do usługi** — Użyj tej opcji, jeśli chcesz, aby aplikacja do samodzielnego uwierzytelnienia z usługą Data Lake Store. W takiej sytuacji należy utworzyć aplikację usługi Azure Active Directory (AD) i użyć klucza z aplikacji usługi Azure AD do uwierzytelniania w usłudze Data Lake Store. W związku z tym jest to mechanizm uwierzytelniania *nieinterakcyjnym*. Aby uzyskać więcej informacji oraz instrukcje, zobacz [uwierzytelniania do usługi Data Lake store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

W poniższej tabeli przedstawiono, jak są obsługiwane przez użytkownika końcowego i mechanizmów uwierzytelniania do usługi Data Lake Store. Oto, jak odczytać tabeli.

* Symbol ✔ * oznacza, że opcja uwierzytelniania jest obsługiwana i łączy do artykułu, który demonstruje sposób użycia opcji uwierzytelniania. 
* ✔ symbol oznacza, że opcja uwierzytelniania jest obsługiwana. 
* Puste komórki oznaczają, że opcja uwierzytelniania nie jest obsługiwany.


|Użyj tej opcji uwierzytelniania z...                   |.NET         |Java     |PowerShell |Interfejs wiersza polecenia 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Użytkownik końcowy (bez uwierzytelniania Wieloskładnikowego **)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(przestarzałe)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Użytkownik końcowy (za pomocą usługi MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Usługi i usługą (przy użyciu klucza klienta)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Usługi i usługą (przy użyciu certyfikatu klienta) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Kliknij <b>✔\* </b> symbolu. Jest to łącze.</i><br>
<i>** Oznacza MFA, uwierzytelnianie wieloskładnikowe</i>

Zobacz [scenariusze uwierzytelniania usługi Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md) Aby uzyskać więcej informacji na temat korzystania z usługi Azure Active Directory do uwierzytelniania.

## <a name="next-steps"></a>Kolejne kroki

* [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)


