---
title: Tożsamość usługi zarządzanej przy użyciu usługi Azure Service Bus (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Tożsamości usługi zarządzanej za pomocą usługi Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2018
ms.author: sethm
ms.openlocfilehash: 30df312e349bd6f6ebd1f38141075382be2522a2
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397988"
---
# <a name="managed-service-identity-preview"></a>Tożsamość usługi zarządzanej (wersja zapoznawcza)

Tożsamość usługi zarządzanej (MSI) jest funkcją między platformą Azure, która pozwala na tworzenie bezpieczna tożsamość skojarzonym z wdrożeniem w ramach którego działa kod aplikacji. Następnie można skojarzyć z tą tożsamością z rolami kontroli dostępu, które udzielić uprawnień niestandardowych na potrzeby uzyskiwania dostępu do określonych zasobów platformy Azure wymaganych przez aplikację.

Za pomocą pliku MSI platforma Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie trzeba przechowywać i chronić klucze dostępu w kodzie aplikacji albo konfiguracji dla tożsamości, sama lub za zasoby, których chcesz uzyskać dostęp. Aplikacja kliencka usługi Service Bus, systemem wewnątrz aplikacji usługi Azure App Service lub na maszynie wirtualnej z włączona obsługa tożsamości usługi Zarządzanej nie trzeba obsługiwać zasady sygnatury dostępu Współdzielonego i klucze lub innych tokenów dostępu. Aplikacja kliencka potrzebuje jedynie adres punktu końcowego w usłudze Service Bus w przestrzeni nazw. Gdy aplikacja nawiązuje połączenie, Service Bus wiąże kontekście MSI klienta w przypadku operacji, które przedstawiono w przykładzie w dalszej części tego artykułu. 

Gdy jest on skojarzony z tożsamości usługi zarządzanej, klient usługi Service Bus można wykonywać operacje wszystkim uprawnionym. Upoważnienia kojarzenie Instalatora MSI przy użyciu ról usługi Service Bus. 

## <a name="service-bus-roles-and-permissions"></a>Uprawnienia i role usługi Service Bus

W przypadku początkowego publicznej wersji zapoznawczej tożsamość usługi zarządzanej można dodać tylko do ról "Właściciel" lub "Współautor" w przestrzeni nazw usługi Service Bus, która przyznaje pełną kontrolę tożsamości, na wszystkich jednostek w przestrzeni nazw. Jednak operacje, aby zmienić topologii przestrzeni nazw są początkowo zarządzania tylko jednak obsługiwane usługi Azure Resource Manager i nie przy użyciu natywnego interfejsu zarządzania interfejsu REST usługi Service Bus. Ta obsługa oznacza, że nie można użyć klienta programu .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) obiekt w obrębie tożsamości usługi zarządzanej.

## <a name="use-service-bus-with-a-managed-service-identity"></a>Usługa Service Bus za pomocą tożsamości usługi zarządzanej

W poniższej sekcji opisano kroki wymagane do tworzenia i wdrażania przykładowej aplikacji, która jest uruchamiana tożsamości usługi zarządzanej, jak udzielić dostępu tej tożsamości, przestrzeń nazw komunikatów usługi Service Bus i jak aplikacja wchodzi w interakcje z usługą Service Bus jednostki przy użyciu tej tożsamości.

Tego wprowadzenia opisano aplikacji sieci web hostowanych w [usługi Azure App Service](https://azure.microsoft.com/services/app-service/). Kroki wymagane w przypadku aplikacji hostowanych maszyn wirtualnych są podobne.

### <a name="create-an-app-service-web-application"></a>Tworzenie aplikacji sieci web usługi App Service

Pierwszym krokiem jest do tworzenia aplikacji ASP.NET z aplikacji. Jeśli nie znasz dobrze jak to zrobić na platformie Azure, postępuj zgodnie z [ten poradnik](../app-service/app-service-web-get-started-dotnet-framework.md). Jednak zamiast tworzyć aplikację MVC, jak pokazano w tym samouczku, należy utworzyć aplikację formularzy sieci Web.

### <a name="set-up-the-managed-service-identity"></a>Konfigurowanie tożsamości usługi zarządzanej

Po utworzeniu aplikacji, przejdź do nowo utworzonej aplikacji internetowej w witrynie Azure portal (również wyświetlany na instrukcje), a następnie przejdź do **tożsamości usługi zarządzanej** strony, a następnie włączyć tę funkcję: 

![](./media/service-bus-managed-service-identity/msi1.png)

Po włączeniu funkcji nową tożsamość usługi jest tworzone w usłudze Azure Active Directory, a następnie skonfigurowane do hosta usługi App Service.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Utwórz nową przestrzeń nazw komunikatów usługi Service Bus

Następnie [tworzenie przestrzeni nazw komunikatów usługi Service Bus](service-bus-create-namespace-portal.md) w jednym z regionów świadczenia usługi Azure, które mają wersję zapoznawczą obsługi kontroli RBAC: **wschodnie stany USA**, **wschodnie stany USA 2**, lub **Europa Zachodnia** . 

Przejdź do obszaru nazw **kontrola dostępu (IAM)** strony w portalu, a następnie kliknij przycisk **Dodaj** można dodać tożsamości usługi zarządzanej do **właściciela** roli. Aby to zrobić, wyszukaj nazwę aplikacji sieci web w **Dodaj uprawnienia** panelu **wybierz** pola, a następnie kliknij pozycję. Następnie kliknij przycisk **Save** (Zapisz).

![](./media/service-bus-managed-service-identity/msi2.png)
 
Tożsamość usługi zarządzanej aplikacji sieci web ma teraz dostęp do przestrzeni nazw usługi Service Bus, a w kolejce został wcześniej utworzony. 

### <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz można zmodyfikować domyślnej strony utworzonej aplikacji ASP.NET. Można użyć kodu aplikacji sieci web z [to repozytorium serwisu GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Strony Default.aspx jest stroną docelową. Kod można znaleźć w pliku Default.aspx.cs. Wynik to aplikacja minimalne sieci web z kilku pól wejścia i **wysyłania** i **otrzymywać** przyciski, łączyć z usługą Service Bus albo wysyłać ani odbierać wiadomości.

Uwaga jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obiekt jest zainicjowany. Zamiast używania dostawcy tokenu dostępu współdzielonego tokenu (SAS), kod tworzy dostawcy tokenu dla tożsamości usługi zarządzanej przy użyciu `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` wywołania. W efekcie nie ma żadnych wpisów tajnych, przechowywanie i używać. Przepływ kontekst tożsamości usługi zarządzanej do usługi Service Bus i uzgadniania autoryzacji są automatycznie obsługiwane przez dostawcę tokenu, który jest modelem prostsze niż przy użyciu sygnatury dostępu Współdzielonego.

Po wprowadzeniu tych zmian, publikowania, a następnie uruchom aplikację. Jest łatwym sposobem uzyskiwania poprawne publikowania danych do pobrania, a następnie zaimportować profil publikowania w programie Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Wysyłać ani odbierać wiadomości, wprowadź nazwę przestrzeni nazw i nazwę obiektu, który został utworzony, a następnie kliknij opcję **wysyłania** lub **otrzymywać**.


> [!NOTE]
> - Tożsamość usługi zarządzanej działa tylko w środowisku platformy Azure na temat usług aplikacji w przypadku maszyn wirtualnych platformy Azure i zestawów skalowania. W przypadku aplikacji .NET biblioteki Microsoft.Azure.Services.AppAuthentication, który jest używany przez pakiet NuGet usługi Service Bus, udostępnia abstrakcję za pośrednictwem protokołu i obsługuje środowisko rozwoju lokalnego. Ta biblioteka umożliwia również przetestować kod lokalnie na komputerze deweloperskim, przy użyciu konta użytkownika z programu Visual Studio, interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub zintegrowane uwierzytelnianie usługi Active Directory. Aby uzyskać więcej informacji na temat opcji lokalny rozwój za pomocą tej biblioteki, zobacz [Service-to-service authentication usługi Azure Key Vault przy użyciu platformy .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Obecnie tożsamości usługi zarządzanej nie działają z miejscami wdrożenia usługi App Service.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)