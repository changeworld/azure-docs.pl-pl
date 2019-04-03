---
title: Uwierzytelnianie w usłudze Azure Data Lake magazynu Gen1 przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i uwierzytelnianie za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
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
ms.openlocfilehash: f83cf183bee930dd07c707b0eb49125cecd70b84
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884639"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Uwierzytelnianie za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory

Azure Data Lake Storage Gen1 używa usługi Azure Active Directory do uwierzytelniania. Przed tworzenia aplikacji współdziałająca z usługą Data Lake Storage Gen1, należy określić sposób uwierzytelniania aplikacji za pomocą usługi Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Opcje uwierzytelniania

* **Uwierzytelnianie użytkowników końcowych** — użytkownik końcowy poświadczeń platformy Azure są używane do uwierzytelniania w usłudze Data Lake Storage Gen1. Aplikację, którą tworzysz do pracy z Data Lake Storage Gen1 wyświetli monit o poświadczenia użytkownika. W rezultacie, ten mechanizm uwierzytelniania jest *interaktywne* i aplikacja zostanie uruchomiona w kontekście zalogowanego użytkownika. Aby uzyskać więcej informacji oraz instrukcje, zobacz [uwierzytelnianie użytkowników końcowych dla Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Service-to-service authentication** — Użyj tej opcji, jeśli chcesz, aby uwierzytelniać Data Lake Storage Gen1 aplikacji. W takiej sytuacji należy utworzyć aplikację usługi Azure Active Directory (AD) i użyć klucza z aplikacji usługi Azure AD do uwierzytelniania w usłudze Data Lake Storage Gen1. W rezultacie, ten mechanizm uwierzytelniania jest *nieinterakcyjnych*. Aby uzyskać więcej informacji oraz instrukcje, zobacz [uwierzytelniania do usługi Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

W poniższej tabeli przedstawiono, jak i mechanizmów uwierzytelniania service to service dla użytkowników końcowych są obsługiwane w przypadku Data Lake Storage Gen1. Oto, jak odczytać tabeli.

* Symbol ✔ * oznacza, że opcja uwierzytelniania jest obsługiwana i łączy do artykułu, który demonstruje sposób użycia opcji uwierzytelniania. 
* ✔ symbol oznacza, że opcja uwierzytelniania jest obsługiwany. 
* Puste komórki oznaczają, że opcja uwierzytelniania nie jest obsługiwany.


|Użyj tej opcji uwierzytelniania za pomocą...                   |.NET         |Java     |PowerShell |Interfejs wiersza polecenia platformy Azure | Python   |REST     |
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


