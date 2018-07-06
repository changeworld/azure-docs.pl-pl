---
title: Tożsamość usługi zarządzanej przy użyciu usługi Azure Event Hubs w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Tożsamości usługi zarządzanej za pomocą usługi Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: sethm
ms.openlocfilehash: 7c8f7fff5e3cf7334ce30a3fa90ae950f841662c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865301"
---
# <a name="managed-service-identity-preview"></a>Tożsamość usługi zarządzanej (wersja zapoznawcza)

Tożsamość usługi zarządzanej (MSI) jest funkcją między platformą Azure, która pozwala na tworzenie bezpieczna tożsamość skojarzonym z wdrożeniem w ramach którego działa kod aplikacji. Następnie można skojarzyć z tą tożsamością z rolami kontroli dostępu, które udzielić uprawnień niestandardowych na potrzeby uzyskiwania dostępu do określonych zasobów platformy Azure wymaganych przez aplikację. 

Za pomocą pliku MSI platforma Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie trzeba przechowywać i chronić klucze dostępu w kodzie aplikacji albo konfiguracji dla tożsamości, sama lub za zasoby, których chcesz uzyskać dostęp. Aplikacja kliencka usługi Event Hubs systemem wewnątrz aplikacji usługi Azure App Service lub na maszynie wirtualnej z włączona obsługa MSI nie trzeba obsługiwać zasady sygnatury dostępu Współdzielonego i klucze lub innych tokenów dostępu. Aplikacja kliencka potrzebuje jedynie adres punktu końcowego przestrzeni nazw usługi Event Hubs. Gdy aplikacja nawiązuje połączenie, usługa Event Hubs wiąże kontekście MSI klienta w przypadku operacji, które przedstawiono w przykładzie w dalszej części tego artykułu.

Gdy jest on skojarzony z tożsamości usługi zarządzanej, wszystkim uprawnionym operacje można wykonywać klienta usługi Event Hubs. Upoważnienia kojarzenie Instalatora MSI przy użyciu ról usługi Event Hubs. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs role i uprawnienia

W przypadku początkowego publicznej wersji zapoznawczej tożsamość usługi zarządzanej można dodać tylko do ról "Właściciel" lub "Współautor" w przestrzeni nazw usługi Event Hubs, która przyznaje pełną kontrolę tożsamości, na wszystkich jednostek w przestrzeni nazw. Jednak operacje, aby zmienić topologii przestrzeni nazw są początkowo zarządzania tylko jednak obsługiwane usługi Azure Resource Manager i nie przy użyciu natywnego interfejsu zarządzania REST centrów zdarzeń. Ta obsługa oznacza, że nie można użyć klienta programu .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) obiekt w obrębie tożsamości usługi zarządzanej. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Usługa Event Hubs za pomocą tożsamości usługi zarządzanej

W poniższej sekcji opisano kroki wymagane do tworzenia i wdrażania przykładowej aplikacji, która jest uruchamiana tożsamości usługi zarządzanej, jak udzielić tego dostępu tożsamości do przestrzeni nazw usługi Event Hubs i jak aplikacja wchodzi w interakcje z usługą event hubs za pomocą tego tożsamość.

Tego wprowadzenia opisano aplikacji sieci web hostowanych w [usługi Azure App Service](https://azure.microsoft.com/services/app-service/). Kroki wymagane w przypadku aplikacji hostowanych maszyn wirtualnych są podobne.

### <a name="create-an-app-service-web-application"></a>Tworzenie aplikacji sieci web usługi App Service

Pierwszym krokiem jest do tworzenia aplikacji ASP.NET z aplikacji. Jeśli nie znasz dobrze jak to zrobić na platformie Azure, postępuj zgodnie z [ten poradnik](../app-service/app-service-web-get-started-dotnet-framework.md). Jednak zamiast tworzyć aplikację MVC, jak pokazano w tym samouczku, należy utworzyć aplikację formularzy sieci Web.

### <a name="set-up-the-managed-service-identity"></a>Konfigurowanie tożsamości usługi zarządzanej

Po utworzeniu aplikacji, przejdź do nowo utworzonej aplikacji internetowej w witrynie Azure portal (również wyświetlany na instrukcje), a następnie przejdź do **tożsamości usługi zarządzanej** strony, a następnie włączyć tę funkcję: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Po włączeniu funkcji nową tożsamość usługi jest tworzone w usłudze Azure Active Directory, a następnie skonfigurowane do hosta usługi App Service.

### <a name="create-a-new-event-hubs-namespace"></a>Utwórz nową przestrzeń nazw usługi Event Hubs

Następnie [tworzenie przestrzeni nazw usługi Event Hubs](event-hubs-create.md) w jednym regionów platformy Azure, które obejmuje obsługę tożsamości usługi Zarządzanej wersji zapoznawczej: **wschodnie stany USA**, **wschodnie stany USA 2**, lub **Europa Zachodnia**. 

Przejdź do obszaru nazw **kontrola dostępu (IAM)** strony w portalu, a następnie kliknij przycisk **Dodaj** można dodać tożsamości usługi zarządzanej do **właściciela** roli. Aby to zrobić, wyszukaj nazwę aplikacji sieci web w **Dodaj uprawnienia** panelu **wybierz** pola, a następnie kliknij pozycję. Następnie kliknij przycisk **Save** (Zapisz).

![](./media/event-hubs-managed-service-identity/msi2.png)
 
Tożsamość usługi zarządzanej dla aplikacji sieci web teraz ma dostęp do przestrzeni nazw usługi Event Hubs i Centrum zdarzeń została wcześniej utworzona. 

### <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz można zmodyfikować domyślnej strony utworzonej aplikacji ASP.NET. Można również użyć kodu aplikacji sieci web z [to repozytorium serwisu GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Po uruchomieniu aplikacji, wskazać EventHubsMSIDemo.aspx w przeglądarce. Alternatywnie ustawić go jako swoją stronę początkową. Kod można znaleźć w pliku EventHubsMSIDemo.aspx.cs. Wynik to aplikacja minimalne sieci web z kilku pól wejścia i **wysyłania** i **otrzymywać** przycisków, które usługi Event hubs do wysyłania lub odbierania zdarzeń. 

Uwaga jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obiekt jest zainicjowany. Zamiast używania dostawcy tokenu dostępu współdzielonego tokenu (SAS), kod tworzy dostawcy tokenu dla tożsamości usługi zarządzanej przy użyciu `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` wywołania. W efekcie nie ma żadnych wpisów tajnych, przechowywanie i używać. Przepływ kontekst tożsamości usługi zarządzanej do usługi Event Hubs i uzgadniania autoryzacji są automatycznie obsługiwane przez dostawcę tokenu jest modelem prostsze niż przy użyciu sygnatury dostępu Współdzielonego.

Po wprowadzeniu tych zmian, publikowania, a następnie uruchom aplikację. Jest łatwym sposobem uzyskiwania poprawne publikowania danych do pobrania, a następnie zaimportować profil publikowania w programie Visual Studio:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Wysyłać ani odbierać wiadomości, wprowadź nazwę przestrzeni nazw i nazwę obiektu, który został utworzony, a następnie kliknij opcję **wysyłania** lub **otrzymywać**. 
 
Należy pamiętać, że tożsamość usługi zarządzanej działa tylko w środowisku platformy Azure, a tylko w przypadku wdrożenie usługi App Service, w której został skonfigurowany. Należy również zauważyć, że tożsamości usługi zarządzanej nie działają z miejscami wdrożenia usługi App Service w tym momencie.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Usługa Event Hubs szczegóły cennika](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)