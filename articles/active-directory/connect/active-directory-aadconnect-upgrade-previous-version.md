---
title: 'Azure AD Connect: Uaktualnianie z poprzedniej wersji | Dokumentacja firmy Microsoft'
description: W tym artykule wyjaśniono różnych metod do uaktualnienia do najnowszej wersji programu Azure Active Directory Connect, w tym uaktualnienia w miejscu i migracji kierunku.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1a6fe4fc7fd5f47bfd4bc4d9168f76c31c78b47b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592480"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej wersji
W tym temacie opisano różne metody, których można użyć, aby uaktualnić instalację Połącz usługi Azure Active Directory (Azure AD) do najnowszej wersji. Zaleca się pozostawienie samodzielnie bieżącego z wersjami programu Azure AD Connect. Możesz również użyć kroki opisane w [migracji w kierunku](#swing-migration) sekcji podczas wprowadzania istotne zmiany konfiguracji.

Jeśli chcesz uaktualnić narzędzia DirSync, zobacz [uaktualnienie z narzędzia Azure AD sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) zamiast tego.

Istnieje kilka różnych strategie, których można użyć do uaktualnienia usługi Azure AD Connect.

| Metoda | Opis |
| --- | --- |
| [Automatycznie uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md) |Jest to najprostsza dla klientów z instalacji ekspresowej. |
| [Uaktualnienie w miejscu](#in-place-upgrade) |Jeśli pojedynczy serwer, możesz uaktualnić instalację w miejscu na tym samym serwerze. |
| [Zasięg migracji](#swing-migration) |Przy użyciu dwóch serwerów Przygotuj jeden z serwerów z nowej wersji lub konfiguracji i zmienić aktywnego serwera, gdy wszystko jest gotowe. |

Aby uzyskać informacje o uprawnieniach, zobacz [uprawnienia wymagane do uaktualnienia](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> Po włączeniu nowy serwer Azure AD Connect w taki sposób, aby uruchomić synchronizację zmian z usługą Azure AD musi nie wycofasz przy użyciu narzędzia DirSync i Azure AD Sync. Powrót do starszych klientów, w tym narzędzia DirSync i Azure AD Sync subskrypcji z usługi Azure AD Connect nie jest obsługiwana i może prowadzić do problemów, takich jak utraty danych w usłudze Azure AD.

## <a name="in-place-upgrade"></a>Uaktualnienie w miejscu
Uaktualnienie w miejscu działa w przypadku przenoszenia z usługi Azure AD Sync lub Azure AD Connect. Ta funkcja nie działa z narzędzia DirSync lub rozwiązanie z programu Forefront Identity Manager (FIM) + Azure AD Connector.

Ta metoda jest preferowana pojedynczego serwera i mniejsza niż około 100 000 obiektów. W przypadku zmiany reguły synchronizacji out-of-box pełny import i pełną synchronizację wystąpić po uaktualnieniu. Ta metoda gwarantuje, że nowa konfiguracja jest stosowane do wszystkich istniejących obiektów w systemie. Uruchom ten może potrwać kilka godzin w zależności od liczby obiektów, które znajdują się w zakresie aparatu synchronizacji. Harmonogram synchronizacji normalne różnicowych (co domyślnie synchronizuje co 30 minut) jest wstrzymana, ale nadal synchronizacji haseł. Należy rozważyć sposób uaktualnienia w miejscu weekend. W przypadku wydania żadnych zmian do konfiguracji out-of-box z nowej usługi Azure AD Connect, następnie import/synchronizacji różnicowej normalne uruchamia zamiast tego.  
![Uaktualnienie w miejscu](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Jeśli wprowadzono zmiany do reguły synchronizacji out-of-box te reguły są ustawiane powrót do konfiguracji domyślnej na uaktualnienie. Aby upewnić się, że konfigurację jest przechowywana między uaktualnień, upewnij się, wprowadzić zmiany, ponieważ są one opisane w [najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

Podczas uaktualniania w miejscu, mogą występować zmian wprowadzonych, które wymagają działań określonych synchronizacji (w tym kroki pełny Import i pełną synchronizację) ma być wykonywana po zakończeniu uaktualniania. Odroczenie takich działań, zapoznaj się z rozdziałem [jak mają być odroczone pełną synchronizację po uaktualnieniu](#how-to-defer-full-synchronization-after-upgrade).

Jeśli używasz usługi Azure AD Connect z niestandardowym łącznika (na przykład ogólny łącznik LDAP i rodzajowy Łącznik usług SQL), należy odświeżyć odpowiedniej konfiguracji łącznika w [Menedżera usługi synchronizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) Po uaktualnieniu w miejscu. Aby uzyskać więcej informacji na temat sposobu Odśwież konfigurację łącznika, zapoznaj się z rozdziałem artykułu [Historia wersji łącznika — Rozwiązywanie problemów z](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Jeśli konfiguracja nie będą odświeżane, importowanie i eksportowanie wykonanie kroków nie będzie działać prawidłowo dla łącznika. Zostanie wyświetlony następujący błąd w dzienniku zdarzeń aplikacji z komunikatem *"wersja zestawu w konfiguracji łącznika usługi AAD ("X.X.XXX. "X") jest starsza niż wersja rzeczywista ("X.X.XXX. "X") elementu "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Migracja typu swing
Jeśli masz złożone wdrożenia lub wiele obiektów, może być niemożliwe do uaktualnienie w miejscu na żywo w systemie. W przypadku niektórych klientów ten proces może potrwać kilka dni — i w tym czasie nie zmiany różnicowe są przetwarzane. Tej metody można użyć również, gdy ma być istotne zmiany w konfiguracji i chcesz wypróbować je przed ich jest przypisany do chmury.

Zalecaną metodą tych scenariuszy jest migrację zasięg. Należy (co najmniej) dwa serwery — jeden serwer aktywnego i jeden serwer tymczasowej. Aktywnego serwera (blue linia ciągła na poniższej ilustracji przedstawiono) jest odpowiedzialny za obciążenia active w środowisku produkcyjnym. Serwerze (przedstawiono linie przerywane purpurowa) jest przygotowywane za pomocą nowej wersji lub konfiguracji. Gdy będzie gotowy pełni, ten serwer jest uaktywniona. Poprzednie active serwer, który teraz ma starą wersję lub konfiguracji zainstalowane, staje się na serwerze i jest uaktualniany.

Dwa serwery, możesz użyć innej wersji. Na przykład aktywnego serwera, który planujesz zlikwidować można użyć usługi Azure AD Sync, a nowy serwer przemieszczania można używać usługi Azure AD Connect. Jeśli używasz migracji zasięg umożliwiające tworzenie nowej konfiguracji jest dobrym pomysłem jest mają takie same jak wersje na dwóch serwerach.  
![Serwer przemieszczania](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Niektórzy klienci wolą trzech lub czterech serwerów dla tego scenariusza. Po uaktualnieniu serwera przemieszczania nie masz kopii zapasowej serwera dla [odzyskiwania po awarii](active-directory-aadconnectsync-operations.md#disaster-recovery). Przy użyciu trzech lub czterech serwerów można przygotować jednego zestawu serwerów podstawowych/w gotowości w nowej wersji, który zapewnia, że jest zawsze serwerze tymczasowym, który jest gotowy do przejęcia.

Te kroki są również działać można przenieść z programu Azure AD Sync lub rozwiązania z FIM + Azure AD Connector. Te kroki nie działają narzędzia DirSync, ale tego samego zasięg migracji (nazywanych również wdrożenie równoległe) jest metoda o kroki dla narzędzia DirSync w [uaktualnienia usługi Azure Active Directory sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Użyj opcji migracji do uaktualnienia
1. Jeśli używasz usługi Azure AD Connect na obu serwerach i Zaplanuj tylko wprowadzanie zmian, upewnij się, że konfiguracja aktywnego serwera i serwerze są zarówno przy użyciu tej samej wersji. Który ułatwia to porównać później. Jeśli uaktualniasz z programu Azure AD Sync, te serwery mają różne wersje. Jeśli uaktualniasz ze starszej wersji programu Azure AD Connect, zaleca się uruchomić przy użyciu dwóch serwerów, które korzystają z tej samej wersji, ale nie jest to wymagane.
2. Jeśli wprowadzono niestandardowej konfiguracji tymczasowej serwera nie ma go, wykonaj kroki opisane w temacie [Przenieś Konfiguracja niestandardowa z aktywnego serwera na serwerze](#move-custom-configuration-from-active-to-staging-server).
3. Jeśli uaktualniasz z wcześniejszych wersji programu Azure AD Connect, należy uaktualnić serwer przemieszczania do najnowszej wersji. Jeśli przenosisz z narzędzia Azure AD Sync, zainstaluj Azure AD Connect na serwerze tymczasowe.
4. Let aparatu synchronizacji, uruchom na serwerze tymczasowe pełny import i pełną synchronizację.
5. Sprawdź, czy nowa konfiguracja nie spowodować nieoczekiwane zmiany przy użyciu kroki opisane w temacie "Zweryfikuj" w [Weryfikowanie konfiguracji serwera](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Jeśli coś nie jest zgodnie z oczekiwaniami, napraw go, uruchom importowania i synchronizacji oraz zweryfikować danych, dopóki wygląd, wykonując następujące kroki.
6. Przełącz przemieszczania serwera active server. To jest ostatni krok "Przełącznika active server" w [Weryfikowanie konfiguracji serwera](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Jeśli uaktualniasz Azure AD Connect, należy uaktualnić serwer, który jest teraz w trybie przejściowym do najnowszej wersji. Wykonaj te same czynności co przed można pobrać danych i konfiguracji uaktualniony. Po uaktualnieniu z programu Azure AD Sync, możesz teraz wyłączyć i zlikwidować stary serwer.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Przenieś Konfiguracja niestandardowa z aktywnego serwera na serwerze
Zostały wprowadzone zmiany konfiguracji do aktywnego serwera, należy upewnić się, że te same zmiany są stosowane na serwerze. Aby ułatwić to przeniesienie, można użyć [Dokumentatora konfiguracji usługi Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Można przenieść synchronizacji niestandardowych reguł został utworzony przy użyciu programu PowerShell. Należy najpierw zastosować inne zmiany tak samo we wszystkich systemach, i nie można migrować zmiany. [Dokumentatora konfiguracji](https://github.com/Microsoft/AADConnectConfigDocumenter) ułatwia porównanie tych dwóch systemów, aby się upewnić, że są one identyczne. Narzędzie może również pomóc w automatyzacji w tej sekcji.

Należy skonfigurować w taki sam sposób następujących czynności na obu serwerach:

* Połączenie do tego samego lasów
* Domeny i jednostki Organizacyjnej filtrowania
* Tej samej funkcji opcjonalnych, takich jak synchronizacja haseł i zapisywania zwrotnego haseł

**Przenoszenie reguł niestandardowych**  
Aby przenieść reguł niestandardowych, wykonaj następujące czynności:

1. Otwórz **Edytor reguł synchronizacji** aktywnego serwera.
2. Wybierz regułę niestandardową. Kliknij przycisk **wyeksportować**. Spowoduje to wyświetlenie okna Notatnika. Zapisz plik tymczasowy z rozszerzeniem nazwy PS1. Dzięki temu skrypt programu PowerShell. Skopiuj plik PS1 na serwerze.  
   ![Wyeksportowanie reguły synchronizacji](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. GUID łącznika różni się na serwerze i należy ją zmienić. Aby uzyskać identyfikator GUID, uruchom **Edytor reguł synchronizacji**, wybierz jedną z reguł out-of-box, które reprezentują ten sam system połączony, a następnie kliknij przycisk **wyeksportować**. Identyfikator GUID w pliku PS1 Zamień na identyfikator GUID z serwera przemieszczenia.
4. W wierszu programu PowerShell Uruchom plik PS1. Spowoduje to utworzenie reguły niestandardowe synchronizacji na serwerze.
5. Powtórz tę czynność dla wszystkich reguł niestandardowych.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Jak mają być odroczone pełną synchronizację po uaktualnieniu
Podczas uaktualniania w miejscu, mogą występować zmian wprowadzonych, które wymagają działań określonych synchronizacji (w tym kroki pełny Import i pełną synchronizację) do wykonania. Na przykład, wymagają zmian schematu łącznika **pełny import** wymagają zmian reguły synchronizacji krok i out-of-box **pełnej synchronizacji** krok ma być wykonywana na zainfekowanym łączników. Podczas uaktualniania, Azure AD Connect Określa, jakie działania synchronizacji są wymagane i zapisuje je jako *zastępuje*. W poniższych cyklu synchronizacji harmonogram synchronizacji przejmuje te zastąpienia i je wykonuje. Gdy przesłonięcie jest prawidłowo wykonane, zostanie ono usunięte.

Mogą wystąpić sytuacje, w których nie chcesz te zastąpienia została wykonana, natychmiast po uaktualnieniu. Na przykład masz wiele obiektów synchronizowanych i ma następujące kroki synchronizacji występuje po godzinach. Aby usunąć te zastąpienia:

1. Podczas uaktualniania **Usuń zaznaczenie pola wyboru** opcję **Uruchom proces synchronizacji, po zakończeniu konfiguracji**. To powoduje wyłączenie harmonogramu synchronizacji i zapobiega cykl synchronizacji miejsce automatycznie usunięcia zastąpień.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync01.png)

2. Po zakończeniu uaktualniania, uruchom następujące polecenie cmdlet, aby dowiedzieć się, jakie zastąpienia zostały dodane: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Zastąpienia są specyficzne dla łącznika. W poniższym przykładzie krok pełny Import i pełną synchronizację zostały dodane do obu lokalną AD Connector i łącznika usługi Azure AD.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync02.png)

3. Należy zanotować istniejących zastąpień, które zostały dodane.
   
4. Aby usunąć zastąpień dla pełny import i pełną synchronizację łącznika dowolnego, uruchom następujące polecenie cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Aby usunąć zastąpień dla wszystkich łączników, uruchom następujący skrypt programu PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Aby wznowić harmonogram, uruchom następujące polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Pamiętaj, aby wykonać kroki wymagane synchronizacji najwcześniejszą wygodne. Możesz ręcznie wykonać te czynności przy użyciu Menedżera usługi synchronizacji lub dodać zastąpienia powrotem przy użyciu polecenia cmdlet Set-ADSyncSchedulerConnectorOverride.

Aby dodać zastąpień dla pełny import i pełną synchronizację łącznika dowolnego, uruchom następujące polecenie cmdlet:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
