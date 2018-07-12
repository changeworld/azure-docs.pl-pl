---
title: Monitorowanie lokalnej infrastruktury do obsługi tożsamości w chmurze.
description: Jest to strona dotycząca programu Azure AD Connect Health, na której opisano, czym on jest i do czego służy.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 006914b279f2d762bf0dee6f83d6866235c062bc
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887508"
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Monitorowanie lokalnej infrastruktury do obsługi tożsamości i usług synchronizacji w chmurze
Program Azure Active Directory (Azure AD) Connect Health pomaga monitorować i zdobywać informacje o lokalnej infrastrukturze do obsługi tożsamości oraz usługach synchronizacji. Umożliwia utrzymywanie niezawodnego połączenia z usługami Office 365 i Microsoft Online Services, udostępniając funkcje monitorowania kluczowych składników tożsamości, takich jak serwery usług Active Directory Federation Services (AD FS), serwery programu Azure AD Connect (nazywane także aparatem synchronizacji), kontrolery domeny usługi Active Directory itp. Zapewnia także łatwy dostęp do kluczowych punktów danych dotyczących tych składników, umożliwiając zdobywanie informacji o użyciu i innych istotnych parametrach, co pozwala na świadome podejmowanie decyzji.

Informacje są prezentowane w [portalu programu Azure AD Connect Health](https://aka.ms/aadconnecthealth). Korzystając z portalu programu Azure AD Connect Health, możesz wyświetlać alerty, wyniki monitorowania wydajności, analizy użycia i inne informacje. Program Azure AD Connect Health pokazuje kondycję kluczowych składników tożsamości w jednym miejscu.

![Co to jest program Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Wraz z rozwojem funkcji programu Azure AD Connect Health portal zapewnia wgląd w dodatkowe informacje za pośrednictwem pulpitu nawigacyjnego obsługiwanego przy użyciu tożsamości. Dzięki temu można zapewnić użytkownikom jeszcze bardziej niezawodne, sprawne i zintegrowane środowisko umożliwiające wydajne działanie.
## <a name="why-use-azure-ad-connect-health"></a>Dlaczego warto korzystać z programu Azure AD Connect Health?
Zintegrowanie katalogów lokalnych z usługą Azure AD zwiększa produktywność użytkowników, zapewniając wspólną tożsamość na potrzeby dostępu do zasobów — zarówno lokalnych, jak i w chmurze. Jednak tego rodzaju integracja wiąże się z koniecznością zapewnienia dobrej kondycji środowiska, aby użytkownicy mieli niezawodny dostęp z dowolnego urządzenia do zasobów lokalnych i znajdujących się w chmurze. Program Azure AD Connect Health pomaga w monitorowaniu i zdobywaniu informacji o lokalnej infrastrukturze do obsługi tożsamości, która umożliwia dostęp do usługi Office 365 lub innych aplikacji usługi Azure AD. Wymaga to jedynie zainstalowania agenta na każdym z lokalnych serwerów tożsamości.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Program Azure AD Connect Health dla usług AD FS](active-directory-aadconnect-health-adfs.md)
Program Azure AD Connect Health dla usług AD FS obsługuje usługi AD FS 2.0 w systemach Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Obsługuje także monitorowanie serwerów proxy usług AD FS lub serwerów proxy aplikacji internetowej, które zapewniają obsługę uwierzytelniania w przypadku dostępu do ekstranetu. Dzięki prostej i niedrogiej instalacji agenta kondycji program Azure AD Connect Health dla usług AD FS oferuje następujący zestaw kluczowych funkcji:

* Monitorowanie z alertami, które informują, kiedy serwery usług AD FS i serwery proxy usług AD FS nie są w dobrej kondycji
* Powiadomienia e-mail dotyczące alertów krytycznych
* Trendy w danych dotyczących wydajności przydatne do planowania wydajności usług AD FS
* Analizy użycia dotyczące różnych wariantów logowania się do usług AD FS (aplikacje, użytkownicy, lokalizacja sieciowa itp.) pomagające zrozumieć sposób korzystania z usług AD FS
* Raporty dotyczące usług AD FS, na przykład lista 50 użytkowników, którzy najczęściej nieprawidłowo podawali nazwę użytkownika/hasło, wraz z ostatnim adresem IP
* Raport ryzykownych adresów IP dla nieudanych logowań do usług AD FS
  
Tutaj dowiesz się więcej o [używaniu programu Azure AD Connect Health dla usług AD FS](active-directory-aadconnect-health-adfs.md)

Poniższy klip wideo zawiera omówienie programu Azure AD Connect Health dla usług AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Program Azure AD Connect Health do celów synchronizacji](active-directory-aadconnect-health-sync.md)
Program Azure AD Connect Health do celów synchronizacji monitoruje i udostępnia informacje na temat procesów synchronizacji, które mają miejsce między lokalną usługą Active Directory i usługą Azure AD. Program Azure AD Connect Health do celów synchronizacji oferuje następujący zestaw kluczowych funkcji:

* Monitorowanie z alertami, które informują, kiedy serwer programu Azure AD Connect (zwany też aparatem synchronizacji) nie jest w dobrej kondycji
* Powiadomienia e-mail dotyczące alertów krytycznych
* Wgląd w dane operacyjne dotyczące synchronizacji, między innymi wykresy opóźnień operacji synchronizacji i trendy w innych operacjach, takich jak dodawanie, aktualizowanie i usuwanie
* Przegląd informacji na temat właściwości synchronizacji i ostatniego pomyślnego eksportu do usługi Azure AD
* Raporty o błędach synchronizacji na poziomie obiektu \(nie wymagają usługi Azure AD w wersji Premium\)

Tutaj dowiesz się więcej o [używaniu programu Azure AD Connect Health do celów synchronizacji](active-directory-aadconnect-health-sync.md)

Poniższy klip wideo zawiera omówienie programu Azure AD Connect Health do celów synchronizacji.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Program Azure AD Connect Health dla usług AD DS](active-directory-aadconnect-health-adds.md)
Program Azure AD Connect Health dla usług Active Directory Domain Services (AD DS) oferuje funkcje monitorowania kontrolerów domeny zainstalowanych w systemach Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Instalacja agenta kondycji umożliwia monitorowanie lokalnego środowiska usług AD DS z chmury. Program Azure AD Connect Health dla usług AD DS oferuje następujący zestaw kluczowych funkcji:

* Monitorowanie alertów w celu wykrywania złej kondycji kontrolerów domeny wraz z powiadomieniami e-mail w przypadku alertów krytycznych
* Pulpit nawigacyjny Kontrolery domeny, który zapewnia szybki wgląd w kondycję i stan operacyjny kontrolerów domeny
* Pulpit nawigacyjny Stan replikacji zawierający najnowsze informacje o replikacji wraz z linkami do poradników rozwiązywania problemów w przypadku wykrycia błędów
* Szybki dostęp z dowolnego miejsca do wykresów danych wydajności utworzonych na podstawie popularnych liczników wydajności, które są niezbędne do celów monitorowania i rozwiązywania problemów

Tutaj dowiesz się więcej o [używaniu programu Azure AD Connect Health dla usług AD DS](active-directory-aadconnect-health-adds.md)

Poniższy klip wideo zawiera omówienie programu Azure AD Connect Health dla usług AD DS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Wprowadzenie do programu Azure AD Connect Health
Aby rozpocząć pracę z programem Azure AD Connect Health, należy wykonać następujące czynności:

1. [Uzyskaj usługę Azure AD w wersji Premium](../fundamentals/active-directory-get-started-premium.md) lub [rozpocznij okres próbny](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Pobierz i zainstaluj agentów programu Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) na serwerach tożsamości.
3. Wyświetl pulpit nawigacyjny programu Azure AD Connect Health na stronie [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Pamiętaj, że przed wyświetleniem danych na pulpicie nawigacyjnym programu Azure AD Connect Health musisz zainstalować agentów programu Azure AD Connect Health na serwerach docelowych.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Pobieranie i instalowanie agenta programu Azure AD Connect Health
* Sprawdź, czy [wymagania programu Azure AD Connect Health zostały spełnione](active-directory-aadconnect-health-agent-install.md#requirements).
* Wprowadzenie do korzystania z programu Azure AD Connect Health dla usług AD FS
    * [Pobierz agenta programu Azure AD Connect Health dla usług AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Zobacz instrukcje instalacji](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Wprowadzenie do korzystania z programu Azure AD Connect Health do celów synchronizacji
    * [Pobierz i zainstaluj najnowszą wersję programu Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771). Agent kondycji do celów synchronizacji zostanie zainstalowany w ramach instalacji programu Azure AD Connect (w wersji 1.0.9125.0 lub nowszej).
* Wprowadzenie do korzystania z programu Azure AD Connect Health dla usług AD DS
    * [Pobierz agenta programu Azure AD Connect Health dla usług AD DS](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Zobacz instrukcje instalacji](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="azure-ad-connect-health-portal"></a>Portal programu Azure AD Connect Health
Portal programu Azure AD Connect Health umożliwia wyświetlanie alertów, wyników monitorowania wydajności i analiz użycia. Adres URL https://aka.ms/aadconnecthealth pozwala na wyświetlenie głównego bloku programu Azure AD Connect Health. Blok możesz traktować jak okno. W głównym bloku znajduje się przycisk **Szybki start**, usługi dostępne w programie Azure AD Connect Health i dodatkowe opcje konfiguracji. Zobacz poniższy zrzut ekranu i zamieszczone pod nim krótkie objaśnienia. Po wdrożeniu agentów usługa kondycji automatycznie identyfikuje usługi monitorowane przez program Azure AD Connect Health.

> [!NOTE]
> Aby uzyskać informacje o licencjonowaniu, zobacz temat [Azure AD Connect Health FAQ (Program Azure AD Connect Health — często zadawane pytania)](active-directory-aadconnect-health-faq.md) lub [stronę z cennikiem usługi Azure AD](https://aka.ms/aadpricing).
    
![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health/portalsidebar.png)

* **Szybki Start**: wybranie tej opcji powoduje otwarcie bloku **Szybki start**. Agenta programu Azure AD Connect Health można pobrać, wybierając pozycję **Pobierz narzędzia**. Można również uzyskać dostęp do dokumentacji i przekazać swoją opinię.
* **Azure Active Directory Connect (synchronizacja)**: ta opcja umożliwia pokazanie serwerów programu Azure AD Connect, które są aktualnie monitorowane przez program Azure AD Connect Health. Pozycja **Błędy synchronizacji** umożliwia wyświetlenie podstawowych błędów synchronizacji Twojej pierwszej dołączonej usługi synchronizacji według kategorii. Wybranie pozycji **Usługi synchronizacji** powoduje otwarcie bloku z informacjami na temat serwerów programu Azure AD Connect. Aby dowiedzieć się więcej o dostępnych możliwościach, zobacz [Używanie programu Azure AD Connect Health w celu synchronizacji](active-directory-aadconnect-health-sync.md).
* **Active Directory Federation Services**: ta opcja umożliwia pokazanie wszystkich usług AD FS, które są aktualnie monitorowane przez program Azure AD Connect Health. Po wybraniu wystąpienia zostaje otwarty blok z informacjami dotyczącymi tego wystąpienia usługi. Informacje te obejmują przegląd, właściwości, alerty, wyniki monitorowania i analizy użycia. Aby dowiedzieć się więcej o dostępnych możliwościach, zobacz [Używanie programu Azure AD Connect Health z usługami AD FS](active-directory-aadconnect-health-adfs.md).
* **Active Directory Domain Services**: ta opcja umożliwia pokazanie wszystkich lasów usług AD DS, które są aktualnie monitorowane przez program Azure AD Connect Health. Po wybraniu lasu zostaje otwarty blok z informacjami dotyczącymi tego lasu. Te informacje obejmują przegląd najważniejszych danych, pulpit nawigacyjny Kontrolery domeny, pulpit nawigacyjny Stan replikacji, alerty i monitorowanie. Aby dowiedzieć się więcej o dostępnych możliwościach, zobacz [Używanie programu Azure AD Connect Health z usługami AD DS](active-directory-aadconnect-health-adds.md).
* **Konfiguracja**: ta sekcja obejmuje opcje umożliwiające włączanie i wyłączanie ustawień:

  - Pozycja **Ustawienia** zawiera podstawową konfigurację Twoich agentów. Ustawienie automatycznego uaktualniania umożliwia aktualizowanie agenta programu Azure AD Connect Health do najnowszej wersji — agent programu Azure AD Connect Health zostanie automatycznie zaktualizowany do najnowszej wersji, gdy stanie się ona dostępna. Ta opcja jest domyślnie włączona. Pozwalaj firmie Microsoft na dostęp do danych o kondycji katalogu usługi Azure AD wyłącznie w celu rozwiązywania problemów — jeśli ta opcja jest włączona, firma Microsoft może zobaczyć te same dane co Ty. Te informacje mogą ułatwić rozwiązywanie problemów i uzyskiwanie pomocy. Ta opcja jest domyślnie wyłączona.
* W sekcji **Kontrola dostępu oparta na rolach (IAM)** można zarządzać dostępem do danych programu Connect Health na podstawie ról. 

## <a name="related-links"></a>Powiązane linki
* [Instalowanie agenta programu Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operacje w programie Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](active-directory-aadconnect-health-adfs.md)
* [Używanie programu Azure AD Connect Health w celu synchronizacji](active-directory-aadconnect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health — często zadawane pytania](active-directory-aadconnect-health-faq.md)
* [Historia wersji programu Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
