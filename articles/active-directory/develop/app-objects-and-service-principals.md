---
title: Aplikacje & podmioty usługi w usłudze Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o relacji między obiektami głównej aplikacji i usługi w usłudze Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: a636ff15da09bcf1891618d65270376f26fd3239
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885603"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Obiekty aplikacji i jednostki usługi w usłudze Azure Active Directory

Czasami znaczenie terminu "aplikacja" może być błędnie zrozumiane, gdy jest używane w kontekście usługi Azure Active Directory (Azure AD). W tym artykule wyjaśniono koncepcyjne i konkretne aspekty integracji aplikacji usługi Azure AD, z ilustracją rejestracji i zgody dla [aplikacji wielodostępnej.](developer-glossary.md#multi-tenant-application)

## <a name="overview"></a>Omówienie

Aplikacja, która została zintegrowana z usługą Azure AD ma implikacje, które wykraczają poza aspekt oprogramowania. "Aplikacja" jest często używany jako termin koncepcyjny, odnosząc się nie tylko do oprogramowania aplikacji, ale także jego rejestracji usługi Azure AD i roli w uwierzytelnianiu/autoryzacji "konwersacje" w czasie wykonywania.

Z definicji aplikacja może działać w tych rolach:

- [Rola klienta](developer-glossary.md#client-application) (zużywanie zasobu)
- [Rola serwera zasobów](developer-glossary.md#resource-server) (udostępnianie interfejsów API klientom)
- Zarówno rola klienta, jak i rola serwera zasobów

[Przepływ autoryzacji OAuth 2.0](developer-glossary.md#authorization-grant) definiuje protokół konwersacji, który umożliwia klientowi/zasobowi dostęp/ochronę danych zasobu.

W poniższych sekcjach zobaczysz, jak model aplikacji usługi Azure AD reprezentuje aplikację w czasie projektowania i w czasie wykonywania.

## <a name="application-registration"></a>Rejestracja aplikacji

Podczas rejestrowania aplikacji usługi Azure AD w [witrynie Azure portal][AZURE-Portal]two objects są tworzone w dzierżawie usługi Azure AD:

- Obiekt aplikacji i
- Obiekt jednostki usługi

### <a name="application-object"></a>Obiekt aplikacji

Aplikacja usługi Azure AD jest definiowana przez jego jeden i jedyny obiekt aplikacji, który znajduje się w dzierżawie usługi Azure AD, w której została zarejestrowana aplikacja, znany jako dzierżawy "domowej" aplikacji. [Jednostka Aplikacji][MS-Graph-App-Entity] programu Microsoft Graph definiuje schemat właściwości obiektu aplikacji.

### <a name="service-principal-object"></a>Obiekt główny usługi

Aby uzyskać dostęp do zasobów, które są zabezpieczone przez dzierżawę usługi Azure AD, jednostka, która wymaga dostępu, musi być reprezentowana przez podmiot zabezpieczeń. Dotyczy to zarówno użytkowników (podmiotu zabezpieczeń użytkownika), jak i aplikacji (jednostki usługi).

Podmiot zabezpieczeń definiuje zasady dostępu i uprawnienia dla użytkownika/aplikacji w dzierżawie usługi Azure AD. Umożliwia to podstawowe funkcje, takie jak uwierzytelnianie użytkownika/aplikacji podczas logowania i autoryzacji podczas dostępu do zasobów.

Gdy aplikacja ma uprawnienia dostępu do zasobów w dzierżawie (po rejestracji lub [zgodzie),](developer-glossary.md#consent)tworzony jest obiekt jednostki usługi. Jednostka Microsoft Graph [ServicePrincipal][MS-Graph-Sp-Entity] definiuje schemat właściwości obiektu jednostki głównej usługi.

### <a name="application-and-service-principal-relationship"></a>Relacja aplikacji i jednostki usługi

Należy wziąć pod uwagę obiekt aplikacji jako *globalnej* reprezentacji aplikacji do użytku we wszystkich dzierżawców i jednostki usługi jako *reprezentacji lokalnej* do użytku w określonej dzierżawy.

Obiekt aplikacji służy jako szablon, z którego *pochodzą* typowe i domyślne właściwości do stosowania podczas tworzenia odpowiednich obiektów jednostki usługi. Obiekt aplikacji ma zatem relację 1:1 z aplikacją i relacje 1:many z odpowiadającymi mu obiektami głównymi usługi.

Podmiot usługi musi zostać utworzony w każdej dzierżawie, w której jest używana aplikacja, umożliwiając jej ustanowienie tożsamości dla logowania i/lub dostępu do zasobów zabezpieczonych przez dzierżawcę. Aplikacja jednej dzierżawy ma tylko jedną jednostkę usługi (w swojej głównej dzierżawie) utworzoną do użycia podczas rejestracji aplikacji i mającą na to zgodę. Wielodostępna aplikacja sieci Web/interfejs API ma również jednostkę usługi utworzoną w każdej dzierżawie, w której użytkownik z tej dzierżawy wyraził zgodę na jego użycie.

> [!NOTE]
> Wszelkie zmiany wprowadzone w obiekcie aplikacji, są również odzwierciedlane w jego jednostki usługi jednostki tylko w dzierżawy domowej aplikacji (dzierżawy, gdzie został zarejestrowany). W przypadku aplikacji z wieloma dzierżawcami zmiany w obiekcie aplikacji nie są odzwierciedlane w obiektach głównej usługi dzierżawy konsumentów, dopóki dostęp nie zostanie usunięty za pośrednictwem [panelu dostępu do aplikacji](https://myapps.microsoft.com) i przyznany ponownie.
>
> Należy również zauważyć, że aplikacje macierzyste są domyślnie rejestrowane jako wielodostępne.

## <a name="example"></a>Przykład

Na poniższym diagramie przedstawiono relację między obiektem aplikacji aplikacji a odpowiadającymi mu obiektami głównej usługi w kontekście przykładowej aplikacji wielodostępnej o nazwie **aplikacja HR**. Istnieją trzy dzierżawy usługi Azure AD w tym przykładowym scenariuszu:

- **Adatum** — najemca używany przez firmę, która opracowała **aplikację HR**
- **Contoso** — dzierżawa używana przez organizację Contoso, która jest konsumentem **aplikacji HR**
- **Fabrikam** — dzierżawa używana przez organizację Fabrikam, która również korzysta z **aplikacji HR**

![Relacja między obiektem aplikacji a obiektem głównym usługi](./media/app-objects-and-service-principals/application-objects-relationship.svg)

W tym przykładowym scenariuszu:

| Krok | Opis |
|------|-------------|
| 1    | Jest procesem tworzenia obiektów jednostki aplikacji i usługi w dzierżawie domowej aplikacji. |
| 2    | Gdy administratorzy contoso i fabrikam wypełniają zgodę, obiekt głównej usługi jest tworzony w dzierżawie usługi Azure AD firmy i przypisywany uprawnienia, które administrator przyznał. Należy również pamiętać, że aplikacja HR może być skonfigurowana/ zaprojektowana w taki sposób, aby umożliwić użytkownikom zgodę na indywidualne użytkowanie. |
| 3    | Dzierżawy konsumentów aplikacji HR (Contoso i Fabrikam) każdy ma własny obiekt jednostki usługi. Każdy z nich reprezentuje ich użycie wystąpienia aplikacji w czasie wykonywania, podlega uprawnieniach udzielonych przez odpowiedniego administratora. |

## <a name="next-steps"></a>Następne kroki

- Za pomocą [Eksploratora wykresu firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer) można zbadać obiekty głównej aplikacji i usługi.
- Dostęp do obiektu aplikacji można uzyskać za pomocą interfejsu API programu Microsoft Graph, edytora manifestów aplikacji [portalu Azure][AZURE-Portal] lub [poleceń cmdlet usługi Azure AD PowerShell,](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)reprezentowanych przez [jego jednostkę aplikacji][MS-Graph-App-Entity]OData .
- Obiekt jednostkowy usługi aplikacji można uzyskać za pośrednictwem interfejsu API programu Microsoft Graph lub [poleceń cmdlet usługi Azure AD PowerShell,](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)reprezentowanych przez jego [jednostkę OData ServicePrincipal][MS-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
