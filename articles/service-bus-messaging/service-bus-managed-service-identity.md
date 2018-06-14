---
title: Tożsamość usługi zarządzanej z wersji zapoznawczej usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Zarządzana usługa tożsamości za pomocą usługi Azure Service Bus
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
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 7b9901ee3478cb193c808b65d2dbbcf8b596a3c1
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874656"
---
# <a name="managed-service-identity-preview"></a>Tożsamość usługi zarządzanej (wersja zapoznawcza)

Zarządzane tożsamości usługi (MSI) to funkcja między Azure, która umożliwia tworzenie bezpiecznej tożsamość skojarzoną z wdrożenia w ramach którego działa kod aplikacji. Następnie można skojarzyć tej tożsamości z ról kontroli dostępu, które niestandardowe uprawnienia do uzyskiwania dostępu do określonych zasobów platformy Azure, przez tę aplikację.

MSI platformy Azure zarządza tej tożsamości środowiska wykonawczego. Nie ma potrzeby przechowywania i ochrony dostępu do kluczy w kodzie aplikacji lub konfiguracji dla samej siebie tożsamości lub zasobów potrzebnych do uzyskania dostępu. Aplikacji klienckiej usługi Service Bus, systemem wewnątrz aplikacji usługi Azure App Service lub w maszynie wirtualnej z włączona obsługa MSI nie trzeba samodzielnie zasady sygnatury dostępu Współdzielonego i klucze lub innych tokenów dostępu. Aplikacja kliencka musi tylko adres punktu końcowego przestrzeni nazw usługi magistrali komunikatów. Podczas łączenia z aplikacji, usługi Service Bus wiąże kontekstu MSI klienta w przypadku operacji, które przedstawiono w przykładzie w dalszej części tego artykułu. 

Gdy jest on skojarzony z tożsamości zarządzanego usługi, klienta usługi Service Bus można wykonywać operacje wszystkim uprawnionym. Upoważnienia kojarzenie MSI z ról usługi Service Bus. 

## <a name="service-bus-roles-and-permissions"></a>Magistrala usług ról i uprawnień

Dla początkowego publicznej wersji zapoznawczej tożsamość usługi zarządzanej można dodawać tylko do ról "Właściciela" lub "Współautora" przestrzeni nazw usługi Service Bus, która przyznaje pełną kontrolę tożsamości na wszystkich jednostek w przestrzeni nazw. Jednak operacje, które zmienić topologii przestrzeni nazw są początkowo zarządzania tylko jednak obsługiwane usługi Azure Resource Manager, a nie za pomocą natywnego interfejsu zarządzania REST magistrali usługi. Ta obsługa oznacza, że nie można użyć klienta programu .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) obiektu w tożsamości zarządzanych usług.

## <a name="use-service-bus-with-a-managed-service-identity"></a>Używanie usługi Service Bus za pomocą tożsamości zarządzanych usług

W poniższej sekcji opisano kroki wymagane do tworzenia i wdrażania przykładowej aplikacji, która jest uruchamiana tożsamości zarządzanych usług, jak umożliwić tej tożsamości dostęp do przestrzeni nazw usługi magistrali komunikatów i sposób komunikowania się z usługą Service Bus jednostki przy użyciu tej tożsamości.

To wprowadzenie opisuje aplikacji sieci web hostowanych w [usłudze Azure App Service](https://azure.microsoft.com/services/app-service/). Kroki wymagane dla aplikacji hostowanej maszyny Wirtualnej są podobne.

### <a name="create-an-app-service-web-application"></a>Tworzenie aplikacji sieci web usługi aplikacji

Pierwszym krokiem jest do tworzenia aplikacji platformy ASP.NET z usługi aplikacji. Jeśli nie masz doświadczenia w obsłudze jak to zrobić na platformie Azure, wykonaj [ten przewodnik](../app-service/app-service-web-get-started-dotnet-framework.md). Jednak zamiast tworzyć aplikację MVC, jak pokazano w samouczku, tworzenie aplikacji formularzy sieci Web.

### <a name="set-up-the-managed-service-identity"></a>Konfigurowanie tożsamości zarządzanych usług

Po utworzeniu aplikacji przejdź do nowo utworzonej aplikacji sieci web w portalu Azure (także wyświetlane w instrukcje), a następnie przejdź do **zarządzane tożsamość usługi** strony i włączyć funkcję: 

![](./media/service-bus-managed-service-identity/msi1.png)

Po włączeniu funkcji nową tożsamość usługi jest utworzone w usłudze Azure Active Directory, a następnie skonfigurowane do hosta usługi aplikacji.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Utwórz nowy obszar nazw usługi magistrali komunikatów

Następnie [tworzenie przestrzeni nazw usługi magistrali komunikatów](service-bus-create-namespace-portal.md) w jednym z regiony platformy Azure, które obsługują podglądu RBAC: **nam wschodnie**, **nam wschodnie 2**, lub **Europa Zachodnia** . 

Przejdź do obszaru nazw **kontroli dostępu (IAM)** w portalu, a następnie kliknij przycisk **Dodaj** Aby dodać tożsamość usługi zarządzanej **właściciela** roli. Aby to zrobić, wyszukaj nazwę aplikacji sieci web w **dodać uprawnienia** panelu **wybierz** pola, a następnie kliknij polecenie wpisu. Następnie kliknij przycisk **Save** (Zapisz).

![](./media/service-bus-managed-service-identity/msi2.png)
 
Tożsamość usługi zarządzanej aplikacji sieci web ma teraz dostęp do przestrzeni nazw usługi Service Bus, a do kolejki wcześniej została utworzona. 

### <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz należy zmodyfikować domyślną stronę utworzoną aplikację ASP.NET. Można również użyć kodu aplikacji sieci web z [to repozytorium GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity).

Strona Default.aspx jest strony docelowej. Kod znajduje się w pliku Default.aspx.cs. Wynik jest aplikacją minimalnego sieci web z kilku pola wejścia i **wysyłania** i **odbierania** przyciski podłączoną do usługi Service Bus albo wysłać lub odebrać wiadomości.

Uwaga jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obiekt został zainicjowany. Zamiast za pomocą dostawcy tokenu dostępu tokenu dostępu Współdzielonego, kod tworzy dostawcę tokenów tożsamości zarządzanych usług z `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` wywołania. W efekcie nie ma żadnych kluczy tajnych zachować do użycia. Przepływ kontekst tożsamości zarządzana Usługa Service Bus i uzgadnianie autoryzacji automatycznie są obsługiwane przez dostawcę tokenu jest modelem prostsze niż przy użyciu sygnatury dostępu Współdzielonego.

Po dokonaniu zmian, publikowanie i uruchomić aplikację. Łatwo uzyskać publikowania danych jest pobieranie, a następnie zaimportuj profil publikowania w programie Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Do wysyłania i odbierania wiadomości, wprowadź nazwę przestrzeni nazw i nazwę jednostki został utworzony, a następnie kliknij opcję **wysyłania** lub **odbierania**.
 
Należy pamiętać, że tożsamość usługi zarządzanej działa tylko w środowisku platformy Azure i tylko w przypadku wdrożenia usługi App Service, w którym można skonfigurować. Należy również zauważyć, że zarządzana usługa tożsamości nie współpracujesz z miejsc wdrożenia usługi aplikacji w tej chwili.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)