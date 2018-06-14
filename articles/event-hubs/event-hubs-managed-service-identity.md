---
title: Tożsamość usługi zarządzanej z wersji zapoznawczej usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Użyj tożsamości usług zarządzanych przy użyciu usługi Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2017
ms.author: sethm
ms.openlocfilehash: dd50e4f6ebc5fdf5496a5127fde20bd052087b59
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783445"
---
# <a name="managed-service-identity-preview"></a>Tożsamość usługi zarządzanej (wersja zapoznawcza)

Zarządzane tożsamości usługi (MSI) to funkcja między Azure, która umożliwia tworzenie bezpiecznej tożsamość skojarzoną z wdrożenia w ramach którego działa kod aplikacji. Następnie można skojarzyć tej tożsamości z ról kontroli dostępu, które niestandardowe uprawnienia do uzyskiwania dostępu do określonych zasobów platformy Azure, przez tę aplikację. 

MSI platformy Azure zarządza tej tożsamości środowiska wykonawczego. Nie ma potrzeby przechowywania i ochrony dostępu do kluczy w kodzie aplikacji lub konfiguracji dla samej siebie tożsamości lub zasobów potrzebnych do uzyskania dostępu. Uruchomiona wewnątrz aplikacji usługi Azure App Service lub maszynę wirtualną z obsługą MSI włączonych aplikacji klienta usługi Event Hubs nie trzeba samodzielnie zasady sygnatury dostępu Współdzielonego i klucze lub innych tokenów dostępu. Aplikacja kliencka musi tylko adres punktu końcowego przestrzeni nazw usługi Event Hubs. Podczas łączenia z aplikacji, usługi Event Hubs wiąże kontekstu MSI klienta w przypadku operacji, które przedstawiono w przykładzie w dalszej części tego artykułu.

Po jest skojarzony z tożsamością usługi zarządzane przez klienta usługi Event Hubs można wykonać operacji wszystkim uprawnionym. Upoważnienia kojarzenie MSI z ról usługi Event Hubs. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs role i uprawnienia

Dla początkowego publicznej wersji zapoznawczej tożsamość usługi zarządzanej można dodawać tylko do ról "Właściciela" lub "Współautora" przestrzeni nazw usługi Event Hubs, która przyznaje pełną kontrolę tożsamości na wszystkich jednostek w przestrzeni nazw. Jednak operacje, które zmienić topologii przestrzeni nazw są początkowo zarządzania tylko jednak obsługiwane usługi Azure Resource Manager, a nie za pomocą natywnego interfejsu REST centra zdarzeń w zarządzania. Ta obsługa oznacza, że nie można użyć klienta programu .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) obiektu w tożsamości zarządzanych usług. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Centra zdarzeń za pomocą tożsamości zarządzanych usług

W poniższej sekcji opisano kroki wymagane do tworzenia i wdrażania przykładowej aplikacji, która działa w sekcji tożsamości zarządzanych usług, jak umożliwić tej tożsamości dostęp do obszaru nazw usługi Event Hubs i sposób komunikowania się z usługą event hubs przy użyciu którego tożsamość.

To wprowadzenie opisuje aplikacji sieci web hostowanych w [usłudze Azure App Service](https://azure.microsoft.com/services/app-service/). Kroki wymagane dla aplikacji hostowanej maszyny Wirtualnej są podobne.

### <a name="create-an-app-service-web-application"></a>Tworzenie aplikacji sieci web usługi aplikacji

Pierwszym krokiem jest do tworzenia aplikacji platformy ASP.NET z usługi aplikacji. Jeśli nie masz doświadczenia w obsłudze jak to zrobić na platformie Azure, wykonaj [ten przewodnik](../app-service/app-service-web-get-started-dotnet-framework.md). Jednak zamiast tworzyć aplikację MVC, jak pokazano w samouczku, tworzenie aplikacji formularzy sieci Web.

### <a name="set-up-the-managed-service-identity"></a>Konfigurowanie tożsamości zarządzanych usług

Po utworzeniu aplikacji przejdź do nowo utworzonej aplikacji sieci web w portalu Azure (także wyświetlane w instrukcje), a następnie przejdź do **zarządzane tożsamość usługi** strony i włączyć funkcję: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Po włączeniu funkcji nową tożsamość usługi jest utworzone w usłudze Azure Active Directory, a następnie skonfigurowane do hosta usługi aplikacji.

### <a name="create-a-new-event-hubs-namespace"></a>Tworzenie nowej przestrzeni nazw usługi Event Hubs

Dalej [tworzenie przestrzeni nazw usługi Event Hubs](event-hubs-create.md) w jednym z regiony platformy Azure, które obsługują podglądu MSI: **nam wschodnie**, **nam wschodnie 2**, lub **Europa**. 

Przejdź do obszaru nazw **kontroli dostępu (IAM)** w portalu, a następnie kliknij przycisk **Dodaj** Aby dodać tożsamość usługi zarządzanej **właściciela** roli. Aby to zrobić, wyszukaj nazwę aplikacji sieci web w **dodać uprawnienia** panelu **wybierz** pola, a następnie kliknij polecenie wpisu. Następnie kliknij przycisk **Save** (Zapisz).

![](./media/event-hubs-managed-service-identity/msi2.png)
 
Tożsamość usługi zarządzanej aplikacji sieci web ma teraz dostęp do przestrzeni nazw usługi Event Hubs, a do Centrum zdarzeń wcześniej została utworzona. 

### <a name="run-the-app"></a>Uruchomienie aplikacji

Teraz należy zmodyfikować domyślną stronę utworzoną aplikację ASP.NET. Można również użyć kodu aplikacji sieci web z [to repozytorium GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Po uruchomieniu aplikacji wskazać EventHubsMSIDemo.aspx w przeglądarce. Można również ustawić go jako stronę początkową. Kod znajduje się w pliku EventHubsMSIDemo.aspx.cs. Wynik jest aplikacją minimalnego sieci web z kilku pola wejścia i **wysyłania** i **odbierania** przyciski podłączoną do usługi Event Hubs wysyłać lub odbierać wiadomości. 

Uwaga jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obiekt został zainicjowany. Zamiast za pomocą dostawcy tokenu dostępu tokenu dostępu Współdzielonego, kod tworzy dostawcę tokenów tożsamości zarządzanych usług z `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` wywołania. W efekcie nie ma żadnych kluczy tajnych zachować do użycia. Przepływ kontekst tożsamości zarządzanych usług centra zdarzeń i uzgadnianie autoryzacji automatycznie są obsługiwane przez dostawcę tokenu jest modelem prostsze niż przy użyciu sygnatury dostępu Współdzielonego.

Po dokonaniu zmian, publikowanie i uruchomić aplikację. Łatwo uzyskać publikowania danych jest pobieranie, a następnie zaimportuj profil publikowania w programie Visual Studio:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Do wysyłania i odbierania wiadomości, wprowadź nazwę przestrzeni nazw i nazwę jednostki został utworzony, a następnie kliknij opcję **wysyłania** lub **odbierania**. 
 
Należy pamiętać, że tożsamość usługi zarządzanej działa tylko w środowisku platformy Azure i tylko w przypadku wdrożenia usługi App Service, w którym można skonfigurować. Należy również zauważyć, że zarządzana usługa tożsamości nie współpracujesz z miejsc wdrożenia usługi aplikacji w tej chwili.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Szczegóły cennika usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)