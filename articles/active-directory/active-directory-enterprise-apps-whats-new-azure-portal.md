---
title: What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, co nowego w zarządzaniu aplikacja dla przedsiębiorstw w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
editor: ''
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: fefc508679a309262d07a582fc8f5bdf9f67cfe5
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310122"
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory 

Azure Active Directory (Azure AD) rozszerzona enterprise narzędzia do zarządzania aplikacjami, o nowe funkcje i możliwości wprowadzania aplikacji zarządzania jest prostszy i wydajne.

Poniżej przedstawiono niektóre usprawnienia dla usługi Azure AD w [witryny Azure portal](https://portal.azure.com).

- Galerię aplikacji ulepszone środowisko przy użyciu modelu tworzenia aplikacji uproszczone i obsługa techniczna dla wszystkich typów aplikacji, których użyto do. 
- Środowisko dobór Szybki Start, która pomoże Ci rozpocząć korzystanie z pilota aplikacji. 
- Skonfiguruj zasady samoobsługi za pomocą kilku kliknięć. 
- Ulepszenia serwera proxy aplikacji, pojedynczy konfiguracji logowania jednokrotnego, a następnie przełącz własnych środowisk aplikacji, co pozwala uzyskać wydajność niż przed.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Ulepszenia dotyczące galerii aplikacji usługi Azure Active Directory

Dodaj ulubione aplikacje, czy są one z [galerii aplikacji](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery), niestandardowych aplikacji jest rozszerzenie w chmurze lub nowe aplikacje, które tworzysz.  Możesz zacząć korzystać z tego nowego środowiska, klikając **Dodaj** w obszarze **aplikacje dla przedsiębiorstw** lub **wszystkie aplikacje**.
 
  ![Dodawanie aplikacji](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Raz w galerii, zobaczysz, że wszystkie polecane aplikacje zapewniające obsługę użytkownika wyświetlane frontonu i Centrum. Możesz przeglądać szeroką gamę różnych kategorii, aby przejść do aplikacji, które Cię interesują lub środowisko wyszukiwania umożliwia szybkie znajdowanie aplikacji, którą chcesz zintegrować.

  ![Galeria aplikacji](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Dodawanie niestandardowych aplikacji z jednego miejsca

Oprócz dodawania wstępnie zintegrowanych aplikacji z galerii, wszystko, czego konfiguracji niestandardowej aplikacji środowisk, aby były używane do w portalu klasycznym zarządzania są teraz możliwe w nowym portalu. Czy rozszerzania aplikacji ze środowiska lokalnego przy użyciu serwera proxy aplikacji, integrowania własnego hasła lub aplikacja Federacyjna usługa rejestracji Jednokrotnej, lub tworzenia zupełnie nowym aplikacji za pomocą rejestru aplikacji, możesz uzyskać do niego z tym jednym miejscu.

  ![Dodawanie własnej aplikacji](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Aby rozpocząć dodawanie własnych aplikacji**:

1. Kliknij przycisk **dodać łącze własne** w górnej części galerii aplikacji. 
1. Zostaną wyświetlone dwie opcje masz przed sobą: **wdrożyć istniejącą aplikację** lub **opracowanie nowej aplikacji**. Czytaj dalej, aby dowiedzieć się różnią się dwie opcje i sposób ich użycia.

### <a name="deploying-existing-applications"></a>Wdrażanie istniejącej aplikacji

1. Jeśli coś już uruchomioną aplikację i po prostu chcesz zintegrować ją z usługi Azure AD na potrzeby logowania jednokrotnego na lub inicjowania obsługi administracyjnej, wybierz **wdrożyć istniejącą aplikację** opcji. Wybierz nazwę swojej aplikacji, kliknij przycisk **Dodaj**.
1. Gotowe. Zamiast konieczności poznać wszystkie szczegóły dotyczące aplikacji na początku, możesz teraz skonfigurować działania swoją nową aplikację, przechodząc do menu po lewej stronie i konfigurowanie aplikacji do swoich potrzeb w dowolnym momencie.

  ![Dodawanie istniejącej aplikacji za pomocą jednego kliknięcia](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Tworzenie nowej aplikacji

1. Jeśli tworzysz nową aplikację, istnieje łatwy sposób można uzyskać w rejestrze aplikacji odpowiednie z galerii:
1. Kliknij przycisk **dodać własne** galerii aplikacji, wybierz opcję **tworzenia istniejącej aplikacji**, a zobaczysz szybki link bezpośrednio do środowiska Dodaj aplikacji.

  ![Dodawanie aplikacji nowo opracowane za pomocą kilku kliknięć](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Po dodaniu aplikacji przy użyciu rejest aplikacji, zobaczysz go wyświetlane na liście aplikacji przedsiębiorstwa, w której będziesz mieć możliwość konfigurowania logowania jednokrotnego i zarządzanie zasadami dostępu dla nowej aplikacji.

  ![Zarządzanie dostępem do nowej aplikacji w obszarze aplikacje dla przedsiębiorstw](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quickstart-get-going-with-your-new-application-right-away"></a>Szybki Start: Rozpocznij pracę z usługą razu swoją nową aplikację 

Po dodaniu aplikacji, czy należy wstępnie zintegrowanych lub własnej aplikacji, utworzyliśmy dopasowane środowisko szybki start, aby ułatwić szybkie zaangażowaniu w nowym środowisku aplikacji. Jeśli stosujesz systematycznie każdej opcji, utworzymy przeprowadzą Cię przez interfejs użytkownika i pokazują, jak rozpocząć korzystanie z pilota nowych aplikacji tak szybko, jak to możliwe. 
 
  ![Nowe środowisko aplikacji w przewodniku Szybki Start](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Ten przewodnik Szybki Start środowiska w dowolnym momencie, a także dla dowolnej aplikacji można znaleźć, klikając **Szybki Start** menu nawigacji po lewej stronie aplikacji.


## <a name="updated-application-proxy-configuration"></a>Konfiguracja serwera proxy aplikacji

Teraz Przyjrzyjmy powiedzieć, jedną z nowych aplikacji, którą dodałeś działa w środowiska lokalne i chcesz ją zintegrować z usługą Azure AD.  Jedną z najfajniejszych rzeczy nowe nowe środowisko konfiguracji aplikacji w nowej usłudze Azure AD portal to, czy, dzieląc aplikację tryb logowania jednokrotnego z jej konfiguracji serwera proxy aplikacji, można teraz łatwo udostępnić hasło logowania jednokrotnego lub aplikacji federacyjnych są uruchomione w sieci firmowej prawa do chmury bez konieczności tworzenia wielu wystąpień aplikacji.

Można również skonfigurować dowolne nowe aplikacje, które zostały dodane do użytku z prawej strony serwera Proxy aplikacji usługi Azure AD z nowego portalu, w tym aplikacje, które obsługują natywnych środowisk uwierzytelniania Windows.

  ![Konfigurowanie aplikacji do korzystania z opcji logowania jednokrotnego zintegrowanego uwierzytelniania Windows](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 
Aby rozpocząć konfigurowanie natywnej aplikacji uwierzytelniania Windows przy użyciu serwera Proxy aplikacji:
1. Kliknij element nawigacji rejestracji jednokrotnej i wybierz polecenie **zintegrowane uwierzytelnianie Windows** w obszarze ustawień logowania jednokrotnego i skonfiguruj ustawienia do swoich potrzeb.
1. Na podstawie obsługuje te nowe tryby uwierzytelniania, można teraz również przekazywać certyfikaty z domeny niestandardowe do obsługi aplikacji działających na bezpieczne punkty końcowe w ramach Twojej organizacji.  
 
   ![Przekazywanie certyfikatu do użycia z serwera Proxy aplikacji](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

1. Aby przekazać nowy certyfikat dla aplikacji ulubionych w środowisku lokalnym, kliknij polecenie **serwera proxy aplikacji** opcję z menu nawigacji po lewej stronie, kliknij przycisk **certyfikatu** selektor i przekaż certyfikat plik, który możemy użyć w celu zaszyfrowania żądania z naszego punktu końcowego w chmurze do aplikacji.

## <a name="advanced-federated-single-sign-on-configuration"></a>Zaawansowane federacyjnego pojedynczego konfiguracji logowania jednokrotnego

Dla osób, które już dziś przy użyciu aplikacji federacyjnych istnieje wiele nowych funkcji w opartej na SAML logowania jednokrotnego konfiguracji. Na początek z teraz możesz można całkowicie dostosować, dodawania, usuwania i mapowanie istniejących atrybutów użytkownika wystawione jako oświadczenia w tokeny SAML.
 
  ![Dostosowywanie atrybutów użytkownika tokenu SAML przekazane do aplikacji federacyjnych](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)

Aby sprawdzić, się z nowym Federacyjna usługa rejestracji Jednokrotnej w konfiguracji:
1. Otwieranie aplikacji federacyjnych **logowanie jednokrotne** menu nawigacji po lewej stronie i upewnij się, że "*opartej na SAML logowania jednokrotnego** wybrany tryb. 
1. Jeden raz, zaznacz pole wyboru w obszarze **atrybutów użytkownika** nagłówek, aby zmodyfikować wszystkie atrybuty uwzględnione w tokenie języka SAML przekazany do tej aplikacji.

Możesz można również utworzyć, przerzucania i zarządzanie certyfikatami używanymi do federacyjnego logowania jednokrotnego, a także edytować, który pobiera powiadomienie, gdy Twój certyfikat wkrótce wygaśnie. Zostaną wyświetlone te nowe opcje w obszarze **certyfikaty** na tym samym — okienko logowanie jednokrotne.
 
  ![Tworzenie nowego certyfikatu, dostosowywanie wygaśnięcia powiadomienia e-mail i opcje podpisywania certyfikatu](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-parameter"></a>Stan przekazywania parametru
Na koniec możemy została również rozszerzony zestaw parametrów adresu URL protokołu SAML, firma Microsoft obsługuje do uwzględnienia **stan przekazywania parametru**, czyli stronę użytkownicy nastąpi przejście wewnątrz aplikacji federacyjnych po zalogowaniu zostanie ukończona. Jest to przydatne ustawienie, aby skonfigurować, aby wysłać użytkownikom do określonego miejsca w aplikacji, szybko je przerywaj.

  ![Ustawienie parametru tan przekaźnika SAML](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Aby ustawić parametr Stan przekazywania**:

1. Włącz **Pokaż zaawansowane ustawienia adresu URL** pole wyboru w obszarze **domena i adresy URL** na jednokrotnego okienko konfiguracji. 
1. Zbiór nowy adres URL danych wejściowych, że pola są wyświetlane, która pozwala na ustawienie tego parametru i inne ustawienia adresu URL protokołu SAML.

## <a name="bring-your-own-password-sso-applications"></a>Przenoszenie własne hasło logowania jednokrotnego aplikacji

Wiemy, że nie każda aplikacja obsługuje federacyjnego gotową do. Na przykład może być jedną z nowych aplikacji, którą dodałeś ma ekran logowania niestandardowe wykorzystywanym przez użytkowników nazwy użytkownika i hasła do logowania się na. Nadal możesz zintegrować aplikacje tego typu za pomocą usługi Azure AD przy użyciu naszego **Przenoszenie własnych aplikacji** funkcji, która jest teraz dostępna do skonfigurowania w nowym portalu.
 
  ![Integrowanie niestandardowych hasło vaulting aplikacji z usługą Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Aby wyewidencjonować funkcji "Przenoszenie własnych aplikacji"**:

1. Po ustawieniu jednej tryb logowania jednokrotnego dla nowej aplikacji niestandardowej, które zostały dodane do **opartego na hasłach logowania jednokrotnego**, wprowadź adres URL, w którym aplikacja renderuje ekran logowania i 
1. Kliknij pozycję **Zapisz**.  
1. Po wykonaniu tej czynności, firma Microsoft będzie automatycznie scrape tego adresu URL o nazwę użytkownika i hasło pole wprowadzania i pozwalają na korzystanie z usługi Azure AD na bezpieczne przesyłanie haseł do tej aplikacji przy użyciu rozszerzenia przeglądarki panelu dostępu.

## <a name="configure-self-service-application-access"></a>Konfigurowanie samoobsługowego dostępu do aplikacji

Po dodaniu wiele nowych aplikacji może chcesz umożliwić użytkownikom przeglądanie i dodać te aplikacje do ich własnych panelach dostępu bez konieczności odblokowane jako administrator. Teraz za pomocą tej najnowszej wersji, można skonfigurować i zarządzać samoobsługowego dostępu do aplikacji bezpośrednio z nowego portalu.

  ![Konfigurowanie samoobsługowego dostępu do aplikacji o hasło logowania jednokrotnego aplikacji](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Aby skonfigurować i zarządzać dostępem do aplikacji samoobsługowej**:

1. Aby rozpocząć pracę, możesz wybrać **samoobsługi** opcji z aplikacji w lewym menu nawigacji i ustaw **zezwalać użytkownikom na żądanie dostępu do tej aplikacji?** opcję "**tak**". 
1. Spowoduje to włączenie można skonfigurować, kto może zatwierdzać dostęp do tej aplikacji, a grupa samoobsługi użytkowników, którzy zostaną dodane. Ponadto jeśli aplikacja jest skonfigurowana dla hasła logowania jednokrotnego, widoczna będzie również inne rozwiązanie, które umożliwia opcjonalnie tych osób zatwierdzających zarządzać hasłami, przypisany do aplikacji.

## <a name="feedback"></a>Opinia

Mamy nadzieję, że możesz takimi jak wymaganie użycia ulepszone środowisko usługi Azure AD. Pamiętaj o opinie dostępne! Opinie i pomysły dotyczące poprawy **portalu administracyjnego** części naszych [forum z opiniami](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Firma Microsoft jest podekscytowani o tworzeniu nowego ciekawostki codziennie, użyj wskazówek z kształtem i zdefiniować, co mamy utworzyć w następnej kolejności.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacjami w usłudze Azure Active Directory](manage-apps/what-is-application-management.md).



