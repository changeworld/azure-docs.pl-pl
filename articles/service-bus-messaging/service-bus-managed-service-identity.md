---
title: Zarządzane tożsamości dla zasobów platformy Azure z Azure Service Bus | Microsoft Docs
description: Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure z Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 139ebe02727bab8cc80f6f0c6bbbd2156f025c21
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70992303"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Azure Service Bus
[Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) jest funkcją między platformą Azure, która pozwala na tworzenie bezpieczna tożsamość skojarzonym z wdrożeniem w ramach którego działa kod aplikacji. Następnie można skojarzyć z tą tożsamością z rolami kontroli dostępu, które udzielić uprawnień niestandardowych na potrzeby uzyskiwania dostępu do określonych zasobów platformy Azure wymaganych przez aplikację.

Za pomocą tożsamości zarządzanych platformy Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie trzeba przechowywać i chronić klucze dostępu w kodzie aplikacji albo konfiguracji dla tożsamości, sama lub za zasoby, których chcesz uzyskać dostęp. Aplikacja kliencka Service Bus uruchomiona w aplikacji Azure App Service lub na maszynie wirtualnej z włączonymi jednostkami zarządzanymi dla obsługi zasobów platformy Azure nie musi obsługiwać reguł i kluczy SAS ani żadnych innych tokenów dostępu. Aplikacja kliencka wymaga tylko adresu punktu końcowego przestrzeni nazw wiadomości Service Bus. Gdy aplikacja nawiązuje połączenie, Service Bus powiązać kontekstu jednostki zarządzanej z klientem w operacji, która jest wyświetlana w przykładzie w dalszej części tego artykułu. Po skojarzeniu jej z tożsamością zarządzaną klient Service Bus może wykonać wszystkie autoryzowane operacje. Autoryzacja jest przyznawana przez skojarzenie jednostki zarządzanej z rolami Service Bus. 

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (użytkownik, Grupa lub aplikacja) próbuje uzyskać dostęp do jednostki Service Bus, żądanie musi być autoryzowane. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Najpierw jest uwierzytelniana tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0. Nazwa zasobu do żądania tokenu to `https://servicebus.azure.net`.
 1. Następnie token jest przesyłany w ramach żądania do usługi Service Bus, aby autoryzować dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak maszyna wirtualna platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacja funkcji platformy Azure, może używać tożsamości zarządzanej do uzyskiwania dostępu do zasobów. Aby dowiedzieć się, jak uwierzytelniać żądania wysyłane przez zarządzaną tożsamość do usługi Service Bus, zobacz [uwierzytelnianie dostępu do zasobów Azure Service Bus za pomocą Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](service-bus-managed-service-identity.md). 

Krok autoryzacji wymaga, aby co najmniej jedna rola RBAC była przypisana do podmiotu zabezpieczeń. Azure Service Bus oferuje role RBAC, które obejmują zestawy uprawnień dla Service Bus zasobów. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Aby dowiedzieć się więcej na temat przypisywania ról RBAC do Azure Service Bus, zobacz [wbudowane role RBAC dla Azure Service Bus](#built-in-rbac-roles-for-azure-service-bus). 

Aplikacje natywne i aplikacje sieci Web, które wysyłają żądania do Service Bus mogą również autoryzować się z usługą Azure AD. W tym artykule przedstawiono sposób żądania tokenu dostępu i używania go do autoryzacji żądań dotyczących zasobów Service Bus. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC na potrzeby praw dostępu
Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md). Azure Service Bus definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do jednostek Service Bus, a także definiuje role niestandardowe na potrzeby uzyskiwania dostępu do danych.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów lub przestrzeni nazw Service Bus. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub zarządzaną tożsamością dla zasobów platformy Azure.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Wbudowane role RBAC dla Azure Service Bus
W przypadku Azure Service Bus Zarządzanie przestrzeniami nazw i wszystkimi powiązanymi zasobami za pośrednictwem Azure Portal i interfejsu API usługi Azure Resource Management jest już chronione przy użyciu modelu *kontroli dostępu opartej na rolach* (RBAC). Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzacji dostępu do Service Bus przestrzeni nazw:

- [Właściciel danych Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Umożliwia dostęp do danych Service Bus przestrzeni nazw i jej jednostek (kolejek, tematów, subskrypcji i filtrów)
- [Nadawca danych Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Ta rola umożliwia dostęp do Service Bus przestrzeni nazw i jej jednostek.
- [Azure Service Bus odbiorca danych](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Ta rola umożliwia uzyskanie dostępu do Service Bus przestrzeni nazw i jej jednostek. 

## <a name="resource-scope"></a>Zakres zasobów 
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można określić zakres dostępu do zasobów Service Bus, rozpoczynając od najwęższego zakresu:

- **Kolejka**, **temat**lub **subskrypcja**: Przypisanie roli dotyczy konkretnej jednostki Service Bus. Obecnie Azure Portal nie obsługuje przypisywania użytkowników/grup/tożsamości zarządzanych do Service Bus ról RBAC na poziomie subskrypcji. 
- **Service Bus przestrzeń nazw**: Przypisanie roli obejmuje całą topologię Service Bus w przestrzeni nazw oraz do skojarzonej z nią grupy odbiorców.
- **Grupa zasobów**: Przypisanie roli dotyczy wszystkich zasobów Service Bus w grupie zasobów.
- **Subskrypcja**: Przypisanie roli dotyczy wszystkich zasobów Service Bus we wszystkich grupach zasobów w subskrypcji.

> [!NOTE]
> Należy pamiętać, że propagacja ról RBAC może potrwać do 5 minut. 

Aby uzyskać więcej informacji na temat sposobu definiowania wbudowanych ról, zobacz [Omówienie definicji ról](../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje na temat tworzenia niestandardowych ról RBAC, zobacz [Tworzenie ról niestandardowych dla Access Control opartej na rolach](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej
Aby można było używać zarządzanych tożsamości dla zasobów platformy Azure w celu autoryzowania Service Bus zasobów z maszyny wirtualnej, należy najpierw włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager biblioteki klienckie](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Przyznawanie uprawnień do zarządzanej tożsamości w usłudze Azure AD
Aby autoryzować żądanie do usługi Service Bus z tożsamości zarządzanej w aplikacji, najpierw Skonfiguruj ustawienia kontroli dostępu opartej na rolach (RBAC) dla tej tożsamości zarządzanej. Azure Service Bus definiuje role RBAC, które obejmują uprawnienia do wysyłania i odczytywania z Service Bus. Gdy rola RBAC zostanie przypisana do zarządzanej tożsamości, zarządzana tożsamość otrzymuje dostęp do Service Bus jednostek w odpowiednim zakresie.

Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [uwierzytelnianie i autoryzacja przy użyciu Azure Active Directory w celu uzyskania dostępu do zasobów Service Bus](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Używanie Service Bus z tożsamościami zarządzanymi dla zasobów platformy Azure
Aby użyć Service Bus z tożsamościami zarządzanymi, należy przypisać tożsamość roli i odpowiedniego zakresu. Procedura opisana w tej sekcji używa prostej aplikacji, która działa w ramach zarządzanej tożsamości i uzyskuje dostęp do zasobów Service Bus.

Tutaj korzystamy z przykładowej aplikacji sieci Web hostowanej w [Azure App Service](https://azure.microsoft.com/services/app-service/). Aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji sieci Web, zobacz [Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](../app-service/app-service-web-get-started-dotnet.md)

Po utworzeniu aplikacji wykonaj następujące kroki: 

1. Przejdź do pozycji **Ustawienia** i wybierz pozycję **tożsamość**. 
1. Wybierz **stan** , który ma być **włączony**. 
1. Wybierz pozycję **Zapisz**, aby zapisać ustawienie. 

    ![Tożsamość zarządzana dla aplikacji sieci Web](./media/service-bus-managed-service-identity/identity-web-app.png)

Po włączeniu tego ustawienia zostanie utworzona nowa tożsamość usługi w Azure Active Directory (Azure AD) i skonfigurowana na hoście App Service.

Teraz Przypisz tę tożsamość usługi do roli w wymaganym zakresie w zasobach Service Bus.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Aby przypisać role RBAC przy użyciu Azure Portal
Aby przypisać rolę do przestrzeni nazw Service Bus, przejdź do przestrzeni nazw w Azure Portal. Wyświetl ustawienia Access Control (IAM) dla zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

> [!NOTE]
> Poniższe kroki przypisują rolę tożsamości usługi do przestrzeni nazw Service Bus. Możesz wykonać te same czynności, aby przypisać rolę w innych obsługiwanych zakresach (grupy zasobów i subskrypcji). 
> 
> [Utwórz przestrzeń nazw Service Bus Messaging](service-bus-create-namespace-portal.md) , jeśli jej nie masz. 

1. W Azure Portal przejdź do przestrzeni nazw Service Bus i Wyświetl **Przegląd** dla przestrzeni nazw. 
1. Wybierz pozycję **Access Control (IAM)** w menu po lewej stronie, aby wyświetlić ustawienia kontroli dostępu dla przestrzeni nazw Service Bus.
1.  Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.
3.  Wybierz pozycję **Dodaj** , aby dodać nową rolę.
4.  Na stronie **Dodawanie przypisania roli** wybierz role Azure Service Bus, które chcesz przypisać. Następnie wyszukaj w celu zlokalizowania tożsamości usługi, która została zarejestrowana w celu przypisania roli.
    
    ![Dodawanie strony przypisania roli](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Wybierz pozycję **Zapisz**. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono tożsamość usługi ma Azure Service Bus właściciel danych.
    
    ![Tożsamość przypisana do roli](./media/service-bus-managed-service-identity/role-assigned.png)

Po przypisaniu roli aplikacja sieci Web będzie miała dostęp do Service Bus jednostek w ramach zdefiniowanego zakresu. 

### <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz Zmodyfikuj domyślną stronę utworzonej aplikacji ASP.NET. Możesz użyć kodu aplikacji sieci Web z [tego repozytorium GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Domyślna strona. aspx to strona docelowa. Kod można znaleźć w pliku Default.aspx.cs. Wynikiem jest minimalna aplikacja sieci Web z kilkoma polami wprowadzania oraz za pomocą przycisków **Wyślij** i Odbierz łączących się z Service Bus wysyłania lub odbierania wiadomości.

Uwaga jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obiekt jest zainicjowany. Zamiast używania dostawcy tokenu dostępu współdzielonego tokenu (SAS), kod tworzy dostawcy tokenu dla tożsamości zarządzanej przy użyciu `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` wywołania. W związku z tym nie ma żadnych wpisów tajnych do zachowania i używania. Przepływ tożsamości zarządzanej do Service Bus i uzgadnianie autoryzacji są automatycznie obsługiwane przez dostawcę tokenu. Jest to prostsze model niż używanie sygnatury dostępu współdzielonego.

Po wprowadzeniu tych zmian, publikowania, a następnie uruchom aplikację. Poprawne publikowanie danych można łatwo uzyskać, pobierając i importując profil publikacji w programie Visual Studio:

![Pobierz profil publikowania](./media/service-bus-managed-service-identity/msi3.png)
 
Aby wysłać lub odebrać wiadomości, wprowadź nazwę przestrzeni nazw i nazwę utworzonej jednostki. Następnie kliknij opcję **Wyślij** lub Odbierz.


> [!NOTE]
> - Tożsamość zarządzana działa tylko w środowisku platformy Azure, w usługach App Services, maszynach wirtualnych platformy Azure i w zestawach skalowania. W przypadku aplikacji .NET Biblioteka Microsoft. Azure. Services. AppAuthentication, która jest używana przez pakiet NuGet Service Bus, zapewnia streszczenie tego protokołu i obsługuje lokalne środowisko programistyczne. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze deweloperskim przy użyciu konta użytkownika z programu Visual Studio, interfejsu wiersza polecenia platformy Azure 2,0 lub Active Directory zintegrowanego uwierzytelniania. Aby uzyskać więcej informacji na temat lokalnych opcji tworzenia w tej bibliotece, zobacz [uwierzytelnianie między usługą w celu Azure Key Vault przy użyciu platformy .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Obecnie zarządzane tożsamości nie działają w przypadku App Service miejsc wdrożenia.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
