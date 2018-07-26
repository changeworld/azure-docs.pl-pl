---
title: Raporty dotyczące logowań w portalu usługi Azure Active Directory | Microsoft Docs
description: Wprowadzenie do raportów dotyczących logowań w portalu Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0d651f9d4fa48cec3a61f1f307f4447fe2cba63b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248955"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące logowań w portalu Azure Active Directory

Dzięki raportom usługi Azure Active Directory (Azure AD) w witrynie [Azure Portal](https://portal.azure.com) możesz uzyskać wszystkie informacje, które pomogą ustalić, jak działa środowisko.

Architektura raportowania w usłudze Azure Active Directory obejmuje następujące składniki:

- **Działanie** 
    - **Działania związane z logowaniem** — informacje na temat użycia zarządzanych aplikacji i działania użytkownika związane z logowaniem
    - **Dzienniki inspekcji** — informacje o aktywności systemu obejmujące zarządzanie użytkownikami i grupami oraz zarządzane aplikacje i działania dotyczące katalogu.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. Aby uzyskać więcej informacji, zobacz Ryzykowne logowania.
    - **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. Aby uzyskać więcej informacji, zobacz Użytkownicy oflagowani w związku z ryzykiem.

Ten temat zawiera przegląd działań dotyczących logowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
* Użytkownicy w Administrator zabezpieczeń, Czytelnik zabezpieczeń roli czytelnika raportów
* Administratorzy globalni
* Dowolny użytkownik (inny niż administrator) może uzyskać dostęp do danych na temat własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?
* Dzierżawca musi mieć skojarzoną licencję usługi Azure AD w wersji Premium, aby wyświetlić pełny raport o wszystkich operacjach logowania


## <a name="sign-in-activities"></a>Działania związane z logowaniem

Dzięki informacjom zawartym w raporcie logowania użytkownika można uzyskać odpowiedzi na pytania, takie jak:

* Co to jest wzorzec logowania użytkownika?
* Ilu użytkowników zalogowało się w ciągu tygodnia?
* Jaki jest stan tych logowań?

Pierwszym punktem wejścia do działań logowania wszystkie dane są **logowania** w sekcji działania **usługi Azure Active Directory**.


![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/61.png "Działania związane z logowaniem")


Domyślny widok listy dziennika logowań pokazuje następujące dane:

- Data logowania
- Powiązany użytkownik
- Aplikacja, do której zalogował się użytkownik
- Stan logowania
- Stan wykrywania ryzyka
- Stan wymagania dotyczącego uwierzytelniania wieloskładnikowego (MFA)

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/01.png "Działania związane z logowaniem")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/19.png "Działania związane z logowaniem")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/02.png "Działania związane z logowaniem")

Klikając pozycję w widoku listy, możesz wyświetlić jej wszystkie dostępne szczegóły w widoku poziomym.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/03.png "Działania związane z logowaniem")

> [!NOTE]
> Klienci, teraz można rozwiązywać problemy z zasad dostępu warunkowego za pomocą wszystkich raportów logowania. Klikając **dostępu warunkowego** kartę rekordu logowania, klientów można przeglądać stanu dostępu warunkowego i dokładniej omówić szczegółowe informacje o zasadach które są stosowane do logowania i wyników dla każdej zasady.
> Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące informacji urzędu certyfikacji w wszystkich logowań](active-directory-reporting-faq.md#conditional-access).

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/ConditionalAccess.png "Działania związane z logowaniem")


## <a name="filter-sign-in-activities"></a>Filtrowanie działań związanych z logowaniem

Aby zawęzić zgłaszane dane do odpowiedniego poziomu, możesz przefiltrować dane logowania przy użyciu następujących pól domyślnych:

- Użytkownik
- Aplikacja
- Stan logowania
- Stan wykrywania ryzyka
- Date

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/04.png "Działania związane z logowaniem")

Filtr **Użytkownik** umożliwia określenie nazwy lub głównej nazwy wybranego użytkownika (nazwy UPN).

Filtr **Aplikacja** umożliwia określenie nazwy wybranej aplikacji.

Filtr **Stan logowania** umożliwia wybranie jednej z następujących wartości:

- Wszyscy
- Powodzenie
- Niepowodzenie

Filtr **Wykryto ryzyko** umożliwia wybranie jednej z następujących wartości:

- Wszyscy
- Yes
- Nie

Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- 1 miesiąc
- 7 dni
- 24 godziny
- Niestandardowy zakres czasu

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

Jeśli dodasz kolejne pola do widoku logowań, te pola zostaną automatycznie dodane do listy filtrów. Na przykład dodanie pola **Aplikacja kliencka** do listy powoduje udostępnienie kolejnej opcji filtru, która umożliwia ustawienie następujących filtrów:

- Przeglądarka      
- Exchange ActiveSync (obsługiwane)               
- Exchange ActiveSync (nieobsługiwane)
- Inni klienci               
    - IMAP
    - MAPI
    - Starsi klienci pakietu Office
    - POP
    - SMTP


![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/12.png "Działania związane z logowaniem")


## <a name="download-sign-in-activities"></a>Pobieranie działań związanych z logowaniem

Jeśli chcesz pracować z danymi dotyczącymi działań związanych z logowaniem poza witryną Azure Portal, możesz je pobrać. Kliknięcie przycisku **Pobierz** spowoduje utworzenie pliku CSV zawierającego 5000 najnowszych rekordów.  Oprócz przycisku pobierania w witrynie Azure Portal jest też dostępna opcja generowania skryptu umożliwiającego pobieranie danych.  

![Pobierz](./media/active-directory-reporting-activity-sign-ins/71.png "Pobierz")

Jeśli potrzebujesz bardziej elastycznych opcji, możesz użyć rozwiązania do tworzenia skryptu. Klikając **skryptu** tworzy skrypt środowiska PowerShell, który zawiera wszystkie filtry, które zostały ustawione. Pobierz i uruchom ten skrypt **z trybu administratora** można wygenerować pliku CSV. 

### <a name="running-the-script-on-a-windows-10-machine"></a>Uruchomienie skryptu na komputerze z systemem Windows 10

Jeśli chcesz uruchomić skrypt **systemu Windows 10** maszyny, musisz najpierw wykonać kilka dodatkowych kroków. 

1. Zainstaluj [modułu AzureRM](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l).
2. Zaimportuj moduł, otwierając wiersz polecenia programu PowerShell i uruchamiając polecenie **AzureRM Import-Module**.
3. Uruchom **Set-ExecutionPolicy unrestricted** i wybierz polecenie **tak na wszystko**. 
4. Teraz możesz uruchamiać pobranego skryptu programu PowerShell w trybie administratora, aby wygenerować plik CSV.

Liczba rekordów, które możesz pobrać, jest ograniczona przez kwestie techniczne oraz przez [reguły przechowywania raportów w usłudze Azure Active Directory](active-directory-reporting-retention.md).  


## <a name="sign-in-activities-shortcuts"></a>Skróty działań związanych z logowaniem

Oprócz usługi Azure Active Directory, witryny Azure portal zapewnia dodatkowe punkty wejścia do rejestrowania danych działań:

- Omówienie ochrony zabezpieczeń tożsamości
- Użytkownicy
- Grupy
- Aplikacje dla przedsiębiorstw


### <a name="users-sign-ins-activities"></a>Działania logowania użytkowników

Dzięki informacjom zawartym w raporcie logowania użytkownika można uzyskać odpowiedzi na pytania, takie jak:

- Co to jest wzorzec logowania użytkownika?
- Ilu użytkowników zalogowało się w ciągu tygodnia?
- Jaki jest stan tych logowań?

Punktem wyjścia dla tych danych jest wykres logowania użytkowników znajdujący się na stronie omówienia **ochrony zabezpieczeń tożsamości**. Wykres logowania użytkownika przedstawia tygodniowe agregacje logowań dla wszystkich użytkowników w danym okresie czasu. Domyślny okres to 30 dni.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/06.png "Działania związane z logowaniem")

Po kliknięciu dnia na wykresie logowań zostanie wyświetlony przegląd działań logowania dla tego dnia.

Każdy wiersz na liście działań logowania określa następujące informacje:

* Kto się zalogował?
* Do której aplikacji się logowano?
* Jaki jest stan logowania?
* Jaki jest stan uwierzytelniania wieloskładnikowego dla logowania?

Klikając pozycję, można uzyskać więcej szczegółowych informacji na temat operacji logowania:

- Identyfikator użytkownika
- Użytkownik
- Nazwa użytkownika
- Identyfikator aplikacji
- Aplikacja
- Klient
- Lokalizacja
- Adres IP
- Date
- Wymagane uwierzytelnianie wieloskładnikowe
- Stan logowania

 
Na stronie **Użytkownicy** znajduje się pełny przegląd wszystkich logowań użytkowników dostępny po kliknięciu pozycji **Logowania** w sekcji **Działanie**.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/08.png "Działania związane z logowaniem")

## <a name="usage-of-managed-applications"></a>Użycie zarządzanych aplikacji

Dzięki widokowi skoncentrowanemu na aplikacji w ramach danych logowania można uzyskać odpowiedzi na pytania, takie jak:

* Kto korzysta z aplikacji?
* Jakie są 3 najczęściej używane aplikacje w organizacji?
* Ostatnio została wdrożona aplikacja. W jaki sposób działa?

Punktem wyjścia do tych danych są 3 najczęściej używane aplikacje w organizacji uwzględnione w raporcie z ostatnich 30 dni znajdującym się w sekcji **Przegląd** w obszarze **Aplikacje dla przedsiębiorstw**.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/10.png "Działania związane z logowaniem")

Wykres użycia aplikacji przedstawia tygodniowe agregacje logowań 3 najczęściej używanych aplikacji w danym czasie. Domyślny okres to 30 dni.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/47.png "Działania związane z logowaniem")

Jeśli chcesz, możesz ustawić fokus na konkretnej aplikacji.

![Raportowanie](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Raportowanie")

Po kliknięciu dnia na wykresie użycia aplikacji zostanie wyświetlona szczegółowa lista działań związanych z logowaniem.

Opcja **Logowania** umożliwia pełny przegląd zdarzeń logowania do aplikacji.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/11.png "Działania związane z logowaniem")



## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej na temat kodów błędów działań związanych z logowaniem, zobacz [Kody błędów w raportach działań związanych z logowaniem w portalu usługi Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).

