---
title: Aplikacja usługi Azure Active Directory i obiekty nazwę głównej usługi
description: Omówienie relacji między aplikacją i obiektów nazw głównych usług w usłudze Azure Active Directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: a885170ce5c7e509e6497a8ac0e8d6790f9ea577
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581564"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Aplikacja i obiektów nazw głównych usług w usłudze Azure Active Directory (Azure AD)
Czasami znaczenie "aplikacja" mogą być źle zrozumiane gdy są używane w kontekście usługi Azure AD. Celem tego artykułu jest wyjaśnienie koncepcyjne i konkretnych aspektów integracji aplikacji usługi Azure AD, przy użyciu ilustrację rejestracji i zgoda na [aplikację wielodostępną](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Przegląd
Aplikacja, która jest zintegrowana z usługą Azure AD ma skutki, które wykraczają poza aspekt oprogramowania. "Aplikacja" jest często używana jako koncepcyjny termin odwołujące się do nie tylko oprogramowanie aplikacji, ale również jego rejestracji w usłudze Azure AD i rolę w uwierzytelniania/autoryzacji "konwersacji" w czasie wykonywania. Zgodnie z definicją, aplikacja może działać w [klienta](active-directory-dev-glossary.md#client-application) roli (Korzystanie z zasobem) [serwer zasobów](active-directory-dev-glossary.md#resource-server) roli (uwidaczniającą interfejsy API do klientów) lub nawet jednocześnie. Protokół konwersacji jest definiowany przez [przepływ OAuth 2.0 autoryzację](active-directory-dev-glossary.md#authorization-grant), umożliwiając klienta lub zasobu dostępu/ochrony zasobów danych odpowiednio. Teraz przejdźmy poziom głębiej i zobacz, jak reprezentuje aplikacji w czasie projektowania i środowiska wykonawczego w modelu aplikacji usługi Azure AD. 

## <a name="application-registration"></a>Rejestracja aplikacji
Podczas rejestrowania aplikacji usługi Azure AD w [witryny Azure portal][AZURE-Portal], dwa obiekty są tworzone w dzierżawie usługi Azure AD: obiekt aplikacji i obiektu jednostki usługi.

#### <a name="application-object"></a>Obiekt aplikacji
Aplikację usługi Azure AD jest zdefiniowany przez jego jeden i tylko obiekt aplikacji, w której znajduje się w dzierżawie usługi Azure AD, w którym aplikacja została zarejestrowana, znane jako "głównej" dzierżawy aplikacji. Azure AD Graph [Jednostka aplikacji] [ AAD-Graph-App-Entity] definiuje schemat dla właściwości obiektu aplikacji. 

#### <a name="service-principal-object"></a>obiektu jednostki usługi
Aby uzyskać dostęp do zasobów, które są zabezpieczone przez dzierżawę usługi Azure AD, jednostka, która wymaga dostępu musi być reprezentowana przez podmiot zabezpieczeń. Ta zasada obowiązuje dla użytkowników (nazwy głównej użytkownika) i aplikacji (nazwy głównej usługi). Podmiot zabezpieczeń definiuje zasady dostępu i uprawnień dla aplikacji/użytkownika w tej dzierżawie. Dzięki temu podstawowe funkcje, takie jak uwierzytelnianie aplikacji/użytkownika podczas logowania i autoryzacji podczas uzyskiwania dostępu do zasobów.

Kiedy aplikacja otrzymuje uprawnień dostępu do zasobów w dzierżawie (rejestracji lub [zgody](active-directory-dev-glossary.md#consent)), tworzony jest obiekt nazwy głównej usługi. Azure AD Graph [jednostki ServicePrincipal] [ AAD-Graph-Sp-Entity] definiuje schemat dla właściwości obiektu jednostki usługi firmy. 

#### <a name="application-and-service-principal-relationship"></a>Aplikacja i relacji jednostki usługi
Należy wziąć pod uwagę obiekt aplikacji jako *globalnego* reprezentację aplikacji na potrzeby wszystkich dzierżaw i jednostki usługi jako *lokalnego* reprezentacji do użycia w określonej dzierżawie. Służy obiekt aplikacji jako szablonu, z których typowe i domyślne właściwości są *pochodne* podczas tworzenia odpowiednich obiektów nazw głównych usług. Obiekt aplikacji ma związku z tym relacji 1:1 z aplikacji i relacji 1: duży zakres, z odpowiednie obiekty nazwy głównej usługi.

Jednostka usługi musi zostać utworzona w każdej dzierżawy, gdy aplikacja jest używana, dzięki czemu może ustanowić tożsamość dla logowania i/lub dostęp do zasobów, które są chronione przez dzierżawy. Aplikacja jednej dzierżawy ma tylko jedną jednostkę usługi (w jego głównej dzierżawy), utworzone i które wyraził zgodę do użycia podczas rejestracji aplikacji. Wielodostępnych aplikacji/interfejsu API sieci Web ma również nazwę główną usługi utworzone w ramach każdej dzierżawy gdzie użytkownik z tej dzierżawy wyraziła zgodę na jego użycia. 

> [!NOTE]
> Wszelkie zmiany wprowadzone do obiektu aplikacji, również są odzwierciedlane w jego obiektu jednostki usługi w głównej dzierżawy aplikacji tylko (dzierżawy, w którym zarejestrowano). W przypadku aplikacji wielodostępnych zmiany wprowadzone w obiekcie aplikacji nie są odzwierciedlane w żadnych dzierżawców konsumenta obiektów nazw głównych usług, do momentu usunięcia dostęp za pośrednictwem [panelu dostępu do aplikacji](https://myapps.microsoft.com) i ponownie przyznane.
><br>  
> Należy również zauważyć, że natywnych aplikacji są rejestrowane jako wielodostępna domyślnie.
> 
> 

## <a name="example"></a>Przykład
Na poniższym diagramie przedstawiono relację między aplikacji obiektu aplikacji i odpowiedniej usługi obiektów nazw głównych w kontekście przykładowej aplikacji wielodostępnych, nazywany **aplikacji działu KADR**. W tym scenariuszu istnieją trzy dzierżaw usługi Azure AD: 

* **Adatum** -dzierżawy używane przez firmę, który opracował **aplikacji działu KADR**
* **Contoso** -dzierżawcy stosowaną w organizacji Contoso, czyli konsumenta **aplikacji działu KADR**
* **Firma Fabrikam** -dzierżawy używane przez organizację Fabrikam korzysta również **aplikacji działu KADR**

![Relacja między obiekt aplikacji i obiektu jednostki usługi](./media/app-objects-and-service-principals/application-objects-relationship.png)

Na poprzednim rysunku kroku 1 jest proces tworzenia aplikacji i obiektów nazw głównych usług w głównej dzierżawy aplikacji.

W kroku 2 gdy został wykonany przez administratorów Contoso i Fabrikam zgody, obiektu jednostki usługi jest utworzone w ramach dzierżawy usługi Azure AD firmy i przypisane uprawnienia, które udzielane przez administratora. Należy również zauważyć, że aplikacji działu KADR może być skonfigurowane/umożliwia zgody przez użytkowników do użytku osobistego.

W kroku 3 dzierżaw klientów HR aplikacji (Contoso i Fabrikam) każdego ma swoje własne obiektu jednostki usługi. Każda reprezentuje ich użycie wystąpienie aplikacji w czasie wykonywania, podlega uprawnienia wyraził zgodę administrator odpowiednich.

## <a name="next-steps"></a>Kolejne kroki
Obiekt aplikacji aplikacji można uzyskać dostęp za pośrednictwem interfejsu API programu Graph usługi Azure AD, [witryny Azure portal] [ AZURE-Portal] edytorze manifestu aplikacji, lub [poleceń cmdlet programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), jako reprezentowany przez jej OData [Jednostka aplikacji][AAD-Graph-App-Entity].

Obiektu jednostki usługi aplikacji można uzyskać dostęp za pośrednictwem interfejsu API programu Graph usługi Azure AD lub [poleceń cmdlet programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), reprezentowany przez jej OData [jednostki ServicePrincipal] [ AAD-Graph-Sp-Entity].

[Programu Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) jest przydatne w przypadku wykonywania zapytań, aplikacji i obiektów nazw głównych usług.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
