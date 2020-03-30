---
title: Instalowanie lokalnej bramy danych
description: Zanim będzie można uzyskać dostęp do danych lokalnych z usługi Azure Logic Apps, pobierz i zainstaluj lokalną bramę danych
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: 797cd82327d68003d4e5f007d1f16e9534092ac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283994"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalowanie lokalnej bramy danych dla usługi Azure Logic Apps

Zanim będzie można [połączyć się z lokalnymi źródłami danych z usługi Azure Logic Apps,](../logic-apps/logic-apps-gateway-connection.md)pobierz i zainstaluj [lokalną bramę danych na](https://aka.ms/on-premises-data-gateway-installer) komputerze lokalnym. Brama działa jako mostek, który zapewnia szybki transfer danych i szyfrowanie między źródłami danych w środowisku lokalnym a aplikacjami logiki. Tej samej instalacji bramy można używać z innymi usługami w chmurze, takimi jak Usługa Power BI, Power Automate, Power Apps i Usługi Azure Analysis Services. Aby uzyskać informacje dotyczące korzystania z bramy z tymi usługami, zobacz następujące artykuły:

* [Lokalna brama danych usługi Microsoft Power Automatyzuj](/power-automate/gateway-reference)
* [Lokalna brama danych usługi Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Lokalna brama danych usługi Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Lokalna brama danych usług Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

W tym artykule pokazano, jak pobrać, zainstalować i skonfigurować lokalną bramę danych, aby uzyskać dostęp do lokalnych źródeł danych z usługi Azure Logic Apps. Więcej informacji na temat [działania bramy danych można](#gateway-cloud-service) również dowiedzieć się więcej w dalszej części tego tematu. Aby uzyskać więcej informacji na temat bramy, zobacz [Co to jest brama lokalna?](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure z subskrypcją, [zarejestruj się, aby uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

  * Twoje konto platformy Azure musi należeć do jednej [dzierżawy lub katalogu usługi Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology) Do instalowania bramy na komputerze lokalnym i administrowania bramą na komputerze lokalnym należy użyć tego samego konta platformy Azure.

  * Podczas instalacji bramy logujesz się za pomocą konta platformy Azure, które łączy instalację bramy z kontem platformy Azure i tylko z tym kontem. Później w witrynie Azure Portal należy użyć tego samego konta platformy Azure i dzierżawy usługi Azure AD podczas tworzenia zasobu bramy platformy Azure, który rejestruje i żąda instalacji bramy. W usłudze Azure Logic Apps lokalne wyzwalacze i akcje używają zasobu bramy do łączenia się z lokalnymi źródłami danych.

    > [!NOTE]
    > Można połączyć tylko jedną instalację bramy i jeden zasób bramy platformy Azure ze sobą. Nie można połączyć tej samej instalacji bramy z wieloma kontami platformy Azure lub zasobami bramy platformy Azure. Jednak konto platformy Azure można połączyć z wielu instalacji bramy i zasobów bramy platformy Azure. W wyzwalaczu lokalnym lub akcji można wybrać z różnych subskrypcji platformy Azure, a następnie wybrać skojarzony zasób bramy.

  * Musisz zalogować się za pomocą konta służbowego lub konta szkolnego, znanego `username@contoso.com`również jako konto *organizacji,* które wygląda jak . Nie można używać kont B2B (gościa) platformy Azure @hotmail.com @outlook.comani osobistych kont Microsoft, takich jak lub .

    > [!TIP]
    > Jeśli zarejestrowałeś się w usłudze Office 365 i nie podałeś służbowego adresu e-mail, Twój adres może `username@domain.onmicrosoft.com`wyglądać. Twoje konto jest przechowywane w dzierżawie w usłudze Azure Active Directory (Azure AD). W większości przypadków główna nazwa użytkownika (UPN) dla konta usługi Azure AD jest taka sama jak adres e-mail.
    >
    > Aby użyć [subskrypcji programu Visual Studio Standard](https://visualstudio.microsoft.com/vs/pricing/) połączonej z kontem Microsoft, należy najpierw utworzyć [dzierżawę w usłudze Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) lub użyć katalogu domyślnego. Dodaj użytkownika z hasłem do katalogu, a następnie nadaj temu użytkownikowi dostęp do subskrypcji platformy Azure. Następnie można zalogować się podczas instalacji bramy przy użyciu tej nazwy użytkownika i hasła.

* Oto wymagania dotyczące komputera lokalnego:

  **Minimalne wymagania**

  *  .NET Framework 4.7.2
  * 64-bitowa wersja systemu Windows 7 lub Windows Server 2008 R2 (lub nowszego)

  **Zalecane wymagania**

  * 8-rdzeniowy procesor
  * Pamięć 8 GB
  * 64-bitowa wersja systemu Windows Server 2012 R2 lub nowsza
  * Pamięć masowa dysków SSD do buforowania

  > [!NOTE]
  > Brama nie obsługuje systemu Windows Server Core.

* **Powiązane zagadnienia**

  * Zainstaluj lokalną bramę danych tylko na komputerze lokalnym, a nie na kontrolerze domeny. Nie trzeba instalować bramy na tym samym komputerze co źródło danych. Potrzebujesz tylko jednej bramy dla wszystkich źródeł danych, więc nie trzeba instalować bramy dla każdego źródła danych.

    > [!TIP]
    > Aby zminimalizować opóźnienia, można zainstalować bramę jak najbliżej źródła danych lub na tym samym komputerze, przy założeniu, że masz uprawnienia.

  * Zainstaluj bramę na komputerze, który jest w sieci przewodowej, podłączony do Internetu, zawsze włączony i nie przechodzi w tryb uśpienia. W przeciwnym razie brama nie może działać, a wydajność może wystąpić w sieci bezprzewodowej.

  * Jeśli planujesz używać uwierzytelniania systemu Windows, upewnij się, że brama została zainstalowana na komputerze, który jest członkiem tego samego środowiska usługi Active Directory, co źródła danych.

  * Region wybrany dla instalacji bramy jest tą samą lokalizacją, którą należy wybrać podczas późniejszego tworzenia zasobu bramy platformy Azure dla aplikacji logiki. Domyślnie ten region jest tą samą lokalizacją co dzierżawa usługi Azure AD, która zarządza twoim kontem platformy Azure. Można jednak zmienić lokalizację podczas instalacji bramy.

  * Jeśli aktualizujesz instalację bramy do najnowszej wersji, najpierw odinstaluj bieżącą bramę, aby uzyskać czystsze środowisko.

  * Brama ma dwa tryby: tryb standardowy i tryb osobisty, który dotyczy tylko usługi Power BI. Na tym samym komputerze nie można mieć więcej niż jednej bramy uruchomionej w tym samym trybie.

  * Usługa Azure Logic Apps obsługuje operacje odczytu i zapisu za pośrednictwem bramy. Jednak te operacje mają [ograniczenia dotyczące ich wielkości ładunku](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalowanie bramy danych

1. [Pobierz i uruchom instalator bramy na komputerze lokalnym](https://aka.ms/on-premises-data-gateway-installer).

1. Przejrzyj minimalne wymagania, zachowaj domyślną ścieżkę instalacji, zaakceptuj warunki użytkowania, a następnie wybierz pozycję **Zainstaluj**.

   ![Przejrzyj wymagania i zaakceptuj warunki użytkowania](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Po pomyślnym zainstalowaniu bramy podaj adres e-mail konta platformy Azure, a następnie wybierz pozycję **Zaloguj się**, na przykład:

   ![Logowanie się za pomocą konta służbowego](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Instalacja bramy może łączyć się tylko z jednym kontem platformy Azure.

1. Wybierz **pozycję Zarejestruj nową bramę na tym komputerze** > **Dalej**. Ten krok rejestruje instalację bramy za pomocą [usługi w chmurze bramy](#gateway-cloud-service).

   ![Rejestrowanie bramy na komputerze lokalnym](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Podaj te informacje dotyczące instalacji bramy:

   * Nazwa bramy unikatowa w dzierżawie usługi Azure AD
   * Klucz odzyskiwania, który musi mieć co najmniej osiem znaków, którego chcesz użyć
   * Potwierdzenie klucza odzyskiwania

   ![Podaj informacje dotyczące instalacji bramy](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Zapisz i przechowuj klucz odzyskiwania w bezpiecznym miejscu. Ten klucz jest potrzebny, jeśli kiedykolwiek chcesz zmienić lokalizację, przenieść, odzyskać lub przejąć instalację bramy.

   Zanotuj opcję **Dodaj do istniejącego klastra bramy,** którą można wybrać podczas instalowania dodatkowych bram dla [scenariuszy o wysokiej dostępności](#high-availability).

1. Sprawdź region usługi w chmurze bramy i [usługi Azure Service Bus,](https://azure.microsoft.com/services/service-bus/) który jest używany przez instalację bramy. Domyślnie ten region jest tą samą lokalizacją co dzierżawa usługi Azure AD dla twojego konta platformy Azure.

   ![Potwierdź region dla usługi bramy i magistrali usługowej](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Aby zaakceptować domyślny region, wybierz pozycję **Konfiguruj**. Jeśli jednak region domyślny nie jest najbliższym, możesz zmienić region.

   *Dlaczego warto zmienić region instalacji bramy?*

   Na przykład, aby zmniejszyć opóźnienia, można zmienić region bramy do tego samego regionu co aplikacja logiki. Można też wybrać region najbliżej lokalnego źródła danych. *Zasób bramy na platformie Azure* i aplikacji logiki może mieć różne lokalizacje.

   1. Obok bieżącego regionu wybierz pozycję **Zmień region**.

      ![Zmienianie bieżącego regionu bramy](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na następnej stronie otwórz listę **Wybierz region,** wybierz odpowiedni region i wybierz pozycję **Gotowe**.

      ![Wybieranie innego regionu dla usługi bramy](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Przejrzyj informacje w ostatecznym oknie potwierdzenia. W tym przykładzie użyto tego samego konta dla aplikacji logiki, usługi Power BI, Power Apps i Power Automate, dzięki czemu brama jest dostępna dla wszystkich tych usług. Gdy będziesz gotowy, wybierz przycisk **Zamknij**.

   ![Potwierdzanie informacji o bramie danych](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Teraz [utwórz zasób platformy Azure dla instalacji bramy](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Sprawdzanie lub dostosowywanie ustawień komunikacji

Lokalna brama danych zależy od [usługi Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) dla łączności w chmurze i ustanawia odpowiednie połączenia wychodzące do skojarzonego z bramą regionu platformy Azure. Jeśli środowisko pracy wymaga, aby ruch przechodzi przez serwer proxy lub zaporę, aby uzyskać dostęp do Internetu, to ograniczenie może uniemożliwić lokalnej bramy danych łączenia się z usługą w chmurze bramy i usługi Azure Service Bus. Brama ma kilka ustawień komunikacji, które można dostosować. Więcej informacji można znaleźć w następujących tematach:

* [Dostosowywanie ustawień komunikacji dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Konfigurowanie ustawień serwera proxy dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Obsługa wysokiej dostępności

Aby uniknąć pojedynczych punktów awarii lokalnego dostępu do danych, można mieć wiele instalacji bramy (tylko w trybie standardowym) z każdym na innym komputerze i skonfigurować je jako klaster lub grupę. W ten sposób, jeśli brama podstawowa jest niedostępna, żądania danych są kierowane do drugiej bramy i tak dalej. Ponieważ na komputerze można zainstalować tylko jedną bramę standardową, należy zainstalować każdą dodatkową bramę w klastrze na innym komputerze. Wszystkie łączniki, które współpracują z lokalną bramą danych, obsługują wysoką dostępność.

* Musisz już mieć co najmniej jedną instalację bramy z tym samym kontem platformy Azure co brama podstawowa i klucz odzyskiwania dla tej instalacji.

* Brama podstawowa musi być uruchomiona aktualizacja bramy od listopada 2017 r. lub nowszego.

Po skonfigurowaniu bramy podstawowej po zainstalowaniu innej bramy wybierz pozycję **Dodaj do istniejącego klastra bramy**, wybierz bramę podstawową, która jest pierwszą zainstalowaną bramą, i podaj klucz odzyskiwania dla tej bramy. Aby uzyskać więcej informacji, zobacz [Klastry wysokiej dostępności dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Zmienianie lokalizacji, migrowanie, przywracanie lub przejęcie istniejącej bramy

Jeśli musisz zmienić lokalizację bramy, przenieść instalację bramy na nowy komputer, odzyskać uszkodzoną bramę lub przejąć na własność istniejącą bramę, potrzebny jest klucz odzyskiwania dostarczony podczas instalacji bramy.

1. Uruchom instalator bramy na komputerze, na który ma istniejącą bramę. Jeśli nie masz najnowszego instalatora bramy, [pobierz najnowszą wersję bramy](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Przed przywróceniem bramy na komputerze z oryginalną instalacją bramy należy najpierw odinstalować bramę na tym komputerze. Ta akcja rozłącza oryginalną bramę.
   > Jeśli usuniesz lub usuniesz klaster bramy dla dowolnej usługi w chmurze, nie możesz przywrócić tego klastra.

1. Po otwarciu instalatora zaloguj się przy użyciu tego samego konta platformy Azure, które zostało użyte do zainstalowania bramy.

1. Wybierz **opcję Migruj, przywracaj lub przejmij istniejącą bramę** > **Dalej**, na przykład:

   ![Wybierz opcję "Migrowanie, przywracanie lub przejmowanie istniejącej bramy"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Wybierz spośród dostępnych klastrów i bram i wprowadź klucz odzyskiwania dla wybranej bramy, na przykład:

   ![Wybierz bramę i podaj klucz odzyskiwania](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Aby zmienić region, wybierz pozycję **Zmień region**i wybierz nowy region.

1. Gdy będziesz gotowy, wybierz pozycję **Konfiguruj,** aby można było zakończyć zadanie.

## <a name="tenant-level-administration"></a>Administracja na poziomie dzierżawy

Aby uzyskać wgląd we wszystkie lokalne bramy danych w dzierżawie usługi Azure AD, administratorzy globalni w tej dzierżawie mogą zalogować się do [centrum administracyjnego platformy zasilania](https://powerplatform.microsoft.com) jako administrator dzierżawy i wybrać opcję Bramy **danych.** Aby uzyskać więcej informacji, zobacz [Administracja na poziomie dzierżawy dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Ponowne uruchamianie bramy

Domyślnie instalacja bramy na komputerze lokalnym działa jako konto usługi systemu Windows o nazwie "Lokalna usługa bramy danych". Jednak instalacja bramy używa `NT SERVICE\PBIEgwService` nazwy dla poświadczeń konta "Log on As" i ma uprawnienia "Zaloguj się jako usługa".

> [!NOTE]
> Konto usługi systemu Windows różni się od konta używanego do łączenia się z lokalnymi źródłami danych i z konta platformy Azure, którego używasz podczas logowania się do usług w chmurze.

Jak każda inna usługa systemu Windows, można uruchomić i zatrzymać bramę na różne sposoby. Aby uzyskać więcej informacji, zobacz [Ponowne uruchamianie lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Jak działa brama

Użytkownicy w organizacji mogą uzyskiwać dostęp do danych lokalnych, do których mają już autoryzowany dostęp. Jednak zanim ci użytkownicy będą mogli połączyć się z lokalnym źródłem danych, należy zainstalować i skonfigurować lokalną bramę danych. Zazwyczaj administratorem jest osoba, która instaluje i konfiguruje bramę. Te akcje mogą wymagać uprawnień administratora serwera lub specjalnej wiedzy na temat serwerów lokalnych.

Brama ułatwia szybszą i bezpieczniejszą komunikację za kulisami. Ta komunikacja przepływa między użytkownikiem w chmurze, usługi w chmurze bramy i lokalnego źródła danych. Usługa w chmurze bramy szyfruje i przechowuje poświadczenia źródła danych i szczegóły bramy. Usługa kieruje również zapytania i ich wyniki między użytkownikiem, bramą i lokalnym źródłem danych.

Brama współpracuje z zaporami i używa tylko połączeń wychodzących. Cały ruch pochodzi jako bezpieczny ruch wychodzący z agenta bramy. Brama przekazuje dane ze źródeł lokalnych na zaszyfrowanych kanałach za pośrednictwem [usługi Azure Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md) Ta magistrala usług tworzy kanał między bramą a usługą wywołującą, ale nie przechowuje żadnych danych. Wszystkie dane przesyłane przez bramę są szyfrowane.

![Architektura lokalnej bramy danych](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> W zależności od usługi w chmurze może być konieczne skonfigurowanie źródła danych dla bramy.

W tych krokach opisano, co się dzieje podczas interakcji z elementem połączonym z lokalnym źródłem danych:

1. Usługa w chmurze tworzy kwerendę wraz z zaszyfrowanymi poświadczeniami dla źródła danych. Następnie usługa wysyła zapytanie i poświadczenia do kolejki bramy do przetwarzania.

1. Usługa w chmurze bramy analizuje kwerendę i wypycha żądanie do usługi Azure Service Bus.

1. Usługa Azure Service Bus wysyła oczekujące żądania do bramy.

1. Brama pobiera kwerendę, odszyfrowuje poświadczenia i łączy się z jednym lub kilkoma źródłami danych z tymi poświadczeniami.

1. Brama wysyła zapytanie do źródła danych do uruchomienia.

1. Wyniki są wysyłane ze źródła danych z powrotem do bramy, a następnie do usługi w chmurze bramy. Usługa w chmurze bramy następnie używa wyników.

### <a name="authentication-to-on-premises-data-sources"></a>Uwierzytelnianie w lokalnych źródłach danych

Przechowywane poświadczenia są używane do łączenia się z bramy do lokalnych źródeł danych. Niezależnie od użytkownika brama używa przechowywanych poświadczeń do nawiązania połączenia. Mogą istnieć wyjątki uwierzytelniania dla określonych usług, takich jak zapytanie bezpośrednie i usługi LiveConnect dla usług analysis services w usłudze Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Usługi w chmurze firmy Microsoft używają [usługi Azure AD](../active-directory/fundamentals/active-directory-whatis.md) do uwierzytelniania użytkowników. Dzierżawa usługi Azure AD zawiera nazwy użytkowników i grupy zabezpieczeń. Zazwyczaj adres e-mail używany do logowania jest taki sam jak główna nazwa użytkownika (UPN) dla twojego konta.

### <a name="what-is-my-upn"></a>Co to jest moja niania.

Jeśli nie jesteś administratorem domeny, możesz nie znać nazwy UPN. Aby znaleźć numer UPN dla `whoami /upn` swojego konta, uruchom polecenie ze stacji roboczej. Chociaż wynik wygląda jak adres e-mail, wynikiem jest nazwa UPN dla lokalnego konta domeny.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Synchronizowanie lokalnej usługi Active Directory z usługą Azure AD

Lokalna liczba kont usługi Active Directory i konta usługi Azure AD musi być taka sama. Dlatego upewnij się, że każde lokalne konto usługi Active Directory jest zgodne z twoim kontem usługi Azure AD. Usługi w chmurze wiedzą tylko o kontach w usłudze Azure AD. Nie trzeba więc dodawać konta do lokalnej usługi Active Directory. Jeśli konto nie istnieje w usłudze Azure AD, nie można użyć tego konta.

Poniżej przedstawiono sposoby dopasowania lokalnych kont usługi Active Directory do usługi Azure AD.

* Ręczne dodawanie kont do usługi Azure AD.

  Utwórz konto w witrynie Azure portal lub w centrum administracyjnym usługi Microsoft 365. Upewnij się, że nazwa konta jest zgodna z nazwą UPN dla lokalnego konta usługi Active Directory.

* Synchronizuj konta lokalne z dzierżawą usługi Azure AD przy użyciu narzędzia Azure Active Directory Connect.

  Narzędzie Azure AD Connect udostępnia opcje synchronizacji katalogów i konfiguracji uwierzytelniania. Opcje te obejmują synchronizację skrótów haseł, uwierzytelnianie przekazywane i federację. Jeśli nie jesteś administratorem dzierżawy ani administratorem domeny lokalnej, skontaktuj się z administratorem IT, aby skonfigurować usługę Azure AD Connect. Usługa Azure AD Connect zapewnia, że usługa Azure AD UPN jest zgodna z lokalną owszeńką UPN usługi Active Directory. To dopasowywanie pomaga, jeśli używasz połączeń na żywo usług Analysis Services z możliwościami usługi Power BI lub logowania jednokrotnego.This matching helps if you're using Analysis Services live connections with Power BI or single sign-on (SSO) capabilities.

  > [!NOTE]
  > Synchronizowanie kont za pomocą narzędzia Azure AD Connect tworzy nowe konta w dzierżawie usługi Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Często zadawane pytania i rozwiązywanie problemów

Więcej informacji można znaleźć w następujących tematach:

* [Lokalna brama danych — często zadawane pytania](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Rozwiązywanie problemów z lokalną bramą danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorowanie i optymalizowanie wydajności bramy](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Następne kroki

* [Łączenie się z danymi lokalnymi z aplikacji logiki](../logic-apps/logic-apps-gateway-connection.md)
* [Funkcje integracji w przedsiębiorstwie](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md)
