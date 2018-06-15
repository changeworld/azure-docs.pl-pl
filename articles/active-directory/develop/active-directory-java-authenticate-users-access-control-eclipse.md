---
title: Jak używać kontroli dostępu (Java)
description: Dowiedz się, jak opracowanie i kontroli dostępu za pomocą języka Java na platformie Azure.
services: active-directory
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: 247dfd59-0221-4193-97ec-4f3ebe01d3c7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: 67a185f4478ab343f93b055a3c54e44ad0bd6889
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158476"
---
# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Jak uwierzytelniać użytkowników sieci Web za pomocą usługi kontroli dostępu na platformie Azure przy użyciu programu Eclipse
W tym przewodniku opisano sposób korzystania z usługi kontroli dostępu (ACS) platformy Azure, w ramach zestawu narzędzi platformy Azure dla programu Eclipse. Aby uzyskać więcej informacji dotyczących usług ACS, zobacz [następne kroki](#next_steps) sekcji.

> [!NOTE]
> Filtr kontroli usługi dostępu do usługi Azure jest podgląd technologii społeczności. Jako wydaniu wstępnym oprogramowania go nie jest oficjalnie obsługiwana przez firmę Microsoft.
> 
> 

## <a name="what-is-acs"></a>Co to jest usługa ACS?
Większość deweloperów nie są ekspertów tożsamości i zazwyczaj nie powinny być poświęcić czas tworzenie mechanizmów uwierzytelniania i autoryzacji dla usług i aplikacji. ACS jest usługą platformy Azure, która zapewnia łatwy sposób uwierzytelniania użytkowników, którzy chcą korzystać z usług i aplikacji sieci web bez konieczności współczynnik uwierzytelniania złożonego logiki do kodu.

Następujące funkcje są dostępne w usłudze:

* Integracja z systemem Windows Identity Foundation (WIF).
* Obsługa dostawców tożsamości popularnych sieci web (IP) w tym identyfikator Windows Live ID, Google, Facebook i Yahoo!.
* Obsługa usługi Active Directory Federation Services (AD FS) 2.0.
* Open Data Protocol (OData) — na podstawie usługi zarządzania, która zapewnia dostęp programistyczny do ustawień usługi ACS.
* Portal zarządzania, który umożliwia dostęp administracyjny do ustawienia usługi ACS.

Aby uzyskać więcej informacji na temat usług ACS, zobacz [2.0 usługi kontroli dostępu][Access Control Service 2.0].

## <a name="concepts"></a>Pojęcia
Azure ACS jest oparty na podmiotów zabezpieczeń na podstawie oświadczeń tożsamości - jednolity sposób tworzenia mechanizmów uwierzytelniania dla aplikacji uruchomionych lokalnie lub w chmurze. Tożsamość oparta na oświadczeniach umożliwia wspólne dla aplikacji i usług można uzyskać tożsamości potrzebne informacje o użytkownikach w organizacji z innych organizacji i w Internecie.

Aby wykonać zadania w tym przewodniku, należy zrozumieć następujące kwestie:

**Klient** — w kontekście ten przewodnik jest przeglądarka, który próbuje uzyskać dostęp do aplikacji sieci web.

**Jednostki uzależnionej (RP) aplikacji** -RP aplikacji jest witryny sieci Web lub usługi, która outsources uwierzytelniania do jednego zewnętrznego urzędu certyfikacji. W żargonu tożsamości mówi się, że RP ufa tego urzędu. W tym przewodniku wyjaśniono, jak skonfigurować aplikację do zaufania usług ACS.

**Token** -tokenu to kolekcja danych zabezpieczeń, które jest zwykle wydawane po pomyślnym uwierzytelnieniu użytkownik. Zawiera zestaw *oświadczeń*, atrybuty uwierzytelnionego użytkownika. Oświadczenie może reprezentować nazwę użytkownika, identyfikator dla roli użytkownika, do której należy wiek użytkownika i tak dalej. Token jest zwykle podpisane cyfrowo, co oznacza jego można zawsze ustalić źródło do jego wystawcę i jego zawartość nie dało się zmodyfikować. Użytkownik uzyskuje dostęp do aplikacji planu odzyskiwania z uwzględnieniem prawidłowy token wystawiony przez urząd certyfikacji, któremu ufa aplikacji planu odzyskiwania.

**Dostawca tożsamości (IP)** -IP jest urząd, który służy do uwierzytelniania tożsamości użytkownika i wystawia tokeny zabezpieczające. Faktyczną pracę wystawianie tokenów jest zaimplementowana, chociaż specjalne usługi o nazwie zabezpieczeń usługi tokenów (STS). Typowe przykłady adresy IP, które obejmują Identyfikatora Windows Live, Facebook, business repozytoria użytkownika (na przykład usługi Active Directory) i tak dalej.
Gdy jest skonfigurowana do zaufania adresu IP, system będzie zaakceptować i sprawdzanie poprawności tokenów wystawionych przez tego adresu IP. Usługi ACS może ufać wielu adresów IP, co oznacza, gdy aplikacja ufa ACS, można natychmiast oferowanie aplikacji dla wszystkich uwierzytelnionych użytkowników z wszystkich adresów IP że ufa ACS w Twoim imieniu.

**Dostawcy federacyjnego (FP)** -adresów IP bezpośredniego znają użytkowników i ich przy użyciu swoich poświadczeń uwierzytelniania i wystawiać oświadczenia dotyczące wiedzieli o nich. Dostawcy federacyjnego (FP) jest inny rodzaj urzędu: zamiast bezpośrednio uwierzytelniania użytkowników, działa jako pośrednik i brokerów uwierzytelnianie między jednego planu odzyskiwania i jeden lub więcej adresów IP. Zarówno adresy IP i klatek na sekundę wystawiać tokeny zabezpieczające, dlatego za pomocą tokenu usługi zabezpieczenia (STS). Usługi ACS jest jedną FP.

**Aparat reguł ACS** -logiki służący do przekształcania przychodzące tokeny z zaufanych adresów IP ma być używane przez RP tokeny są oznaczone w postaci reguły przekształcania oświadczeń proste. Usługi ACS funkcjami aparat reguł, który zajmuje się stosowanie dowolną logikę przekształcania określony dla Twojego planu odzyskiwania.

**Namespace ACS** — przestrzeń nazw jest partycją najwyższego poziomu usług ACS, używanej do organizowania ustawień. Przestrzeń nazw zawiera listę adresów IP ufasz, aplikacje planu odzyskiwania, które mają służyć, aparat przetwarzania przychodzących tokenów z reguły spodziewać się reguły i tak dalej. Przestrzeń nazw przedstawia różne punkty końcowe, które będą używane przez projektanta i aplikacji można uzyskać ACS do realizacji funkcji.

Na poniższej ilustracji przedstawiono, jak działa ACS uwierzytelniania z aplikacji sieci web:

![Diagram przepływu ACS][acs_flow]

1. (W tym przypadku przeglądarce) zażąda strony z planu odzyskiwania.
2. Ponieważ żądanie nie jest jeszcze uwierzytelniony, RP przekierowuje użytkownika do urzędu certyfikacji, który jest zaufany, który jest ACS. ACS będzie zawierał użytkownika z opcją adresów IP, które zostały określone dla tego planu odzyskiwania. Gdy użytkownik wybierze odpowiednie adres IP.
3. Klient przechodzi do strony uwierzytelniania IP i monituje użytkownika o logowania.
4. Po uwierzytelnieniu klienta (np. tożsamość wprowadzona poświadczeń) adres IP wystawia token zabezpieczający.
5. Po wystawieniu tokenu zabezpieczającego, adres IP przekierowuje klienta do usługi kontroli dostępu, a klient wysyła tokenu zabezpieczającego wydanego przez adres IP usług ACS.
6. Usługi ACS sprawdza poprawność tokenu zabezpieczającego wydanego przez adres IP, dane wejściowe tożsamość oświadczeń w tym tokenie do aparatu reguł ACS, oblicza oświadczeń tożsamości danych wyjściowych i generuje nowy token zabezpieczający, który zawiera te dane wyjściowe oświadczenia.
7. Usługi ACS przekierowuje klienta do planu odzyskiwania. Klient wysyła nowe tokenu zabezpieczającego wydane przez usługi ACS do planu odzyskiwania. RP weryfikuje podpis tokenu zabezpieczającego wydane przez usługi ACS, sprawdza poprawność oświadczeń z tym tokenem i zwraca stronę pierwotnie żądany.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać zadania w tym przewodniku, potrzebne następujące elementy:

* Java Developer Kit (JDK), wersji 1.6 lub nowszej.
* Zaćmienie-IDE for Java EE Developers indygo lub nowszej. Ten można pobrać z <http://www.eclipse.org/downloads/>. 
* Dystrybucja serwera sieci web opartych na języku Java lub serwera aplikacji, takich jak Apache Tomcat, GlassFish, serwer aplikacji JBoss lub Jetty.
* subskrypcję platformy Azure, która może zostać pobrany z <http://www.microsoft.com/windowsazure/offers/>.
* Zestaw narzędzi platformy Azure dla programu Eclipse, kwietnia 2014 o wersji lub nowszym. Aby uzyskać więcej informacji, zobacz [instalowaniu zestawu narzędzi platformy Azure dla programu Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Certyfikat X.509 do korzystania z aplikacji. Należy ten certyfikat w publicznego certyfikatu (.cer) i wymiana informacji osobistych (. Format PFX). (Opcje tworzenia ten certyfikat zostanie opisane w dalszej części tego samouczka).
* Znajomość Azure obliczeniowe emulatora i wdrażania techniki omówione w [tworzenia aplikacji Hello World na platformie Azure w programie Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Utwórz Namespace ACS
Aby rozpocząć korzystanie z usługi kontroli dostępu (ACS) na platformie Azure, należy utworzyć przestrzeń nazw usług ACS. Przestrzeń nazw zawiera unikatowy zakres na potrzeby adresowania zasobów usługi ACS od w aplikacji.

1. Zaloguj się do [portalu zarządzania Azure][Azure Management Portal].
2. Kliknij przycisk **usługi Active Directory**. 
3. Aby utworzyć nowy obszar nazw kontroli dostępu, kliknij przycisk **nowy**, kliknij przycisk **usługi aplikacji**, kliknij przycisk **kontroli dostępu**, a następnie kliknij przycisk **szybkie tworzenie**. 
4. Wprowadź nazwę dla przestrzeni nazw. Azure sprawdza, czy nazwa jest unikatowa.
5. Wybierz region, w którym jest używana przestrzeń nazw. Aby uzyskać najlepszą wydajność należy użyć obszaru, w której wdrażana jest aplikacja.
6. Jeśli masz więcej niż jedną subskrypcję, wybierz subskrypcję, która ma być używany dla przestrzeni nazw usługi ACS.
7. Kliknij przycisk **Utwórz**.

Azure tworzy i włącza przestrzeni nazw. Poczekaj, aż stan w nowej przestrzeni nazw jest **Active** przed kontynuowaniem. 

## <a name="add-identity-providers"></a>Dodaj dostawców tożsamości
To zadanie służy do dodawania adresów IP do użycia z aplikacją RP dla uwierzytelniania. Dla celów demonstracyjnych to zadanie przedstawiono sposób dodawania usługi Windows Live jako adresu IP, ale będzie można użyć dowolnego z adresy IP wyświetlane w portalu zarządzania usługi ACS.

1. W [portalu zarządzania Azure][Azure Management Portal], kliknij przycisk **usługi Active Directory**wybierz przestrzeń nazw kontroli dostępu, a następnie kliknij przycisk **Zarządzaj**. Zostanie otwarty w portalu zarządzania usługi ACS.
2. W okienku nawigacji po lewej stronie portalu zarządzania usługi ACS, kliknij przycisk **dostawców tożsamości**.
3. Identyfikator Windows Live ID jest domyślnie włączona i nie można go usunąć. Do celów tego samouczka tylko identyfikator Windows Live ID jest używany. Ten ekran jest jednak, którym można dodać inne adresy IP, klikając **Dodaj** przycisku.

Identyfikator Windows Live ID jest teraz włączony jako adresu IP dla przestrzeni nazw usługi ACS. Następnie należy określić aplikację sieci web Java (ma zostać utworzony później) jako planu odzyskiwania.

## <a name="add-a-relying-party-application"></a>Dodawanie jednostki uzależnionej strony aplikacji
To zadanie służy do konfigurowania usług ACS do rozpoznawania aplikacji sieci web w języku Java jako prawidłową aplikację planu odzyskiwania.

1. W portalu zarządzania usługi ACS, kliknij przycisk **aplikacje firm jednostki uzależnionej**.
2. Na **jednostki uzależnionej aplikacje firm** kliknij przycisk **Dodaj**.
3. Na **Dodaj aplikację jednostki uzależnionej strony** strony, wykonaj następujące czynności:
   
   1. W **nazwa**, wpisz nazwę planu odzyskiwania. Do celów tego samouczka, wpisz **aplikacji sieci Web Azure**.
   2. W **tryb**, wybierz pozycję **wprowadź ustawienia ręcznie**.
   3. W **obszaru**, wpisz identyfikator URI, do którego jest stosowana tokenu zabezpieczającego wydane przez usługi ACS. Dla tego zadania, wpisz **http://localhost:8080/**.
      ![Jednostki uzależnionej obszar strony do użycia w emulatorze obliczeń][relying_party_realm_emulator]
   4. W **zwrotny adres URL** wpisz adres URL, do którego ACS zwraca token zabezpieczający. Dla tego zadania, wpisz ** http://localhost:8080/MyACSHelloWorld/index.jsp ** 
       ![jednostki uzależnionej strony zwrotnego adresu URL do użycia w emulatorze obliczeń][relying_party_return_url_emulator]
   5. Zaakceptuj wartości domyślne w pozostałych polach.
4. Kliknij pozycję **Zapisz**.

Teraz pomyślnie skonfigurowano aplikację sieci web Java uruchamianych w emulatorze obliczeń platformy Azure (w http://localhost:8080/) być RP w przestrzeni nazw usługi ACS. Następnie należy utworzyć zasady, które ACS używa do przetwarzania oświadczenia dla planu odzyskiwania.

## <a name="create-rules"></a>Tworzenie reguł
To zadanie służy do definiowania reguł, które określają sposób oświadczenia są przekazywane z adresów IP do Twojego planu odzyskiwania. Na potrzeby tego przewodnika możemy skonfiguruje po prostu ACS do skopiowania typy oświadczeń przychodzących i wartości bezpośrednio w tokenie dane wyjściowe bez filtrowania i modyfikuje je.

1. Na stronie głównej portalu zarządzania usługi ACS, kliknij przycisk **reguły grupy**.
2. Na **grup reguł** kliknij przycisk **domyślne grupy reguł dla aplikacji sieci Web Azure**.
3. Na **Edytuj grupę reguł** kliknij przycisk **Generuj**.
4. Na **generowania reguł: domyślne grupy reguł dla aplikacji sieci Web Azure** strony, upewnij się, identyfikator Windows Live ID jest zaznaczony, a następnie kliknij przycisk **Generuj**.    
5. Na **Edytuj grupę reguł** kliknij przycisk **zapisać**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Przekaż certyfikat do obszaru nazw usług ACS
W tym zadaniu przekazywania. Certyfikat PFX, który będzie używany do podpisywania żądań tokenów utworzonych przez usługi ACS przestrzeni nazw.

1. Na stronie głównej portalu zarządzania usługi ACS, kliknij przycisk **certyfikaty i klucze**.
2. Na **certyfikaty i klucze** kliknij przycisk **Dodaj** powyżej **podpisywania tokenu**.
3. Na **certyfikat podpisywania tokenu dodać lub klucza** strony:
   1. W **używany do** , kliknij przycisk **aplikację jednostki uzależnionej** i wybierz **aplikacji sieci Web Azure** (który wcześniej ustawiony jako nazwa jednostki uzależnionej strony aplikacji).
   2. W **typu** zaznacz **certyfikatu X.509**.
   3. W **certyfikatu** sekcji, kliknij przycisk Przeglądaj i przejdź do pliku certyfikatu X.509, którego chcesz używać. Są to. Plik PFX. Wybierz plik, kliknij przycisk **Otwórz**, a następnie wprowadź hasło certyfikatu w **hasło** pola tekstowego. Pamiętaj, że do celów testowych, mogą używać niezależne-signed certyfikatu. Aby utworzyć certyfikat z podpisem własnym, użyj **nowy** przycisk **Biblioteka filtru ACS** okna dialogowego (opisana dalej), lub użyj **encutil.exe** narzędzie z [projektu witryny sieci Web] [ project website] z Azure Starter Kit for Java.
   4. Upewnij się, że **jako główny** jest zaznaczony. Twoje **certyfikat podpisywania tokenu dodać lub klucza** strony powinien być podobny do następującego.
       ![Dodaj certyfikat podpisywania tokenu][add_token_signing_cert]
   5. Kliknij przycisk **zapisać** Aby zapisać ustawienia i zamknąć **certyfikat podpisywania tokenu dodać lub klucza** strony.

Następnie zapoznaj się z informacjami na stronie integracji aplikacji i skopiuj identyfikator URI, który należy skonfigurować aplikację sieci web Java do użycia usług ACS.

## <a name="review-the-application-integration-page"></a>Przejrzyj stronę integracji aplikacji
Można znaleźć wszystkie informacje i kod niezbędnych do skonfigurowania aplikacji sieci web Java (RP aplikacji) do pracy z usług ACS na stronie integracji aplikacji w portalu zarządzania usługi ACS. Informacje te będą potrzebne podczas konfigurowania aplikacji sieci web Java uwierzytelniania federacyjnego.

1. W portalu zarządzania usługi ACS, kliknij przycisk **integracji aplikacji**.  
2. W **integracji aplikacji** kliknij przycisk **strony logowania**.
3. W **integracji strony logowania** kliknij przycisk **aplikacji sieci Web Azure**.

W **integracji strony logowania: aplikacji sieci Web Azure** strony, adres URL na liście **opcja 1: Link do strony logowania usługi hostowanej ACS** będzie używany w aplikacji sieci web w języku Java. Tę wartość, należy podczas dodawania biblioteki filtr usługi kontroli dostępu platformy Azure do aplikacji w języku Java.

## <a name="create-a-java-web-application"></a>Tworzenie aplikacji sieci web w języku Java
1. W środowisku Eclipse w menu kliknij **pliku**, kliknij przycisk **nowy**, a następnie kliknij przycisk **dynamiczny projekt sieci Web**. (Jeśli nie widzisz **dynamiczny projekt sieci Web** wymienione jako projekt dostępne po kliknięciu przycisku **pliku**, **nowy**, następnie wykonaj następujące czynności: kliknij **pliku**, kliknij przycisk **nowy**, kliknij przycisk **projektu**, rozwiń węzeł **Web**, kliknij przycisk **dynamiczny projekt sieci Web**i kliknij przycisk  **Następnie**.) Do celów tego samouczka, nazwij projekt **MyACSHelloWorld**. (Upewnij się, możesz użyć tej nazwy, kolejne kroki w tym samouczku oczekiwać nosić MyACSHelloWorld pliku WAR). Na ekranie pojawi się podobny do następującego:
   
    ![Utwórz projekt Hello World ACS exampple][create_acs_hello_world]
   
    Kliknij przycisk **Zakończ**.
2. W widoku Eksplorator projektów w środowisku Eclipse, rozwiń węzeł **MyACSHelloWorld**. Kliknij prawym przyciskiem myszy folder **WebContent**, kliknij polecenie **New** (Nowy), a następnie kliknij polecenie **JSP File** (Plik JSP).
3. W **New JSP File** okna dialogowego, nazwa pliku **index.jsp**. Zachowaj folder nadrzędny MyACSHelloWorld/WebContent, jak pokazano poniżej:
   
    ![Dodaj plik JSP, na przykład ACS][add_jsp_file_acs]
   
    Kliknij przycisk **Dalej**.
4. W **wybierz szablon JSP** zaznacz pozycję **New JSP File (html)** i kliknij przycisk **Zakończ**.
5. Po otwarciu pliku index.jsp w środowisku Eclipse Dodaj tekst do wyświetlenia **Hello, World ACS!** wewnątrz istniejącego elementu `<body>`. Zaktualizowana `<body>` zawartość powinna być widoczna w następujący sposób:
   
        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
   
    Zapisz index.jsp.

## <a name="add-the-acs-filter-library-to-your-application"></a>Dodaj bibliotekę filtru ACS do aplikacji
1. W obszarze Eksplorator projektów w środowisku Eclipse, kliknij prawym przyciskiem myszy **MyACSHelloWorld**, kliknij przycisk **ścieżkę kompilacji**, a następnie kliknij przycisk **Konfiguruj ścieżkę kompilacji**.
2. W **ścieżka kompilacji języka Java** okna dialogowego, kliknij przycisk **biblioteki** kartę.
3. Kliknij przycisk **Dodaj bibliotekę**.
4. Kliknij przycisk **filtr usługi kontroli dostępu platformy Azure (za pomocą technologii Otwórz MS)** , a następnie kliknij przycisk **dalej**. **Filtr usługi kontroli dostępu platformy Azure** zostanie wyświetlone okno dialogowe.  ( **Lokalizacji** pole może mieć inną ścieżkę, w zależności od tego, w którym zainstalowano Eclipse, a numer wersji może być różne w zależności od aktualizacji oprogramowania.)
   
    ![Dodaj bibliotekę filtru ACS][add_acs_filter_lib]
5. Przy użyciu przeglądarki otworzyć **integracji strony logowania** strony portalu zarządzania Kopiuj adres URL wyświetlany w **opcja 1: łącze do strony logowania usługi hostowanej ACS** pól i wklej ją do **ACS Punktu końcowego uwierzytelniania** pola w oknie dialogowym Eclipse.
6. Przy użyciu przeglądarki otworzyć **Edytowanie aplikacji jednostki uzależnionej strony** strony portalu zarządzania Kopiuj adres URL wyświetlany w **obszaru** pól i wklej ją do **jednostki uzależnionej strony obszaru**pola w oknie dialogowym Eclipse.
7. W ramach **zabezpieczeń** sekcji okna dialogowego Eclipse, jeśli chcesz użyć istniejącego certyfikatu, kliknij przycisk **Przeglądaj**, przejdź do certyfikatów, które mają używać, zaznacz go, a następnie kliknij przycisk **Otwórz**. Lub, jeśli chcesz utworzyć nowy certyfikat, kliknij przycisk **nowy** do wyświetlenia **nowego świadectwa** okna dialogowego, następnie podaj hasło, nazwa pliku .cer i nazwę pliku PFX dla nowego certyfikatu.
8. Sprawdź **osadzić certyfikatu w pliku WAR**. Osadzanie certyfikatu w ten sposób obejmuje on w danym wdrożeniu bez konieczności ręcznego dodawania jako składnik. (Jeśli zamiast tego muszą być przechowywane certyfikat zewnętrznie z pliku WAR, można dodać certyfikatu jako części roli i usuń zaznaczenie pola wyboru **osadzić certyfikatu w pliku WAR**.)
9. [Opcjonalnie] Zachowaj **połączeń HTTPS wymagają** zaznaczone. Jeśli ustawisz tę opcję, należy uzyskać dostępu do aplikacji przy użyciu protokołu HTTPS. Jeśli nie chcesz wymagać połączeń HTTPS, usuń zaznaczenie tej opcji.
10. Do emulatora obliczeń wdrożenia programu **filtru ACS Azure** ustawienia będzie wyglądała podobnie do następującego.
    
    ![Azure ustawienia filtru ACS do emulatora obliczeń wdrożenia][add_acs_filter_lib_emulator]
11. Kliknij przycisk **Zakończ**.
12. Kliknij przycisk **tak** przedstawionej z pola z okna dialogowego informacją, że zostanie utworzony plik web.xml.
13. Kliknij przycisk **OK** zamknąć **ścieżka kompilacji języka Java** okna dialogowego.

## <a name="deploy-to-the-compute-emulator"></a>Wdrażanie na emulator obliczeń
1. W obszarze Eksplorator projektów w środowisku Eclipse, kliknij prawym przyciskiem myszy **MyACSHelloWorld**, kliknij przycisk **Azure**, a następnie kliknij przycisk **pakietu dla platformy Azure**.
2. Dla **Nazwa projektu**, typ **MyAzureACSProject** i kliknij przycisk **dalej**.
3. Wybierz JDK i serwera aplikacji. (Te kroki są tu szczegółowo [tworzenia aplikacji Hello World na platformie Azure w programie Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) samouczka).
4. Kliknij przycisk **Zakończ**.
5. Kliknij przycisk **uruchamianie w emulatorze Azure** przycisku.
6. Po uruchomieniu aplikacji sieci web Java w emulatorze obliczeń, zamknij wszystkie wystąpienia przeglądarki (tak, aby wszystkie bieżące sesje przeglądarki nie zakłóca test logowania ACS).
7. Uruchom aplikację, otwierając <http://localhost:8080/MyACSHelloWorld/> w przeglądarce (lub <https://localhost:8080/MyACSHelloWorld/> , gdy zaznaczono **połączeń HTTPS wymagają**). Powinien być wyświetlany monit o logowania usługi Windows Live ID, a następnie użytkownik należy podjąć w celu zwrotny adres URL określony dla jednostki uzależnionej strony aplikacji.
8. Po zakończeniu przeglądania aplikacji, kliknij przycisk **zresetować emulatora usługi Azure** przycisku.

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure
Aby wdrożyć na platformie Azure, musisz zmienić jednostki uzależnionej obszar strony i zwrotnego adresu URL dla przestrzeni nazw usługi ACS.

1. W portalu zarządzania Azure w **Edytowanie aplikacji jednostki uzależnionej strony** strony, zmodyfikuj **obszaru** jako adres URL witryny wdrożone. Zastąp **przykład** o nazwie DNS określona dla danego wdrożenia.
   
    ![Jednostki uzależnionej obszar strony do użycia w produkcji][relying_party_realm_production]
2. Modyfikowanie **zwrotny adres URL** jako adres URL aplikacji. Zastąp **przykład** o nazwie DNS określona dla danego wdrożenia.
   
    ![Jednostki uzależnionej zwrotny adres URL strony do użycia w produkcji][relying_party_return_url_production]
3. Kliknij przycisk **zapisać** Aby zapisać Twoje zaktualizowane odpowiedzieć obszar strony i wrócić zmiany adresu URL.
4. Zachowaj **integracji strony logowania** strony Otwórz w przeglądarce, musisz skopiować z niej wkrótce.
5. W obszarze Eksplorator projektów w środowisku Eclipse, kliknij prawym przyciskiem myszy **MyACSHelloWorld**, kliknij przycisk **ścieżkę kompilacji**, a następnie kliknij przycisk **Konfiguruj ścieżkę kompilacji**.
6. Kliknij przycisk **biblioteki** , kliknij pozycję **filtr usługi kontroli dostępu platformy Azure**, a następnie kliknij przycisk **Edytuj**.
7. Przy użyciu przeglądarki otworzyć **integracji strony logowania** strony portalu zarządzania Kopiuj adres URL wyświetlany w **opcja 1: łącze do strony logowania usługi hostowanej ACS** pól i wklej ją do **ACS Punktu końcowego uwierzytelniania** pola w oknie dialogowym Eclipse.
8. Przy użyciu przeglądarki otworzyć **Edytowanie aplikacji jednostki uzależnionej strony** strony portalu zarządzania Kopiuj adres URL wyświetlany w **obszaru** pól i wklej ją do **jednostki uzależnionej strony obszaru**pola w oknie dialogowym Eclipse.
9. W ramach **zabezpieczeń** sekcji okna dialogowego Eclipse, jeśli chcesz użyć istniejącego certyfikatu, kliknij przycisk **Przeglądaj**, przejdź do certyfikatów, które mają używać, zaznacz go, a następnie kliknij przycisk **Otwórz**. Lub, jeśli chcesz utworzyć nowy certyfikat, kliknij przycisk **nowy** do wyświetlenia **nowego świadectwa** okna dialogowego, następnie podaj hasło, nazwa pliku .cer i nazwę pliku PFX dla nowego certyfikatu.
10. Zachowaj **osadzić certyfikatu w pliku WAR** zaznaczone, przy założeniu, aby osadzić certyfikatu w pliku WAR.
11. [Opcjonalnie] Zachowaj **połączeń HTTPS wymagają** zaznaczone. Jeśli ustawisz tę opcję, należy uzyskać dostępu do aplikacji przy użyciu protokołu HTTPS. Jeśli nie chcesz wymagać połączeń HTTPS, usuń zaznaczenie tej opcji.
12. W przypadku wdrożenia na platformie Azure ustawienia filtra ACS Azure będzie wyglądać podobny do następującego.
    
    ![Ustawienia usługi Azure ACS filtru w przypadku wdrożenia produkcyjnego][add_acs_filter_lib_production]
13. Kliknij przycisk **Zakończ** zamknąć **Edytuj biblioteki** okna dialogowego.
14. Kliknij przycisk **OK** zamknąć **właściwości MyACSHelloWorld** okna dialogowego.
15. W programie Eclipse kliknij **publikowania do chmury Azure** przycisku. Odpowiedz na monity, podobnie jak w **do wdrożenia aplikacji na platformie Azure** sekcji [tworzenia aplikacji Hello World na platformie Azure w programie Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) tematu. 

Po wdrożeniu aplikacji sieci web, zamknij wszystkie sesje Otwórz przeglądarkę, uruchom aplikację sieci web i powinien monit logowania się przy użyciu poświadczeń identyfikatora Windows Live ID, a następnie wysyłane do jednostki uzależnionej aplikacji firm zwrotny adres URL.

Gdy wszystko będzie gotowe za pomocą aplikacji ACS Hello World, pamiętaj, aby usunąć wdrożenie (znajdują się informacje dotyczące usuwania wdrożenia w [tworzenia aplikacji Hello World na platformie Azure w programie Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) tematu).

## <a name="next_steps"></a>Następne kroki
Do badania z Assertion Markup języka SAML (Security) zwracane przez usługi ACS do aplikacji, zobacz [sposób wyświetlania SAML zwrócony przez usługę Azure kontroli dostępu][How to view SAML returned by the Azure Access Control Service]. Aby jeszcze bardziej Eksploruj funkcje usług ACS i eksperymentować bardziej zaawansowanych scenariuszy, zobacz [2.0 usługi kontroli dostępu][Access Control Service 2.0].

Ponadto, w tym przykładzie używany **osadzić certyfikatu w pliku WAR** opcji. Tej opcji upraszcza wdrażanie certyfikatu. Jeśli zamiast tego chcesz zachować niezależnie od pliku WAR certyfikatu podpisywania, można zrobić w następujący sposób:

1. W ramach **zabezpieczeń** sekcji **filtr usługi kontroli dostępu platformy Azure** okno dialogowe, typ **${env. JAVA_HOME}/MyCert.cer** i usuń zaznaczenie pola wyboru **osadzić certyfikatu w pliku WAR**. (Dostosuj mycert.cer, jeśli różni się nazwy pliku certyfikatu). Kliknij przycisk **Zakończ** aby zamknąć okno dialogowe.
2. Skopiuj certyfikat jako składnik we wdrożeniu: W Eksploratorze projektu w środowisku Eclipse, rozwiń węzeł **MyAzureACSProject**, kliknij prawym przyciskiem myszy **WorkerRole1**, kliknij przycisk **właściwości**, Rozwiń węzeł **roli Azure**i kliknij przycisk **składniki**.
3. Kliknij pozycję **Add** (Dodaj).
4. W ramach **Dodaj składnik** okna dialogowego:
   
   1. W **importu** sekcji:
      1. Użyj **pliku** przycisk, aby przejść do certyfikatu, którego chcesz użyć. 
      2. Dla **metody**, wybierz pozycję **kopiowania**.
   2. Aby uzyskać **jako nazwę**, kliknij pole tekstowe i zaakceptuj nazwę domyślną.
   3. W **Wdróż** sekcji:
      1. Dla **metody**, wybierz pozycję **kopiowania**.
      2. Aby uzyskać **do katalogu**, typ **JAVA_HOME %**.
   4. Twoje **Dodaj składnik** okna dialogowego powinien wyglądać podobnie do następującego.
      
       ![Dodaj składnik certyfikatu][add_cert_component]
   5. Kliknij przycisk **OK**.

W tym momencie certyfikat będzie uwzględnione w danym wdrożeniu. Należy pamiętać, że niezależnie od tego, czy osadzić certyfikatu w pliku WAR i dodaj go jako składnik do wdrożenia, należy przekazać certyfikat do obszaru nazw zgodnie z opisem w [przekazać certyfikat do obszaru nazw ACS] [ Upload a certificate to your ACS namespace] sekcji.

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: active-directory-java-view-saml-returned-by-access-control.md
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png

