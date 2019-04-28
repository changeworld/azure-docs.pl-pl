---
title: Zarządzane tożsamości dla zasobów platformy Azure w wersji zapoznawczej usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Użyj zarządzanych tożsamości dla zasobów platformy Azure z usługą Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: 5edeebd9698384785082e5a441c24e136ed22481
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317079"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>Zarządzanych tożsamości dla zasobów platformy Azure z usługą Service Bus 

[Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) jest funkcją między platformą Azure, która pozwala na tworzenie bezpieczna tożsamość skojarzonym z wdrożeniem w ramach którego działa kod aplikacji. Następnie można skojarzyć z tą tożsamością z rolami kontroli dostępu, które udzielić uprawnień niestandardowych na potrzeby uzyskiwania dostępu do określonych zasobów platformy Azure wymaganych przez aplikację.

Za pomocą tożsamości zarządzanych platformy Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie trzeba przechowywać i chronić klucze dostępu w kodzie aplikacji albo konfiguracji dla tożsamości, sama lub za zasoby, których chcesz uzyskać dostęp. Service Bus, aplikacja kliencka włączone uruchomiona wewnątrz aplikacji usługi Azure App Service lub maszyny wirtualnej z zarządzanymi jednostkami dla pomocy technicznej nie musi obsługiwać zasady sygnatury dostępu Współdzielonego i klucze lub innymi tokenami dostępu do zasobów platformy Azure. Aplikacja kliencka potrzebuje jedynie adres punktu końcowego w usłudze Service Bus w przestrzeni nazw. Gdy aplikacja nawiązuje połączenie, usługi Service Bus wiąże jednostki zarządzanej kontekst klienta w przypadku operacji, które przedstawiono w przykładzie w dalszej części tego artykułu. Gdy jest on skojarzony z tożsamości zarządzanej, klient usługi Service Bus można wykonywać operacje wszystkim uprawnionym. Upoważnienia kojarzenie jednostki zarządzanej przy użyciu ról usługi Service Bus. 

## <a name="service-bus-roles-and-permissions"></a>Uprawnienia i role usługi Service Bus

Tożsamość zarządzana można dodać tylko do ról "Właściciel" lub "Współautor" w przestrzeni nazw usługi Service Bus. Przyzna tożsamości pełnej kontroli, który dla wszystkich jednostek w przestrzeni nazw. Jednak zarządzania, które operacje, aby zmienić topologii przestrzeni nazw są początkowo tylko jednak obsługiwane usługi Azure Resource Manager. Nie jest za pomocą natywnego interfejsu zarządzania interfejsu REST usługi Service Bus. Ta obsługa oznacza, że nie można użyć klienta programu .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) obiektu w tożsamość zarządzaną.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Za pomocą usługi Service Bus zarządzanych tożsamości dla zasobów platformy Azure

W poniższej sekcji opisano kroki wymagane do tworzenia i wdrażania przykładowej aplikacji, która jest uruchamiana tożsamości zarządzanej, jak udzielić dostępu tej tożsamości, przestrzeń nazw komunikatów usługi Service Bus i jak aplikacja wchodzi w interakcje z jednostkami usługi Service Bus przy użyciu tej tożsamości.

Tego wprowadzenia opisano aplikacji sieci web hostowanych w [usługi Azure App Service](https://azure.microsoft.com/services/app-service/). Kroki wymagane w przypadku aplikacji hostowanych maszyn wirtualnych są podobne.

### <a name="create-an-app-service-web-application"></a>Tworzenie aplikacji sieci web usługi App Service

Pierwszym krokiem jest do tworzenia aplikacji ASP.NET z aplikacji. Jeśli nie znasz dobrze jak to zrobić na platformie Azure, postępuj zgodnie z [ten poradnik](../app-service/app-service-web-get-started-dotnet-framework.md). Jednak zamiast tworzyć aplikację MVC, jak pokazano w tym samouczku, należy utworzyć aplikację formularzy sieci Web.

### <a name="set-up-the-managed-identity"></a>Konfigurowanie tożsamości zarządzanej

Po utworzeniu aplikacji, przejdź do nowo utworzonej aplikacji internetowej w witrynie Azure portal (również wyświetlany na instrukcje), a następnie przejdź do **tożsamości usługi zarządzanej** strony, a następnie włączyć tę funkcję: 

![](./media/service-bus-managed-service-identity/msi1.png)

Po włączeniu funkcji nową tożsamość usługi jest tworzone w usłudze Azure Active Directory, a następnie skonfigurowane do hosta usługi App Service.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Utwórz nową przestrzeń nazw komunikatów usługi Service Bus

Następnie [tworzenie przestrzeni nazw komunikatów usługi Service Bus](service-bus-create-namespace-portal.md) w jednym z regionów świadczenia usługi Azure, które mają wersję zapoznawczą obsługi kontroli RBAC: **Wschodnie stany USA**, **wschodnie stany USA 2**, lub **Europa Zachodnia**. 

Przejdź do obszaru nazw **kontrola dostępu (IAM)** strony w portalu, a następnie kliknij przycisk **Dodaj przypisanie roli** tożsamości zarządzanej, aby dodać **właściciela** roli. Aby to zrobić, wyszukaj nazwę aplikacji sieci web w **Dodaj uprawnienia** panelu **wybierz** pola, a następnie kliknij pozycję. Następnie kliknij przycisk **Save** (Zapisz).

Tożsamość zarządzana aplikacja sieci web ma teraz dostęp do przestrzeni nazw usługi Service Bus, a w kolejce został wcześniej utworzony. 

### <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz można zmodyfikować domyślnej strony utworzonej aplikacji ASP.NET. Można użyć kodu aplikacji sieci web z [to repozytorium serwisu GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Strony Default.aspx jest stroną docelową. Kod można znaleźć w pliku Default.aspx.cs. Wynik to aplikacja minimalne sieci web z kilku pól wejścia i **wysyłania** i **otrzymywać** przyciski, łączyć z usługą Service Bus albo wysyłać ani odbierać wiadomości.

Uwaga jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obiekt jest zainicjowany. Zamiast używania dostawcy tokenu dostępu współdzielonego tokenu (SAS), kod tworzy dostawcy tokenu dla tożsamości zarządzanej przy użyciu `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` wywołania. W efekcie nie ma żadnych wpisów tajnych, przechowywanie i używać. Przepływ kontekst tożsamości zarządzanej usługi Service Bus i uzgadniania autoryzacji są automatycznie obsługiwane przez dostawcę tokenów. Jest to model prostsze niż przy użyciu sygnatury dostępu Współdzielonego.

Po wprowadzeniu tych zmian, publikowania, a następnie uruchom aplikację. Można uzyskać łatwo poprawnych danych publikacji, pobieranie, a następnie zaimportować profil publikowania w programie Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Aby wysyłać ani odbierać wiadomości, wprowadź nazwę przestrzeni nazw i nazwę obiektu, który został utworzony. Następnie kliknij opcję **wysyłania** lub **otrzymywać**.


> [!NOTE]
> - Tożsamość zarządzana działa tylko w środowisku platformy Azure na temat usług aplikacji w przypadku maszyn wirtualnych platformy Azure i zestawów skalowania. W przypadku aplikacji .NET biblioteki Microsoft.Azure.Services.AppAuthentication, który jest używany przez pakiet NuGet usługi Service Bus, udostępnia abstrakcję za pośrednictwem protokołu i obsługuje środowisko rozwoju lokalnego. Ta biblioteka umożliwia również przetestować kod lokalnie na komputerze deweloperskim, przy użyciu konta użytkownika z programu Visual Studio, interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub zintegrowane uwierzytelnianie usługi Active Directory. Aby uzyskać więcej informacji na temat opcji lokalny rozwój za pomocą tej biblioteki, zobacz [Service-to-service authentication usługi Azure Key Vault przy użyciu platformy .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Obecnie zarządzanych tożsamości nie działają z miejscami wdrożenia usługi App Service.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)