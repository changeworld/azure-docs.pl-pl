---
title: Zarządzane tożsamości dla zasobów platformy Azure — usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat korzystania z zarządzanych tożsamości dla zasobów platformy Azure z usługą Azure Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 05/20/2019
ms.author: shvija
ms.openlocfilehash: 4e6f16a15547583baab63f452504d36eb2e43b85
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978466"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Zarządzanych tożsamości dla zasobów platformy Azure z usługą Event Hubs

[Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) jest funkcją między platformą Azure, która pozwala na tworzenie bezpieczna tożsamość skojarzonym z wdrożeniem w ramach którego działa kod aplikacji. Następnie można skojarzyć z tą tożsamością z rolami kontroli dostępu, które udzielić uprawnień niestandardowych na potrzeby uzyskiwania dostępu do określonych zasobów platformy Azure wymaganych przez aplikację. 

Za pomocą tożsamości zarządzanych platformy Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie trzeba przechowywać i chronić klucze dostępu w kodzie aplikacji albo konfiguracji dla tożsamości, sama lub za zasoby, których chcesz uzyskać dostęp. Aplikacja kliencka usługi Event Hubs systemem wewnątrz aplikacji usługi Azure App Service lub na maszynie wirtualnej z zarządzanych tożsamości dla włączona obsługa zasobów platformy Azure nie trzeba obsługiwać zasady sygnatury dostępu Współdzielonego i klucze lub innych tokenów dostępu. Aplikacja kliencka potrzebuje jedynie adres punktu końcowego przestrzeni nazw usługi Event Hubs. Gdy aplikacja nawiązuje połączenie, kontekst tożsamości zarządzanej usługi Event Hubs wiąże się do klienta w przypadku operacji, które przedstawiono w przykładzie w dalszej części tego artykułu.

Gdy jest on skojarzony z tożsamości zarządzanej, klienta usługi Event Hubs można wykonywać operacje wszystkim uprawnionym. Upoważnienia skojarzenie tożsamości zarządzanej przy użyciu ról usługi Event Hubs. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs role i uprawnienia
Możesz dodać zarządzanych tożsamości w celu **Event Hubs danych właściciela** roli przestrzeń nazw usługi Event Hubs. Ta rola przyznaje tożsamości, pełną kontrolę nad (Zarządzanie i operacje na danych) na wszystkich jednostek w przestrzeni nazw.

>[!IMPORTANT]
> Wcześniej obsługiwane dodawanie zarządzanych tożsamości w celu **właściciela** lub **Współautor** roli. Jednak uprawnienia dostępu do danych **właściciela** i **Współautor** roli nie są już uznawane. Jeśli używasz **właściciela** lub **Współautor** roli, przełącz się do przy użyciu **Event Hubs danych właściciela** roli.

Aby użyć nowej wbudowanej roli, wykonaj następujące kroki: 

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do przestrzeni nazw usługi Event Hubs.
3. Na **Event Hubs Namespace** wybierz opcję **Control(IAM) dostępu** menu po lewej stronie.
4. Na **kontrola dostępu (IAM)** wybierz opcję **Dodaj** w **Dodaj przypisanie roli** sekcji. 

    ![Dodawanie przycisku przypisania roli](./media/event-hubs-managed-service-identity/add-role-assignment-button.png)
5. Na **Dodaj przypisanie roli** wykonaj następujące czynności: 
    1. Aby uzyskać **roli**, wybierz opcję **właścicielem danych centra zdarzeń Azure**. 
    2. Wybierz **tożsamości** mają zostać dodane do roli.
    3. Wybierz pozycję **Zapisz**. 

        ![Rola właściciela danych centra zdarzeń](./media/event-hubs-managed-service-identity/add-role-assignment-dialog.png)
6. Przełącz się do **przypisań ról** strony i upewnij się, że użytkownik został dodany do **właścicielem danych centra zdarzeń Azure** roli. 

    ![Upewnij się, że użytkownik zostanie dodany do roli](./media/event-hubs-managed-service-identity/role-assignments.png)
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Usługa Event Hubs z zarządzanych tożsamości dla zasobów platformy Azure

W poniższej sekcji opisano następujące czynności:

1. Tworzenie i wdrażanie przykładowej aplikacji, która jest uruchamiana w kontekście zarządzanych tożsamości.
2. Udostępnienia tej tożsamości przestrzeni nazw usługi Event Hubs.
3. Jak aplikacja wchodzi w interakcje z usługą event hubs przy użyciu tej tożsamości.

Tego wprowadzenia opisano aplikacji sieci web hostowanych w [usługi Azure App Service](https://azure.microsoft.com/services/app-service/). Kroki wymagane w przypadku aplikacji hostowanych maszyn wirtualnych są podobne.

### <a name="create-an-app-service-web-application"></a>Tworzenie aplikacji sieci web usługi App Service

Pierwszym krokiem jest do tworzenia aplikacji ASP.NET z aplikacji. Jeśli nie znasz dobrze jak to zrobić na platformie Azure, postępuj zgodnie z [ten poradnik](../app-service/app-service-web-get-started-dotnet-framework.md). Jednak zamiast tworzyć aplikację MVC, jak pokazano w tym samouczku, należy utworzyć aplikację formularzy sieci Web.

### <a name="set-up-the-managed-identity"></a>Konfigurowanie tożsamości zarządzanej

Po utworzeniu aplikacji, przejdź do nowo utworzonej aplikacji internetowej w witrynie Azure portal (również wyświetlany na instrukcje), a następnie przejdź do **tożsamości usługi zarządzanej** strony, a następnie włączyć tę funkcję: 

![Zarządzane strony tożsamości usługi](./media/event-hubs-managed-service-identity/msi1.png)
 
Po włączeniu funkcji nową tożsamość usługi jest tworzone w usłudze Azure Active Directory, a następnie skonfigurowane do hosta usługi App Service.

### <a name="create-a-new-event-hubs-namespace"></a>Utwórz nową przestrzeń nazw usługi Event Hubs

Następnie [tworzenie przestrzeni nazw usługi Event Hubs](event-hubs-create.md). 

Przejdź do obszaru nazw **kontrola dostępu (IAM)** strony w portalu, a następnie kliknij przycisk **Dodaj przypisanie roli** tożsamości zarządzanej, aby dodać **właściciela** roli. Aby to zrobić, wyszukaj nazwę aplikacji sieci web w **Dodaj uprawnienia** panelu **wybierz** pola, a następnie kliknij pozycję. Następnie kliknij przycisk **Save** (Zapisz). Tożsamość zarządzaną w aplikacji internetowej ma dostęp do przestrzeni nazw usługi Event Hubs i Centrum zdarzeń została wcześniej utworzona. 

### <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz można zmodyfikować domyślnej strony utworzonej aplikacji ASP.NET. Można również użyć kodu aplikacji sieci web z [to repozytorium serwisu GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Po uruchomieniu aplikacji, wskazać EventHubsMSIDemo.aspx w przeglądarce. Można również ustawić jako swoją stronę początkową. Kod można znaleźć w pliku EventHubsMSIDemo.aspx.cs. Wynik to aplikacja minimalne sieci web z kilku pól wejścia i **wysyłania** i **otrzymywać** przycisków, które usługi Event hubs do wysyłania lub odbierania zdarzeń. 

Uwaga jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obiekt jest zainicjowany. Zamiast używania dostawcy tokenu dostępu współdzielonego tokenu (SAS), kod tworzy dostawcy tokenu dla tożsamości zarządzanej przy użyciu `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` wywołania. W efekcie nie ma żadnych wpisów tajnych, aby zapisać i używać. Przepływ kontekst tożsamości zarządzanej do usługi Event Hubs i uzgadniania autoryzacji są automatycznie obsługiwane przez dostawcę tokenu, który jest modelem prostsze niż przy użyciu sygnatury dostępu Współdzielonego.

Po wprowadzeniu tych zmian, publikowania, a następnie uruchom aplikację. Publikowania danych można uzyskać, pobierając i następnie importowania profilu publikowania w programie Visual Studio:

![Importuj profil publikowania](./media/event-hubs-managed-service-identity/msi3.png)
 
Wysyłać ani odbierać wiadomości, wprowadź nazwę przestrzeni nazw i nazwę obiektu, który został utworzony, a następnie kliknij opcję **wysyłania** lub **otrzymywać**. 
 
Tożsamość zarządzana działa tylko w środowisku platformy Azure, a tylko w przypadku wdrożenie usługi App Service, w której został skonfigurowany. Zarządzanych tożsamości nie działają z miejscami wdrożenia usługi App Service w tym momencie.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Usługa Event Hubs szczegóły cennika](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
