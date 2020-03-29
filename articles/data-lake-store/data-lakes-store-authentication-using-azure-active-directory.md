---
title: Uwierzytelnianie w usłudze Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak uwierzytelniać się przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60193598"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Uwierzytelnianie przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory

Usługa Azure Data Lake Storage Gen1 używa usługi Azure Active Directory do uwierzytelniania. Przed dokonaniem tworzenia aplikacji, która współpracuje z data lake storage gen1, należy zdecydować, jak uwierzytelnić aplikację za pomocą usługi Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Opcje uwierzytelniania

* **Uwierzytelnianie użytkownika końcowego** — poświadczenia platformy Azure użytkownika końcowego są używane do uwierzytelniania przy użyciu usługi Data Lake Storage Gen1. Aplikacja utworzona do pracy z data lake storage gen1 monituje o te poświadczenia użytkownika. W rezultacie ten mechanizm uwierzytelniania jest *interaktywny,* a aplikacja działa w kontekście zalogowanego użytkownika. Aby uzyskać więcej informacji i instrukcji, zobacz [Uwierzytelnianie użytkownika końcowego dla usługi Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Uwierzytelnianie usługi do usługi** — użyj tej opcji, jeśli chcesz, aby aplikacja uwierzytelniła się przy użyciu usługi Data Lake Storage Gen1. W takich przypadkach należy utworzyć aplikację usługi Azure Active Directory (AD) i użyć klucza z aplikacji usługi Azure AD do uwierzytelniania przy użyciu usługi Data Lake Storage Gen1. W rezultacie ten mechanizm uwierzytelniania *jest nieinterakcyjny*. Aby uzyskać więcej informacji i instrukcji, zobacz [Uwierzytelnianie usługi do usługi dla usługi Magazyn danych Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

W poniższej tabeli przedstawiono, jak mechanizmy uwierzytelniania użytkowników końcowych i usługi do usługi są obsługiwane dla usługi Data Lake Storage Gen1. Oto jak czytasz tabelę.

* Symbol ✔* oznacza, że opcja uwierzytelniania jest obsługiwana i zawiera łącza do artykułu, który pokazuje, jak korzystać z opcji uwierzytelniania. 
* Symbol ✔ oznacza, że opcja uwierzytelniania jest obsługiwana. 
* Puste komórki oznaczają, że opcja uwierzytelniania nie jest obsługiwana.


|Użyj tej opcji uwierzytelniania z...                   |.NET         |Java     |PowerShell |Interfejs wiersza polecenia platformy Azure | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Użytkownik końcowy (bez usługi MFA**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(przestarzałe)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Użytkownik końcowy (z uwierzytelniać usługi MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Usługa do usługi (przy użyciu klucza klienta)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Usługa do usługi (przy użyciu certyfikatu klienta) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Kliknij symbol <b>✔.\* </b> To link.</i><br>
<i>** MfA oznacza uwierzytelnianie wieloskładnikowe</i>

Zobacz [Scenariusze uwierzytelniania dla usługi Azure Active Directory,](../active-directory/develop/authentication-scenarios.md) aby uzyskać więcej informacji na temat używania usługi Azure Active Directory do uwierzytelniania.

## <a name="next-steps"></a>Następne kroki

* [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)


