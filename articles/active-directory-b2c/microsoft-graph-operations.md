---
title: Obsługiwane operacje programu Microsoft Graph
titleSuffix: Azure AD B2C
description: Indeks operacji programu Microsoft Graph obsługiwanych do zarządzania zasobami usługi Azure AD B2C, w tym użytkowników, przepływów użytkowników, dostawców tożsamości, zasad niestandardowych, kluczy zasad i innych.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184252"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Operacje programu Microsoft Graph dostępne dla usługi Azure AD B2C

Następujące operacje interfejsu API programu Microsoft Graph są obsługiwane do zarządzania zasobami usługi Azure AD B2C, w tym użytkowników, dostawców tożsamości, przepływów użytkowników, zasad niestandardowych i kluczy zasad.

Każde łącze w poniższych sekcjach jest przeznaczone dla odpowiedniej strony w odwołaniu interfejsu API programu Microsoft Graph dla tej operacji.

## <a name="user-management"></a>Zarządzanie użytkownikami

- [Wyświetlanie użytkowników](https://docs.microsoft.com/graph/api/user-list)
- [Tworzenie użytkownika konsumenckiego](https://docs.microsoft.com/graph/api/user-post-users)
- [Uzyskaj użytkownika](https://docs.microsoft.com/graph/api/user-get)
- [Aktualizowanie użytkownika](https://docs.microsoft.com/graph/api/user-update)
- [Usuń użytkownika](https://docs.microsoft.com/graph/api/user-delete)

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników usługi Azure AD B2C za pomocą interfejsu API programu Microsoft Graph, zobacz [Zarządzanie kontami użytkowników usługi Azure AD B2C za pomocą programu Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="identity-providers-user-flow"></a>Dostawcy tożsamości (przepływ użytkowników)

Zarządzanie dostawcami tożsamości dostępnymi dla przepływów użytkowników w dzierżawie usługi Azure AD B2C.

- [Lista dostawców tożsamości zarejestrowanych w dzierżawie usługi Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Tworzenie dostawcy tożsamości](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Uzyskaj dostawcę tożsamości](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Aktualizowanie dostawcy tożsamości](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Usuwanie dostawcy tożsamości](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Przepływ użytkownika

Skonfiguruj wstępnie utworzone zasady rejestracji, logowania, połączonej rejestracji i logowania, resetowania hasła i aktualizacji profilu.

- [Lista przepływów użytkowników](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Tworzenie przepływu użytkownika](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Uzyskaj przepływ użytkownika](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Usuwanie przepływu użytkownika](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Zasady niestandardowe

Poniższe operacje umożliwiają zarządzanie zasadami struktury zaufania usługi Azure AD B2C, znanymi jako [zasady niestandardowe.](custom-policy-overview.md)

- [Wyświetlanie listy wszystkich zasad struktury zaufania skonfigurowanych w dzierżawie](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Tworzenie zasad struktury zaufania](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Odczytywanie właściwości istniejących zasad struktury zaufania](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Aktualizowanie lub tworzenie zasad struktury zaufania.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Usuwanie istniejących zasad struktury zaufania](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Klucze zasad

Struktura środowiska tożsamości przechowuje wpisy tajne, do których odwołuje się w zasadach niestandardowych, aby ustanowić zaufanie między składnikami. Te wpisy tajne mogą być symetryczne lub asymetryczne klucze/wartości. W witrynie Azure portal te jednostki są wyświetlane jako **klucze zasad**.

Zasobem najwyższego poziomu dla kluczy zasad w interfejsie API programu Microsoft Graph jest [zestaw kluczy zaufanej struktury](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Każdy **zestaw kluczy** zawiera co najmniej jeden **klawisz**. Aby utworzyć klucz, najpierw utwórz pusty zestaw kluczy, a następnie wygeneruj klucz w zestawie kluczy. Można utworzyć ręczny klucz tajny, przekazać certyfikat lub klucz PKCS12. Kluczem może być wygenerowany klucz tajny, zdefiniowany ciąg (np. klucz tajny aplikacji Facebooka) lub przesłany certyfikat. Jeśli zestaw kluczy ma wiele kluczy, aktywny jest tylko jeden z kluczy.

### <a name="trust-framework-policy-keyset"></a>Zestaw kluczy zasad struktury zaufania

- [Wyświetlanie listy zestawień kluczy struktury zaufania](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Tworzenie zestawy kluczy struktury zaufania](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Pobierz zestaw kluczy](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Aktualizowanie zestawy kluczy struktury zaufania](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Usuwanie zestawy kluczy struktury zaufania](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Klucz zasad struktury zaufania

- [Uzyskaj aktualnie aktywny klucz w zestawie kluczy](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Generowanie klucza w zestawie kluczy](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Przekazywanie klucza tajnego opartego na ciągu](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Przekazywanie certyfikatu X.509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Przekazywanie certyfikatu formatu PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplikacje

- [Wyświetlanie listy aplikacji](https://docs.microsoft.com/graph/api/application-list)
- [Tworzenie aplikacji](https://docs.microsoft.com/graph/api/resources/application)
- [Aktualizacja aplikacji](https://docs.microsoft.com/graph/api/application-update)
- [Tworzenie usługiPrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Tworzenie oauth2Permission Grant](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Usuwanie aplikacji](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Właściwości rozszerzenia aplikacji

- [Właściwości rozszerzenia listy](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Usługa Azure AD B2C udostępnia katalog, który może pomieścić 100 atrybutów niestandardowych na użytkownika. W przypadku przepływów użytkowników te właściwości rozszerzenia są [zarządzane przy użyciu portalu Azure.](custom-policy-custom-attributes.md) W przypadku zasad niestandardowych usługa Azure AD B2C tworzy dla Ciebie właściwość przy pierwszym zapisie wartości do właściwości rozszerzenia.

## <a name="audit-logs"></a>Dzienniki inspekcji

- [Lista dzienników inspekcji](https://docs.microsoft.com/graph/api/directoryaudit-list)

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do dzienników inspekcji usługi Azure AD B2C za pomocą interfejsu API programu Microsoft Graph, zobacz [Uzyskiwanie dostępu do dzienników inspekcji usługi Azure AD B2C](view-audit-logs.md).
