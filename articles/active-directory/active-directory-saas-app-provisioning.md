---
title: Automatyczne inicjowanie obsługi użytkowników aplikacji SaaS w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Wprowadzenie do wykorzystania usługi Azure AD można automatycznie udostępnić, usuwanie i aktualizowane na bieżąco kont użytkowników dla wielu aplikacji SaaS innych firm.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 56af1915326429b37f87dbaa397e1c9b832c675a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatyzowanie użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Co to jest automatyczne Inicjowanie obsługi użytkowników dla aplikacji SaaS?
Azure Active Directory (Azure AD) pozwala na automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w chmurze ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) aplikacji, takich jak Dropbox, Salesforce, ServiceNow i inne.

**Poniżej przedstawiono kilka przykładów co ta funkcja umożliwia:**

* Automatycznie Utwórz nowych kont w systemach prawym dla nowych użytkowników w celu dołączenia zespołu lub organizacji.
* Automatycznie Dezaktywuj kont w systemach prawo w przypadku osób pozostawia zespół lub organizacja.
* Upewnij się, że tożsamości w aplikacji i systemów są aktualizowane na podstawie zmian w katalogu lub system zasobów ludzkich.
* Udostępnianie obiektów nienależących do użytkownika, takich jak grupy, do aplikacji, które je obsługują.

**Inicjowanie obsługi użytkowników automatycznych zawiera również następujące funkcje:**

* Możliwość odpowiada tożsamościom istniejących między systemami źródłowym i docelowym.
* Dostosowywalny atrybut mapowania, które definiują, jakie dane użytkownika powinien przepływać z systemu źródłowego do systemu docelowego.
* Opcjonalne wiadomości e-mail dla alertów do inicjowania obsługi błędów
* Dzienniki raportowania i działania do monitorowania i rozwiązywania problemów.

## <a name="why-use-automated-provisioning"></a>Dlaczego warto używać automatyczne Inicjowanie obsługi?
Niektóre typowe motywacji dla tej funkcji obejmują:

* Unikanie kosztów, wydajność i błędu ludzkiego związanego z procesów ręcznych inicjowania obsługi administracyjnej.
* Unikanie kosztów związanych z udostępniania i utrzymywania opracowany niestandardowe rozwiązania inicjowania obsługi administracyjnej i skryptów
* W celu zabezpieczenia organizacji natychmiastowe usunięcie tożsamości użytkowników z klucza aplikacji SaaS w przypadku opuszczenia organizacji.
* Aby łatwo importować dużej liczby użytkowników do określonej aplikacji SaaS lub systemu.
* Do korzystania z o jeden zestaw zasad, aby określić, który zostanie zainicjowana i który można zalogować się do aplikacji.


## <a name="how-does-automatic-provisioning-work"></a>Jak działa automatyczne udostępnianie?
    
**Usługi Azure AD udostępniania** udostępnia użytkownikom aplikacji SaaS i innymi systemami, łącząc się z punktów końcowych interfejsu API zarządzania użytkownika dostarczone przez dostawcę każdej aplikacji. Zezwalaj na te punkty końcowe interfejsu API zarządzania użytkownika usługi Azure AD, można programowo tworzyć, aktualizować i usuwać użytkowników. Dla wybranych aplikacji, inicjowania obsługi usługi mogą także tworzyć aktualizować i usuwać dodatkowe obiekty dotyczące tożsamości, takie jak grup i ról. 

![Inicjowanie obsługi administracyjnej](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*rysunku 1: usługi Azure AD, świadczenie usługi*

![Udostępnianie wychodzące](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*na rysunku 2: "Wychodzący" użytkownika inicjowania obsługi przepływu pracy z usługą Azure AD popularnych aplikacji SaaS*

![Przychodzące inicjowania obsługi administracyjnej](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*rysunek 3: użytkownik "Przychodzącego" Inicjowanie obsługi przepływu pracy z popularnych aplikacji zarządzania kapitału człowieka (HCM) do usługi Azure Active Directory i usługi Active Directory systemu Windows Server*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jakie aplikacje i systemy można użyć z inicjowaniem obsługi administracyjnej użytkowników usługi Azure AD?

Funkcje platformy Azure AD wstępnie zintegrowane pomocy technicznej w różnych popularnych aplikacji SaaS i systemami kadr, a także ogólne pomocy technicznej dla aplikacji, które implementuje określonych części standard SCIM 2.0.

Aby uzyskać listę wszystkich aplikacji, dla których usługi Azure AD obsługuje wstępnie zintegrowanych łączników inicjowania obsługi administracyjnej, zobacz [lista samouczków aplikacji do inicjowania obsługi użytkowników](active-directory-saas-tutorial-list.md).

Aby uzyskać informacje dotyczące dodawania obsługi usługi Azure AD do aplikacji Inicjowanie obsługi użytkowników, zobacz [przy użyciu SCIM do automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji](active-directory-scim-provisioning.md).

Skontaktuj się z usługą Azure AD engineering team do żądania obsługi inicjowania obsługi administracyjnej dodatkowych aplikacji, przesłać wiadomość za pośrednictwem [forum opinii w usłudze Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).    

> [!NOTE]
> Aby aplikacja do obsługi użytkowników automatycznego inicjowania obsługi administracyjnej on zawierał Zarządzanie użytkownikami niezbędne interfejsów API, które umożliwiają automatyzację tworzenia, obsługi i usuwania użytkowników zewnętrznych programów. W związku z tym nie wszystkie aplikacje SaaS są zgodne z tą funkcją. W przypadku aplikacji, które obsługują zarządzanie użytkownikami interfejsy API zespołu inżynieryjnego usługi Azure AD będą w stanie Tworzenie łącznika inicjowania obsługi administracyjnej do tych aplikacji, a tej pracy jest priorytety zgodnie z potrzebami aktualnych i potencjalnych klientów. 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Jak skonfigurować automatyczne Inicjowanie obsługi administracyjnej dla aplikacji?

Konfiguracja programu Azure AD usługi inicjowania obsługi administracyjnej dla wybranej aplikacji jest uruchamiany w  **[portalu Azure](https://portal.azure.com)**. W **usługi Azure Active Directory > aplikacje przedsiębiorstwa** wybierz opcję **Dodaj**, następnie **wszystkie**, a następnie dodaj jednej z następujących pozycji w zależności od danego scenariusza:

* Wszystkie aplikacje w **polecanych aplikacji** automatyczne udostępnianie obsługi sekcji. Zobacz [lista samouczków aplikacji do inicjowania obsługi użytkowników] active-directory-saas — samouczek — list.md) dla dodatkowych reguł.

* Użyj opcji "bez galerii aplikacji" opracowany niestandardowej integracji SCIM

![Galeria](./media/active-directory-saas-app-provisioning/gallery.png)

Na ekranie aplikacji zarządzania inicjowania obsługi administracyjnej jest skonfigurowany w **inicjowania obsługi administracyjnej** kartę.

![Ustawienia](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **Poświadczenia administratora** należy podać do usługi Azure AD, inicjowania obsługi usługi, które umożliwiają podłączenie go do zarządzania użytkownikami udostępniany przez aplikację interfejsu API. Ta sekcja umożliwia również włączyć powiadomienia e-mail, jeśli poświadczenia się nie powieść lub zadanie inicjowania obsługi administracyjnej przechodzi w stan [kwarantanny](#quarantine).

* **Mapowania atrybutów** można skonfigurować które określają, które pola w systemie źródłowym (przykład: usługi Azure AD) będzie ich zawartość zsynchronizowano do pola, które w systemie docelowym (przykład: ServiceNow). Jeśli aplikacja docelowa obsługuje tę funkcję, w tej sekcji umożliwi opcjonalnie skonfigurować inicjowania obsługi grup oprócz kont użytkowników. "Zgodnych właściwości" umożliwiają wybierz pola, które są używane do dopasowania kont między systemami. "[Wyrażenia](active-directory-saas-writing-expressions-for-attribute-mappings.md)" umożliwiają modyfikowanie i przekształcanie wartości pobrać z systemu źródłowego zapisywane do systemu docelowego. Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowań atrybutów](active-directory-saas-customizing-attribute-mappings.md).

![Ustawienia](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **Filtrami zakresów** Poinformuj użytkowników, którzy inicjowania obsługi usługi i grupy w systemie źródłowym powinny być udostępniane i/lub cofnąć jej zainicjowania jej do systemu docelowego. Istnieją dwa aspekty zakresu filtry, które są oceniane razem określające, który znajduje się w zakresie do inicjowania obsługi:

    * **Filtr w wartości atrybutu** -menu "Zakres obiektu źródłowego" w mapowaniu atrybutu umożliwia filtrowanie na określone wartości danego atrybutu. Na przykład można określić, że tylko użytkownicy z atrybutem "Dział", "Sprzedaży" musi należeć do zakresu do inicjowania obsługi. Aby uzyskać więcej informacji, zobacz [za pomocą filtrów zakresu](active-directory-saas-scoping-filters.md).

    * **Filtr przydziałów** — w obsługi menu 'Scope' > sekcji Ustawienia portalu umożliwia określenie, czy tylko "przypisanych" użytkowników i grup powinny być w zakresie udostępniania lub jeśli wszyscy użytkownicy w katalogu usługi Azure AD powinna być Zainicjowano obsługę administracyjną. Aby uzyskać informacje na "przypisywanie" użytkowników i grup, zobacz [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* **Ustawienia** sterowania działaniem usługi inicjowania obsługi administracyjnej dla aplikacji, w tym, czy jest obecnie uruchomiona lub nie.

* **Dzienniki inspekcji** Podaj rekordów każda operacja wykonywana przez usługę Azure AD, inicjowania obsługi usługi. Aby uzyskać więcej informacji, zobacz [inicjowania obsługi administracyjnej Przewodnik po raportowaniu](active-directory-saas-provisioning-reporting.md).

![Ustawienia](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> Użytkownik usługi Azure AD, świadczenie usługi można również skonfigurować i zarządzane przy użyciu [interfejsu API programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Co się dzieje podczas inicjowania obsługi?

W przypadku usługi Azure AD jest systemem źródłowym, inicjowania obsługi usługi korzysta z [różnicowej zapytania funkcji interfejsu API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) do monitorowania użytkowników i grup. Inicjowania obsługi administracyjnej jest uruchamiana usługa synchronizacji początkowej z systemu źródłowego i docelowego systemu, a następnie okresowo synchronizacje przyrostowe. 

### <a name="initial-sync"></a>Początkowej synchronizacji
Podczas inicjowania obsługi administracyjnej usługa jest uruchomiona, pierwsza synchronizacja nigdy wykonywane następujące czynności:

1. Zapytanie wszyscy użytkownicy i grupy z systemu źródłowego pobierania wszystkie atrybuty zdefiniowane w [mapowania atrybutów](active-directory-saas-customizing-attribute-mappings.md).
2. Filtrowanie użytkowników i grup zwrócone, przy użyciu dowolnego skonfigurowane [przypisania](active-directory-coreapps-assign-user-azure-portal.md) lub [opartych na atrybutach filtrami zakresów](active-directory-saas-scoping-filters.md).
3. Jeśli użytkownik zostanie znaleziony do przypisania lub w zakresie udostępniania, usługa wysyła kwerendę do systemu docelowego zgodnego użytkownika przy użyciu wyznaczone [dopasowania atrybutów](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties). Przykład: Jeśli nazwa userPrincipal w systemie źródłowym jest atrybut zgodnego i mapowany na nazwę użytkownika w systemie docelowym, a następnie inicjowania obsługi usługi kwerendy system docelowy nazw użytkowników, która pasuje do wartości nazwy userPrincipal w systemie źródłowym.
4. Jeśli nie odnaleziono zgodnego użytkownika w systemie docelowym, zostanie utworzony przy użyciu atrybutów zwrócony z systemu źródłowego.
5. Jeśli pasujący użytkownik zostanie znaleziony, jest aktualizowany przy użyciu atrybutów obsługiwanych przez system źródła.
6. Jeśli mapowanie atrybutów zawierają atrybuty "reference", usługa wykonuje dodatkowe aktualizacje do tworzenia i łączenia przywoływanych obiektów w systemie docelowym. Na przykład użytkownik może mieć atrybut "Manager" w systemie docelowym, który jest połączony inny użytkownik utworzony w systemie docelowym.
7. Utrwalanie znak wodny końca synchronizacji początkowej, co zapewnia punkt początkowy dla kolejne synchronizacje przyrostowe.

Niektóre aplikacje, takie jak usługi ServiceNow, Google Apps lub okno obsługi nie tylko inicjowania obsługi użytkowników, ale również inicjowania obsługi grup i ich elementy członkowskie. W takich przypadkach grupy inicjowania obsługi jest włączony w [mapowania](active-directory-saas-customizing-attribute-mappings.md), świadczenie usługi synchronizuje użytkowników i grup, a następnie synchronizuje członkostwa w grupach. 

### <a name="incremental-syncs"></a>Synchronizacje przyrostowe
Po początkowej synchronizacji wszystkie kolejne synchronizacje będą:

1. Zapytanie systemie źródłowym żadnych użytkowników i grup, które zostały zaktualizowane od czasu ostatniego znaku wodnego była przechowywana.
2. Filtrowanie użytkowników i grup zwrócone, przy użyciu dowolnego skonfigurowane [przypisania](active-directory-coreapps-assign-user-azure-portal.md) lub [opartych na atrybutach filtrami zakresów](active-directory-saas-scoping-filters.md).
3. Jeśli użytkownik zostanie znaleziony do przypisania lub w zakresie udostępniania, usługa wysyła kwerendę do systemu docelowego zgodnego użytkownika przy użyciu wyznaczone [dopasowania atrybutów](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties).
4. Jeśli nie odnaleziono zgodnego użytkownika w systemie docelowym, zostanie utworzony przy użyciu atrybutów zwrócony z systemu źródłowego.
5. Jeśli pasujący użytkownik zostanie znaleziony, jest aktualizowany przy użyciu atrybutów obsługiwanych przez system źródła.
6. Jeśli mapowanie atrybutów zawierają atrybuty "reference", usługa wykonuje dodatkowe aktualizacje do tworzenia i łączenia przywoływanych obiektów w systemie docelowym. Na przykład użytkownik może mieć atrybut "Manager" w systemie docelowym, który jest połączony inny użytkownik utworzony w systemie docelowym.
7. Jeśli użytkownik, który został wcześniej w zakresie obsługi zostanie usunięty z zakresu (w tym trwa nieprzypisane), usługa wyłącza użytkownika w systemie docelowym za pośrednictwem aktualizacji.
8. Jeśli użytkownik, który został wcześniej w zakresie obsługi zostało wyłączone lub usunięte nietrwale w systemie źródłowym, usługa wyłącza użytkownika w systemie docelowym za pośrednictwem aktualizacji.
9. Jeśli użytkownik, który został wcześniej w zakresie obsługi jest całkowicie usunięty w systemie źródłowym, usługa Usuwa użytkownika w systemie docelowym. W usłudze Azure AD użytkownicy są całkowicie usunięty 30 dni po ich usunięcie nietrwałe.
10. Utrwalić nowy znak wodny końca synchronizacji przyrostowej, co zapewnia punkt początkowy dla kolejne synchronizacje przyrostowe.

>[!NOTE]
> Można opcjonalnie wyłączyć utworzenia, aktualizacji lub usuwania za pomocą **akcji obiektów docelowych** pola wyboru w [mapowań atrybutów](active-directory-saas-customizing-attribute-mappings.md) sekcji. Logika wyłączenia użytkownika podczas aktualizacji również jest kontrolowany przez mapowanie atrybutu z pola, takie jak "accountEnabled".

Usługa dostarczania będzie kontynuował działanie symetryczna synchronizacje przyrostowe przez czas nieokreślony, w odstępach czasu zdefiniowanych w [samouczek specyficzne dla poszczególnych aplikacji](active-directory-saas-tutorial-list.md), dopóki nie wystąpi jedno z następujących zdarzeń:

* Usługa jest zatrzymana ręcznie, za pomocą portalu Azure lub odpowiednie polecenie interfejsu API programu Graph 
* Nowe synchronizacji początkowej zostanie wywołany za pomocą **Wyczyść stan i uruchom ponownie** opcji w portalu Azure lub za pomocą odpowiedniego polecenia interfejsu API programu Graph. Czyści wszystkie przechowywane znaku wodnego i powoduje, że wszystkie obiekty źródłowy ma zostać obliczone ponownie.
* Nowe synchronizacji początkowej zostanie wywołany z powodu zmiany w mapowań atrybutów lub filtry zakresu. To także czyści wszystkie przechowywane znaku wodnego i powoduje, że wszystkie obiekty źródłowy ma zostać obliczone ponownie.
* Proces udostępniania przechodzi w kwarantannie (patrz poniżej) z powodu wysoki współczynnik błędów i pozostaje w kwarantannie przez więcej niż cztery tygodnie. W takim przypadku usługa zostanie automatycznie wyłączone.

### <a name="errors-and-retries"></a>Błędy i ponownych prób 
Jeśli użytkownik nie dodano, zaktualizowane lub usunięte w systemie docelowym z powodu błędu w systemie docelowym, operacja zostanie ponowiona w następnym cyklu synchronizacji. Jeśli użytkownik zakończy się niepowodzeniem, liczby ponownych prób zostanie rozpoczęta występują rzadziej, stopniowo skalowanie do tylko jednej próby na dzień. Aby rozwiązać, należy sprawdzić Administratorzy [dzienniki inspekcji](active-directory-saas-provisioning-reporting.md) dla "depozytu procesu" zdarzenia, aby określić katalog główny spowodować i podejmij odpowiednie działanie. Typowe błędy mogą obejmować:

* Użytkownicy nie mają atrybutu w systemie źródłowym, który jest wymagany w systemie docelowym
* Użytkownicy mający wartość atrybutu w systemie źródłowym, dla którego w systemie docelowym jest ograniczenia unique i taką samą wartość znajduje się w innym rekordzie użytkownika

Te błędy można rozwiązać przez dostosowanie wartości atrybutów dla danego użytkownika w systemie źródłowym lub przez dopasowanie mapowań atrybutów nie powoduje konfliktów.   

### <a name="quarantine"></a>Kwarantanny
Większość lub wszystkie wywołania wykonane dla docelowego systemu stale się niepowodzeniem z powodu błędu (na przykład w przypadku poświadczeń administratora nieprawidłowy), a następnie zadanie inicjowania obsługi administracyjnej przechodzi w stan "kwarantanny". To jest wskazane [inicjowania obsługi administracyjnej raport z podsumowaniem](active-directory-saas-provisioning-reporting.md)oraz za pośrednictwem poczty e-mail, jeśli powiadomienia e-mail zostały skonfigurowane w portalu Azure. 

W przypadku kwarantanny, częstotliwość synchronizacje przyrostowe stopniowo ograniczono raz dziennie. 

Zadanie inicjowania obsługi administracyjnej zostanie usunięty z kwarantanny po dokonaniu wszystkich błędów ataku jest ustalany i rozpoczyna się w następnym cyklu synchronizacji. Jeśli zadanie inicjowania obsługi administracyjnej pozostaje w kwarantannie przez więcej niż cztery tygodnie, zadanie inicjowania obsługi administracyjnej jest wyłączone.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Jak długo trwa udostępnianie moich użytkowników?**

Wydajność może się różnić w zależności od tego, czy zadanie inicjowania obsługi administracyjnej wykonuje początkowej synchronizacji lub synchronizacji przyrostowej.

W początkowej synchronizacje czas potrzebny na zakończenie będzie bezpośrednio zależne od liczby użytkowników, grup i członków grupy są obecne w systemie źródłowym. Systemów źródłowych bardzo małych setki obiekty mogą przeprowadzać synchronizacje początkową w ciągu kilku minut. Jednak systemów źródłowych z setkami tysięcy lub miliony połączonych obiektów będzie trwać dłużej.

Dla synchronizacje przyrostowe czas, jaki zajmuje zależy wykryto w tym cyklu synchronizacji zmian numerów. Jeśli ma mniej niż 5000 użytkownika lub wykryto zmiany członkostwa grupy, te często można synchronizować w cyklu 40 minut. 

Należy pamiętać, że ogólną wydajność jest zależna od systemów źródłowych i docelowych. Niektóre systemy docelowe implementuje limity szybkości żądania i ograniczania przepustowości, że można wpływu na wydajność podczas operacji synchronizacji dużej i wbudowanych usługi Azure AD, inicjowania obsługi administracyjnej łączników w tych systemach to uwzględniać.

Wydajność jest również wolniej, jeśli istnieje wiele błędów (zarejestrowane w [dzienniki inspekcji](active-directory-saas-provisioning-reporting.md)) i inicjowania obsługi usługi stała się w stanie "kwarantanny".

**Jak ulepszyć wydajność synchronizacji**

Większości problemów z wydajnością wystąpić podczas początkowej synchronizacje systemów, które ma dużą liczbę grup i elementów członkowskich grupy.

Jeśli synchronizacja grup lub członkostwa w grupach nie jest wymagana, synchronizacji można znacznie poprawić wydajność przez:

1. Ustawienie **inicjowania obsługi administracyjnej > Ustawienia > zakres** menu **synchronizowanie wszystkich**, zamiast synchronizowania przypisanych użytkowników i grup.
2. Użyj [filtrami zakresów](active-directory-saas-scoping-filters.md) zamiast przypisania, aby filtrować listę użytkowników udostępniane.

> [!NOTE]
> Dla aplikacji, które obsługuje inicjowania obsługi grupy nazwy i właściwości grupy (takie jak usługi ServiceNow i usługi Google Apps) również wyłączenie to skraca czas potrzebny na potrzeby początkowej synchronizacji zakończyć. Jeśli nie chcesz udostępniać nazwy grupy i członkostwa w grupach aplikacji, można wyłączyć tę opcję w [mapowania atrybutów](active-directory-saas-customizing-attribute-mappings.md) konfiguracji inicjowania obsługi administracyjnej.

**Jak można śledzić postęp bieżącego zadania inicjowania obsługi administracyjnej**

Zobacz [inicjowania obsługi administracyjnej Przewodnik po raportowaniu](active-directory-saas-provisioning-reporting.md).

**Jak wiedzą, jeśli użytkownicy nie mogą pobrać udostępniane poprawnie?**

Wszystkie błędy są rejestrowane w usłudze Azure AD dzienniki inspekcji. Aby uzyskać więcej informacji, zobacz [inicjowania obsługi administracyjnej Przewodnik po raportowaniu](active-directory-saas-provisioning-reporting.md).

**Jak można utworzyć aplikację, która współdziała z usługą udostępniania?**

Zobacz [przy użyciu SCIM do automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

**Jak przesłać opinię do zespołu inżynieryjnego**

Skontaktuj się z nami za pośrednictwem [forum opinii w usłudze Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Pokrewne artykuły:
* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS](active-directory-saas-tutorial-list.md)
* [Dostosowywanie mapowań atrybutów do inicjowania obsługi użytkowników](active-directory-saas-customizing-attribute-mappings.md)
* [Tworzenie wyrażeń na potrzeby mapowań atrybutów](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtry zakresu dla Inicjowanie obsługi użytkowników](active-directory-saas-scoping-filters.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](active-directory-scim-provisioning.md)
* [Omówienie synchronizacji interfejsu API usługi Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

