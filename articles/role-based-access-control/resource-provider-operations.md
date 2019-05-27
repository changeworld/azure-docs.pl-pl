---
title: Operacje dostawcy zasobów w usłudze Azure Resource Manager | Dokumentacja firmy Microsoft
description: Wyświetla operacje dostępne dla dostawcy zasobów usługi Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 07c1a726e33eb8287634b63ef2e309483c05c3f3
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962121"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operacje dostawcy zasobów w usłudze Azure Resource Manager

W tym artykule wymieniono operacje dostępne dla każdego dostawcy zasobów usługi Azure Resource Manager. Te operacje mogą być używane w [ról niestandardowych](custom-roles.md) zapewnienie szczegółową [kontroli dostępu opartej na rolach (RBAC)](overview.md) do zasobów platformy Azure. Operacja ciągi mają następujący format: `{Company}.{ProviderName}/{resourceType}/{action}`

Zawsze ewoluuje operacji dostawcy zasobów. Aby uzyskać najnowsze operacji, użyj [Get AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) lub [lista operacji dostawcy az](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.AAD/domainServices/delete | Delete Domain Service |
> | action | Microsoft.AAD/domainServices/oucontainer/delete | Usuwanie kontenera jednostki organizacyjnej |
> | action | Microsoft.AAD/domainServices/oucontainer/read | Odczytaj jednostki organizacyjnej kontenerów |
> | action | Microsoft.AAD/domainServices/oucontainer/write | Zapis kontenera jednostki organizacyjnej |
> | action | Microsoft.AAD/domainServices/read | Read Domain Services |
> | action | Microsoft.AAD/domainServices/ReplicaSets/delete | Usuń lokację klastra |
> | action | Microsoft.AAD/domainServices/ReplicaSets/read | Witryna klastra odczytu |
> | action | Microsoft.AAD/domainServices/ReplicaSets/write | Zapis witryna klastra |
> | action | Microsoft.AAD/domainServices/write | Write Domain Service |
> | action | Microsoft.AAD/locations/operationresults/read |  |
> | action | Microsoft.AAD/Operations/read |  |
> | action | Microsoft.AAD/register/action | Register Domain Service |
> | action | Microsoft.AAD/unregister/action | Unregister Domain Service |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.aadiam/diagnosticsettings/DELETE | Usuwanie ustawienia diagnostyczne |
> | action | microsoft.aadiam/diagnosticsettings/read | Odczytywanie ustawienie diagnostyczne |
> | action | microsoft.aadiam/diagnosticsettings/write | Zapisywanie ustawienia diagnostyczne |
> | action | microsoft.aadiam/diagnosticsettingscategories/read | Odczytywanie kategorii ustawienie diagnostyczne |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Addons/operations/read | Pobiera obsługiwane operacje jednostki Uzależnionej. |
> | action | Microsoft.Addons/register/action | Zarejestrowanie określonej subskrypcji Microsoft.Addons |
> | action | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Wyświetla listę bieżących informacji planu pomocy technicznej dotyczących określonej subskrypcji. |
> | action | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Usuwa planu określonego pomocy technicznej firmy Canonical |
> | action | Microsoft.Addons/supportProviders/supportPlanTypes/read | Pobierz stan planu określonego pomocy technicznej firmy Canonical. |
> | action | Microsoft.Addons/supportProviders/supportPlanTypes/write | Dodaje określony typ planu pomocy technicznej firmy Canonical. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ADHybridHealthService/addsservices/action | Utwórz nowy las dla dzierżawy. |
> | action | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Pobiera wszystkie serwery dla określonej nazwy usługi. |
> | action | Microsoft.ADHybridHealthService/addsservices/alerts/read | Pobiera szczegóły alertów dla lasu, takich jak alertid alertu zgłoszone Data, ostatni wykryte, alertu opis alertu, ostatniego alertu, zaktualizowaną poziomu, alertów stanu, alertów, rozwiązywanie problemów z łączami itp. |
> | action | Microsoft.ADHybridHealthService/addsservices/configuration/read | Pobiera konfigurację usługi w lesie. Przykład — nazwa lasu poziom funkcjonalności nazewnictwa domeny głównego roli FSMO, roli FSMO wzorca schematu itp. |
> | action | Microsoft.ADHybridHealthService/addsservices/delete | Usuwa usługę i jej serwery oraz dane dotyczące kondycji. |
> | action | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Pobiera szczegóły domen i lokacji w lesie. Stan kondycji przykładu, aktywne alerty rozwiązane alerty, właściwości, takie jak poziom funkcjonalności domeny, lasu, wzorzec infrastruktury, kontrolera PDC, etc wzorca RID.  |
> | action | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Pobiera ustawienia preferencji użytkownika dla tego lasu.<br>Przykład — MetricCounterName, takie jak ldapsuccessfulbinds ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Ustawienia interfejsu użytkownika wykresy itd. |
> | action | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Pobiera lasu podsumowania dla danego lasu, takie jak nazwa lasu, liczbę domen w tym lesie, liczbie lokacji oraz lokacji szczegóły itp. |
> | action | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Pobiera listę obsługiwanych metryk dla danej usługi.<br>Przykład ekstranetu blokady konta, łączna liczba nieudanych żądań oczekujących żądań tokenów (Proxy), żądań tokenów/s itp. usługi AD FS.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czas powiązania protokołu LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla ADDomainService.<br>Uruchom opóźnienie profilu, ustanowionych połączeń TCP, liczba bajtów prywatnych agentów szczegółowe informacje, Statystyka eksportu do usługi Azure AD dla usługi ADSync. |
> | action | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Biorąc pod uwagę usługę, ten interfejs API pobiera dane metryk.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | action | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Ten interfejs API pobiera listę wszystkich dołączono ADDomainServices dla dzierżawy w warstwie premium. |
> | action | Microsoft.ADHybridHealthService/addsservices/read | Pobiera szczegóły usługi dla określonej nazwy usługi. |
> | action | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Pobiera szczegóły replikacji dla wszystkich serwerów dla określonej nazwy usługi. |
> | action | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Pobiera liczbę kontrolerów domeny i ich błędy replikacji, jeśli istnieje. |
> | action | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Pobiera ukończyć listy kontrolerów domeny wraz ze szczegółami replikacji dla podanego lasu. |
> | action | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Dodanie wystąpienia serwera do usługi. |
> | action | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Podczas rejestracji serwera ADDomainService ten interfejs api jest wywoływana, aby uzyskać poświadczenia dla dołączania nowych serwerów. |
> | action | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Usuwa serwer dla danej usługi i dzierżawy. |
> | action | Microsoft.ADHybridHealthService/addsservices/write | Tworzy lub aktualizuje wystąpienia ADDomainService dla dzierżawy. |
> | action | Microsoft.ADHybridHealthService/configuration/action | Aktualizuje konfigurację dzierżawy. |
> | action | Microsoft.ADHybridHealthService/configuration/read | Odczytuje konfigurację dzierżawy. |
> | action | Microsoft.ADHybridHealthService/configuration/write | Tworzy konfigurację dzierżawy. |
> | action | Microsoft.ADHybridHealthService/logs/contents/read | Pobiera zawartość elementu instalacji agenta i dzienniki rejestracji, przechowywane w obiekcie blob. |
> | action | Microsoft.ADHybridHealthService/logs/read | Pobiera dzienniki rejestracji i Instalacja agenta dla dzierżawy. |
> | action | Microsoft.ADHybridHealthService/operations/read | Pobiera listę operacji obsługiwanych przez system. |
> | action | Microsoft.ADHybridHealthService/register/action | Rejestruje dostawcę zasobów usługi kondycji ADHybrid i włącza funkcję tworzenia zasobów ADHybrid kondycji usługi. |
> | action | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Pobiera listę dostępnych regionów, używane przez DevOps do odbiorcy zdarzeń pomocy technicznej. |
> | action | Microsoft.ADHybridHealthService/reports/badpassword/read | Pobiera listę nieudane próby wprowadzenia hasła dla wszystkich użytkowników w Active Directory Federation Services. |
> | action | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Pobiera identyfikator URI sygnatury dostępu Współdzielonego obiektów Blob zawierający stan i ostateczną wynikiem nowo dodawanych do kolejki zadania raportowania dla częstotliwości nieprawidłowo podawali nazwę użytkownika/hasło prób na identyfikator użytkownika na adres IP na dzień dla danej dzierżawy. |
> | action | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Pobiera listę DevOps wyraził zgodę dzierżaw. Zwykle używane przez dział pomocy technicznej. |
> | action | Microsoft.ADHybridHealthService/reports/isdevops/read | Pobiera wartość wskazującą, czy dzierżawcy jest wyraził zgodę metodyki DevOps, czy nie. |
> | action | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Aktualizuje userid(objectid) dla dzierżawy ops wybranego deweloperów. |
> | action | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Pobiera wybrane wdrożenie do danej dzierżawy. |
> | action | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Podany identyfikator dzierżawy pobiera dzierżawy lokalizacji magazynu. |
> | action | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Pobiera lokalizacji geograficznej, z którego będzie można uzyskać dostępu do danych. |
> | action | Microsoft.ADHybridHealthService/services/action | Aktualizuje wystąpienia usługi w dzierżawie. |
> | action | Microsoft.ADHybridHealthService/services/alerts/read | Odczytuje alerty dla usługi. |
> | action | Microsoft.ADHybridHealthService/services/alerts/read | Odczytuje alerty dla usługi. |
> | action | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Daną funkcję nazwy sprawdza, czy usługa ma wszystko, co jest wymagane, aby użyć tej funkcji. |
> | action | Microsoft.ADHybridHealthService/services/delete | Usuwa wystąpienie usługi w dzierżawie. |
> | action | Microsoft.ADHybridHealthService/services/exporterrors/read | Pobiera błędów eksportu dla usługi w danym synchronizacji. |
> | action | Microsoft.ADHybridHealthService/services/exportstatus/read | Pobiera stan eksportu dla danej usługi. |
> | action | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Pobiera alerty opinii dla danej usługi i serwera. |
> | action | Microsoft.ADHybridHealthService/services/metricmetadata/read | Pobiera listę obsługiwanych metryk dla danej usługi.<br>Przykład ekstranetu blokady konta, łączna liczba nieudanych żądań oczekujących żądań tokenów (Proxy), żądań tokenów/s itp. usługi AD FS.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czas powiązania protokołu LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla ADDomainService.<br>Uruchom opóźnienie profilu, ustanowionych połączeń TCP, liczba bajtów prywatnych agentów szczegółowe informacje, Statystyka eksportu do usługi Azure AD dla usługi ADSync. |
> | action | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Biorąc pod uwagę usługę, ten interfejs API pobiera średnia dla metryki dla danej usługi.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | action | Microsoft.ADHybridHealthService/services/metrics/groups/read | Biorąc pod uwagę usługę, ten interfejs API pobiera dane metryk.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | action | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Biorąc pod uwagę usługę, ten interfejs API pobiera zagregowany widok, aby uzyskać metryki dla danej usługi.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | action | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Dodawanie lub aktualizacji konfiguracji monitorowania dla usługi. |
> | action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Pobiera konfiguracji monitorowania dla danej usługi. |
> | action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Dodawanie lub aktualizacji konfiguracji monitorowania dla usługi. |
> | action | Microsoft.ADHybridHealthService/services/premiumcheck/read | Ten interfejs API pobiera listę wszystkich usług dołączono dla dzierżawy w warstwie premium. |
> | action | Microsoft.ADHybridHealthService/services/read | Odczytuje wystąpień usługi w dzierżawie. |
> | action | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Pobiera wszystkie raport ryzykownych adresów IP, identyfikatorów URI dla ostatnich 7 dni. |
> | action | Microsoft.ADHybridHealthService/services/reports/details/read | Raport pobiera 50 pierwszych użytkowników z błędami nieprawidłowego hasła z ostatnich 7 dni |
> | action | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Generuje raport ryzykownych adresów IP i zwraca identyfikator URI wskazujących na niego. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/action | Tworzy wystąpienie serwera w usłudze. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Odczytuje alerty dla serwera. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Podczas rejestracji serwera ten interfejs api jest wywoływana, aby uzyskać poświadczenia dla dołączania nowych serwerów. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Dla danego serwera ten interfejs API pobiera listę typów danych, które są przekazywane przez serwery i ostatni czas dla każdego przekazywania. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/delete | Usuwa wystąpienie serwera w usłudze. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Pobiera szczegóły błędu eksportowania synchronizacji dla danej usługi synchronizacji. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Biorąc pod uwagę usługę, ten interfejs API pobiera dane metryk.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Pobiera listę łączników i nazwy profilu uruchamiania dla danej usługi i element członkowski usługi. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/read | Odczytuje wystąpienie serwera w usłudze. |
> | action | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Pobiera konfigurację usługi dla danej dzierżawy. |
> | action | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Pobiera stan umieszczania na białej liście funkcji dla danej dzierżawy. |
> | action | Microsoft.ADHybridHealthService/services/write | Tworzy wystąpienie usługi w dzierżawie. |
> | action | Microsoft.ADHybridHealthService/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Service Health ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Advisor/configurations/read | Pobieranie konfiguracji |
> | action | Microsoft.Advisor/configurations/write | Tworzy/aktualizuje konfigurację |
> | action | Microsoft.Advisor/generateRecommendations/action | Wygeneruje zalecenia |
> | action | Microsoft.Advisor/generateRecommendations/read | Pobiera Generowanie zaleceń dotyczących stanu |
> | action | Microsoft.Advisor/operations/read | Pobiera operacje dla Advisor firmy Microsoft |
> | action | Microsoft.Advisor/recommendations/available/action | Nowe zalecenie jest dostępna w programie Microsoft advisor |
> | action | Microsoft.Advisor/recommendations/read | Odczytuje zalecenia |
> | action | Microsoft.Advisor/recommendations/suppressions/delete | Usuwa pominięć |
> | action | Microsoft.Advisor/recommendations/suppressions/read | Pobiera pominięć |
> | action | Microsoft.Advisor/recommendations/suppressions/write | Tworzy aktualizacje pominięć |
> | action | Microsoft.Advisor/register/action | Rejestruje subskrypcję usługi Advisor firmy Microsoft |
> | action | Microsoft.Advisor/suppressions/delete | Usuwa pominięć |
> | action | Microsoft.Advisor/suppressions/read | Pobiera pominięć |
> | action | Microsoft.Advisor/suppressions/write | Tworzy aktualizacje pominięć |
> | action | Microsoft.Advisor/unregister/action | Wyrejestrowuje subskrypcję usługi Advisor firmy Microsoft |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.AlertsManagement/actionRules/delete | Usuń regułę akcji w ramach danej subskrypcji. |
> | action | Microsoft.AlertsManagement/actionRules/read | Pobierz wszystkie reguły akcji dla filtrów wejściowych. |
> | action | Microsoft.AlertsManagement/actionRules/write | Utwórz lub zaktualizuj reguły akcji w ramach danej subskrypcji |
> | action | Microsoft.AlertsManagement/alerts/changestate/action | Zmień stan alertu. |
> | action | Microsoft.AlertsManagement/alerts/diagnostics/read | Pobierz wszystkie diagnostyki dla alertu |
> | action | Microsoft.AlertsManagement/alerts/history/read | Pobieranie historii alertu |
> | action | Microsoft.AlertsManagement/alerts/read | Pobierz wszystkie alerty dla danych wejściowych filtrów. |
> | action | Microsoft.AlertsManagement/alertsList/read | Pobierz wszystkie alerty dla danych wejściowych filtrów między subskrypcjami |
> | action | Microsoft.AlertsManagement/alertsSummary/read | Pobierz podsumowanie alertów |
> | action | Microsoft.AlertsManagement/alertsSummaryList/read | Pobierz podsumowanie alertów w subskrypcjach |
> | action | Microsoft.AlertsManagement/Operations/read | Odczytuje operacji podano |
> | action | Microsoft.AlertsManagement/register/action | Rejestruje subskrypcję usługi Microsoft zarządzania alertami |
> | action | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Usuń regułę alertów wykrywanie inteligentne w ramach danej subskrypcji |
> | action | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Pobieranie wszystkich reguł alertów inteligentne wykrywanie filtry wejściowe |
> | action | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Utwórz lub zaktualizuj regułę alertu wykrywanie inteligentne w ramach danej subskrypcji |
> | action | Microsoft.AlertsManagement/smartGroups/changestate/action | Zmień stan grupy inteligentne |
> | action | Microsoft.AlertsManagement/smartGroups/history/read | Pobieranie historii grupy inteligentne |
> | action | Microsoft.AlertsManagement/smartGroups/read | Pobierz wszystkie inteligentne grupy dla danych wejściowych filtrów |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Sprawdza, czy podany serwer Analysis Server Nazwa jest prawidłowa i nie jest używany. |
> | action | Microsoft.AnalysisServices/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | action | Microsoft.AnalysisServices/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | action | Microsoft.AnalysisServices/operations/read | Pobiera informacje o operacjach |
> | action | Microsoft.AnalysisServices/register/action | Rejestruje dostawcę zasobów usług Analysis Services. |
> | action | Microsoft.AnalysisServices/servers/delete | Usuwa serwer Analysis Server. |
> | action | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Wyświetl stan bramy skojarzonej z serwerem. |
> | action | Microsoft.AnalysisServices/servers/read | Pobiera informacje o konkretnym serwerze Analysis Server. |
> | action | Microsoft.AnalysisServices/servers/resume/action | Wznawia działanie serwera Analysis Server. |
> | action | Microsoft.AnalysisServices/servers/skus/read | Pobierz dostępne informacje o jednostkach SKU dla serwera |
> | action | Microsoft.AnalysisServices/servers/suspend/action | Wstrzymuje działanie serwera Analysis Server. |
> | action | Microsoft.AnalysisServices/servers/write | Tworzy lub aktualizuje konkretny serwer Analysis Server. |
> | action | Microsoft.AnalysisServices/skus/read | Pobiera informacje o jednostkach SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ApiManagement/checkNameAvailability/read | Sprawdza, czy pod warunkiem, że nazwa usługi jest dostępny |
> | action | Microsoft.ApiManagement/operations/read | Odczytywać wszystkie operacje interfejsu API dostępne dla zasobu Microsoft.ApiManagement |
> | action | Microsoft.ApiManagement/register/action | Rejestruj subskrypcję dostawcy zasobów Microsoft.ApiManagement |
> | action | Microsoft.ApiManagement/reports/read | Pobierz raporty agregowane przez okresy czasu, regionu geograficznego, deweloperów, produktów, interfejsów API, operacje, subskrypcji i byRequest. |
> | action | Microsoft.ApiManagement/service/apis/delete | Usuwa określony interfejs API wystąpienia usługi API Management. |
> | action | Microsoft.ApiManagement/service/apis/diagnostics/delete | Usuwa określony diagnostyczne z interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/diagnostics/read | Wyświetla listę wszystkich diagnostyki interfejsu API. lub pobiera szczegóły diagnostyczne dla interfejsu API, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apis/diagnostics/write | Tworzy nowy diagnostyczne dla interfejsu API lub aktualizuje istniejący zestaw. lub aktualizacje szczegóły diagnostyczne dla interfejsu API określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Usuwa określony komentarz z problemu. |
> | action | Microsoft.ApiManagement/service/apis/issues/attachments/read | Wyświetla wszystkie załączniki dla wydania skojarzone z określonego interfejsu API. lub pobiera szczegóły problemu załącznika dla interfejsu API, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apis/issues/attachments/write | Tworzy nowy załącznik do problemu w interfejsie API, lub aktualizuje istniejący zestaw. |
> | action | Microsoft.ApiManagement/service/apis/issues/comments/delete | Usuwa określony komentarz z problemu. |
> | action | Microsoft.ApiManagement/service/apis/issues/comments/read | Wyświetla wszystkie komentarze do wydania skojarzone z określonego interfejsu API. lub pobiera szczegóły problemu komentarz dotyczący interfejsu API, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apis/issues/comments/write | Tworzy nowego komentarza dotyczącego problemu w interfejsie API, lub aktualizuje istniejący zestaw. |
> | action | Microsoft.ApiManagement/service/apis/issues/delete | Usuwa określony problem z interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/issues/read | Wyświetla listę wszystkich problemów skojarzonych z określonym interfejsem API. lub pobiera szczegóły problemu dla interfejsu API, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apis/issues/write | Tworzy nowy problem dla interfejsu API lub aktualizuje istniejący zestaw. lub aktualizacji istniejącej wydania dla interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/operations/delete | Usuwa określonej operacji interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/operations/policies/delete | Usuwa konfigurację zasad w operacji interfejsu Api. |
> | action | Microsoft.ApiManagement/service/apis/operations/policies/read | Pobierz listę konfiguracji zasad na poziomie operacji interfejsu API. lub Konfiguracja zasad na poziomie operacji interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/operations/policies/write | Tworzy lub aktualizuje konfigurację zasad na poziomie operacji interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/operations/policy/delete | Usuwanie konfiguracji zasad na poziomie operacji |
> | action | Microsoft.ApiManagement/service/apis/operations/policy/read | Pobierz konfigurację zasad na poziomie operacji |
> | action | Microsoft.ApiManagement/service/apis/operations/policy/write | Tworzenie konfiguracji zasad na na operację |
> | action | Microsoft.ApiManagement/service/apis/operations/read | Wyświetla listę to kolekcja operacji dla określonego interfejsu API. lub pobiera szczegóły operacji interfejsu API, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apis/operations/tags/delete | Odłącz tag z działania. |
> | action | Microsoft.ApiManagement/service/apis/operations/tags/read | Wyświetla wszystkie znaczniki skojarzone z operacją. lub Pobierz tag skojarzone z operacją. |
> | action | Microsoft.ApiManagement/service/apis/operations/tags/write | Przypisywanie tagów do operacji. |
> | action | Microsoft.ApiManagement/service/apis/operations/write | Tworzy nową operację w interfejsie API, lub aktualizuje istniejący zestaw. lub aktualizacje szczegóły operacji w interfejsie API określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apis/operationsByTags/read | Zawiera kolekcję operacji skojarzonych z tagami. |
> | action | Microsoft.ApiManagement/service/apis/policies/delete | Usuwa konfigurację zasad w interfejsie Api. |
> | action | Microsoft.ApiManagement/service/apis/policies/read | Pobierz konfigurację zasad na poziomie interfejsu API. lub Konfiguracja zasad na poziomie interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/policies/write | Tworzy lub aktualizuje konfigurację zasad interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/policy/delete | Usuwanie konfiguracji zasad na poziomie interfejsu API |
> | action | Microsoft.ApiManagement/service/apis/policy/read | Pobierz konfigurację zasad na poziomie interfejsu API |
> | action | Microsoft.ApiManagement/service/apis/policy/write | Tworzenie konfiguracji zasad na na interfejs API |
> | action | Microsoft.ApiManagement/service/apis/products/read | Wyświetla listę wszystkich produktów, które interfejs API jest częścią. |
> | action | Microsoft.ApiManagement/service/apis/read | Wyświetla listę wszystkich interfejsów API wystąpienia usługi API Management. lub pobiera szczegółowe informacje o interfejsie API, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apis/releases/delete | Usuwa wszystkie wersje interfejsu API lub usuwa z określonej wersji w interfejsie API. |
> | action | Microsoft.ApiManagement/service/apis/releases/read | Wyświetla listę wszystkich wersji interfejsu API.<br>Wersja interfejsu API jest tworzony podczas wykonywania bieżącej poprawki interfejsu API.<br>Wersje są również używane do wycofania do poprzedniej poprawki.<br>Wyniki będą stronicowane i może być ograniczona przez parametry $top i $skip.<br>lub zwraca szczegółowe informacje o interfejs API wersji. |
> | action | Microsoft.ApiManagement/service/apis/releases/write | Tworzy nową wersję interfejsu API. lub aktualizacje, szczegółowe informacje o wersji interfejsu API określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apis/revisions/delete | Usuwa wszystkie poprawki interfejsu API |
> | action | Microsoft.ApiManagement/service/apis/revisions/read | Wyświetla wszystkie poprawki interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/schemas/delete | Usuwa konfigurację schematu w interfejsie Api. |
> | action | Microsoft.ApiManagement/service/apis/schemas/read | Uzyskaj konfiguracją w schemacie na poziomie interfejsu API. lub konfiguracją w schemacie na poziomie interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/schemas/write | Tworzy lub aktualizuje schemat konfiguracji dla interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Usuń tag opis interfejsu API. |
> | action | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Wyświetla wszystkie opisy tagów w zakresie interfejsu API. Model podobne do struktury swagger — tagDescription jest zdefiniowana na poziomie interfejsu API, ale tagu może być przypisana do operacji lub pobrać opisu tagu w zakresie interfejsu API |
> | action | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Utwórz/zaktualizuj opis tagu w zakresie interfejsu Api. |
> | action | Microsoft.ApiManagement/service/apis/tags/delete | Odłącz tag z interfejsu Api. |
> | action | Microsoft.ApiManagement/service/apis/tags/read | Wyświetla wszystkie znaczniki skojarzone z interfejsem API. lub Pobierz tag skojarzonych z interfejsem API. |
> | action | Microsoft.ApiManagement/service/apis/tags/write | Przypisywanie tagów do interfejsu Api. |
> | action | Microsoft.ApiManagement/service/apis/write | Nowe tworzy lub aktualizuje istniejące API określonego wystąpienia usługi API Management. lub aktualizacji interfejsu API określonego wystąpienia usługi API Management. |
> | action | Microsoft.ApiManagement/service/apisByTags/read | Wyświetla listę zbiór interfejsów API skojarzone z tagami. |
> | action | Microsoft.ApiManagement/service/apiVersionSets/delete | Usuwa określone ustawiona wersja interfejsu Api. |
> | action | Microsoft.ApiManagement/service/apiVersionSets/read | Zawiera kolekcję zestawów wersji interfejsu API w wystąpieniu określonej usługi. lub pobiera szczegółowe informacje o wersji interfejsu Api zestawu określonego przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Pobierz listę jednostek wersji |
> | action | Microsoft.ApiManagement/service/apiVersionSets/write | Tworzy lub aktualizuje zestaw wersji interfejsu Api. lub aktualizacje szczegóły VersionSet interfejsu Api, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Aktualizuje zasoby Microsoft.ApiManagement działać w sieci wirtualnej, aby pobrać zaktualizowane ustawienia sieciowe. |
> | action | Microsoft.ApiManagement/service/authorizationServers/delete | Usuwa wystąpienie serwera określonego autoryzacji. |
> | action | Microsoft.ApiManagement/service/authorizationServers/read | Wyświetla listę kolekcji serwerów autoryzacji zdefiniowane w ramach wystąpienia usługi. lub pobiera szczegóły serwera autoryzacji, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/authorizationServers/write | Tworzy nowy serwer autoryzacji lub aktualizuje istniejący serwer autoryzacji. lub aktualizacje szczegóły serwera autoryzacji, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/backends/delete | Usuwa określony wewnętrznej bazy danych. |
> | action | Microsoft.ApiManagement/service/backends/read | Zawiera kolekcję zapleczy w wystąpieniu określonej usługi. lub pobiera szczegółowe informacje o wewnętrznej bazy danych określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/backends/reconnect/action | Powiadamia serwera proxy usługi APIM utworzyć nowe połączenie z zapleczem usługi po określonym czasie. Jeśli nie określono limitu czasu, limit czasu równy 2 minuty jest używany. |
> | action | Microsoft.ApiManagement/service/backends/write | Tworzy lub aktualizuje zaplecza. lub aktualizuje istniejącego zaplecza. |
> | action | Microsoft.ApiManagement/service/backup/action | Kopii zapasowych usługi API Management do określonego kontenera w użytkownika podane konto magazynu |
> | action | Microsoft.ApiManagement/service/caches/delete | Usuwa określonych pamięci podręcznej. |
> | action | Microsoft.ApiManagement/service/caches/read | Zawiera zbiór wszystkich zewnętrznych pamięci podręcznych w wystąpieniu określonej usługi. lub pobiera szczegółowe informacje o pamięci podręcznej, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/caches/write | Tworzy lub aktualizuje zewnętrzna pamięć podręczna, które zostaną użyte w wystąpienia usługi Api Management. lub aktualizacje szczegółowe informacje o pamięci podręcznej, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/certificates/delete | Usuwa określonego certyfikatu. |
> | action | Microsoft.ApiManagement/service/certificates/read | Wyświetla listę kolekcji wszystkie certyfikaty w wystąpieniu określonej usługi. lub pobiera szczegóły certyfikatu określonego przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/certificates/write | Tworzy lub aktualizuje certyfikat używany do uwierzytelniania przy użyciu wewnętrznej bazy danych. |
> | action | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Usuwa określony element zawartości. |
> | action | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Zwraca listę elementów zawartości lub zwraca szczegóły elementu zawartości |
> | action | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Tworzy nowy element zawartości lub aktualizuje określony element zawartości |
> | action | Microsoft.ApiManagement/service/contentTypes/read | Zwraca listę typów zawartości |
> | action | Microsoft.ApiManagement/service/delete | Usuń wystąpienie usługi API Management |
> | action | Microsoft.ApiManagement/service/diagnostics/delete | Usuwa określony diagnostyki. |
> | action | Microsoft.ApiManagement/service/diagnostics/read | Wyświetla listę wszystkich diagnostykę wystąpienia usługi API Management. lub pobiera szczegóły diagnostyczne określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/diagnostics/write | Tworzy nowy diagnostyki lub aktualizuje istniejący zestaw. lub aktualizacje szczegóły diagnostyczne określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/getssotoken/action | Pobiera token logowania jednokrotnego, który może służyć do logowania do interfejsu API zarządzania usługi w starszej wersji portalu jako administrator |
> | action | Microsoft.ApiManagement/service/groups/delete | Usuwa określoną grupę wystąpienia usługi API Management. |
> | action | Microsoft.ApiManagement/service/groups/read | Zawiera kolekcję grup zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły grupy określonej przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/groups/users/delete | Usuwanie istniejącego użytkownika z istniejącej grupy. |
> | action | Microsoft.ApiManagement/service/groups/users/read | Zawiera kolekcję obiektów użytkownika skojarzone z grupą. |
> | action | Microsoft.ApiManagement/service/groups/users/write | Dodawanie istniejącego użytkownika do istniejącej grupy |
> | action | Microsoft.ApiManagement/service/groups/write | Tworzy lub aktualizuje grupę. lub aktualizacje szczegóły grupy określonej przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/identityProviders/delete | Usuwa konfigurację dostawcy określonej tożsamości. |
> | action | Microsoft.ApiManagement/service/identityProviders/read | Wyświetla listę kolekcji przez dostawcę tożsamości, które skonfigurowano w wystąpieniu określonej usługi. lub pobiera szczegóły konfiguracji dostawcy tożsamości zostały skonfigurowane w wystąpieniu określonej usługi. |
> | action | Microsoft.ApiManagement/service/identityProviders/write | Tworzy lub aktualizuje konfigurację dostawca tożsamości. lub aktualizuje istniejącą konfigurację dostawca tożsamości. |
> | action | Microsoft.ApiManagement/service/issues/read | Wyświetla listę kolekcji problemów w wystąpieniu określonej usługi. lub szczegółowe informacje o problemie pobiera z usługi API Management |
> | action | Microsoft.ApiManagement/service/locations/networkstatus/read | Pobiera stan dostępu do sieci, zasobów, od których zależy od usługi, w tym miejscu. |
> | action | Microsoft.ApiManagement/service/loggers/delete | Usuwa określonego rejestratora. |
> | action | Microsoft.ApiManagement/service/loggers/read | Zawiera kolekcję rejestratorów w wystąpieniu określonej usługi. lub pobiera szczegóły rejestratora określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/loggers/write | Tworzy lub aktualizuje rejestrator. lub aktualizuje istniejące rejestratora. |
> | action | Microsoft.ApiManagement/service/managedeployments/action | Zmiana jednostki SKU/jednostek, dodawać i usuwać regionalnych wdrożeń usługi API Management |
> | action | Microsoft.ApiManagement/service/networkstatus/read | Pobiera stan dostępu do sieci, zasobów, od których zależy usługa. |
> | action | Microsoft.ApiManagement/service/notifications/action | Wysyła powiadomienie do określonego użytkownika |
> | action | Microsoft.ApiManagement/service/notifications/read | Zawiera kolekcję właściwości zdefiniowane w ramach wystąpienia usługi. lub pobiera szczegóły powiadomienia, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Usuwa wiadomości e-mail z listy powiadomień. |
> | action | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Pobiera listę adresatów wiadomości E-mail z powiadomieniem subskrypcję powiadomienia. |
> | action | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Dodaje adres E-mail do listy adresatów powiadomienia. |
> | action | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Usuwa użytkownika usługi API Management z listy powiadomień. |
> | action | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Pobiera listę użytkowników adresata powiadomień subskrypcję powiadomienia. |
> | action | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Dodaje użytkownika zarządzania interfejsu API do listy adresatów powiadomienia. |
> | action | Microsoft.ApiManagement/service/notifications/write | Tworzenie lub powiadomienia o wydawcy usługi API Management Update. |
> | action | Microsoft.ApiManagement/service/openidConnectProviders/delete | Usuwa określonego dostawcy OpenID Connect wystąpienia usługi API Management. |
> | action | Microsoft.ApiManagement/service/openidConnectProviders/read | Wyświetla listę wszystkich dostawców uwierzytelniania OpenId Connect. lub pobiera określonego dostawcy OpenID Connect. |
> | action | Microsoft.ApiManagement/service/openidConnectProviders/write | Tworzy lub aktualizuje dostawcy OpenID Connect. lub aktualizacje określonego dostawcy OpenID Connect. |
> | action | Microsoft.ApiManagement/service/operationresults/read | Pobiera bieżący stan długotrwałej operacji |
> | action | Microsoft.ApiManagement/service/policies/delete | Usuwa konfigurację globalnych zasad usługi Api Management. |
> | action | Microsoft.ApiManagement/service/policies/read | Wyświetla listę wszystkich definicji globalnych zasad usługi Api Management. lub Pobierz definicję globalnych zasad usługi Api Management. |
> | action | Microsoft.ApiManagement/service/policies/write | Tworzy lub aktualizuje konfigurację globalnych zasad usługi Api Management. |
> | action | Microsoft.ApiManagement/service/policy/delete | Usuwanie konfiguracji zasad na poziomie dzierżawy |
> | action | Microsoft.ApiManagement/service/policy/read | Pobierz konfigurację zasad na poziomie dzierżawy |
> | action | Microsoft.ApiManagement/service/policy/write | Tworzenie konfiguracji zasad na w dzierżawie |
> | action | Microsoft.ApiManagement/service/policySnippets/read | Wyświetla wszystkie fragmenty kodu zasad. |
> | action | Microsoft.ApiManagement/service/portalsettings/read | Uzyskaj logowania w ustawieniach portalu lub logowania Odbierz Konfigurowanie ustawień portalu lub uzyskać ustawienia delegowania dla portalu. |
> | action | Microsoft.ApiManagement/service/portalsettings/write | Aktualizowanie ustawień logowania. lub ustawienia tworzenia lub aktualizacji logowania. lub ustawienia aktualizacji Zarejestruj lub aktualizacji Zarejestruj ustawień lub ustawień delegowania aktualizacji. lub ustawienia tworzenia lub aktualizacji delegowania. |
> | action | Microsoft.ApiManagement/service/products/apis/delete | Usuwa określony interfejs API z określonego produktu. |
> | action | Microsoft.ApiManagement/service/products/apis/read | Listy to zbiór interfejsów API skojarzone z produktem. |
> | action | Microsoft.ApiManagement/service/products/apis/write | Dodaje interfejs API do określonego produktu. |
> | action | Microsoft.ApiManagement/service/products/delete | Usuń produkt. |
> | action | Microsoft.ApiManagement/service/products/groups/delete | Usuwa skojarzenie między określonej grupy i produkt. |
> | action | Microsoft.ApiManagement/service/products/groups/read | Zawiera kolekcję grup dla deweloperów, które skojarzony z określonym produktem. |
> | action | Microsoft.ApiManagement/service/products/groups/write | Dodaje skojarzenie między grupą deweloperów określony za pomocą określonego produktu. |
> | action | Microsoft.ApiManagement/service/products/policies/delete | Usuwa konfigurację zasad w produkcie. |
> | action | Microsoft.ApiManagement/service/products/policies/read | Pobierz konfigurację zasad na poziomie produktu. lub Konfiguracja zasad na poziomie produktu. |
> | action | Microsoft.ApiManagement/service/products/policies/write | Tworzy lub aktualizuje konfigurację zasad dla produktu. |
> | action | Microsoft.ApiManagement/service/products/policy/delete | Usuwanie konfiguracji zasad na poziomie produktu |
> | action | Microsoft.ApiManagement/service/products/policy/read | Pobierz konfigurację zasad na poziomie produktu |
> | action | Microsoft.ApiManagement/service/products/policy/write | Tworzenie konfiguracji zasad na produktu |
> | action | Microsoft.ApiManagement/service/products/read | Wyświetla listę kolekcji produktów w wystąpieniu określonej usługi. lub pobiera szczegóły produktu, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/products/subscriptions/read | Wyświetla listę kolekcji w subskrypcji określonego produktu. |
> | action | Microsoft.ApiManagement/service/products/tags/delete | Odłącz tag z produktu. |
> | action | Microsoft.ApiManagement/service/products/tags/read | Wyświetla wszystkie znaczniki skojarzone z produktem. lub Pobierz tag skojarzony z produktem. |
> | action | Microsoft.ApiManagement/service/products/tags/write | Przypisywanie tagu produktu. |
> | action | Microsoft.ApiManagement/service/products/write | Tworzy lub aktualizuje produktu. lub zaktualizować istniejące szczegółowe informacje. |
> | action | Microsoft.ApiManagement/service/productsByTags/read | Zawiera kolekcję produkty powiązane z tagami. |
> | action | Microsoft.ApiManagement/service/properties/delete | Usuwa określoną właściwość z wystąpienia usługi API Management. |
> | action | Microsoft.ApiManagement/service/properties/read | Zawiera kolekcję właściwości zdefiniowane w ramach wystąpienia usługi. lub pobiera szczegółowe informacje o właściwości określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/properties/write | Tworzy lub aktualizuje właściwości. lub aktualizuje określoną właściwość. |
> | action | Microsoft.ApiManagement/service/quotas/periods/read | Pobierz wartość licznika limitu przydziału dla okresu |
> | action | Microsoft.ApiManagement/service/quotas/periods/write | Ustaw bieżącą wartość licznika limitu przydziału |
> | action | Microsoft.ApiManagement/service/quotas/read | Pobieranie wartości do limitu przydziału |
> | action | Microsoft.ApiManagement/service/quotas/write | Ustaw bieżącą wartość licznika limitu przydziału |
> | action | Microsoft.ApiManagement/service/read | Odczytywanie metadanych dla wystąpienia usługi API Management |
> | action | Microsoft.ApiManagement/service/regions/read | Wyświetla listę wszystkich regionach platformy azure, w których usługa istnieje. |
> | action | Microsoft.ApiManagement/service/reports/read | Pobierz raport agregowane przez okresów lub Pobierz raport agregowane według regionu geograficznego lub raport Get agregowane przez deweloperów.<br>lub Pobierz raport agregowane przez produktów.<br>lub Pobierz raport agregowane przez interfejsy API lub Pobierz raport agregowane przez operacje lub Pobierz raport agregowane według subskrypcji.<br>lub Pobierz żądań danych raportowania |
> | action | Microsoft.ApiManagement/service/restore/action | Przywracanie usługi API Management z określonego kontenera użytkownika podane konto magazynu |
> | action | Microsoft.ApiManagement/service/subscriptions/delete | Usuwa określoną subskrypcję. |
> | action | Microsoft.ApiManagement/service/subscriptions/read | Wyświetla listę wszystkich subskrypcji wystąpienia usługi API Management. lub pobiera określonej jednostki subskrypcji. |
> | action | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Generuje ponownie klucz podstawowy istniejącej subskrypcji wystąpienia usługi API Management. |
> | action | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Generuje ponownie klucz pomocniczy dla istniejącej subskrypcji wystąpienia usługi API Management. |
> | action | Microsoft.ApiManagement/service/subscriptions/write | Tworzy lub aktualizuje subskrypcji określonego użytkownika z określonym produktem. lub aktualizacje Szczegóły subskrypcji, określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/tagResources/read | Zawiera kolekcję zasobów skojarzonych z tagami. |
> | action | Microsoft.ApiManagement/service/tags/delete | Usuwa określony tag wystąpienia usługi API Management. |
> | action | Microsoft.ApiManagement/service/tags/read | Zawiera kolekcję tagów zdefiniowany w ramach wystąpienia usługi. lub pobiera Szczegóły tagu określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/tags/write | Tworzy tag. lub aktualizacje Szczegóły tagu określony przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/templates/delete | Resetuj domyślny szablon wiadomości e-mail usługi API Management |
> | action | Microsoft.ApiManagement/service/templates/read | Pobiera wszystkie szablony wiadomości e-mail lub wiadomości e-mail są pobierane z usługi API Management Szczegóły szablonu |
> | action | Microsoft.ApiManagement/service/templates/write | Tworzenie lub aktualizowanie szablonu wiadomości e-mail usługi API Management lub aktualizacji usługi API Management, szablon wiadomości e-mail |
> | action | Microsoft.ApiManagement/service/tenant/delete | Usuń konfigurację zasad dla dzierżawy |
> | action | Microsoft.ApiManagement/service/tenant/deploy/action | Uruchamia zadanie wdrożenia, aby zastosować zmiany z gałęzi git określony w konfiguracji w bazie danych. |
> | action | Microsoft.ApiManagement/service/tenant/operationResults/read | Pobierz listę wyników operacji lub Pobierz wyniki operacji |
> | action | Microsoft.ApiManagement/service/tenant/read | Uzyskaj definicję globalnych zasad usługi Api Management. lub Pobierz dzierżawy dostęp do informacji o szczegóły |
> | action | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Ponowne generowanie podstawowego klucza dostępu |
> | action | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Wygeneruj ponownie pomocniczy klucz dostępu |
> | action | Microsoft.ApiManagement/service/tenant/save/action | Tworzy zatwierdzenie z migawki konfiguracji w określonej gałęzi w repozytorium |
> | action | Microsoft.ApiManagement/service/tenant/syncState/read | Pobierz stan ostatniej synchronizacji narzędzia git |
> | action | Microsoft.ApiManagement/service/tenant/validate/action | Sprawdza poprawność zmian z gałęzi określonej usługi git |
> | action | Microsoft.ApiManagement/service/tenant/write | Ustawienia zasad konfiguracji dla dzierżawy lub szczegółowe informacje dotyczące aktualizacji dzierżawy dostępu |
> | action | Microsoft.ApiManagement/service/updatecertificate/action | Przekaż certyfikat SSL dla usługi API Management |
> | action | Microsoft.ApiManagement/service/updatehostname/action | Konfigurowanie, zaktualizować lub usunąć niestandardowe nazwy domen dla usługi API Management |
> | action | Microsoft.ApiManagement/service/users/action | Rejestrowanie nowego użytkownika |
> | action | Microsoft.ApiManagement/service/users/confirmations/send/action | Wysyła potwierdzenie z |
> | action | Microsoft.ApiManagement/service/users/delete | Usuwa określonego użytkownika. |
> | action | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Pobiera adres URL przekierowania zawierający token uwierzytelniania dla danego użytkownika logowania się do portalu dla deweloperów. |
> | action | Microsoft.ApiManagement/service/users/groups/read | Wyświetla listę wszystkich grup użytkowników. |
> | action | Microsoft.ApiManagement/service/users/identities/read | Lista wszystkich tożsamości użytkownika. |
> | action | Microsoft.ApiManagement/service/users/keys/read | Pobierz klucze skojarzone z użytkownikiem |
> | action | Microsoft.ApiManagement/service/users/read | Zawiera kolekcję zarejestrowanych użytkowników w wystąpieniu określonej usługi. lub pobiera szczegóły użytkownika określonego przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/users/subscriptions/read | Wyświetla listę kolekcji w subskrypcji określonego użytkownika. |
> | action | Microsoft.ApiManagement/service/users/token/action | Pobiera Token autoryzacji dostępu współdzielonego dla użytkownika. |
> | action | Microsoft.ApiManagement/service/users/write | Tworzy lub aktualizuje użytkownika. lub aktualizacje szczegóły użytkownika określonego przez jego identyfikator. |
> | action | Microsoft.ApiManagement/service/write | Utwórz nowe wystąpienie usługi API Management |
> | action | Microsoft.ApiManagement/unregister/action | Wyrejestruj subskrypcję dostawcy zasobów Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Authorization/classicAdministrators/delete | Usuwa administratora z subskrypcji. |
> | action | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Pobiera stany operacji administratora dla subskrypcji. |
> | action | Microsoft.Authorization/classicAdministrators/read | Odczytuje administratorów dla subskrypcji. |
> | action | Microsoft.Authorization/classicAdministrators/write | Dodaje lub modyfikuje administratora dla subskrypcji. |
> | action | Microsoft.Authorization/denyAssignments/delete | Usuń przypisanie odmowy w podanym zakresie. |
> | action | Microsoft.Authorization/denyAssignments/read | Pobierz informacje o przypisaniu odmowy. |
> | action | Microsoft.Authorization/denyAssignments/write | Utwórz przypisanie odmowy w podanym zakresie. |
> | action | Microsoft.Authorization/elevateAccess/action | Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy |
> | action | Microsoft.Authorization/locks/delete | Usuwa blokady w podanym zakresie. |
> | action | Microsoft.Authorization/locks/read | Pobiera blokady w podanym zakresie. |
> | action | Microsoft.Authorization/locks/write | Dodaje blokady w podanym zakresie. |
> | action | Microsoft.Authorization/operations/read | Pobiera listę operacji |
> | action | Microsoft.Authorization/permissions/read | Wyświetla wszystkie uprawnienia obiektu wywołującego w danym zakresie. |
> | action | Microsoft.Authorization/policyAssignments/delete | Usuń przypisanie zasad w podanym zakresie. |
> | action | Microsoft.Authorization/policyAssignments/read | Pobierz informacje o przypisaniu zasad. |
> | action | Microsoft.Authorization/policyAssignments/write | Utwórz przypisanie zasad w podanym zakresie. |
> | action | Microsoft.Authorization/policyDefinitions/delete | Usuń definicję zasad. |
> | action | Microsoft.Authorization/policyDefinitions/read | Pobierz informacje o definicji zasad. |
> | action | Microsoft.Authorization/policyDefinitions/write | Utwórz niestandardową definicję zasad. |
> | action | Microsoft.Authorization/policySetDefinitions/delete | Usuń definicję zestawu zasad. |
> | action | Microsoft.Authorization/policySetDefinitions/read | Pobierz informacje o definicji zestawu zasad. |
> | action | Microsoft.Authorization/policySetDefinitions/write | Utwórz niestandardową definicję zestawu zasad. |
> | action | Microsoft.Authorization/providerOperations/read | Pobiera operacje dla wszystkich dostawców zasobów do użycia w definicjach ról. |
> | action | Microsoft.Authorization/roleAssignments/delete | Usuwa przypisanie roli w podanym zakresie. |
> | action | Microsoft.Authorization/roleAssignments/read | Pobiera informacje o przypisaniu roli. |
> | action | Microsoft.Authorization/roleAssignments/write | Tworzy przypisanie roli w podanym zakresie. |
> | action | Microsoft.Authorization/roleDefinitions/delete | Usuwa określoną niestandardową definicję roli. |
> | action | Microsoft.Authorization/roleDefinitions/read | Pobiera informacje o definicji roli. |
> | action | Microsoft.Authorization/roleDefinitions/write | Tworzy lub aktualizuje niestandardową definicję roli z określonymi uprawnieniami i zakresami możliwymi do przypisania. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Przeczytaj informacje o rejestracji usługi Azure Automation DSC firmy |
> | action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Zapisuje żądanie, aby ponownie wygenerować klucze usługi Azure Automation DSC |
> | action | Microsoft.Automation/automationAccounts/certificates/delete | Usuwa zasób certyfikatu usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/certificates/getCount/action | Odczytuje liczba certyfikatów |
> | action | Microsoft.Automation/automationAccounts/certificates/read | Pobiera zasób certyfikatu usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/certificates/write | Tworzy lub aktualizuje zasób certyfikatu usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/compilationjobs/read | Odczytuje usługi Azure Automation DSC w kompilacji |
> | action | Microsoft.Automation/automationAccounts/compilationjobs/read | Odczytuje usługi Azure Automation DSC w kompilacji |
> | action | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapisuje usługi Azure Automation DSC w kompilacji |
> | action | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapisuje usługi Azure Automation DSC w kompilacji |
> | action | Microsoft.Automation/automationAccounts/configurations/content/read | Odczytuje zawartość nośnika konfiguracji |
> | action | Microsoft.Automation/automationAccounts/configurations/delete | Usuwa zawartość Azure DSC usługi Automation |
> | action | Microsoft.Automation/automationAccounts/configurations/getCount/action | Odczytuje liczba zawartości Azure DSC usługi Automation |
> | action | Microsoft.Automation/automationAccounts/configurations/read | Pobiera zawartość usługi Azure Automation DSC firmy |
> | action | Microsoft.Automation/automationAccounts/configurations/write | Zapisuje zawartość Azure DSC usługi Automation |
> | action | Microsoft.Automation/automationAccounts/connections/delete | Usuwa zasób połączenia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/connections/getCount/action | Odczytuje liczba połączeń |
> | action | Microsoft.Automation/automationAccounts/connections/read | Pobiera zasób połączenia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/connections/write | Tworzy lub aktualizuje zasób połączenia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/connectionTypes/delete | Usuwa zasób typu połączenia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/connectionTypes/read | Pobiera zasób typu połączenia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/connectionTypes/write | Tworzy zasób typu połączenia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/credentials/delete | Usuwa zasób poświadczenia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/credentials/getCount/action | Odczytuje liczba poświadczeń |
> | action | Microsoft.Automation/automationAccounts/credentials/read | Pobiera zasób poświadczenia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/credentials/write | Tworzy lub aktualizuje zasób poświadczenia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/delete | Usuwa konto usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Usuwa zasoby hybrydowego procesu roboczego elementu Runbook |
> | action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | action | Microsoft.Automation/automationAccounts/jobs/output/read | Pobiera dane wyjściowe zadania |
> | action | Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Pobiera zawartość elementu runbook usługi Azure Automation w czasie wykonywania zadania |
> | action | Microsoft.Automation/automationAccounts/jobs/stop/action | Zatrzymuje zadanie usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/jobSchedules/delete | Usuwa harmonogram zadań usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/jobSchedules/read | Pobiera harmonogram zadań usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/jobSchedules/write | Tworzy harmonogram zadań usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem usługi automation |
> | action | Microsoft.Automation/automationAccounts/listKeys/action | Odczytuje klucze konta usługi automation |
> | action | Microsoft.Automation/automationAccounts/modules/activities/read | Pobiera działania usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/modules/delete | Usuwa moduł usługi Azure Automation Powershell |
> | action | Microsoft.Automation/automationAccounts/modules/getCount/action | Pobiera liczbę modułów programu Powershell w ramach konta usługi Automation |
> | action | Microsoft.Automation/automationAccounts/modules/read | Pobiera moduł usługi Azure Automation Powershell |
> | action | Microsoft.Automation/automationAccounts/modules/write | Tworzy lub aktualizuje moduł usługi Azure Automation Powershell |
> | action | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Usuwa konfigurację węzła DSC usługi Automation platformy Azure |
> | action | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Odczytuje zawartość konfiguracji węzła DSC usługi Automation platformy Azure |
> | action | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Odczytuje konfigurację węzła DSC usługi Automation platformy Azure |
> | action | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Zapisuje konfiguracji węzła DSC usługi Automation platformy Azure |
> | action | Microsoft.Automation/automationAccounts/nodecounts/read | Odczytuje podsumowanie liczby węzłów dla określonego typu |
> | action | Microsoft.Automation/automationAccounts/nodes/delete | Usuwa węzłów usługi Azure Automation DSC |
> | action | Microsoft.Automation/automationAccounts/nodes/read | Odczytuje węzłów usługi Azure Automation DSC |
> | action | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Odczytuje zawartość raportu w usłudze Azure Automation DSC |
> | action | Microsoft.Automation/automationAccounts/nodes/reports/read | Odczytuje raporty usługi Azure Automation DSC |
> | action | Microsoft.Automation/automationAccounts/nodes/write | Tworzy lub aktualizuje węzłów usługi Azure Automation DSC |
> | action | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Pobiera TypeFields usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/python2Packages/delete | Usuwa pakiet w ramach usługi Azure Automation języka Python 2 |
> | action | Microsoft.Automation/automationAccounts/python2Packages/read | Pobiera pakiet w ramach usługi Azure Automation języka Python 2 |
> | action | Microsoft.Automation/automationAccounts/python2Packages/write | Tworzy lub aktualizuje pakiet w ramach usługi Azure Automation języka Python 2 |
> | action | Microsoft.Automation/automationAccounts/python3Packages/delete | Usuwa pakiet usługi Azure Automation Python 3 |
> | action | Microsoft.Automation/automationAccounts/python3Packages/read | Pobiera pakiet usługi Azure Automation Python 3 |
> | action | Microsoft.Automation/automationAccounts/python3Packages/write | Tworzy lub aktualizuje pakiet usługi Azure Automation Python 3 |
> | action | Microsoft.Automation/automationAccounts/read | Pobiera konto usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/content/read | Pobiera zawartość elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/delete | Usuwa element runbook usługi Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Tworzy zawartość wersji roboczej elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Pobiera wyniki operacji wersję roboczą elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/draft/read | Pobiera wersję roboczą elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Pobiera zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Wznawia zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Zatrzymuje zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Wstrzymuje zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Tworzy zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Cofnij operacje edycji próbnej wersji elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Pobiera liczbę elementów runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publikuje wersję roboczą elementu runbook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element runbook usługi Automation |
> | action | Microsoft.Automation/automationAccounts/runbooks/write | Tworzy lub aktualizuje element runbook usługi Automation |
> | action | Microsoft.Automation/automationAccounts/schedules/delete | Usuwa zasób harmonogramu usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/schedules/getCount/action | Pobiera liczba harmonogramów usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/schedules/read | Pobiera zasób harmonogramu usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/schedules/write | Tworzy lub aktualizuje zasób harmonogramu usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Usuwa konfiguracji aktualizacji oprogramowania usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Pobiera konfiguracji aktualizacji oprogramowania usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Tworzy lub aktualizuje konfiguracji aktualizacji oprogramowania automatyzacji platformy Azure |
> | action | Microsoft.Automation/automationAccounts/statistics/read | Pobiera statystyki usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Uruchom maszynę wdrażania aktualizacji usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Pobiera zadanie poprawki zarządzania aktualizacji usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/usages/read | Pobiera użycia usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/variables/delete | Usuwa zasób zmiennej usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/variables/read | Odczytuje zasób zmiennej usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/variables/write | Tworzy lub aktualizuje zasób zmiennej usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/watchers/delete | Usuń zadanie obserwatora usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/watchers/read | Pobiera zadanie obserwatora usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/watchers/start/action | Uruchom zadanie obserwatora usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/watchers/stop/action | Zatrzymaj zadanie obserwatora usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/watchers/streams/read | Pobiera strumień zadań usługi Azure Automation obserwatora |
> | action | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Usuwanie akcji zadania obserwatora usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Pobiera zadania akcji obserwatora usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Tworzenie akcji zadania obserwatora usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/watchers/write | Tworzy zadanie obserwatora usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/webhooks/action | Generuje identyfikator URI dla elementu webhook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/webhooks/delete | Usuwa element webhook usługi Azure Automation  |
> | action | Microsoft.Automation/automationAccounts/webhooks/read | Odczytuje element webhook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/webhooks/write | Tworzy lub aktualizuje element webhook usługi Azure Automation |
> | action | Microsoft.Automation/automationAccounts/write | Tworzy lub aktualizuje konto usługi Azure Automation |
> | action | Microsoft.Automation/operations/read | Pobiera dostępne operacje dla zasobów usługi Azure Automation |
> | action | Microsoft.Automation/register/action | Rejestruje subskrypcję usługi Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Usuń zasób katalogu usługi B2C |
> | action | Microsoft.AzureActiveDirectory/b2cDirectories/read | Wyświetl zasób katalogu usługi B2C |
> | action | Microsoft.AzureActiveDirectory/b2cDirectories/write | Utwórz lub zaktualizuj zasób katalogu B2C |
> | action | Microsoft.AzureActiveDirectory/b2ctenants/read | Wyświetla listę wszystkich dzierżaw B2C, w przypadku których użytkownik jest członkiem |
> | action | Microsoft.AzureActiveDirectory/operations/read | Odczytuj wszystkie operacje interfejsu API dostępne dla dostawcy zasobów Microsoft.AzureActiveDirectory |
> | action | Microsoft.AzureActiveDirectory/register/action | Rejestruj subskrypcję dostawcy zasobów Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.AzureStack/Operations/read | Pobiera właściwości operacji dostawcy zasobów |
> | action | Microsoft.AzureStack/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.AzureStack |
> | action | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Usuwa subskrypcję klienta usługi Azure Stack |
> | action | Microsoft.AzureStack/registrations/customerSubscriptions/read | Pobiera właściwości subskrypcji usługi Azure Stack klienta |
> | action | Microsoft.AzureStack/registrations/customerSubscriptions/write | Tworzy lub aktualizuje subskrypcją usługi Azure Stack klienta |
> | action | Microsoft.AzureStack/registrations/delete | Usuwa rejestrację w usłudze Azure Stack |
> | action | Microsoft.AzureStack/registrations/getActivationKey/action | Pobiera najnowszego klucza aktywacji usługi Azure Stack |
> | action | Microsoft.AzureStack/registrations/products/listDetails/action | Pobiera rozszerzone szczegóły produktu usługi Azure Stack w portalu Marketplace |
> | action | Microsoft.AzureStack/registrations/products/read | Pobiera właściwości produktu usługi Azure Stack w portalu Marketplace |
> | action | Microsoft.AzureStack/registrations/read | Pobiera właściwości rejestracji w usłudze Azure Stack |
> | action | Microsoft.AzureStack/registrations/write | Tworzy lub aktualizuje rejestracji w usłudze Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Batch/batchAccounts/applications/delete | Usuwa aplikację |
> | action | Microsoft.Batch/batchAccounts/applications/read | Wyświetla listę aplikacji lub pobiera właściwości aplikacji |
> | action | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktywuje pakietu aplikacji |
> | action | Microsoft.Batch/batchAccounts/applications/versions/delete | Usunięcie pakietu aplikacji |
> | action | Microsoft.Batch/batchAccounts/applications/versions/read | Pobiera właściwości pakietu aplikacji |
> | action | Microsoft.Batch/batchAccounts/applications/versions/write | Tworzy nowy pakiet aplikacji lub aktualizuje istniejący pakiet aplikacji |
> | action | Microsoft.Batch/batchAccounts/applications/write | Tworzy nową aplikację lub aktualizuje istniejącą aplikację |
> | action | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Pobiera wyniki długotrwałej operacji certyfikat na koncie usługi Batch |
> | action | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Anuluje nie powiodło się usunięcie certyfikatu na konto usługi Batch |
> | action | Microsoft.Batch/batchAccounts/certificates/delete | Usuwa certyfikat z kontem usługi Batch |
> | action | Microsoft.Batch/batchAccounts/certificates/read | Wyświetla listę certyfikatów na koncie usługi Batch lub pobiera właściwości certyfikatu |
> | action | Microsoft.Batch/batchAccounts/certificates/write | Tworzy nowy certyfikat na koncie usługi Batch lub aktualizuje istniejący certyfikat |
> | action | Microsoft.Batch/batchAccounts/delete | Usuwa konto usługi Batch |
> | action | Microsoft.Batch/batchAccounts/listkeys/action | Listy dostępu do kluczy konta usługi Batch |
> | action | Microsoft.Batch/batchAccounts/operationResults/read | Pobiera wyniki długotrwałej operacji konto usługi Batch |
> | action | Microsoft.Batch/batchAccounts/poolOperationResults/read | Pobiera wyniki długotrwałej operacji puli na koncie usługi Batch |
> | action | Microsoft.Batch/batchAccounts/pools/delete | Usuwa puli z konta usługi Batch |
> | action | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Wyłącza automatyczne skalowanie puli konta usługi Batch |
> | action | Microsoft.Batch/batchAccounts/pools/read | Wyświetla pule na konto usługi Batch lub pobiera właściwości puli |
> | action | Microsoft.Batch/batchAccounts/pools/stopResize/action | Zatrzymuje na bieżące zmiany rozmiaru operacji w puli konta usługi Batch |
> | action | Microsoft.Batch/batchAccounts/pools/write | Tworzy nową pulę na koncie usługi Batch lub aktualizuje istniejącą pulę |
> | action | Microsoft.Batch/batchAccounts/read | Wyświetla wszystkie konta usługi Batch lub pobiera właściwości konta usługi Batch |
> | action | Microsoft.Batch/batchAccounts/regeneratekeys/action | Odtwarza uzyskać dostęp do kluczy dla konta usługi Batch |
> | action | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchronizuje klucze dostępu dla konta magazynu automatycznie skonfigurowany dla konta usługi Batch |
> | action | Microsoft.Batch/batchAccounts/write | Tworzy nowe konto usługi Batch lub aktualizuje istniejące konto usługi Batch |
> | action | Microsoft.Batch/locations/accountOperationResults/read | Pobiera wyniki długotrwałej operacji konto usługi Batch |
> | action | Microsoft.Batch/locations/checkNameAvailability/action | Sprawdza, czy nazwa konta jest prawidłowy, a nie w użyciu. |
> | action | Microsoft.Batch/locations/quotas/read | Pobiera przydziały usługi Batch w określonej subskrypcji w określonym regionie platformy Azure |
> | action | Microsoft.Batch/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.Batch |
> | action | Microsoft.Batch/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Batch i włącza funkcję tworzenia konta usługi Batch |
> | action | Microsoft.Batch/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Batch, co uniemożliwia tworzenie kont usługi Batch |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Billing/billingAccounts/departments/read | Lista wszystkich działach w ramach rozliczeń zakresu konta |
> | action | Microsoft.Billing/billingAccounts/enrollmentAccounts/read | Wyświetlanie listy wszystkich kont rejestracji w ramach rozliczeń zakresu konta |
> | action | Microsoft.Billing/billingAccounts/read | Wszystkie konta rozliczeniowego, użytkownika, który ma dostęp do listy |
> | action | Microsoft.Billing/billingPeriods/read | Wyświetla listę dostępnych okresów rozliczeniowych |
> | action | Microsoft.Billing/departments/read | Wszystkie działy, na którym użytkownik ma dostęp do listy |
> | action | Microsoft.Billing/invoices/read | Wyświetla dostępne faktur |
> | action | Microsoft.Billing/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.Billing |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.BingMaps/mapApis/Delete | Operacja usuwania |
> | action | Microsoft.BingMaps/mapApis/listSecrets/action | Wyświetl klucze tajne |
> | action | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Odczyt tokenu autoryzacji logowania jednokrotnego dla zasobu |
> | action | Microsoft.BingMaps/mapApis/Read | Operacja odczytu |
> | action | Microsoft.BingMaps/mapApis/regenerateKey/action | Generuje ponownie klucz |
> | action | Microsoft.BingMaps/mapApis/Write | Operacja zapisu |
> | action | Microsoft.BingMaps/Operations/read | Opis operacji. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Blockchain/blockchainMembers/delete | Usuwa istniejącą składową łańcucha bloków. |
> | action | Microsoft.Blockchain/blockchainMembers/read | Pobiera lub wyświetla ich listę istniejących członków łańcucha bloków. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Łączy do węzła transakcji składowej łańcucha bloków. |
> | action | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Usuwa istniejący węzeł transakcji składowej łańcucha bloków. |
> | action | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Pobiera lub wyświetla ich listę istniejących węzłów transakcji składowej łańcucha bloków. |
> | action | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Tworzy lub aktualizuje węzła transakcji składowej łańcucha bloków. |
> | action | Microsoft.Blockchain/blockchainMembers/write | Tworzy lub aktualizuje element członkowski łańcucha bloków. |
> | action | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Pobiera wyniki operacji elementów członkowskich łańcucha bloków. |
> | action | Microsoft.Blockchain/locations/checkNameAvailability/action | Sprawdza, czy ta nazwa zasobu jest prawidłowa i nie jest używany. |
> | action | Microsoft.Blockchain/operations/read | Wyświetl listę wszystkich operacji dostawcy zasobów usługi Microsoft łańcucha bloków. |
> | action | Microsoft.Blockchain/register/action | Rejestruje subskrypcję dostawcy zasobów łańcucha bloków. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Odczytaj wszelkie artefakty strategii |
> | action | Microsoft.Blueprint/blueprintAssignments/delete | Usuń wszelkie artefakty strategii |
> | action | Microsoft.Blueprint/blueprintAssignments/read | Odczytaj wszelkie artefakty strategii |
> | action | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Pobierz identyfikator obiektu jednostki usługi Azure Blueprints. |
> | action | Microsoft.Blueprint/blueprintAssignments/write | Utwórz lub zaktualizuj wszelkie artefakty strategii |
> | action | Microsoft.Blueprint/blueprints/artifacts/delete | Usuń wszelkie artefakty strategii |
> | action | Microsoft.Blueprint/blueprints/artifacts/read | Odczytaj wszelkie artefakty strategii |
> | action | Microsoft.Blueprint/blueprints/artifacts/write | Utwórz lub zaktualizuj wszelkie artefakty strategii |
> | action | Microsoft.Blueprint/blueprints/delete | Usuń wszelkie strategie |
> | action | Microsoft.Blueprint/blueprints/read | Odczytaj wszelkie strategie |
> | action | Microsoft.Blueprint/blueprints/versions/artifacts/read | Odczytaj wszelkie artefakty strategii |
> | action | Microsoft.Blueprint/blueprints/versions/delete | Usuń wszelkie strategie |
> | action | Microsoft.Blueprint/blueprints/versions/read | Odczytaj wszelkie strategie |
> | action | Microsoft.Blueprint/blueprints/versions/write | Utwórz lub zaktualizuj wszelkie strategie |
> | action | Microsoft.Blueprint/blueprints/write | Utwórz lub zaktualizuj wszelkie strategie |
> | action | Microsoft.Blueprint/register/action | Rejestruje dostawcę zasobów usługi Azure Blueprints |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.BotService/botServices/channels/delete | Usuwanie usługi Bot |
> | action | Microsoft.BotService/botServices/channels/read | Usługa Bot odczytu |
> | action | Microsoft.BotService/botServices/channels/write | Usługa Bot zapisu |
> | action | Microsoft.BotService/botServices/connections/delete | Usuwanie usługi Bot |
> | action | Microsoft.BotService/botServices/connections/read | Usługa Bot odczytu |
> | action | Microsoft.BotService/botServices/connections/write | Usługa Bot zapisu |
> | action | Microsoft.BotService/botServices/delete | Usuwanie usługi Bot |
> | action | Microsoft.BotService/botServices/read | Usługa Bot odczytu |
> | action | Microsoft.BotService/botServices/write | Usługa Bot zapisu |
> | action | Microsoft.BotService/locations/operationresults/read | Odczytaj stan operacji asynchronicznej |
> | action | Microsoft.BotService/Operations/read | Odczytaj operacje dla wszystkich typów zasobów |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Cache/checknameavailability/action | Sprawdza, czy nazwa jest dostępna do użycia z nową pamięcią podręczną Redis Cache |
> | action | Microsoft.Cache/locations/operationresults/read | Pobiera wynik długotrwałej operacji, dla której nagłówek „Location” został wcześniej zwrócony do klienta |
> | action | Microsoft.Cache/operations/read | Wyświetla listę operacji obsługiwanych przez dostawcę „Microsoft.Cache”. |
> | action | Microsoft.Cache/redis/delete | Usuń całą pamięć podręczną Redis |
> | action | Microsoft.Cache/redis/export/action | Wyeksportuj dane usługi Redis do określonych prefiksem obiektów blob magazynu w podanym formacie |
> | action | Microsoft.Cache/redis/firewallRules/delete | Usuń reguły zapory IP pamięci podręcznej Redis Cache |
> | action | Microsoft.Cache/redis/firewallRules/read | Pobierz reguły zapory IP pamięci podręcznej Redis Cache |
> | action | Microsoft.Cache/redis/firewallRules/write | Edytuj reguły zapory IP pamięci podręcznej Redis Cache |
> | action | Microsoft.Cache/redis/forceReboot/action | Wymuś ponowny rozruch wystąpienia pamięci podręcznej, nawet z utratą danych. |
> | action | Microsoft.Cache/redis/import/action | Zaimportuj dane w podanym formacie z wielu obiektów blob do usługi Redis |
> | action | Microsoft.Cache/redis/linkedservers/delete | Usuń podłączony serwer z usługi Redis Cache |
> | action | Microsoft.Cache/redis/linkedservers/read | Pobierz połączone serwery skojarzona z usługą Redis Cache. |
> | action | Microsoft.Cache/redis/linkedservers/write | Dodaj połączony serwer do usługi Redis Cache |
> | action | Microsoft.Cache/redis/listKeys/action | Wyświetl wartości kluczy dostępu pamięci podręcznej Redis w portalu zarządzania |
> | action | Microsoft.Cache/redis/listUpgradeNotifications/read | Lista najnowszych powiadomień o uaktualnieniach dla dzierżawy pamięci podręcznej. |
> | action | Microsoft.Cache/redis/metricDefinitions/read | Pobiera dostępne metryki pamięci podręcznej Redis |
> | action | Microsoft.Cache/redis/patchSchedules/delete | Usuń harmonogram stosowania poprawek pamięci podręcznej Redis Cache |
> | action | Microsoft.Cache/redis/patchSchedules/read | Pobiera harmonogram stosowania poprawek pamięci podręcznej Redis Cache |
> | action | Microsoft.Cache/redis/patchSchedules/write | Modyfikuj harmonogram stosowania poprawek pamięci podręcznej Redis Cache |
> | action | Microsoft.Cache/redis/read | Wyświetl ustawienia i konfigurację pamięci podręcznej Redis w portalu zarządzania |
> | action | Microsoft.Cache/redis/regenerateKey/action | Zmień wartość kluczy dostępu pamięci podręcznej Redis w portalu zarządzania |
> | action | Microsoft.Cache/redis/start/action | Uruchom wystąpienie pamięci podręcznej. |
> | action | Microsoft.Cache/redis/stop/action | Zatrzymaj wystąpienie pamięci podręcznej. |
> | action | Microsoft.Cache/redis/write | Zmodyfikuj ustawienia i konfigurację pamięci podręcznej Redis w portalu zarządzania |
> | action | Microsoft.Cache/register/action | Rejestruje dostawcę zasobów „Microsoft.Cache” z subskrypcją |
> | action | Microsoft.Cache/unregister/action | Wyrejestrowuje dostawcę zasobów „Microsoft.Cache” z subskrypcją |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Capacity/appliedreservations/read | Odczyt wszystkich rezerwacji |
> | action | Microsoft.Capacity/calculateexchange/action | Oblicza ilość programu exchange i ceny zakupu nowego i przywraca zasadom błędy. |
> | action | Microsoft.Capacity/calculateprice/action | Oblicz cenę dowolnej rezerwacji |
> | action | Microsoft.Capacity/catalogs/read | Wykaz odczytu rezerwacji |
> | action | Microsoft.Capacity/checkoffers/action | Sprawdź wszystkie oferty subskrypcji |
> | action | Microsoft.Capacity/checkscopes/action | Sprawdź dowolnej subskrypcji |
> | action | Microsoft.Capacity/commercialreservationorders/read | Pobierz zamówienia rezerwacji utworzone w ramach dowolnej dzierżawy |
> | action | Microsoft.Capacity/exchange/action | Exchange żadnych rezerwacji |
> | action | Microsoft.Capacity/operations/read | Wszelkie operacja odczytu |
> | action | Microsoft.Capacity/register/action | Rejestruje dostawcę zasobów pojemności i włącza funkcję tworzenia zasobów pojemności. |
> | action | Microsoft.Capacity/reservationorders/action | Aktualizuj żadnych rezerwacji |
> | action | Microsoft.Capacity/reservationorders/availablescopes/action | Znajdź wszystkie dostępne zakresu |
> | action | Microsoft.Capacity/reservationorders/calculaterefund/action | Oblicza ilość zwrot kosztów i ceny zakupu nowego i przywraca zasadom błędy. |
> | action | Microsoft.Capacity/reservationorders/delete | Usuń wszelkie rezerwacji |
> | action | Microsoft.Capacity/reservationorders/merge/action | Scal wszystkie rezerwacji |
> | action | Microsoft.Capacity/reservationorders/read | Odczyt wszystkich rezerwacji |
> | action | Microsoft.Capacity/reservationorders/reservations/action | Aktualizuj żadnych rezerwacji |
> | action | Microsoft.Capacity/reservationorders/reservations/delete | Usuń wszelkie rezerwacji |
> | action | Microsoft.Capacity/reservationorders/reservations/read | Odczyt wszystkich rezerwacji |
> | action | Microsoft.Capacity/reservationorders/reservations/revisions/read | Odczyt wszystkich rezerwacji |
> | action | Microsoft.Capacity/reservationorders/reservations/write | Utwórz zastrzeżenie |
> | action | Microsoft.Capacity/reservationorders/return/action | Zwróć wszystkie rezerwacji |
> | action | Microsoft.Capacity/reservationorders/split/action | Podziel żadnych rezerwacji |
> | action | Microsoft.Capacity/reservationorders/swap/action | Zamień wszystkie rezerwacji |
> | action | Microsoft.Capacity/reservationorders/write | Utwórz zastrzeżenie |
> | action | Microsoft.Capacity/tenants/register/action | Zarejestruj dowolnej dzierżawy |
> | action | Microsoft.Capacity/unregister/action | Wyrejestruj dowolnej dzierżawy |
> | action | Microsoft.Capacity/validatereservationorder/action | Sprawdzanie poprawności dowolnego rezerwacji |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Cdn/CheckNameAvailability/action |  |
> | action | Microsoft.Cdn/CheckResourceUsage/action |  |
> | action | Microsoft.Cdn/edgenodes/delete |  |
> | action | Microsoft.Cdn/edgenodes/read |  |
> | action | Microsoft.Cdn/edgenodes/write |  |
> | action | Microsoft.Cdn/operationresults/delete |  |
> | action | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | action | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | action | Microsoft.Cdn/operationresults/profileresults/read |  |
> | action | Microsoft.Cdn/operationresults/profileresults/write |  |
> | action | Microsoft.Cdn/operationresults/read |  |
> | action | Microsoft.Cdn/operationresults/write |  |
> | action | Microsoft.Cdn/operations/read |  |
> | action | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | action | Microsoft.Cdn/profiles/delete |  |
> | action | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | action | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | action | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | action | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | action | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | action | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | action | Microsoft.Cdn/profiles/endpoints/delete |  |
> | action | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | action | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | action | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | action | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | action | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | action | Microsoft.Cdn/profiles/endpoints/read |  |
> | action | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | action | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | action | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | action | Microsoft.Cdn/profiles/endpoints/write |  |
> | action | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | action | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | action | Microsoft.Cdn/profiles/read |  |
> | action | Microsoft.Cdn/profiles/write |  |
> | action | Microsoft.Cdn/register/action | Rejestruje subskrypcję dostawcy zasobów CDN i umożliwia tworzenie profilów CDN. |
> | action | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Usuń istniejący certyfikat |
> | action | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Pobierz listę certyfikatów |
> | action | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejącą |
> | action | Microsoft.CertificateRegistration/certificateOrders/Delete | Usuwanie istniejącego certyfikatu usługi App Service |
> | action | Microsoft.CertificateRegistration/certificateOrders/Read | Pobierz listę zamówień certyfikatów |
> | action | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Ponowne wystawianie zamówienia istniejącego certyfikatu |
> | action | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Odnawianie istniejącego zamówienia certyfikatu |
> | action | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Ponowne wysyłanie wiadomości e-mail z certyfikatu |
> | action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Wyślij ponownie żądanie z wiadomości e-mail do innego adresu e-mail |
> | action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Pobieranie pieczętowanie witryny dla wystawionego certyfikatu usługi App Service |
> | action | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Pobieranie listy akcji certyfikatów |
> | action | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Pobieranie historii wiadomości e-mail certyfikatu |
> | action | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Weryfikowanie własności domeny |
> | action | Microsoft.CertificateRegistration/certificateOrders/Write | Dodaj nowe zamówienia certyfikatu lub zaktualizuj istniejącą |
> | action | Microsoft.CertificateRegistration/operations/Read | Lista wszystkich operacji z rejestracji certyfikatu usługi dla aplikacji |
> | action | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Jednostki usługi aprowizacji dla nazwy głównej usługi aplikacji |
> | action | Microsoft.CertificateRegistration/register/action | Zarejestruj dostawcę zasobów Certificates firmy Microsoft dla subskrypcji |
> | action | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Sprawdź poprawność obiektu zakup certyfikatu bez przesyłania |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ClassicCompute/capabilities/read | Pokazuje możliwości |
> | action | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Sprawdza dostępność danej nazwy domeny. |
> | action | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Pobiera dostępność danej nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/active/write | Ustawia nazwę aktywnej domeny. |
> | action | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Pokazuje zestaw dostępności dla zasobu. |
> | action | Microsoft.ClassicCompute/domainNames/capabilities/read | Pokazuje możliwości nazwy domeny |
> | action | Microsoft.ClassicCompute/domainNames/delete | Usuwa nazwy domen dla zasobów. |
> | action | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Pokazuje miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Pobierz rolę w miejscu wdrożenia nazwy domeny |
> | action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Pobierz wystąpienie roli dla roli w miejscu wdrożenia nazwy domeny |
> | action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Pobierz stan miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Dodaj stan miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Pobierz domenę uaktualnienia dla miejsca wdrożenia w nazwie domeny |
> | action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Zaktualizuj domenę uaktualnienia dla miejsca wdrożenia w nazwie domeny |
> | action | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Tworzy lub aktualizuje wdrożenie. |
> | action | Microsoft.ClassicCompute/domainNames/extensions/delete | Usuwa rozszerzenia nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Odczytuje stan operacji dla rozszerzeń nazw domen. |
> | action | Microsoft.ClassicCompute/domainNames/extensions/read | Zwraca rozszerzenia nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/extensions/write | Dodaje rozszerzenia nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Usuwa nową wewnętrzną usługę równoważenia obciążenia. |
> | action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Odczytuje stan operacji dla wewnętrznych modułów równoważenia obciążenia nazw domen. |
> | action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Pobiera wewnętrzne usługi równoważenia obciążenia. |
> | action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Tworzy nową wewnętrzną usługę równoważenia obciążenia. |
> | action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Odczytuje stan operacji dla zestawów punktów końcowych z równoważeniem obciążenia nazw domen. |
> | action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Pobierz zestawy punktów końcowych ze zrównoważonym obciążeniem. |
> | action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Dodaj zestaw punktów końcowych ze zrównoważonym obciążeniem. |
> | action | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Pobierz stan operacji nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Odczytuje stan operacji dla rozszerzeń nazw domen. |
> | action | Microsoft.ClassicCompute/domainNames/read | Zwraca nazwy domen dla zasobów. |
> | action | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Usuwa używane certyfikaty usługi. |
> | action | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Odczytuje stan operacji dla certyfikatów usługi nazw domen. |
> | action | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Zwraca używane certyfikaty usługi. |
> | action | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Dodaje lub modyfikuje używane certyfikaty usługi. |
> | action | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Przerywa migrację miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Zatwierdza migrację miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/delete | Usuwa dane miejsce wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Odczytuje stan operacji dla gniazd nazw domen. |
> | action | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Przygotowuje migrację miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/read | Pokazuje miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Usuwa odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Odczytuje stan operacji dla odwołań rozszerzenia ról w ramach gniazd nazw domen. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Zwraca odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Dodaje lub modyfikuje odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Pobierz definicję metryki roli dla nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Pobierz metrykę roli dla nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Pobierz stan operacji dla roli gniazda nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Pobiera definicje metryk. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/read | Pobiera rolę dla miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Pobiera plik połączenia pulpitu zdalnego dla wystąpienia roli w ramach roli gniazda nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Pobiera stan operacji dla wystąpienia roli w ramach roli gniazda nazwy domeny. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Pobiera wystąpienie roli. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Ponownie kompiluje wystąpienie roli. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Odtwarza z obrazu wystąpienie roli. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Uruchamia ponownie wystąpienia roli. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Pobierz jednostkę SKU roli dla miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/roles/write | Dodaj rolę dla miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/start/action | Uruchamia miejsce wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Zmienia stan miejsca wdrożenia na Zatrzymane. |
> | action | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Zmienia stan miejsca wdrożenia na Uruchomione. |
> | action | Microsoft.ClassicCompute/domainNames/slots/stop/action | Wstrzymuje miejsce wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Uaktualnia domenę (z przeszukaniem). |
> | action | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Weryfikuje migrację miejsca wdrożenia. |
> | action | Microsoft.ClassicCompute/domainNames/slots/write | Tworzy lub aktualizuje wdrożenie. |
> | action | Microsoft.ClassicCompute/domainNames/swap/action | Zamienia miejsce przejściowe z miejscem produkcyjnym. |
> | action | Microsoft.ClassicCompute/domainNames/write | Dodaje lub modyfikuje nazwy domen dla zasobów. |
> | action | Microsoft.ClassicCompute/moveSubscriptionResources/action | Przenieś wszystkie zasoby klasyczne do innej subskrypcji. |
> | action | Microsoft.ClassicCompute/operatingSystemFamilies/read | Generuje listę rodzin systemów operacyjnych gościa dostępnych na platformie Microsoft Azure, a dodatkowo pokazuje wersje systemu operacyjnego dostępne w ramach każdej rodziny. |
> | action | Microsoft.ClassicCompute/operatingSystems/read | Generuje listę wersji systemów operacyjnych gościa dostępnych obecnie na platformie Microsoft Azure. |
> | action | Microsoft.ClassicCompute/operations/read | Pobiera listę operacji. |
> | action | Microsoft.ClassicCompute/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | action | Microsoft.ClassicCompute/quotas/read | Pobiera przydział dla subskrypcji. |
> | action | Microsoft.ClassicCompute/register/action | Zarejestruj się w usłudze Classic Compute |
> | action | Microsoft.ClassicCompute/resourceTypes/skus/read | Pobiera listę jednostek Sku dla obsługiwanych typów zasobów. |
> | action | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Weryfikuj dostępność subskrypcji dla klasycznej operacji przenoszenia. |
> | action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszyną wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Pobiera dostępne operacje asynchroniczne |
> | action | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Dołącza dysk z danymi do maszyny wirtualnej. |
> | action | Microsoft.ClassicCompute/virtualMachines/capture/action | Przechwyć maszynę wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/delete | Usuwa maszyny wirtualne. |
> | action | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Odłącza dysk z danymi od maszyny wirtualnej. |
> | action | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Pobierz ustawienia diagnostyczne maszyny wirtualnej. |
> | action | Microsoft.ClassicCompute/virtualMachines/disks/read | Pobiera listę dysków z danymi |
> | action | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Pobiera plik RDP dla maszyny wirtualnej. |
> | action | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Odczytuje stan operacji dla rozszerzeń maszyny wirtualnej. |
> | action | Microsoft.ClassicCompute/virtualMachines/extensions/read | Pobiera rozszerzenie maszyny wirtualnej. |
> | action | Microsoft.ClassicCompute/virtualMachines/extensions/write | Umieszcza rozszerzenie maszyny wirtualnej. |
> | action | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Pobierz definicję metryki maszyny wirtualnej. |
> | action | Microsoft.ClassicCompute/virtualMachines/metrics/read | Pobiera metryki. |
> | action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszyną wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | action | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Odczytuje stan operacji dla maszyn wirtualnych. |
> | action | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Wykonuje konserwację maszyny wirtualnej. |
> | action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne. |
> | action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Pobiera definicje metryk. |
> | action | Microsoft.ClassicCompute/virtualMachines/read | Pobiera listę maszyn wirtualnych. |
> | action | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Ponownie wdraża maszynę wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/restart/action | Uruchamia ponownie maszyny wirtualne. |
> | action | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Zamyka maszynę wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/start/action | Uruchamia maszynę wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/stop/action | Zatrzymuje maszynę wirtualną. |
> | action | Microsoft.ClassicCompute/virtualMachines/write | Dodaje lub modyfikuje maszyny wirtualne. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Pobierz stan operacji połączenia krzyżowego usługi Express Route |
> | action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Usuń komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Pobierz stan operacji komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Pobierz komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Dodaj komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | action | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Pobierz połączenia krzyżowe usługi Express Route. |
> | action | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Dodaj połączenia krzyżowe usługi Express Route. |
> | action | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Pobiera listę obsługiwanych urządzeń. |
> | action | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń. |
> | action | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grupy zabezpieczeń sieci. |
> | action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne sieciowych grup zabezpieczeń |
> | action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne, ta operacja jest uzupełniana przez dostawcę zasobów szczegółowych informacji sieciowe grupy zabezpieczeń. |
> | action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Pobiera zdarzenia dla sieciowej grupy zabezpieczeń |
> | action | Microsoft.ClassicNetwork/networkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń. |
> | action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Usuwa regułę zabezpieczeń. |
> | action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Odczytuje stan operacji dla reguł zabezpieczeń grupy zabezpieczeń sieci. |
> | action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Pobiera regułę zabezpieczeń. |
> | action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Dodaje lub aktualizuje regułę zabezpieczeń. |
> | action | Microsoft.ClassicNetwork/networkSecurityGroups/write | Dodaje nową sieciową grupę zabezpieczeń. |
> | action | Microsoft.ClassicNetwork/operations/read | Pobierz operacje sieci klasycznej. |
> | action | Microsoft.ClassicNetwork/quotas/read | Pobiera przydział dla subskrypcji. |
> | action | Microsoft.ClassicNetwork/register/action | Zarejestruj się w usłudze Classic Network |
> | action | Microsoft.ClassicNetwork/reservedIps/delete | Usuwa zastrzeżony adres IP. |
> | action | Microsoft.ClassicNetwork/reservedIps/join/action | Dołącz zarezerwowany adres IP |
> | action | Microsoft.ClassicNetwork/reservedIps/link/action | Połącz zarezerwowany adres IP |
> | action | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Odczytuje stan operacji dla zarezerwowanych adresów IP. |
> | action | Microsoft.ClassicNetwork/reservedIps/read | Pobiera zastrzeżone adresy IP |
> | action | Microsoft.ClassicNetwork/reservedIps/write | Dodaje nowy zastrzeżony adres IP |
> | action | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Przerywa migrację sieci wirtualnej |
> | action | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Pokazuje możliwości |
> | action | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Sprawdza dostępność danego adresu IP w sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Zatwierdza migrację sieci wirtualnej |
> | action | Microsoft.ClassicNetwork/virtualNetworks/delete | Usuwa sieć wirtualną. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Odwołuje cofnięcie certyfikatu klienta. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Odczytuje cofnięte certyfikaty klienta. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Cofa certyfikat klienta. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Usuwa certyfikat klienta bramy sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Pobiera certyfikat na podstawie odcisku palca. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Wyświetla pakiet certyfikatu bramy sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Znajduje certyfikaty główne klienta. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Przekazuje nowy certyfikat główny klienta. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Tworzy połączenie bramy między lokacjami. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Rozłącza połączenie bramy między lokacjami. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Pobiera listę połączeń. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testuje połączenie bramy między lokacjami. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Usuwa bramę sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Pobiera skrypt konfiguracji urządzenia. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Pobiera dane diagnostyczne bramy. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Pobiera klucz usługi obwodu. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Wyświetla pakiet bramy sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Odczytuje stan operacji dla bram sieci wirtualnych. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Pobiera pakiet bramy sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Pobiera bramy sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Uruchamia diagnostyczne dla bramy sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Zatrzymuje diagnostyczne dla bramy sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Dodaje bramę sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/join/action | Umożliwia dołączenie do sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Odczytuje stan operacji dla sieci wirtualnych. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Łączy równorzędnie sieć wirtualną z inną siecią wirtualną. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Przygotowuje migrację sieci wirtualnej |
> | action | Microsoft.ClassicNetwork/virtualNetworks/read | Pobiera sieć wirtualną. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Usuwa zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Pobiera zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Dodaje lub aktualizuje zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla skojarzonej sieciowej grupy zabezpieczeń podsieci sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Weryfikuje migrację sieci wirtualnej |
> | action | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Pobiera komunikację równorzędną sieci wirtualnej. |
> | action | Microsoft.ClassicNetwork/virtualNetworks/write | Dodaje nową sieć wirtualną. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ClassicStorage/capabilities/read | Pokazuje możliwości |
> | action | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Sprawdza dostępność konta magazynu. |
> | action | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Pobierz dostępność konta magazynu. |
> | action | Microsoft.ClassicStorage/disks/read | Zwraca dysk konta magazynu. |
> | action | Microsoft.ClassicStorage/images/operationstatuses/read | Pobiera stan operacji obrazu. |
> | action | Microsoft.ClassicStorage/images/read | Zwraca obraz. |
> | action | Microsoft.ClassicStorage/operations/read | Pobiera operacje magazynu klasycznego |
> | action | Microsoft.ClassicStorage/osImages/read | Zwraca obraz systemu operacyjnego. |
> | action | Microsoft.ClassicStorage/osPlatformImages/read | Pobiera obraz platformy systemu operacyjnego. |
> | action | Microsoft.ClassicStorage/publicImages/read | Pobiera publiczny obraz maszyny wirtualnej |
> | action | Microsoft.ClassicStorage/quotas/read | Pobiera przydział dla subskrypcji. |
> | action | Microsoft.ClassicStorage/register/action | Zarejestruj w magazynie klasycznym |
> | action | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Przerywa migrację konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Zatwierdza migrację konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/delete | Usuwa konto magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/disks/delete | Usuwa dany dysk konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | action | Microsoft.ClassicStorage/storageAccounts/disks/read | Zwraca dysk konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/disks/write | Dodaje dysk konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/images/delete | Usuwa obraz konta magazynu podanego. (Przestarzałe. Użyj "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | action | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Zwraca stan operacji obrazu konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/images/read | Zwraca obraz konta magazynu. (Przestarzałe. Użyj "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | action | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla klucze dostępu dla kont magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | action | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Usuwa określony obraz systemu operacyjnego konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/osImages/read | Zwraca obraz systemu operacyjnego konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/osImages/write | Dodaje podany obraz systemu operacyjnego konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Przygotowuje migrację konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/read | Zwraca konto magazynu z podanym kontem. |
> | action | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Pobiera ustawienia diagnostyczne. |
> | action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | action | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Pobiera definicje metryk. |
> | action | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Pobiera metryki. |
> | action | Microsoft.ClassicStorage/storageAccounts/services/read | Pobiera dostępne usługi. |
> | action | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Weryfikuje migrację konta magazynu. |
> | action | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Usuwa dany obraz maszyny wirtualnej. |
> | action | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Pobiera stan operacji obrazu danej maszyny wirtualnej. |
> | action | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Zwraca obraz maszyny wirtualnej. |
> | action | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Dodaje dany obraz maszyny wirtualnej. |
> | action | Microsoft.ClassicStorage/storageAccounts/write | Dodaje nowe konto magazynu. |
> | action | Microsoft.ClassicStorage/vmImages/read | Wyświetla listę obrazów maszyn wirtualnych. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.CognitiveServices/accounts/delete | Usuwa konta interfejsów API |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Wykrywanie ludzkich twarzy na obrazie prostokąty zwracany twarzy oraz opcjonalnie faceIds, charakterystycznych elementów krajobrazu i atrybutów. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Usuń listę określonego twarzy. Obrazy powiązane twarzy na liście twarzy również zostaną usunięte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Usuwanie twarzy list twarzy przez określony faceListId i persisitedFaceId. Obraz twarzy powiązane również zostaną usunięte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Dodaj twarzy na listę określoną twarzy maksymalnie 1000 twarzy. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Pobieranie listy twarzy faceListId, nazwy, userData i twarze na liście twarzy. Wyświetl listę list twarzy faceListId, nazwa i danych użytkownika. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Tworzenie list twarzy pusty za pomocą faceListId określonych przez użytkownika, nazwę i opcjonalny userData. Maksymalnie 64 twarzy list są dozwolone informacji o aktualizacji listy rozpoznawania twarzy, takie jak nazwa i danych użytkownika. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Podane faceId kwerendy rozpoznawania twarzy, wyszukiwanie podobnie wyglądających twarzy z tablicą faceId listy twarzy lub dużych powierzchni. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Dzielenie Release candidate twarze na grupy na podstawie podobieństwa twarzy. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | Identyfikacja 1-do wielu, można znaleźć najbliższe dopasowania określonej osoby twarz z grupy osoba lub grupa osób dużych. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Usuń listę określonego dużych powierzchni. Obrazy powiązane twarzy na liście twarzy dużych również zostaną usunięte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Usuwanie twarzy z listy dużych powierzchni przez określony largeFaceListId i persisitedFaceId. Obraz twarzy powiązane również zostaną usunięte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Aby pobrać utrwalonych twarzy na liście twarzy dużych, largeFaceListId i persistedFaceId. Listę persistedFaceId i danych użytkownika na liście twarzy dużych określonego twarzy. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Dodaj twarzy na listę określoną dużych powierzchni twarzy do 1 000 000. Zaktualizuj pole userData określonego twarzy na liście twarzy dużych, jego persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Pobierz listę dużych powierzchni largeFaceListId, nazwa, userData. Wyświetl listę list twarzy dużych informacji largeFaceListId, nazwy i danych użytkownika. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Prześlij zadania szkolenia twarzy duże listy. Szkolenie jest to istotny krok, można użyć tylko uczonego twarzy duże listy. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Aby sprawdzić stan szkolenia listy dużych powierzchni ukończone lub postępujące. Operacja asynchroniczna jest LargeFaceList szkolenie |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Tworzenie list twarzy dużych pustych za pomocą largeFaceListId określonych przez użytkownika, nazwę i opcjonalny userData. Zaktualizuj informacje listy dużych powierzchni, takie jak nazwa i danych użytkownika. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Usuń istniejącą grupę dużych osoby za pomocą określonego personGroupId. Utrwalone dane w tej grupie dużych osoby zostaną usunięte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Utwórz nową osobę w grupie określona osoba dużych. Aby dodać tę osobę do rozpoznawania twarzy, skontaktuj się z działem |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Usunąć istniejące osoby z grupy dużych osoby. Wszystkie przechowywane danych osobowych i obrazy twarzy w zapisie osoby zostanie usunięty. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Usuwanie twarzy od osoby w grupie dużych osoby. Dane rozpoznawania twarzy i obrazów związanych z tego wpisu rozpoznawania twarzy można również usunięte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Pobierz informacje o twarzy osoby. Twarzy osoby utrwalonych jest określony przez jego largePersonGroupId, personId i persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Dodaj obraz twarzy osoby do grupy dużych osoby identyfikowanie twarzy lub weryfikacji. Aby poradzić sobie z obrazem aktualizacji osoby utrwalone tarczy userData pola. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Pobierać nazwiska osoby i danych użytkownika i utrwalonych faceIds, reprezentujący obraz twarzy osoby zarejestrowane. Wyświetl listę informacji o wszystkich osób w grupie określona osoba dużych, jak personId, nazwa, userData persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Zaktualizuj nazwę lub userData osoby. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Pobierz informacje o dużych osoby grupy, takie jak jego nazwa i danych użytkownika. Ten interfejs API zwraca dużej grupy informacje o osobie listy wszystkich istniejących grup osób dużych largePesonGroupId, nazwę i danych użytkownika. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Prześlij zadania szkolenia grupy dużych osoby. Szkolenie jest to istotny krok, można użyć tylko uczonego osób dużych grup. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Można sprawdzić stan ukończone lub postępujące szkolenia grupy dużych osoby. Operacja asynchroniczna jest LargePersonGroup szkolenie |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Utwórz nową grupę dużych osoby za pomocą largePersonGroupId określonych przez użytkownika, nazwę i opcjonalny userData. Zaktualizuj nazwę istniejącej grupy dużych osoby i userData. Właściwości przechowują bez zmian, jeśli nie znajdują się w treści żądania. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Usuń istniejącą grupę osoby za pomocą określonego personGroupId. Utrwalone dane w tej grupie osób zostaną usunięte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Tworzenie nowej osoby do grupy określonej osoby. Aby dodać tę osobę do rozpoznawania twarzy, skontaktuj się z działem |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Usuń istniejące osoby z grupa osób. Wszystkie przechowywane danych osobowych i obrazy twarzy w zapisie osoby zostanie usunięty. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Usuwanie twarzy od osoby w grupie osób. Dane rozpoznawania twarzy i obrazów związanych z tego wpisu rozpoznawania twarzy można również usunięte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Pobierz informacje o twarzy osoby. Twarzy osoby utrwalonych jest określony przez jego personGroupId, personId i persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Dodaj obraz twarzy osoby do grupy osoby do rozpoznawania twarzy lub weryfikacji. Aby poradzić sobie z obrazem wielu aktualizacji osoby utrwalone tarczy userData pola. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Pobierać nazwiska osoby i danych użytkownika i utrwalonych faceIds, reprezentujący obraz twarzy osoby zarejestrowane. Informacje o wszystkich osób w grupie określona osoba, jak personId, nazwa, userData persistedFaceIds z listy zarejestrowany. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Zaktualizuj nazwę lub userData osoby. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Pobieranie nazwy grupy osoby i danych użytkownika. Aby uzyskać informacje o osobie w tej grupie, użyj pesonGroupId grup listy osób, nazwę i danych użytkownika. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Prześlij zadania szkolenia grupy osoby. Szkolenie jest to istotny krok, można użyć tylko grupa uczonego osoby. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Można sprawdzić stan ukończone lub postępujące szkolenia grupy osoby. Szkolenie w grupie jest operacja asynchroniczna wyzwolone |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Utwórz nową grupę osoby z personGroupId określony, nazwa i userData podanego przez użytkownika. Zaktualizuj nazwę istniejącej grupy osoby i danych użytkownika. Właściwości przechowują bez zmian, jeśli nie znajdują się w treści żądania. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Sprawdź, czy dwie twarze należą do tej samej osoby lub czy należy twarzy jednej osobie. |
> | action | Microsoft.CognitiveServices/accounts/listKeys/action | Wylicz klucze |
> | action | Microsoft.CognitiveServices/accounts/read | Odczytuje konta interfejsów API. |
> | action | Microsoft.CognitiveServices/accounts/regenerateKey/action | Wygeneruj ponownie klucz |
> | action | Microsoft.CognitiveServices/accounts/skus/read | Odczytuje jednostki SKU dostępne dla istniejącego zasobu. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | Interfejs API zwraca listę znanych jednostek oraz ogólne nazwane jednostek (\"osoby\", \"lokalizacji\", \"organizacji\" itp.) w danym dokumencie. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | Interfejs API zwraca listę ciągów wskazujących najważniejsze punkty w tekście wejściowym. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | Interfejs API zwraca wykryty język i wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 oznaczają 100% pewności, że określony język jest poprawny. Obsługiwanych jest 120 języków. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | Interfejs API zwraca wynik liczbowy z zakresu od 0 do 1.<br>Wyniki zbliżone do wartości 1 wskazują na pozytywną tonację, a wyniki zbliżone do wartości 0 wskazują na negatywną tonację.<br>Wynik 0,5 oznacza brak wskaźniki nastrojów klientów (np.<br>oświadczenie faktoid). |
> | action | Microsoft.CognitiveServices/accounts/usages/read | Pobierz użycie przydziału dla istniejącego zasobu. |
> | action | Microsoft.CognitiveServices/accounts/write | Zapisuje konta interfejsów API. |
> | action | Microsoft.CognitiveServices/checkDomainAvailability/action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | action | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | action | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Powiadomienie z Microsoft.Network usunięcia VirtualNetworks lub podsieci. |
> | action | Microsoft.CognitiveServices/Operations/read | Lista wszystkich dostępnych operacji |
> | action | Microsoft.CognitiveServices/register/action | Rejestruje subskrypcję dla usług Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Commerce/RateCard/read | Zwraca oferują danych, Miernik zasobów/metadanych i współczynniki dla danej subskrypcji. |
> | action | Microsoft.Commerce/UsageAggregates/read | Pobiera Microsoft Azure — użytek przez subskrypcję. Wynik zawiera agreguje dane dotyczące użycia, subskrypcji i zasobów powiązane informacje w określonym czasie. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Compute/availabilitySets/delete | Usuwa zestaw dostępności |
> | action | Microsoft.Compute/availabilitySets/read | Pobierz właściwości zestawu dostępności |
> | action | Microsoft.Compute/availabilitySets/vmSizes/read | Wyświetl dostępne rozmiary na potrzeby tworzenia lub aktualizowania maszyny wirtualnej w zestawie dostępności |
> | action | Microsoft.Compute/availabilitySets/write | Tworzy nowy zestaw dostępności lub aktualizuje istniejący zestaw |
> | action | Microsoft.Compute/disks/beginGetAccess/action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dysku na potrzeby uzyskiwania dostępu do obiektu blob |
> | action | Microsoft.Compute/disks/delete | Usuwa dysk |
> | action | Microsoft.Compute/disks/endGetAccess/action | Wycofaj identyfikator URI sygnatury dostępu współdzielonego dysku |
> | action | Microsoft.Compute/disks/read | Pobierz właściwości dysku |
> | action | Microsoft.Compute/disks/write | Tworzy nowy dysk lub aktualizuje istniejący |
> | action | Microsoft.Compute/galleries/delete | Usuwa galerię |
> | action | Microsoft.Compute/galleries/images/delete | Usuwa obraz galerii |
> | action | Microsoft.Compute/galleries/images/read | Pobiera właściwości obrazu galerii |
> | action | Microsoft.Compute/galleries/images/versions/delete | Usuwa wersję obrazu galerii |
> | action | Microsoft.Compute/galleries/images/versions/read | Pobiera właściwości wersji obrazu galerii |
> | action | Microsoft.Compute/galleries/images/versions/write | Tworzy nową wersję obrazu galerii lub aktualizuje istniejącą wersję |
> | action | Microsoft.Compute/galleries/images/write | Tworzy nowy obraz galerii lub aktualizuje istniejący obraz |
> | action | Microsoft.Compute/galleries/read | Pobiera właściwości galerii |
> | action | Microsoft.Compute/galleries/write | Tworzy nową galerię lub aktualizuje istniejącą galerię |
> | action | Microsoft.Compute/hostGroups/delete | Usuwa grupy hostów |
> | action | Microsoft.Compute/hostGroups/read | Pobierz właściwości grupy hostów |
> | action | Microsoft.Compute/hostGroups/write | Tworzy nową grupę hostów lub aktualizuje istniejące grupy hostów |
> | action | Microsoft.Compute/hosts/delete | Usuwa hosta |
> | action | Microsoft.Compute/hosts/read | Pobierz właściwości hosta |
> | action | Microsoft.Compute/hosts/write | Tworzy nowy host lub aktualizuje istniejący host |
> | action | Microsoft.Compute/images/delete | Usuwa obraz |
> | action | Microsoft.Compute/images/read | Pobierz właściwości obrazu |
> | action | Microsoft.Compute/images/write | Tworzy nowy obraz lub aktualizuje istniejący obraz |
> | action | Microsoft.Compute/locations/capsOperations/read | Pobiera stan operacji asynchronicznej Caps |
> | action | Microsoft.Compute/locations/diskOperations/read | Pobiera stan operacji asynchronicznej dysku |
> | action | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Utwórz dzienniki, aby pokazać całkowitą liczbę żądań według przedziału czasu w celu ułatwienia diagnostyki ograniczania. |
> | action | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Utwórz dzienniki, aby pokazać zagregowane wartości ograniczonych żądań pogrupowane według wartości ResourceName lub OperationName albo zastosowanych zasad ograniczania. |
> | action | Microsoft.Compute/locations/operations/read | Pobiera stan operacji asynchronicznej |
> | action | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Pobrać właściwości oferty obrazu platformy |
> | action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Pobrać właściwości jednostki SKU obrazu platformy |
> | action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Pobrać właściwości wersji obrazu platformy |
> | action | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Pobierz właściwości typu VMExtension |
> | action | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Pobierz właściwości wersji VMExtension |
> | action | Microsoft.Compute/locations/publishers/read | Pobierz właściwości wydawcy |
> | action | Microsoft.Compute/locations/runCommands/read | Wyświetla listę dostępnych poleceń uruchamiania w lokalizacji |
> | action | Microsoft.Compute/locations/usages/read | Pobiera limity usług i bieżące dane użycia zasobów obliczeniowych subskrypcji w danej lokalizacji |
> | action | Microsoft.Compute/locations/vmSizes/read | Wyświetla listę dostępnych rozmiarów maszyny wirtualnej w danej lokalizacji |
> | action | Microsoft.Compute/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.Compute |
> | action | Microsoft.Compute/proximityPlacementGroups/delete | Usuwa grupę umieszczania w pobliżu |
> | action | Microsoft.Compute/proximityPlacementGroups/read | Pobierz właściwości grupy umieszczania w pobliżu |
> | action | Microsoft.Compute/proximityPlacementGroups/write | Tworzy nową grupę umieszczania w pobliżu lub aktualizuje istniejącą |
> | action | Microsoft.Compute/register/action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft.Compute |
> | action | Microsoft.Compute/restorePointCollections/delete | Usuwa kolekcję punktów przywracania i należące do niej punkty przywracania |
> | action | Microsoft.Compute/restorePointCollections/read | Pobierz właściwości kolekcji punktów przywracania |
> | action | Microsoft.Compute/restorePointCollections/restorePoints/delete | Usuwa punkt przywracania |
> | action | Microsoft.Compute/restorePointCollections/restorePoints/read | Pobierz właściwości punktu przywracania |
> | action | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Pobierz właściwości punktu przywracania z identyfikatorami URI SAS obiektów blob |
> | action | Microsoft.Compute/restorePointCollections/restorePoints/write | Tworzy nowy punkt przywracania |
> | action | Microsoft.Compute/restorePointCollections/write | Tworzy nową kolekcję punktów przywracania lub aktualizuje istniejącą |
> | action | Microsoft.Compute/sharedVMImages/delete | Usuwa element SharedVMImage |
> | action | Microsoft.Compute/sharedVMImages/read | Pobierz właściwości elementu SharedVMImage |
> | action | Microsoft.Compute/sharedVMImages/versions/delete | Usuń element SharedVMImageVersion |
> | action | Microsoft.Compute/sharedVMImages/versions/read | Pobierz właściwości elementu SharedVMImageVersion |
> | action | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replikuj element SharedVMImageVersion do regionów docelowych |
> | action | Microsoft.Compute/sharedVMImages/versions/write | Utwórz nowy element SharedVMImageVersion lub zaktualizuj istniejący |
> | action | Microsoft.Compute/sharedVMImages/write | Tworzy nowy element SharedVMImage lub aktualizuje istniejący |
> | action | Microsoft.Compute/skus/read | Pobiera listę jednostek SKU Microsoft.Compute dostępnych dla subskrypcji |
> | action | Microsoft.Compute/snapshots/beginGetAccess/action | Pobierz identyfikator URI sygnatury dostępu Współdzielonego migawki dla dostępu do obiektów blob |
> | action | Microsoft.Compute/snapshots/delete | Usuń migawkę |
> | action | Microsoft.Compute/snapshots/endGetAccess/action | Odwołanie identyfikatora URI sygnatury dostępu Współdzielonego migawki |
> | action | Microsoft.Compute/snapshots/read | Pobierz właściwości migawki |
> | action | Microsoft.Compute/snapshots/write | Utwórz nową migawkę lub zaktualizuj istniejącą |
> | action | Microsoft.Compute/unregister/action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft.Compute |
> | action | Microsoft.Compute/virtualMachines/capture/action | Przechwytuje maszynę wirtualną przez skopiowanie wirtualnych dysków twardych oraz generuje szablon umożliwiający tworzenie podobnych maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Konwertuje oparte na obiektach blob dyski maszyny wirtualnej na dyski zarządzane |
> | action | Microsoft.Compute/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | action | Microsoft.Compute/virtualMachines/delete | Usuwa maszynę wirtualną |
> | action | Microsoft.Compute/virtualMachines/extensions/delete | Usuwa rozszerzenie maszyny wirtualnej |
> | action | Microsoft.Compute/virtualMachines/extensions/read | Pobierz właściwości rozszerzenia maszyny wirtualnej |
> | action | Microsoft.Compute/virtualMachines/extensions/write | Tworzy nowe rozszerzenie maszyny wirtualnej lub aktualizuje istniejące rozszerzenie |
> | action | Microsoft.Compute/virtualMachines/generalize/action | Ustawia stan maszyny wirtualnej „Uogólniono” i przygotowuje maszynę wirtualną do przechwycenia |
> | action | Microsoft.Compute/virtualMachines/instanceView/read | Pobiera szczegółowy stan środowiska uruchomieniowego dla maszyny wirtualnej i jej zasobów |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Zaloguj się do maszyny wirtualnej z uprawnieniami administratora systemu Windows lub użytkownika root systemu Linux |
> | action | Microsoft.Compute/virtualMachines/performMaintenance/action | Wykonuje operację konserwacji maszyny wirtualnej. |
> | action | Microsoft.Compute/virtualMachines/powerOff/action | Wyłącza maszynę wirtualną. Należy zauważyć, że maszyna wirtualna będzie w dalszym ciągu objęte rozliczeniami. |
> | action | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | action | Microsoft.Compute/virtualMachines/redeploy/action | Ponownie wdraża maszynę wirtualną |
> | action | Microsoft.Compute/virtualMachines/reimage/action | Odtwarza z obrazu maszynę wirtualną używającą dysku różnicowego. |
> | action | Microsoft.Compute/virtualMachines/restart/action | Uruchamia ponownie maszynę wirtualną |
> | action | Microsoft.Compute/virtualMachines/runCommand/action | Wykonuje wstępnie zdefiniowany skrypt na maszynie wirtualnej |
> | action | Microsoft.Compute/virtualMachines/start/action | Uruchamia maszynę wirtualną |
> | action | Microsoft.Compute/virtualMachines/vmSizes/read | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
> | action | Microsoft.Compute/virtualMachines/write | Tworzy nową maszynę wirtualną lub aktualizuje istniejącą maszynę wirtualną |
> | action | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Wyłącza i zwalnia zasoby obliczeniowe dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | action | Microsoft.Compute/virtualMachineScaleSets/delete | Usuwa zestaw skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/delete/action | Usuwa wystąpienia zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Usuwa rozszerzenie zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Pobiera właściwości rozszerzenia zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Tworzy nowe rozszerzenie zestawu skalowania maszyn wirtualnych lub aktualizuje już istniejące |
> | action | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Ręcznie skontroluj domeny aktualizacji platformy w sieci szkieletowej zestawu skalowania maszyn wirtualnych, aby ukończyć oczekującą aktualizację, która się zablokowała |
> | action | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Pobiera widok wystąpienia zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Ręcznie aktualizuje wystąpienia do najnowszego modelu zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Pobierz właściwości wszystkich interfejsów sieciowych zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Uruchamia uaktualnienie stopniowe, aby przenieść wszystkie wystąpienia zestawu skalowania maszyn wirtualnych do najnowszej dostępnej wersji obrazu systemu operacyjnego. |
> | action | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Pobiera historię uaktualniania systemu operacyjnego dla zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Przeprowadza planowaną konserwację na wystąpieniach zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Wyłącza wystąpienia zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Pobierz właściwości wszystkich publicznych adresów IP zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/read | Pobiera właściwości zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Wdróż ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Odtwarza z obrazu wystąpienia zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) wystąpień zestawu skalowania maszyn wirtualnych  |
> | action | Microsoft.Compute/virtualMachineScaleSets/restart/action | Uruchamia ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Anuluje operację stopniowego uaktualniania zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Pobierz najnowszy stan stopniowego uaktualniania dla zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/scale/action | Sprawdź, czy dla istniejącego zestawu skalowania maszyn wirtualnych można przeprowadzić skalowanie do wewnątrz / na zewnątrz do określonej liczby wystąpień |
> | action | Microsoft.Compute/virtualMachineScaleSets/skus/read | Wyświetla prawidłowe jednostki SKU dla istniejącego zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/start/action | Uruchamia wystąpienia zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Wyłącza i zwalnia zasoby obliczeniowe dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Usuń konkretną maszynę wirtualną z zestawu skalowania maszyn wirtualnych. |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Pobiera widok wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Pobierz właściwości publicznego adresu IP utworzonego za pomocą zestawu skalowania maszyn wirtualnych. Zestaw skalowania maszyn wirtualnych można utworzyć najwyżej jeden publiczny adres IP na konfigurację protokołu (prywatny adres IP) |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Pobierz właściwości jeden lub wszystkie konfiguracje protokołu IP interfejsu sieciowego utworzonego za pomocą zestawu skalowania maszyn wirtualnych. Konfiguracje protokołu IP reprezentują prywatne adresy IP |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Pobierz właściwości jednego lub wszystkich interfejsów sieciowych maszyny wirtualnej utworzonej za pomocą zestawu skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Przeprowadza planowaną konserwację na wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Wyłącza wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Pobiera właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Wdraża ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Odtwarza z obrazu wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Uruchamia ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Wykonuje wstępnie zdefiniowany skrypt na wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Uruchamia wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Aktualizuje właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | action | Microsoft.Compute/virtualMachineScaleSets/write | Tworzy nowy zestaw skalowania maszyn wirtualnych lub aktualizuje istniejący zestaw |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Consumption/balances/read | Lista użycia podsumowania dla okresu rozliczeniowego dla grupy zarządzania. |
> | action | Microsoft.Consumption/budgets/delete | Usuń budżety, subskrypcji lub grupy zarządzania. |
> | action | Microsoft.Consumption/budgets/read | Wyświetl listę budżetów subskrypcji lub grupy zarządzania. |
> | action | Microsoft.Consumption/budgets/write | Tworzy i aktualizowania budżetów subskrypcji lub grupy zarządzania. |
> | action | Microsoft.Consumption/charges/read | Lista opłat |
> | action | Microsoft.Consumption/credits/read | Lista środki na korzystanie z |
> | action | Microsoft.Consumption/events/read | Wyświetl listę zdarzeń |
> | action | Microsoft.Consumption/forecasts/read | Lista prognoz |
> | action | Microsoft.Consumption/lots/read | Wiele list |
> | action | Microsoft.Consumption/marketplaces/read | Lista szczegółów użycia zasobów witryny marketplace dla zakresu dla subskrypcji EA i webdirect, dla których. |
> | action | Microsoft.Consumption/operationresults/read | Lista operationresults |
> | action | Microsoft.Consumption/operations/read | Wyświetl listę wszystkich obsługiwanych operacji przez dostawcę zasobów Microsoft.Consumption. |
> | action | Microsoft.Consumption/operationstatus/read | Lista operationstatus |
> | action | Microsoft.Consumption/pricesheets/read | Lista danych Pricesheets dla subskrypcji lub grupy zarządzania. |
> | action | Microsoft.Consumption/register/action | Zarejestruj się, aby użycie jednostki Uzależnionej |
> | action | Microsoft.Consumption/reservationDetails/read | Lista szczegółów użycia wystąpienia zarezerwowane przez rezerwacji zamówienia lub zarządzania grupy. Jest szczegółowych danych na wystąpienie na poziomie dnia. |
> | action | Microsoft.Consumption/reservationRecommendations/read | Lista jednego lub udostępnione zalecenia dotyczące wystąpień zarezerwowanych w ramach subskrypcji. |
> | action | Microsoft.Consumption/reservationSummaries/read | Lista użycia podsumowanie wystąpienia zarezerwowane przez rezerwacji zamówienia lub zarządzania grupy. Podsumowanie danych może być na poziomie miesięcznej lub dziennego. |
> | action | Microsoft.Consumption/reservationTransactions/read | Lista historii transakcji dla wystąpienia zarezerwowane przez grup zarządzania. |
> | action | Microsoft.Consumption/tags/read | Tworzenie listy tagów dla subskrypcji i umowy EA. |
> | action | Microsoft.Consumption/tenants/read | Wykaz dzierżaw |
> | action | Microsoft.Consumption/tenants/register/action | Rejestrowanie akcji dla zakresu Microsoft.Consumption przez dzierżawcę. |
> | action | Microsoft.Consumption/terms/read | Lista warunków dla subskrypcji lub grupy zarządzania. |
> | action | Microsoft.Consumption/usageDetails/read | Wyświetl listę szczegóły użycia dla zakresu dla subskrypcji EA i webdirect, dla których. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec w określonym kontenerze. |
> | action | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Pobierz dzienniki dla określonego kontenera. |
> | action | Microsoft.ContainerInstance/containerGroups/delete | Usuń określoną grupę kontenerów. |
> | action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla grupy kontenerów. |
> | action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla grupy kontenerów. |
> | action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla grupy kontenerów. |
> | action | Microsoft.ContainerInstance/containerGroups/read | Pobierz wszystkie grupy kontenerów. |
> | action | Microsoft.ContainerInstance/containerGroups/restart/action | Uruchamia ponownie konkretną grupę kontenerów. |
> | action | Microsoft.ContainerInstance/containerGroups/start/action | Uruchamia konkretną grupę kontenerów. |
> | action | Microsoft.ContainerInstance/containerGroups/stop/action | Zatrzymuje określoną grupę kontenerów. Obliczenia, będzie można cofnąć alokacji zasobów i rozliczanie zostanie zatrzymane. |
> | action | Microsoft.ContainerInstance/containerGroups/write | Utwórz lub zaktualizuj określoną grupę kontenerów. |
> | action | Microsoft.ContainerInstance/locations/cachedImages/read | Pobiera obrazy przechowywane w pamięci podręcznej dla subskrypcji w regionie. |
> | action | Microsoft.ContainerInstance/locations/capabilities/read | Dostęp do potrzebnych możliwości dla regionu. |
> | action | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia element Microsoft.ContainerInstance o usuwaniu sieci wirtualnej lub podsieci. |
> | action | Microsoft.ContainerInstance/locations/usages/read | Pobierz użycie dla określonego regionu. |
> | action | Microsoft.ContainerInstance/register/action | Rejestruje subskrypcję dostawcy zasobów wystąpień kontenerów i umożliwia tworzenie grup kontenerów. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ContainerRegistry/checkNameAvailability/read | Sprawdza, czy nazwa rejestru kontenerów jest dostępny do użytku. |
> | action | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft.ContainerRegistry, że trwa usuwanie sieci wirtualnej lub podsieci |
> | action | Microsoft.ContainerRegistry/locations/operationResults/read | Pobiera wynik operacji asynchronicznej |
> | action | Microsoft.ContainerRegistry/operations/read | Wyświetla listę wszystkich dostępnych operacji API REST usługi Azure Container Registry |
> | action | Microsoft.ContainerRegistry/register/action | Rejestruje subskrypcję dostawcy zasobów rejestru kontenera i włącza funkcję tworzenia rejestrów kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/artifacts/delete | Usuwanie artefaktów w rejestrze kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/builds/cancel/action | Anuluje istniejącą kompilację. |
> | action | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Pobiera link do pobierania dzienników kompilacji. |
> | action | Microsoft.ContainerRegistry/registries/builds/read | Pobiera właściwości określonej kompilacji lub wyświetla wszystkie kompilacje dla określonego rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/builds/write | Aktualizuje kompilacji dla rejestru kontenerów przy użyciu określonych parametrów. |
> | action | Microsoft.ContainerRegistry/registries/buildTasks/delete | Usuwa zadanie kompilacji z rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Zawiera listę właściwości kontroli źródła dla zadania kompilacji. |
> | action | Microsoft.ContainerRegistry/registries/buildTasks/read | Pobiera właściwości zadania określonej kompilacji lub wyświetla listę wszystkich zadań kompilacji dla określonego rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Usuwa kroku budowania z zadania kompilacji. |
> | action | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Wyświetla listę argumentów kompilacji dla kroku kompilacji, łącznie z argumentami wpisu tajnego. |
> | action | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Pobiera właściwości kroku kompilacji określonego lub wyświetla ich listę wszystkich kroków kompilacji dla zadania określonej kompilacji. |
> | action | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Tworzy lub aktualizuje kroku kompilacji dla zadania kompilacji z określonymi parametrami. |
> | action | Microsoft.ContainerRegistry/registries/buildTasks/write | Tworzy lub aktualizuje zadanie kompilacji dla rejestru kontenerów przy użyciu określonych parametrów. |
> | action | Microsoft.ContainerRegistry/registries/delete | Usuwa z rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Usuwa filtr siatki zdarzeń z rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Pobiera właściwości filtru siatki określonego zdarzenia lub wyświetla listę wszystkich filtrów siatki zdarzeń dla określonego rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Tworzy lub aktualizuje filtr siatki zdarzeń dla rejestru kontenerów przy użyciu określonych parametrów. |
> | action | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Pobiera lokalizację przekazywania dla użytkownika można było przekazać źródła. |
> | action | Microsoft.ContainerRegistry/registries/importImage/action | Importuj obraz do rejestru kontenerów przy użyciu określonych parametrów. |
> | action | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Uzyskiwanie źródła przekazywania lokalizacji adresu url dla rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/listCredentials/action | Wyświetla listę poświadczenia logowania dla określonego rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/listPolicies/read | Wyświetla listę zasad dla określonego rejestru kontenerów |
> | action | Microsoft.ContainerRegistry/registries/listUsages/read | Wyświetla użycie limitu przydziału dla określonego rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/metadata/read | Pobiera metadane dla określonego repozytorium na potrzeby rejestru kontenerów |
> | action | Microsoft.ContainerRegistry/registries/metadata/write | Aktualizuje metadane repozytorium dla rejestru kontenerów |
> | action | Microsoft.ContainerRegistry/registries/operationStatuses/read | Pobiera stan operacji asynchronicznej dla rejestru |
> | action | Microsoft.ContainerRegistry/registries/pull/read | Ściągnij lub pobieranie obrazów z rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/push/write | Wypychanie lub zapisanie obrazów do rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/quarantineRead/read | Ściąganie lub uzyskać poddane kwarantannie obrazów z rejestru kontenerów |
> | action | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Zapisu/modyfikowania stan kwarantanny obrazów poddane kwarantannie |
> | action | Microsoft.ContainerRegistry/registries/queueBuild/action | Tworzy nową kompilację na podstawie parametrów żądania i dodać go do kolejki kompilacji. |
> | action | Microsoft.ContainerRegistry/registries/read | Pobiera właściwości określonego rejestru kontenerów lub wyświetla ich listę rejestrów kontenerów w ramach określonej grupy zasobów lub subskrypcji. |
> | action | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Ponownie generuje jednego poświadczenia logowania dla określonego rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/replications/delete | Usuwa replikacji z rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Pobiera stan operacji asynchronicznej replikacji |
> | action | Microsoft.ContainerRegistry/registries/replications/read | Pobiera właściwości określonej replikacji lub wyświetla ich listę wszystkich replikacji dla określonego rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/replications/write | Tworzy lub aktualizuje replikacji dla rejestru kontenerów przy użyciu określonych parametrów. |
> | action | Microsoft.ContainerRegistry/registries/runs/cancel/action | Anuluj przebieg istniejących. |
> | action | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Pobiera dziennika adresu URL sygnatury dostępu Współdzielonego dla przebiegu. |
> | action | Microsoft.ContainerRegistry/registries/runs/read | Pobiera właściwości wykonywania względem rejestru kontenerów lub listy przebiegów. |
> | action | Microsoft.ContainerRegistry/registries/runs/write | Aktualizuje przebiegu. |
> | action | Microsoft.ContainerRegistry/registries/scheduleRun/action | Planowanie wykonywania względem rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/sign/write | / Wypychania zawartości zaufania metadanych dla rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/tasks/delete | Usuwa zadanie dla rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Wyświetl listę wszystkich szczegółów zadania dla rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/tasks/read | Pobiera zadanie dla rejestru kontenerów lub listy wszystkich zadań. |
> | action | Microsoft.ContainerRegistry/registries/tasks/write | Tworzy lub aktualizuje zadanie dla rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/updatePolicies/write | Aktualizuje zasady dla określonego rejestru kontenerów |
> | action | Microsoft.ContainerRegistry/registries/webhooks/delete | Usuwa element webhook z rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Pobiera konfigurację identyfikator URI usługi i nagłówki niestandardowe dla elementu webhook. |
> | action | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Wyświetla listę najnowszych zdarzeń dla określonego elementu webhook. |
> | action | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Pobiera stan operacji asynchronicznej dla elementu webhook |
> | action | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Wyzwala zdarzenie typu ping do wysłania do elementu webhook. |
> | action | Microsoft.ContainerRegistry/registries/webhooks/read | Pobiera właściwości określonego elementu webhook lub wyświetla ich listę wszystkich elementów webhook dla określonego rejestru kontenerów. |
> | action | Microsoft.ContainerRegistry/registries/webhooks/write | Tworzy lub aktualizuje element webhook usługi container registry przy użyciu określonych parametrów. |
> | action | Microsoft.ContainerRegistry/registries/write | Tworzy lub aktualizuje rejestr kontenerów z określonymi parametrami. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ContainerService/containerServices/delete | Usuwa usługę kontenera |
> | action | Microsoft.ContainerService/containerServices/read | Pobierz usługę kontenera |
> | action | Microsoft.ContainerService/containerServices/write | Tworzy nową usługę kontenera lub aktualizuje istniejącą |
> | action | Microsoft.ContainerService/locations/operationresults/read | Pobiera stan wyniku operacji asynchronicznej |
> | action | Microsoft.ContainerService/locations/operations/read | Pobiera stan operacji asynchronicznej |
> | action | Microsoft.ContainerService/locations/orchestrators/read | Wyświetla listę obsługiwanych orkiestratorów |
> | action | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Pobierz profil dostępu do klastra zarządzanego według nazwy roli przy użyciu poświadczeń listy |
> | action | Microsoft.ContainerService/managedClusters/accessProfiles/read | Pobierz profil dostępu do klastra zarządzanego według nazwy roli |
> | action | Microsoft.ContainerService/managedClusters/agentPools/delete | Usuwa pulę agentów |
> | action | Microsoft.ContainerService/managedClusters/agentPools/read | Pobiera pulę agentów |
> | action | Microsoft.ContainerService/managedClusters/agentPools/write | Tworzy nową pulę agentów lub aktualizuje istniejący zestaw |
> | action | Microsoft.ContainerService/managedClusters/delete | Usuwa klaster zarządzany |
> | action | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Wyświetl listę poświadczeń administratora clusterAdmin klastra zarządzanego |
> | action | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Wyświetl listę poświadczeń użytkownika clusterUser klastra zarządzanego |
> | action | Microsoft.ContainerService/managedClusters/read | Pobierz klaster zarządzany |
> | action | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Resetowanie profilu usługi AAD zarządzanego klastra |
> | action | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Resetowanie profilu jednostki usługi z zarządzanego klastra |
> | action | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Pobiera profil uaktualniania klastra |
> | action | Microsoft.ContainerService/managedClusters/write | Tworzy nowy klaster zarządzany lub aktualizuje istniejący |
> | action | Microsoft.ContainerService/openShiftClusters/delete | Usuwanie klastra usługi Open Shift |
> | action | Microsoft.ContainerService/openShiftClusters/read | Pobierz klaster Open Shift |
> | action | Microsoft.ContainerService/openShiftClusters/write | Tworzy nowy klaster Open Shift lub aktualizuje istniejący zestaw |
> | action | Microsoft.ContainerService/openShiftManagedClusters/delete | Usunąć klaster zarządzanego Open Shift |
> | action | Microsoft.ContainerService/openShiftManagedClusters/read | Pobierz Open Shift zarządzanego klastra |
> | action | Microsoft.ContainerService/openShiftManagedClusters/write | Tworzy nowy Open Shift zarządzany klaster lub aktualizuje istniejący zestaw |
> | action | Microsoft.ContainerService/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.ContainerService |
> | action | Microsoft.ContainerService/register/action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft.ContainerService |
> | action | Microsoft.ContainerService/unregister/action | Wyrejestrowuje subskrypcję przy użyciu dostawcy zasobów Microsoft.ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ContentModerator/applications/delete | Operacja usuwania |
> | action | Microsoft.ContentModerator/applications/listSecrets/action | Wyświetl klucze tajne |
> | action | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Odczyt jednokrotnego tokenów |
> | action | Microsoft.ContentModerator/applications/read | Operacja odczytu |
> | action | Microsoft.ContentModerator/applications/write | Operacja zapisu |
> | action | Microsoft.ContentModerator/applications/write | Operacja zapisu |
> | action | Microsoft.ContentModerator/listCommunicationPreference/action | Wyświetl Preferencje dotyczące komunikacji |
> | action | Microsoft.ContentModerator/operations/read | operacje odczytu |
> | action | Microsoft.ContentModerator/updateCommunicationPreference/action | Aktualizuj preferencje dotyczące komunikacji |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.CostManagement/cloudConnectors/delete | Usuń określoną wartość cloudConnector. |
> | action | Microsoft.CostManagement/cloudConnectors/read | Wyświetl listę cloudConnectors dla tego uwierzytelnionego użytkownika. |
> | action | Microsoft.CostManagement/cloudConnectors/write | Utwórz lub zaktualizuj określoną wartość cloudConnector. |
> | action | Microsoft.CostManagement/dimensions/read | Wyświetl listę wszystkich obsługiwanych wymiarów przez zakres. |
> | action | Microsoft.CostManagement/exports/action | Uruchom określony eksportu. |
> | action | Microsoft.CostManagement/exports/delete | Usuń eksport określony. |
> | action | Microsoft.CostManagement/exports/read | Wyświetl listę eksportu w zakresie. |
> | action | Microsoft.CostManagement/exports/run/action | Eksportuje przebiegu. |
> | action | Microsoft.CostManagement/exports/write | Utwórz lub zaktualizuj określoną eksportu. |
> | action | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Wyświetl listę externalSubscriptions w ramach externalBillingAccount dla tego uwierzytelnionego użytkownika. |
> | action | Microsoft.CostManagement/externalBillingAccounts/read | Wyświetl listę externalBillingAccounts dla tego uwierzytelnionego użytkownika. |
> | action | Microsoft.CostManagement/externalSubscriptions/read | Wyświetl listę externalSubscriptions dla tego uwierzytelnionego użytkownika. |
> | action | Microsoft.CostManagement/externalSubscriptions/write | Aktualizowanie grupy zarządzania skojarzony z externalSubscription |
> | action | Microsoft.CostManagement/query/action | Wykonywanie zapytań o dane użycia przez zakres. |
> | action | Microsoft.CostManagement/query/read | Wykonywanie zapytań o dane użycia przez zakres. |
> | action | Microsoft.CostManagement/register/action | Rejestrowanie akcji dla zakresu Microsoft.CostManagement przez subskrypcję. |
> | action | Microsoft.CostManagement/reports/action | Harmonogram raportów dotyczących danych użycia w ramach zakresu. |
> | action | Microsoft.CostManagement/reports/read | Harmonogram raportów dotyczących danych użycia w ramach zakresu. |
> | action | Microsoft.CostManagement/tenants/register/action | Rejestrowanie akcji dla zakresu Microsoft.CostManagement przez dzierżawcę. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.CustomerInsights/hubs/adobemetadata/action | Utwórz lub zaktualizuj wszelkie metadane Adobe Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/adobemetadata/read | Odczyt wszystkich metadanych, Adobe Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Usuwanie zasad sygnatury dostępu współdzielonego żadnych klientów platformy Azure szczegółowe informacje |
> | action | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Odczytać dowolny Azure Customer Insights współużytkowane zasady sygnatury dostępu |
> | action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Wygeneruj ponownie klucz podstawowy Azure Customer Insights zasady dostępu współużytkowanego podpisu |
> | action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Wygeneruj ponownie klucz pomocniczy Azure Customer Insights zasady dostępu współużytkowanego podpisu |
> | action | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Utwórz lub zaktualizuj żadnych zasad sygnatury dostępu współdzielonego Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktywuj każdy łącznik Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktywuj każdy łącznik Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/delete | Usunięcie łącznika Insights dowolnego klienta na platformie Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Pobierz stan czasu wykonywania żadnych łącznika usługi Azure Customer Insights |
> | action | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Aktywuj żadnego mapowania łącznika Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Usuń mapowanie łącznika Insights dowolnego klienta na platformie Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Przeczytaj dowolny wynik operacji mapowanie łącznika szczegółowych informacji klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Przeczytaj żadnego mapowania łącznika Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Utwórz lub zaktualizuj wszelkie mapowanie łącznika Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/operations/read | Przeczytaj dowolny wynik operacji łącznika usługi Azure Customer Insights |
> | action | Microsoft.CustomerInsights/hubs/connectors/read | Przeczytaj każdy łącznik Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Utwórz lub zaktualizuj wszelkie klientów platformy Azure Insights łącznika uwierzytelniania bramą |
> | action | Microsoft.CustomerInsights/hubs/connectors/update/action | Zaktualizuj łącznik Insights dowolnego klienta na platformie Azure |
> | action | Microsoft.CustomerInsights/hubs/connectors/write | Utwórz lub zaktualizuj wszelkie łącznik Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/crmmetadata/action | Utwórz lub zaktualizuj wszelkie metadane Crm Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/crmmetadata/read | Odczyt wszystkich klientów platformy Azure Insights Crm metadanych |
> | action | Microsoft.CustomerInsights/hubs/delete | Usunąć dowolne Centrum Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/gdpr/delete | Usuń wszelkie rodo Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/gdpr/read | Przeczytaj rodo Insights dowolnego klienta na platformie Azure |
> | action | Microsoft.CustomerInsights/hubs/gdpr/write | Utwórz lub zaktualizuj wszelkie rodo Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Uzyskaj środki na korzystanie z systemu klienta usługi Azure Insights Centrum rozliczeń |
> | action | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Pobieranie historii rozliczeń Centrum Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/images/delete | Usuń dowolny obraz Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/images/read | Przeczytaj dowolnego obrazu Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/images/write | Utwórz lub zaktualizuj dowolny obraz Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/interactions/delete | Usuń wszelkie Insights interakcyjnymi platformy azure |
> | action | Microsoft.CustomerInsights/hubs/interactions/operations/read | Przeczytaj dowolny wynik operacji Azure Customer Insights interakcji |
> | action | Microsoft.CustomerInsights/hubs/interactions/read | Przeczytaj szczegółowe informacje interakcji z klientem platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Zaproponuj relacji łączy dla żadnych interakcji Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/interactions/write | Utwórz lub zaktualizuj interakcji z klientem platformy Azure Insights |
> | action | Microsoft.CustomerInsights/hubs/kpi/delete | Usuń wszelkie klienta usługi Azure Insights kluczowy wskaźnik wydajności |
> | action | Microsoft.CustomerInsights/hubs/kpi/operations/read | Przeczytaj dowolny wynik operacji usługi Azure Insights klienta kluczowych wskaźników wydajności |
> | action | Microsoft.CustomerInsights/hubs/kpi/read | Przeczytaj dowolnego klienta usługi Azure Insights kluczowy wskaźnik wydajności |
> | action | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Ponowne przetwarzanie dowolnego klienta na platformie Azure Insights kluczowych wskaźników wydajności |
> | action | Microsoft.CustomerInsights/hubs/kpi/write | Utwórz lub zaktualizuj wszelkie klienta usługi Azure Insights kluczowy wskaźnik wydajności |
> | action | Microsoft.CustomerInsights/hubs/links/delete | Usuń wszystkie linki do szczegółowych informacji klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/links/operations/read | Przeczytaj dowolny wynik operacji łącza Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/links/read | Odczytywać wszystkie linki do szczegółowych informacji klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/links/write | Utwórz lub zaktualizuj wszelkie linki klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/msemetadata/action | Utwórz lub zaktualizuj wszelkie metadane Mse Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/msemetadata/read | Odczyt wszystkich klientów platformy Azure Insights Mse metadanych |
> | action | Microsoft.CustomerInsights/hubs/operationresults/read | Pobierz wyniki operacji centrum Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/predictions/delete | Usuń wszelkie prognozy Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/predictions/operations/read | Przeczytaj dowolny wynik operacji Azure Customer Insights prognozy |
> | action | Microsoft.CustomerInsights/hubs/predictions/read | Przeczytaj prognozy Insights żadnych klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/predictions/write | Utwórz lub zaktualizuj wszelkie prognozy klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Usuń wszystkie zasady dopasowania Predykcyjnego Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Przeczytaj dowolny wynik operacji Azure klienta szczegółowych informacji predykcyjne dopasowania zasad |
> | action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Odczytywać wszystkie zasady dopasowania Predykcyjnego Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Utwórz lub zaktualizuj wszelkie zasady dopasowania Predykcyjnego Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/profiles/delete | Usuwanie profilu Insights żadnych klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/profiles/operations/read | Przeczytaj dowolny wynik operacji profil Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/profiles/read | Odczytuj profil Insights dowolnego klienta na platformie Azure |
> | action | Microsoft.CustomerInsights/hubs/profiles/write | Zapis żadnego profilu Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla zasobu |
> | action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla zasobu |
> | action | Microsoft.CustomerInsights/hubs/read | Przeczytaj dowolne Centrum Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Usuń wszystkie linki relacji Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Przeczytaj dowolny wynik operacji łączy relacji szczegółowych informacji klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Odczytywać wszystkie linki relacji Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Utwórz lub zaktualizuj wszelkie linki relacji Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/relationships/delete | Usuń wszystkie relacje Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/relationships/operations/read | Przeczytaj dowolny wynik operacji szczegółowych informacji z klientami platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/relationships/read | Przeczytaj relacje Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/relationships/write | Utwórz lub zaktualizuj wszystkie relacje Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Usunąć wszystkie przypisania Rbac Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Przeczytaj dowolny wynik operacji przypisania Rbac Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/roleAssignments/read | Odczytywać wszystkie przypisania Rbac Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/roleAssignments/write | Utwórz lub zaktualizuj wszelkie przypisania Rbac Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/roles/read | Odczytywać wszystkie role Rbac Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Utwórz lub zaktualizuj wszelkie metadane SalesForce Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Odczytaj wszystkie metadane SalesForce szczegółowych informacji klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/segments/delete | Usuń wszystkie segmenty Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Segmenty żadnych klientów platformy Azure szczegółowe informacje o dynamiczne zarządzanie |
> | action | Microsoft.CustomerInsights/hubs/segments/read | Przeczytaj szczegółowe informacje segmentów klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/segments/static/action | Segmenty systemu zarządzania wszystkie statyczne Insight klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/segments/write | Utwórz lub zaktualizuj segmentów klientów platformy Azure Insights |
> | action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Usuń wszelkie SqlConnectionStrings Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Przeczytaj SqlConnectionStrings Insights dowolnego klienta na platformie Azure |
> | action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Utwórz lub zaktualizuj wszelkie SqlConnectionStrings Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Generowanie zasugerować schematu typu z przykładowych danych |
> | action | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Zarządzaj ustawienia Centrum Azure Customer Insights |
> | action | Microsoft.CustomerInsights/hubs/views/delete | Usuń dowolny widok App Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/views/read | Przeczytaj dowolny widok App Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/views/write | Utwórz lub zaktualizuj dowolny widok App Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/widgettypes/read | Wszystkie typy aplikacji widżetu szczegółowych informacji klientów platformy Azure |
> | action | Microsoft.CustomerInsights/hubs/write | Utwórz lub zaktualizuj dowolne Centrum Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/operations/read | Przeczytaj Metadatas interfejsu Api usługi Insights klientów platformy Azure |
> | action | Microsoft.CustomerInsights/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Customer Insights i włącza funkcję tworzenia zasobów usługi Customer Insights |
> | action | Microsoft.CustomerInsights/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Customer Insights |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Umożliwia zarezerwowanie odbioru w celu wysyłki zwrotnej. |
> | action | Microsoft.DataBox/jobs/cancel/action | Anuluje zamówienie będące w toku. |
> | action | Microsoft.DataBox/jobs/delete | Usuwanie zamówienia |
> | action | Microsoft.DataBox/jobs/listCredentials/action | Wyświetla listę niezaszyfrowanych poświadczeń związanych z zamówieniem. |
> | action | Microsoft.DataBox/jobs/read | Wyświetl listę lub Pobierz zamówienia |
> | action | Microsoft.DataBox/jobs/write | Utwórz lub zaktualizuj zamówienia |
> | action | Microsoft.DataBox/locations/availableSkus/action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | action | Microsoft.DataBox/locations/operationResults/read | Wyświetl listę lub Pobierz wyniki operacji |
> | action | Microsoft.DataBox/locations/validateAddress/action | Weryfikuje adres wysyłkowy i udostępnia alternatywne adresy, jeśli istnieją. |
> | action | Microsoft.DataBox/register/action | Zarejestruj dostawcę Microsoft.Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Wyświetla lub pobiera alerty |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Wyświetla lub pobiera alerty |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Usuwa harmonogramy przepustowości |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Wyświetla lub pobiera harmonogramy przepustowości |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Wyświetla lub pobiera harmonogramy przepustowości |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Tworzy lub aktualizuje harmonogramy przepustowości |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Usuwa urządzenia brzegowe pole danych |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Pobierz aktualizacje na urządzeniu |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/extendedInformation/action | Pobiera rozszerzone informacje o zasobie |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Pobiera rozszerzone informacje o zasobie |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Zainstaluj aktualizacje na urządzeniu |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Wyświetla lub pobiera zadania |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Wyświetla lub pobiera ustawienia sieciowe urządzenia |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Wyświetla lub pobiera stan operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Usuwa zamówienia |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Wyświetla lub pobiera zamówienia |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Wyświetla lub pobiera zamówienia |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Tworzy lub aktualizuje zamówienia |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub pobiera urządzenia brzegowe pole danych |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub pobiera urządzenia brzegowe pole danych |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub pobiera urządzenia brzegowe pole danych |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Usuwa ról |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Wyświetla lub pobiera role |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Wyświetla lub pobiera role |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Tworzy lub aktualizuje ról |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Skanowanie w poszukiwaniu aktualizacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Aktualizowanie ustawień zabezpieczeń |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Usuwa akcji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Wyświetla lub pobiera akcje |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Wyświetla lub pobiera akcje |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Odświeżanie metadanych udostępniania, za pomocą danych z chmury |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Tworzy lub aktualizuje akcji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Usuwa poświadczenia konta magazynu |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Wyświetla lub pobiera poświadczenia konta magazynu |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Wyświetla lub pobiera poświadczenia konta magazynu |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Tworzy lub aktualizuje poświadczenia konta magazynu |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Usuwa wyzwalacze |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Wyświetla lub pobiera wyzwalacze |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Wyświetla lub pobiera wyzwalacze |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Tworzy lub aktualizuje wyzwalacze |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Wyświetla lub pobiera Podsumowanie aktualizacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Przekaż certyfikat do rejestracji urządzeń |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Usuwa użytkownicy udziału |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Wyświetla lub pobiera użytkownicy udziału |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Wyświetla lub pobiera użytkownicy udziału |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Tworzy lub aktualizuje użytkownicy udziału |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Tworzy lub aktualizuje urządzenia brzegowe pole danych |
> | action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Tworzy lub aktualizuje urządzenia brzegowe pole danych |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Databricks/locations/getNetworkPolicies/action | Pobierz zasady intencji sieci dla podsieci, w oparciu o lokalizację używany przez dostawcę NRP |
> | action | Microsoft.Databricks/register/action | Zarejestruj się w usłudze Databricks. |
> | action | Microsoft.Databricks/workspaces/delete | Usuwa obszar roboczy usługi Databricks. |
> | action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Ustawia dostępne ustawienia diagnostyczne dla obszaru roboczego usługi Databricks |
> | action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne definicje dzienników dla obszaru roboczego usługi Databricks |
> | action | Microsoft.Databricks/workspaces/read | Pobiera listę obszarów roboczych usługi Databricks. |
> | action | Microsoft.Databricks/workspaces/write | Tworzy obszar roboczy usługi Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DataCatalog/datacatalogs/delete | Usuń zasób DataCatalog dla dostawcy zasobów usługi Data Catalog. |
> | action | Microsoft.DataCatalog/datacatalogs/read | Odczytaj zasób DataCatalog dla dostawcy zasobów usługi Data Catalog. |
> | action | Microsoft.DataCatalog/datacatalogs/write | Napisz DataCatalog zasobów dla dostawcy zasobów usługi Data Catalog. |
> | action | Microsoft.DataCatalog/operations/read | Odczytuje wszystkie operacje dostępne w dostawcy zasobów usługi Data Catalog. |
> | action | Microsoft.DataCatalog/register/action | Rejestruj subskrypcję dostawcy zasobów usługi Data Catalog |
> | action | Microsoft.DataCatalog/unregister/action | Wyrejestruj subskrypcję dla dostawcy zasobów usługi Data Catalog |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Sprawdza, jeśli nazwa fabryki danych jest dostępne do użycia. |
> | action | Microsoft.DataFactory/datafactories/activitywindows/read | Odczytuje Windows działanie w usłudze Data Factory przy użyciu określonych parametrów. |
> | action | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Odczytuje Windows działania dla działania potoku przy użyciu określonych parametrów. |
> | action | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Odczytuje Windows działania dla potoku przy użyciu określonych parametrów. |
> | action | Microsoft.DataFactory/datafactories/datapipelines/delete | Usuwa dowolnym potoku. |
> | action | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Wstrzymuje dowolnym potoku. |
> | action | Microsoft.DataFactory/datafactories/datapipelines/read | Odczytuje w dowolnym potoku. |
> | action | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Wznawia dowolnym potoku. |
> | action | Microsoft.DataFactory/datafactories/datapipelines/update/action | Aktualizuje dowolnym potoku. |
> | action | Microsoft.DataFactory/datafactories/datapipelines/write | Tworzy lub aktualizuje dowolnym potoku. |
> | action | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Odczytuje Windows działanie dla zestawu danych z określonymi parametrami. |
> | action | Microsoft.DataFactory/datafactories/datasets/delete | Usuwa dowolnym zestawie danych. |
> | action | Microsoft.DataFactory/datafactories/datasets/read | Odczytuje dowolnym zestawie danych. |
> | action | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Odczytuje uruchomienia wycinka danych dla danego zestawu danych o podanej godzinie. |
> | action | Microsoft.DataFactory/datafactories/datasets/slices/read | Pobiera wycinki danych w danym okresie. |
> | action | Microsoft.DataFactory/datafactories/datasets/slices/write | Aktualizuje stan wycinka danych. |
> | action | Microsoft.DataFactory/datafactories/datasets/write | Tworzy lub aktualizuje dowolnym zestawie danych. |
> | action | Microsoft.DataFactory/datafactories/delete | Usuwa z fabryką danych. |
> | action | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Odczytuje informacje o połączeniu dla żadnej bramy. |
> | action | Microsoft.DataFactory/datafactories/gateways/delete | Usuwa wszystkie bramy. |
> | action | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Wyświetla listę kluczy uwierzytelniania dla żadnej bramy. |
> | action | Microsoft.DataFactory/datafactories/gateways/read | Odczytuje żadnej bramy. |
> | action | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Ponownie generuje klucze uwierzytelniania dla żadnej bramy. |
> | action | Microsoft.DataFactory/datafactories/gateways/write | Tworzy lub aktualizuje żadnej bramy. |
> | action | Microsoft.DataFactory/datafactories/linkedServices/delete | Usuwa wszystkie połączonej usługi. |
> | action | Microsoft.DataFactory/datafactories/linkedServices/read | Odczytuje wszystkie połączonej usługi. |
> | action | Microsoft.DataFactory/datafactories/linkedServices/write | Tworzy lub aktualizuje wszystkie połączonej usługi. |
> | action | Microsoft.DataFactory/datafactories/read | Odczytuje z fabryką danych. |
> | action | Microsoft.DataFactory/datafactories/runs/loginfo/read | Odczytuje identyfikator URI sygnatury dostępu Współdzielonego kontenera obiektów blob zawierający dzienniki. |
> | action | Microsoft.DataFactory/datafactories/tables/delete | Usuwa dowolnym zestawie danych. |
> | action | Microsoft.DataFactory/datafactories/tables/read | Odczytuje dowolnym zestawie danych. |
> | action | Microsoft.DataFactory/datafactories/tables/write | Tworzy lub aktualizuje dowolnym zestawie danych. |
> | action | Microsoft.DataFactory/datafactories/write | Tworzy lub aktualizuje fabryki danych. |
> | action | Microsoft.DataFactory/factories/cancelpipelinerun/action | Anuluje uruchomienie określonego przez identyfikator uruchomienia potoku |
> | action | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Tworzy sesję debugowania przepływu danych. |
> | action | Microsoft.DataFactory/factories/dataflows/delete | Usuwa przepływ danych. |
> | action | Microsoft.DataFactory/factories/dataflows/read | Odczytuje przepływ danych. |
> | action | Microsoft.DataFactory/factories/dataflows/write | Utwórz lub zaktualizuj przepływ danych |
> | action | Microsoft.DataFactory/factories/datasets/delete | Usuwa dowolnym zestawie danych. |
> | action | Microsoft.DataFactory/factories/datasets/read | Odczytuje dowolnym zestawie danych. |
> | action | Microsoft.DataFactory/factories/datasets/write | Tworzy lub aktualizuje dowolnym zestawie danych. |
> | action | Microsoft.DataFactory/factories/delete | Usuwa usługi Data Factory. |
> | action | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Usuwa sesję debugowania przepływu danych. |
> | action | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Pobiera dostęp do usługi ADF płaszczyzny danych. |
> | action | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Odczyty dostęp do usługi ADF płaszczyzny danych. |
> | action | Microsoft.DataFactory/factories/getFeatureValue/action | Pobierz wartość funkcji sterowania narażenia dla określonej lokalizacji. |
> | action | Microsoft.DataFactory/factories/getFeatureValue/read | Odczytuje wartość funkcji sterowania narażenia dla określonej lokalizacji. |
> | action | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Pobiera token dostępu usługi GitHub. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/delete | Usuwa wszystkie środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Odczytuje informacje o połączeniu ze środowiska uruchomieniowego integracji. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Pobierz metadane środowiska SSIS Integration Runtime dla określonego środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Odczytuje stan środowiska uruchomieniowego integracji. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Utwórz odwołanie do środowiska uruchomieniowego integracji połączone na określonym udostępnionego Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Wyświetla listę kluczy uwierzytelniania dla dowolnego środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Pobiera dane monitorowania dla dowolnego środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Usuwa węzła dla określonego środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Zwraca adres IP dla określonego węzła Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Odczytuje węzła dla określonego produktu Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Aktualizuje samodzielnie hostowany węzeł środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/read | Odczytuje wszystkie środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Odświeżanie środowiska SSIS Integration Runtime metadanych dla określonego środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Ponownie generuje klucze uwierzytelniania dla określonego środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Usuwa powiązane odwołania środowisko uruchomieniowe integracji z określonego produktu Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/start/action | Uruchamia wszystkie środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Zatrzymuje wszystkie środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Przeprowadza synchronizację poświadczeń dla określonego środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Uaktualnia określonego produktu Integration Runtime. |
> | action | Microsoft.DataFactory/factories/integrationruntimes/write | Tworzy lub aktualizuje wszystkie środowiska Integration Runtime. |
> | action | Microsoft.DataFactory/factories/linkedServices/delete | Usuwa połączoną usługę. |
> | action | Microsoft.DataFactory/factories/linkedServices/read | Odczyty połączoną usługę. |
> | action | Microsoft.DataFactory/factories/linkedServices/write | Utwórz lub zaktualizuj połączone usługi |
> | action | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Odczytuje uruchomienia działania określony identyfikator uruchomienia potoku. |
> | action | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Anuluje uruchomienie określonego przez identyfikator uruchomienia potoku |
> | action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Zapytania, których działanie zostanie uruchomione dla określonego potoku Uruchom identyfikatora. |
> | action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Odczytuje wynik działania zapytanie działa przez określony identyfikator uruchomienia potoku. |
> | action | Microsoft.DataFactory/factories/pipelineruns/read | Odczytuje uruchomienia potoku. |
> | action | Microsoft.DataFactory/factories/pipelines/createrun/action | Tworzy przebiegu potoku. |
> | action | Microsoft.DataFactory/factories/pipelines/delete | Usuwa potoku. |
> | action | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Pobiera postęp uruchomienia działania. |
> | action | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Odczytuje uruchomienia potoku. |
> | action | Microsoft.DataFactory/factories/pipelines/read | Odczytuje potoku. |
> | action | Microsoft.DataFactory/factories/pipelines/write | Utwórz lub zaktualizuj potoku |
> | action | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Wysyła zapytanie do uruchomienia potoku debugowania. |
> | action | Microsoft.DataFactory/factories/querypipelineruns/action | Wysyła zapytanie do uruchomienia potoku. |
> | action | Microsoft.DataFactory/factories/querypipelineruns/read | Odczytuje wynik uruchomienia potoku zapytania. |
> | action | Microsoft.DataFactory/factories/querytriggerruns/action | Wysyła zapytanie do uruchomienia wyzwalacza. |
> | action | Microsoft.DataFactory/factories/querytriggerruns/read | Odczytuje wynik uruchomienia wyzwalacza. |
> | action | Microsoft.DataFactory/factories/read | Odczytuje fabryki danych. |
> | action | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Uruchamia sesję debugowania przepływu danych. |
> | action | Microsoft.DataFactory/factories/triggerruns/read | Odczytuje uruchomienia wyzwalacza. |
> | action | Microsoft.DataFactory/factories/triggers/delete | Usuwa wszystkie wyzwalacza. |
> | action | Microsoft.DataFactory/factories/triggers/read | Odczytuje wszystkie wyzwalacza. |
> | action | Microsoft.DataFactory/factories/triggers/start/action | Uruchamia wszystkie wyzwalacza. |
> | action | Microsoft.DataFactory/factories/triggers/stop/action | Zatrzymuje wszystkie wyzwalacza. |
> | action | Microsoft.DataFactory/factories/triggers/triggerruns/read | Odczytuje uruchomienia wyzwalacza. |
> | action | Microsoft.DataFactory/factories/triggers/write | Tworzy lub aktualizuje dowolnego wyzwalacza. |
> | action | Microsoft.DataFactory/factories/write | Utwórz lub zaktualizuj usługi Data Factory |
> | action | Microsoft.DataFactory/locations/configureFactoryRepo/action | Umożliwia skonfigurowanie repozytorium fabryki. |
> | action | Microsoft.DataFactory/locations/getFeatureValue/action | Pobierz wartość funkcji sterowania narażenia dla określonej lokalizacji. |
> | action | Microsoft.DataFactory/locations/getFeatureValue/read | Odczytuje wartość funkcji sterowania narażenia dla określonej lokalizacji. |
> | action | Microsoft.DataFactory/operations/read | Odczytuje wszystkie operacje w dostawcy fabryki danych firmy Microsoft. |
> | action | Microsoft.DataFactory/register/action | Rejestruje subskrypcję dostawcy zasobów fabryki danych. |
> | action | Microsoft.DataFactory/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów fabryki danych. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Usuwanie zasad obliczeń. |
> | action | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Uzyskaj informacje o zasadach obliczeń. |
> | action | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Utwórz lub zaktualizuj zasady obliczeń. |
> | action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Odłączanie konta DataLakeStore z konta usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Uzyskaj informacje o połączonych kontach DataLakeStore na koncie usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Utwórz lub zaktualizuj połączone konto DataLakeStore konta usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/accounts/delete | Usuwanie konta usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Usuwanie reguły zapory. |
> | action | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Uzyskaj informacje dotyczące reguły zapory. |
> | action | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Utwórz lub zaktualizuj regułę zapory. |
> | action | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Pobierz wynik operacji konto usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/accounts/read | Pobierz informacje o istniejącym kontem usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Lista tokenów sygnatur dostępu Współdzielonego dla magazynu kontenerów z konta usługi DataLakeAnalytics połączonego konta usługi Storage. |
> | action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Pobierz kontenery z konta usługi DataLakeAnalytics połączonego konta usługi Storage. |
> | action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Odłączanie konta magazynu z konta usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Uzyskać informacje na temat połączonego konta usługi Storage na koncie usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Tworzenie lub aktualizowanie konta usługi DataLakeAnalytics połączonego konta usługi Storage. |
> | action | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Przyznaj uprawnienia, aby anulować zadania przesłane przez innych użytkowników. |
> | action | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Przenieś SystemMaxAnalyticsUnits pomiędzy konta tej usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Usuń regułę sieci wirtualnej. |
> | action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Uzyskaj informacje dotyczące reguły sieci wirtualnej. |
> | action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | action | Microsoft.DataLakeAnalytics/accounts/write | Utwórz lub zaktualizuj konto usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/locations/capability/read | Uzyskaj informacje o możliwościach subskrypcji dotyczące przy użyciu usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/locations/operationResults/read | Pobierz wynik operacji konto usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/locations/usages/read | Uzyskiwanie informacji o użyciach limitu przydziału subskrypcji dotyczące przy użyciu usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/operations/read | Pobierz dostępne operacje usługi DataLakeAnalytics. |
> | action | Microsoft.DataLakeAnalytics/register/action | Zarejestruj subskrypcję usługi DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DataLakeStore/accounts/delete | Usuwanie konta DataLakeStore. |
> | action | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Włącz magazyn kluczy dla konta DataLakeStore. |
> | action | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Usuń filtr EventGrid. |
> | action | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Uzyskaj EventGrid filtru. |
> | action | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Utwórz lub zaktualizuj filtr EventGrid. |
> | action | Microsoft.DataLakeStore/accounts/firewallRules/delete | Usuwanie reguły zapory. |
> | action | Microsoft.DataLakeStore/accounts/firewallRules/read | Uzyskaj informacje dotyczące reguły zapory. |
> | action | Microsoft.DataLakeStore/accounts/firewallRules/write | Utwórz lub zaktualizuj regułę zapory. |
> | action | Microsoft.DataLakeStore/accounts/operationResults/read | Pobierz wynik operacji DataLakeStore konta. |
> | action | Microsoft.DataLakeStore/accounts/read | Uzyskaj informacje o istniejących kontach DataLakeStore. |
> | action | Microsoft.DataLakeStore/accounts/Superuser/action | Przyznaj administratora na Data Lake Store w przypadku udzielenia z Microsoft.Authorization/roleAssignments/write. |
> | action | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Usunięcie zaufanego dostawcy tożsamości. |
> | action | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Uzyskać informacje na temat zaufanego dostawcy tożsamości. |
> | action | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Utwórz lub zaktualizuj zaufanego dostawcy tożsamości. |
> | action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Usuń regułę sieci wirtualnej. |
> | action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Uzyskaj informacje dotyczące reguły sieci wirtualnej. |
> | action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | action | Microsoft.DataLakeStore/accounts/write | Utwórz lub zaktualizuj konto DataLakeStore. |
> | action | Microsoft.DataLakeStore/locations/capability/read | Uzyskaj informacje o możliwościach subskrypcji dotyczące przy użyciu DataLakeStore. |
> | action | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta DataLakeStore. |
> | action | Microsoft.DataLakeStore/locations/operationResults/read | Pobierz wynik operacji DataLakeStore konta. |
> | action | Microsoft.DataLakeStore/locations/usages/read | Uzyskiwanie informacji o użyciach limitu przydziału subskrypcji dotyczące przy użyciu DataLakeStore. |
> | action | Microsoft.DataLakeStore/operations/read | Pobierz dostępne operacje DataLakeStore. |
> | action | Microsoft.DataLakeStore/register/action | Zarejestruj subskrypcję DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DataMigration/locations/operationResults/read | Pobierz stan operacji długotrwałej dotyczącej odpowiedzi „202 Zaakceptowano” |
> | action | Microsoft.DataMigration/locations/operationStatuses/read | Pobierz stan operacji długotrwałej dotyczącej odpowiedzi „202 Zaakceptowano” |
> | action | Microsoft.DataMigration/register/action | Rejestruje subskrypcję u dostawcy usługi Azure Database Migration Service |
> | action | Microsoft.DataMigration/services/addWorker/action | Dodaje proces roboczy usługi DMS do dostępnych procesów roboczych usługi |
> | action | Microsoft.DataMigration/services/checkStatus/action | Sprawdź, czy usługa jest wdrożona i uruchomiona |
> | action | Microsoft.DataMigration/services/configureWorker/action | Konfiguruje proces roboczy usługi DMS dla dostępnych procesów roboczych usługi |
> | action | Microsoft.DataMigration/services/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | action | Microsoft.DataMigration/services/projects/accessArtifacts/action | Generuj adres URL, którego można użyć na potrzeby operacji GET lub PUT obejmujących artefakty projektu |
> | action | Microsoft.DataMigration/services/projects/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | action | Microsoft.DataMigration/services/projects/files/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | action | Microsoft.DataMigration/services/projects/files/read | Przeczytaj informacje o zasobach |
> | action | Microsoft.DataMigration/services/projects/files/read/action | Uzyskaj adres URL, za pomocą którego można odczytywać zawartość pliku |
> | action | Microsoft.DataMigration/services/projects/files/readWrite/action | Uzyskaj adres URL, za pomocą którego można odczytywać i zapisywać zawartość pliku |
> | action | Microsoft.DataMigration/services/projects/files/write | Utwórz lub zaktualizuj zasoby i ich właściwości |
> | action | Microsoft.DataMigration/services/projects/read | Przeczytaj informacje o zasobach |
> | action | Microsoft.DataMigration/services/projects/tasks/cancel/action | Anuluj zadanie, jeśli jest aktualnie uruchomione |
> | action | Microsoft.DataMigration/services/projects/tasks/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | action | Microsoft.DataMigration/services/projects/tasks/read | Przeczytaj informacje o zasobach |
> | action | Microsoft.DataMigration/services/projects/tasks/write | Uruchom zadania usługi Azure Database Migration Service |
> | action | Microsoft.DataMigration/services/projects/write | Uruchom zadania usługi Azure Database Migration Service |
> | action | Microsoft.DataMigration/services/read | Przeczytaj informacje o zasobach |
> | action | Microsoft.DataMigration/services/removeWorker/action | Usuwa proces roboczy usługi DMS z dostępnych procesów roboczych usługi |
> | action | Microsoft.DataMigration/services/serviceTasks/cancel/action | Anuluj zadanie, jeśli jest aktualnie uruchomione |
> | action | Microsoft.DataMigration/services/serviceTasks/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | action | Microsoft.DataMigration/services/serviceTasks/read | Przeczytaj informacje o zasobach |
> | action | Microsoft.DataMigration/services/serviceTasks/write | Uruchom zadania usługi Azure Database Migration Service |
> | action | Microsoft.DataMigration/services/slots/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | action | Microsoft.DataMigration/services/slots/read | Przeczytaj informacje o zasobach |
> | action | Microsoft.DataMigration/services/slots/write | Utwórz lub zaktualizuj zasoby i ich właściwości |
> | action | Microsoft.DataMigration/services/start/action | Uruchom usługę DMS, aby umożliwić jej ponowne przetworzenie migracji |
> | action | Microsoft.DataMigration/services/stop/action | Zatrzymaj usługę DMS, aby zminimalizować jej koszt |
> | action | Microsoft.DataMigration/services/updateAgentConfig/action | Aktualizuje konfigurację agenta usługi DMS przy użyciu podanych wartości. |
> | action | Microsoft.DataMigration/services/write | Utwórz lub zaktualizuj zasoby i ich właściwości |
> | action | Microsoft.DataMigration/skus/read | Pobierz listę jednostek SKU obsługiwanych przez zasoby usługi DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Zwraca wyniki operacji serwera MariaDB |
> | action | Microsoft.DBforMariaDB/locations/operationResults/read | ResourceGroup zwracane na podstawie wyników operacji serwera MariaDB |
> | action | Microsoft.DBforMariaDB/locations/operationResults/read | Zwraca wyniki operacji serwera MariaDB |
> | action | Microsoft.DBforMariaDB/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | action | Microsoft.DBforMariaDB/operations/read | Zwraca listę operacji MariaDB. |
> | action | Microsoft.DBforMariaDB/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | action | Microsoft.DBforMariaDB/register/action | Zarejestruj dostawcę zasobów MariaDB |
> | action | Microsoft.DBforMariaDB/servers/configurations/read | Zwraca listę konfiguracji serwera lub pobiera właściwości dla określonej konfiguracji. |
> | action | Microsoft.DBforMariaDB/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | action | Microsoft.DBforMariaDB/servers/databases/delete | Usuwa istniejącą bazę danych MariaDB. |
> | action | Microsoft.DBforMariaDB/servers/databases/read | Zwraca listę baz danych MariaDB lub pobiera właściwości dla określonej bazy danych. |
> | action | Microsoft.DBforMariaDB/servers/databases/write | Tworzy bazę danych MariaDB z określonymi parametrami, lub zaktualizuj właściwości określonej bazy danych. |
> | action | Microsoft.DBforMariaDB/servers/delete | Usuwa istniejącego serwera. |
> | action | Microsoft.DBforMariaDB/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | action | Microsoft.DBforMariaDB/servers/firewallRules/read | Zwraca listę zapory reguły dla serwera lub pobiera właściwości dla reguły zapory określony. |
> | action | Microsoft.DBforMariaDB/servers/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | action | Microsoft.DBforMariaDB/servers/logFiles/read | Zwraca listę MariaDB LogFiles. |
> | action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów MariaDB |
> | action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | action | Microsoft.DBforMariaDB/servers/queryTexts/action | Zwróć tekstów, aby uzyskać listę zapytań |
> | action | Microsoft.DBforMariaDB/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | action | Microsoft.DBforMariaDB/servers/recoverableServers/read | Zwraca informacje możliwe do odzyskania serwera MariaDB |
> | action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Pobierz szczegóły zasady wykrywania zagrożeń serwera skonfigurowane na danym serwerze |
> | action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Zwraca listę statystyki zapytań najpopularniejsze zapytania. |
> | action | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Aktualizowanie konfiguracji dla określonego serwera |
> | action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguł lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | action | Microsoft.DBforMariaDB/servers/waitStatistics/read | Zwraca statystyki oczekiwania dla wystąpienia |
> | action | Microsoft.DBforMariaDB/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Zwraca wyniki operacji serwera MySQL |
> | action | Microsoft.DBforMySQL/locations/operationResults/read | ResourceGroup zwracane na podstawie wyników operacji serwera MySQL |
> | action | Microsoft.DBforMySQL/locations/operationResults/read | Zwraca wyniki operacji serwera MySQL |
> | action | Microsoft.DBforMySQL/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | action | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | action | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | action | Microsoft.DBforMySQL/operations/read | Zwraca listę operacji MySQL. |
> | action | Microsoft.DBforMySQL/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | action | Microsoft.DBforMySQL/register/action | Rejestrowanie dostawcy zasobów MySQL |
> | action | Microsoft.DBforMySQL/servers/configurations/read | Zwraca listę konfiguracji serwera lub pobiera właściwości dla określonej konfiguracji. |
> | action | Microsoft.DBforMySQL/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | action | Microsoft.DBforMySQL/servers/databases/delete | Usuwa istniejącą bazę danych MySQL. |
> | action | Microsoft.DBforMySQL/servers/databases/read | Zwraca listę baz danych MySQL lub pobiera właściwości dla określonej bazy danych. |
> | action | Microsoft.DBforMySQL/servers/databases/write | Tworzy bazę danych MySQL przy użyciu określonych parametrów lub zaktualizuj właściwości określonej bazy danych. |
> | action | Microsoft.DBforMySQL/servers/delete | Usuwa istniejącego serwera. |
> | action | Microsoft.DBforMySQL/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | action | Microsoft.DBforMySQL/servers/firewallRules/read | Zwraca listę zapory reguły dla serwera lub pobiera właściwości dla reguły zapory określony. |
> | action | Microsoft.DBforMySQL/servers/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | action | Microsoft.DBforMySQL/servers/logFiles/read | Zwraca listę PostgreSQL LogFiles. |
> | action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów MySQL |
> | action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | action | Microsoft.DBforMySQL/servers/queryTexts/action | Zwróć tekstów, aby uzyskać listę zapytań |
> | action | Microsoft.DBforMySQL/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | action | Microsoft.DBforMySQL/servers/recoverableServers/read | Zwraca informacje możliwe do odzyskania serwera MySQL |
> | action | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Pobierz szczegóły zasady wykrywania zagrożeń serwera skonfigurowane na danym serwerze |
> | action | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | action | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Zwraca listę statystyki zapytań najpopularniejsze zapytania. |
> | action | Microsoft.DBforMySQL/servers/updateConfigurations/action | Aktualizowanie konfiguracji dla określonego serwera |
> | action | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | action | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguł lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | action | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | action | Microsoft.DBforMySQL/servers/waitStatistics/read | Zwraca statystyki oczekiwania dla wystąpienia |
> | action | Microsoft.DBforMySQL/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Zwraca wyniki operacji serwera PostgreSQL |
> | action | Microsoft.DBforPostgreSQL/locations/operationResults/read | ResourceGroup zwracane na podstawie wyników operacji serwera PostgreSQL |
> | action | Microsoft.DBforPostgreSQL/locations/operationResults/read | Zwraca wyniki operacji serwera PostgreSQL |
> | action | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | action | Microsoft.DBforPostgreSQL/operations/read | Zwraca listę operacji PostgreSQL. |
> | action | Microsoft.DBforPostgreSQL/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | action | Microsoft.DBforPostgreSQL/register/action | Zarejestruj dostawcę zasobów PostgreSQL |
> | action | Microsoft.DBforPostgreSQL/servers/advisors/read | Zwraca listę doradców |
> | action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Zwraca listę zalecanych akcji |
> | action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Zalecenia |
> | action | Microsoft.DBforPostgreSQL/servers/configurations/read | Zwraca listę konfiguracji serwera lub pobiera właściwości dla określonej konfiguracji. |
> | action | Microsoft.DBforPostgreSQL/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | action | Microsoft.DBforPostgreSQL/servers/databases/delete | Usuwa istniejącą bazę danych PostgreSQL. |
> | action | Microsoft.DBforPostgreSQL/servers/databases/read | Zwraca listę baz danych PostgreSQL lub pobiera właściwości dla określonej bazy danych. |
> | action | Microsoft.DBforPostgreSQL/servers/databases/write | Tworzy bazę danych PostgreSQL z określonymi parametrami lub aktualizuje właściwości określonej bazy danych. |
> | action | Microsoft.DBforPostgreSQL/servers/delete | Usuwa istniejącego serwera. |
> | action | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | action | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Zwraca listę zapory reguły dla serwera lub pobiera właściwości dla reguły zapory określony. |
> | action | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | action | Microsoft.DBforPostgreSQL/servers/logFiles/read | Zwraca listę PostgreSQL LogFiles. |
> | action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | action | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Zwróć tekst zapytania |
> | action | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Zwróć tekstów, aby uzyskać listę zapytań |
> | action | Microsoft.DBforPostgreSQL/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | action | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Zwraca informacje możliwe do odzyskania serwera PostgreSQL |
> | action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Pobierz szczegóły zasady wykrywania zagrożeń serwera skonfigurowane na danym serwerze |
> | action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | action | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Zwraca listę statystyki zapytań najpopularniejsze zapytania. |
> | action | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Aktualizowanie konfiguracji dla określonego serwera |
> | action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguł lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | action | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Zwraca statystyki oczekiwania dla wystąpienia |
> | action | Microsoft.DBforPostgreSQL/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |
> | action | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Zwraca listę konfiguracji serwera lub pobiera właściwości dla określonej konfiguracji. |
> | action | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | action | Microsoft.DBforPostgreSQL/serversv2/delete | Usuwa istniejącego serwera. |
> | action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Zwraca listę zapory reguły dla serwera lub pobiera właściwości dla reguły zapory określony. |
> | action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | action | Microsoft.DBforPostgreSQL/serversv2/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | action | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Aktualizowanie konfiguracji dla określonego serwera |
> | action | Microsoft.DBforPostgreSQL/serversv2/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Devices/checkNameAvailability/Action | Sprawdź, czy nazwa usługi IotHub Jeśli jest dostępna |
> | action | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Sprawdź, czy nazwa usługi w przypadku inicjowania obsługi jest dostępne |
> | action | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Usuwa certyfikat |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Pobiera certyfikat |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Sprawdź certyfikat zasobu |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Tworzenie lub aktualizowanie certyfikatów |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Usuwanie zasobu dzierżawy usługi IotHub |
> | action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Usuń grupę konsumentów Centrum zdarzeń |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Pobieranie grupy konsumentów Centrum zdarzeń |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Tworzenie grupy użytkowników Centrum zdarzeń |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eksportowanie urządzeń |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Pobiera zasób statystyki dzierżawy usługi IotHub |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importuj urządzenia |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Pobiera klucz dzierżawy usługi IotHub |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Szczegółowe zadania przesłane na danej usługi IotHub |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Pobiera klucze dzierżawy usługi IotHub |
> | action | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Pobiera definicje dzienników dostępne dla usługi IotHub |
> | action | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Pobierz metryki limitu przydziału |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/Read | Pobiera zasób dzierżawy usługi IotHub |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Komunikat dla wszystkich tras istniejącego testu |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testowanie komunikat względem podanego testu trasy |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla usługi IotHub |
> | action | Microsoft.Devices/elasticPools/iotHubTenants/Write | Utwórz lub zaktualizuj zasób dzierżawy usługi IotHub |
> | action | Microsoft.Devices/ElasticPools/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | action | Microsoft.Devices/iotHubs/certificates/Delete | Usuwa certyfikat |
> | action | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | action | Microsoft.Devices/iotHubs/certificates/Read | Pobiera certyfikat |
> | action | Microsoft.Devices/iotHubs/certificates/verify/Action | Sprawdź certyfikat zasobu |
> | action | Microsoft.Devices/iotHubs/certificates/Write | Tworzenie lub aktualizowanie certyfikatów |
> | action | Microsoft.Devices/iotHubs/Delete | Usuwanie zasobu usługi IotHub |
> | action | Microsoft.Devices/IotHubs/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Devices/IotHubs/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Usuwa filtr usługi Event Grid |
> | action | Microsoft.Devices/iotHubs/eventGridFilters/Read | Pobiera filtr usługi Event Grid |
> | action | Microsoft.Devices/iotHubs/eventGridFilters/Write | Utworzyć lub zaktualizować istniejący filtr usługi Event Grid |
> | action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Usuń grupę konsumentów Centrum zdarzeń |
> | action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Pobieranie grupy konsumentów Centrum zdarzeń |
> | action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Tworzenie grupy użytkowników Centrum zdarzeń |
> | action | Microsoft.Devices/iotHubs/exportDevices/Action | Eksportowanie urządzeń |
> | action | Microsoft.Devices/iotHubs/importDevices/Action | Importuj urządzenia |
> | action | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Pobierz klucz IotHub dla podanej nazwie |
> | action | Microsoft.Devices/iotHubs/iotHubStats/Read | Zebranie statystyk, IotHub |
> | action | Microsoft.Devices/iotHubs/jobs/Read | Szczegółowe zadania przesłane na danej usługi IotHub |
> | action | Microsoft.Devices/iotHubs/listkeys/Action | Pobierz wszystkie klucze usługi IotHub |
> | action | Microsoft.Devices/IotHubs/logDefinitions/read | Pobiera definicje dzienników dostępne dla usługi IotHub |
> | action | Microsoft.Devices/IotHubs/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | action | Microsoft.Devices/iotHubs/operationresults/Read | Pobierz wynik operacji (przestarzałe API) |
> | action | Microsoft.Devices/iotHubs/quotaMetrics/Read | Pobierz metryki limitu przydziału |
> | action | Microsoft.Devices/iotHubs/Read | Pobiera zasoby usługi IotHub |
> | action | Microsoft.Devices/iotHubs/routing/$testall/Action | Komunikat dla wszystkich tras istniejącego testu |
> | action | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testowanie komunikat względem podanego testu trasy |
> | action | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla usługi IotHub |
> | action | Microsoft.Devices/iotHubs/skus/Read | Pobierz prawidłowe jednostki SKU usługi IotHub |
> | action | Microsoft.Devices/iotHubs/Write | Utwórz lub zaktualizuj zasób usługi IotHub |
> | action | Microsoft.Devices/locations/operationresults/Read | Pobierz wynik operacji usług Location based |
> | action | Microsoft.Devices/operationresults/Read | Pobierz wynik operacji |
> | action | Microsoft.Devices/operations/Read | Pobierz wszystkie operacje ResourceProvider |
> | action | Microsoft.Devices/provisioningServices/certificates/Delete | Usuwa certyfikat |
> | action | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | action | Microsoft.Devices/provisioningServices/certificates/Read | Pobiera certyfikat |
> | action | Microsoft.Devices/provisioningServices/certificates/verify/Action | Sprawdź certyfikat zasobu |
> | action | Microsoft.Devices/provisioningServices/certificates/Write | Tworzenie lub aktualizowanie certyfikatów |
> | action | Microsoft.Devices/provisioningServices/Delete | Usuń zasób IotDps |
> | action | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Pobierz klucze IotDps dla nazwy klucza |
> | action | Microsoft.Devices/provisioningServices/listkeys/Action | Pobierz wszystkie klucze IotDps |
> | action | Microsoft.Devices/provisioningServices/logDefinitions/read | Pobiera definicje dzienników dostępne dla usługi aprowizacji |
> | action | Microsoft.Devices/provisioningServices/metricDefinitions/read | Pobiera dostępne metryki dla usługi aprowizacji |
> | action | Microsoft.Devices/provisioningServices/operationresults/Read | Pobierz wynik operacji usługi DPS |
> | action | Microsoft.Devices/provisioningServices/Read | Pobierz zasób IotDps |
> | action | Microsoft.Devices/provisioningServices/skus/Read | Pobierz prawidłowe IotDps jednostki SKU. |
> | action | Microsoft.Devices/provisioningServices/Write | Utwórz zasób IotDps |
> | action | Microsoft.Devices/register/action | Zarejestruj subskrypcję dostawcy zasobów usługi IotHub i włącza funkcję tworzenia zasobów usługi IotHub |
> | action | Microsoft.Devices/register/action | Zarejestruj subskrypcję dostawcy zasobów usługi IotHub i włącza funkcję tworzenia zasobów usługi IotHub |
> | action | Microsoft.Devices/usages/Read | Uzyskiwanie subskrypcji szczegóły użycia dla tego dostawcy. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DevSpaces/controllers/delete | Usuwanie usługi Azure Dev miejsca do magazynowania kontrolera i płaszczyzny danych |
> | action | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Szczegóły połączenia listy dla infrastruktury kontrolera Azure Dev miejsca do magazynowania |
> | action | Microsoft.DevSpaces/controllers/read | Właściwości odczytu Azure Dev miejsca do magazynowania kontrolera |
> | action | Microsoft.DevSpaces/controllers/write | Tworzenie lub właściwości aktualizacji usługi Azure Dev miejsca do magazynowania kontrolera |
> | action | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Sprawdź istniejące mapowanie kontrolera hosta kontenera |
> | action | Microsoft.DevSpaces/locations/operationresults/read | Odczytaj stan dla operacji asynchronicznej |
> | action | Microsoft.DevSpaces/register/action | Procedura Rejestruj dostawcę zasobów miejsca do magazynowania firmy Microsoft Dev z subskrypcją |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DevTestLab/labCenters/delete | Usuń centra laboratoryjne. |
> | action | Microsoft.DevTestLab/labCenters/read | Przeczytaj centra laboratoryjne. |
> | action | Microsoft.DevTestLab/labCenters/write | Dodaje lub modyfikuje centra laboratoryjne. |
> | action | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Do odczytu szablonów Menedżera zasobów platformy azure. |
> | action | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generuje szablonu usługi ARM dla danego artefaktu, przekazuje wymagane pliki na konto magazynu i sprawdza poprawność wygenerowanego artefaktu. |
> | action | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Przeczytaj artefaktów. |
> | action | Microsoft.DevTestLab/labs/artifactSources/delete | Usuń źródła artefaktu. |
> | action | Microsoft.DevTestLab/labs/artifactSources/read | Przeczytaj źródeł artefaktów. |
> | action | Microsoft.DevTestLab/labs/artifactSources/write | Dodaje lub modyfikuje źródeł artefaktów. |
> | action | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Oświadczenie losowe przejęcia maszynę wirtualną w środowisku laboratoryjnym. |
> | action | Microsoft.DevTestLab/labs/costs/read | Przeczytaj kosztów. |
> | action | Microsoft.DevTestLab/labs/costs/write | Dodaje lub modyfikuje kosztów. |
> | action | Microsoft.DevTestLab/labs/CreateEnvironment/action | Tworzenie maszyn wirtualnych w laboratorium. |
> | action | Microsoft.DevTestLab/labs/customImages/delete | Usuwanie obrazów niestandardowych. |
> | action | Microsoft.DevTestLab/labs/customImages/read | Przeczytaj obrazów niestandardowych. |
> | action | Microsoft.DevTestLab/labs/customImages/write | Dodaje lub modyfikuje obrazów niestandardowych. |
> | action | Microsoft.DevTestLab/labs/delete | Usuń labs. |
> | action | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Upewnij się, że bieżący użytkownik nie ma prawidłowego profilu w środowisku laboratoryjnym. |
> | action | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Eksportuje użycia zasobów laboratorium do konta magazynu |
> | action | Microsoft.DevTestLab/labs/formulas/delete | Usunięcie formuły. |
> | action | Microsoft.DevTestLab/labs/formulas/read | Przeczytaj formuły. |
> | action | Microsoft.DevTestLab/labs/formulas/write | Dodaje lub modyfikuje formuły. |
> | action | Microsoft.DevTestLab/labs/galleryImages/read | Przeczytaj galerii obrazów. |
> | action | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Wygenerować identyfikator URI przekazywania obrazów niestandardowych dysku do laboratorium. |
> | action | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Zaimportować maszynę wirtualną do innego laboratorium. |
> | action | Microsoft.DevTestLab/labs/ListVhds/action | Wyświetl listę obrazów dysku dostępne na potrzeby tworzenia obrazu niestandardowego. |
> | action | Microsoft.DevTestLab/labs/notificationChannels/delete | Usuń notificationchannels. |
> | action | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Wyślij powiadomienie na kanale podana. |
> | action | Microsoft.DevTestLab/labs/notificationChannels/read | Przeczytaj notificationchannels. |
> | action | Microsoft.DevTestLab/labs/notificationChannels/write | Dodaje lub modyfikuje notificationchannels. |
> | action | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Ocenia zasady laboratorium. |
> | action | Microsoft.DevTestLab/labs/policySets/policies/delete | Usuń zasady. |
> | action | Microsoft.DevTestLab/labs/policySets/policies/read | Przeczytaj zasady. |
> | action | Microsoft.DevTestLab/labs/policySets/policies/write | Dodawanie lub modyfikowanie zasad. |
> | action | Microsoft.DevTestLab/labs/policySets/read | Przeczytaj zestawy zasad. |
> | action | Microsoft.DevTestLab/labs/read | Przeczytaj labs. |
> | action | Microsoft.DevTestLab/labs/schedules/delete | Usuwanie harmonogramów. |
> | action | Microsoft.DevTestLab/labs/schedules/Execute/action | Wykonania harmonogramu. |
> | action | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Wyświetla listę wszystkich odpowiednich harmonogramów |
> | action | Microsoft.DevTestLab/labs/schedules/read | Przeczytaj harmonogramów. |
> | action | Microsoft.DevTestLab/labs/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | action | Microsoft.DevTestLab/labs/serviceRunners/delete | Usuń usługę drugie miejsca. |
> | action | Microsoft.DevTestLab/labs/serviceRunners/read | Drugie miejsca usługi odczytu. |
> | action | Microsoft.DevTestLab/labs/serviceRunners/write | Dodaje lub modyfikuje używanych modułów uruchamiających usługi. |
> | action | Microsoft.DevTestLab/labs/sharedGalleries/delete | Usuń udostępniony galerii. |
> | action | Microsoft.DevTestLab/labs/sharedGalleries/read | Galerie udostępnionych do odczytu. |
> | action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Usuń udostępnianych obrazów. |
> | action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Przeczytaj udostępnianych obrazów. |
> | action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Dodaje lub modyfikuje udostępnianych obrazów. |
> | action | Microsoft.DevTestLab/labs/sharedGalleries/write | Dodaje lub modyfikuje galerie udostępnionych. |
> | action | Microsoft.DevTestLab/labs/users/delete | Usuń profile użytkowników. |
> | action | Microsoft.DevTestLab/labs/users/disks/Attach/action | Dołączanie i utworzyć dzierżawę dysku do maszyny wirtualnej. |
> | action | Microsoft.DevTestLab/labs/users/disks/delete | Usuń dyski. |
> | action | Microsoft.DevTestLab/labs/users/disks/Detach/action | Odłącz i przerwać dzierżawy dysków dołączonych do maszyny wirtualnej. |
> | action | Microsoft.DevTestLab/labs/users/disks/read | Przeczytaj dysków. |
> | action | Microsoft.DevTestLab/labs/users/disks/write | Dodaje lub modyfikuje dysków. |
> | action | Microsoft.DevTestLab/labs/users/environments/delete | Usuwanie środowisk. |
> | action | Microsoft.DevTestLab/labs/users/environments/read | Odczyt środowisk. |
> | action | Microsoft.DevTestLab/labs/users/environments/write | Dodaje lub modyfikuje środowisk. |
> | action | Microsoft.DevTestLab/labs/users/read | Odczyt profilów użytkowników. |
> | action | Microsoft.DevTestLab/labs/users/secrets/delete | Usuń wpisy tajne. |
> | action | Microsoft.DevTestLab/labs/users/secrets/read | Odczyt kluczy tajnych. |
> | action | Microsoft.DevTestLab/labs/users/secrets/write | Dodaje lub modyfikuje wpisy tajne. |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Usuwanie usługi sieci szkieletowej. |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Wyświetla listę harmonogramy dotyczy uruchomień/zatrzymań ewentualne. |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Sieci szkieletowe odczytu usługi. |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Usuwanie harmonogramów. |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Wykonania harmonogramu. |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Przeczytaj harmonogramów. |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Uruchom usługi Service fabric. |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zatrzymaj usługi Service fabric |
> | action | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Dodaje lub modyfikuje Usługa sieci szkieletowej. |
> | action | Microsoft.DevTestLab/labs/users/write | Dodaje lub modyfikuje profile użytkowników. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Dołączanie dysku danych nowej lub istniejącej maszyny wirtualnej. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Zastosowanie artefaktów do maszyny wirtualnej. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Przejęcie na własność istniejącej maszyny wirtualnej |
> | action | Microsoft.DevTestLab/labs/virtualMachines/delete | Usuń maszyny wirtualne. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Odłącz określony dysk od maszyny wirtualnej. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Pobiera ciąg, który reprezentuje zawartość pliku RDP dla maszyny wirtualnej |
> | action | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Wyświetla listę harmonogramy dotyczy uruchomień/zatrzymań ewentualne. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/read | Przeczytaj maszyn wirtualnych. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Ponowne wdrażanie maszyny wirtualnej |
> | action | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Zmień rozmiar maszyny wirtualnej. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Uruchom ponownie maszynę wirtualną. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Usuwanie harmonogramów. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Wykonania harmonogramu. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Przeczytaj harmonogramów. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Uruchom maszynę wirtualną. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Zatrzymaj maszynę wirtualną |
> | action | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Przesyła wszystkie dyski z danymi dołączonych do maszyny wirtualnej należy do bieżącego użytkownika. |
> | action | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Zwolnij posiadania istniejącej maszyny wirtualnej |
> | action | Microsoft.DevTestLab/labs/virtualMachines/write | Dodaje lub modyfikuje maszyny wirtualne. |
> | action | Microsoft.DevTestLab/labs/virtualNetworks/delete | Usuwanie sieci wirtualnych. |
> | action | Microsoft.DevTestLab/labs/virtualNetworks/read | Przeczytaj sieci wirtualnych. |
> | action | Microsoft.DevTestLab/labs/virtualNetworks/write | Dodaje lub modyfikuje sieci wirtualnych. |
> | action | Microsoft.DevTestLab/labs/vmPools/delete | Usuń pule maszyn wirtualnych. |
> | action | Microsoft.DevTestLab/labs/vmPools/read | Przeczytaj pule maszyn wirtualnych. |
> | action | Microsoft.DevTestLab/labs/vmPools/write | Dodaje lub modyfikuje pule maszyn wirtualnych. |
> | action | Microsoft.DevTestLab/labs/write | Dodawanie lub modyfikowanie laboratoriów. |
> | action | Microsoft.DevTestLab/locations/operations/read | Operacje odczytu. |
> | action | Microsoft.DevTestLab/register/action | Rejestruje subskrypcję |
> | action | Microsoft.DevTestLab/schedules/delete | Usuwanie harmonogramów. |
> | action | Microsoft.DevTestLab/schedules/Execute/action | Wykonania harmonogramu. |
> | action | Microsoft.DevTestLab/schedules/read | Przeczytaj harmonogramów. |
> | action | Microsoft.DevTestLab/schedules/Retarget/action | Aktualizuje zasób docelowy zgodnie z harmonogramem identyfikatora. |
> | action | Microsoft.DevTestLab/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DocumentDB/databaseAccountNames/read | Sprawdza dostępność nazwy. |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Usuwanie kolekcji. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Przeczytaj kolekcję lub listę, wszystkie kolekcje. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/throughput/read | Przeczytaj przepływność kolekcji. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/throughput/write | Zaktualizuj przepływność kolekcji. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Utwórz lub zaktualizuj kolekcję. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Usuń kontener. Dotyczy to tylko typy interfejsów API: "sql". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Przeczytaj kontener lub listę wszystkich kontenerów. Dotyczy to tylko typy interfejsów API: "sql". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/throughput/read | Przeczytaj przepływności kontenerów. Dotyczy to tylko typy interfejsów API: "sql". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/throughput/write | Zaktualizuj przepływności kontenerów. Dotyczy to tylko typy interfejsów API: "sql". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Utwórz lub zaktualizuj kontenera. Dotyczy to tylko typy interfejsów API: "sql". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Usuwanie bazy danych. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Usuwanie wykresu. Dotyczy to tylko typy interfejsów API: "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Przeczytaj wykres lub wyświetlić listę wszystkich wykresów. Dotyczy to tylko typy interfejsów API: "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/throughput/read | Przeczytaj przepływności wykresu. Dotyczy to tylko typy interfejsów API: "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/throughput/write | Zaktualizuj przepływności wykresu. Dotyczy to tylko typy interfejsów API: "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Utwórz lub zaktualizuj wykresu. Dotyczy to tylko typy interfejsów API: "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Przeczytaj bazę danych lub wyświetlić listę wszystkich baz danych. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/throughput/read | Przeczytaj przepływności bazy danych. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/throughput/write | Zaktualizuj przepływności bazy danych. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Tworzenie bazy danych Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Usuń przestrzeń kluczy. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Przeczytaj przestrzeń kluczy lub wyświetlić listę wszystkich keyspaces. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/throughput/read | Przeczytaj przepływności przestrzeń kluczy. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/throughput/write | Zaktualizuj przepływności przestrzeń kluczy. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Usuń tabelę. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Odczyt tabeli lub wyświetlić listę wszystkich tabel. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/throughput/read | Przeczytaj przepływności tabeli. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/throughput/write | Zaktualizuj przepływności tabeli. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Utwórz lub zaktualizuj tabelę. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Utwórz przestrzeń kluczy. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Usuń tabelę. Dotyczy to tylko typy interfejsów API: "table". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Odczyt tabeli lub wyświetlić listę wszystkich tabel. Dotyczy to tylko typy interfejsów API: "table". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/throughput/read | Przeczytaj przepływności tabeli. Dotyczy to tylko typy interfejsów API: "table". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/throughput/write | Zaktualizuj przepływności tabeli. Dotyczy to tylko typy interfejsów API: "table". |
> | action | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Utwórz lub zaktualizuj tabelę. Dotyczy to tylko typy interfejsów API: "table". |
> | action | Microsoft.DocumentDB/databaseAccounts/backup/action | Prześlij żądanie do skonfigurowania kopii zapasowej |
> | action | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Zmień grupę zasobów konta bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Odczytuje definicje metryk w kolekcji. |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Odczytuje metryki kolekcji. |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Przeczytaj kluczowe metryki poziomu bazy danych konta partycji |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Przeczytaj metryki poziomu partycji konta bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Przeczytaj partycje konta bazy danych w kolekcji |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Odczytaj konto bazy danych partycji poziomu użycia |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Odczytuje użycia kolekcji. |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Odczytuje definicje metryk bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Odczytuje metryki bazy danych. |
> | action | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Odczytuje użycia bazy danych. |
> | action | Microsoft.DocumentDB/databaseAccounts/delete | Usuwa konta bazy danych. |
> | action | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Zmień priorytetów trybu failover w regionach konto bazy danych. Służy to do wykonania operacji ręcznej pracy awaryjnej |
> | action | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Pobierz parametry połączenia dla konta bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Wyświetl klucze konta bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Odczytuje bazę danych, definicje metryk kont. |
> | action | Microsoft.DocumentDB/databaseAccounts/metrics/read | Odczytuje metryki konta bazy danych. |
> | action | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | W trybie offline region konta bazy danych.  |
> | action | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online region konta bazy danych. |
> | action | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Odczytaj stan operacji asynchronicznej |
> | action | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Metryki opóźnienie odczytu |
> | action | Microsoft.DocumentDB/databaseAccounts/percentile/read | Przeczytaj percentyle opóźnienia w replikacji |
> | action | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Odczyt metryk opóźnienia dla określonego regionu źródłowego i docelowego |
> | action | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Metryki opóźnienie odczytu dla konkretnego docelowego regionu |
> | action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Usuń serwer proxy połączeń prywatnych punktów końcowych konta bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | Odczyt stanu privateEndpointConnectionProxies operacji asynchronicznej |
> | action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Przeczytaj połączenia serwera proxy prywatnych punktów końcowych konta bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Sprawdź poprawność połączenia serwera proxy prywatnych punktów końcowych konta bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Utwórz lub zaktualizuj serwer proxy połączeń prywatnych punktów końcowych konta bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/read | Odczytuje konto bazy danych. |
> | action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Odczytuje bazę danych, klucze tylko do odczytu konta. |
> | action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Odczytuje bazę danych, klucze tylko do odczytu konta. |
> | action | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Obróć klucze konta bazy danych |
> | action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Odczytuje metryki regionalnych kolekcji. |
> | action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Przeczytaj kluczowe metryki poziomu regionalnego bazy danych konta partycji |
> | action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Przeczytaj metryki poziomu regionalnego bazy danych konta partycji |
> | action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Przeczytaj partycje konta regionalnego bazy danych w kolekcji |
> | action | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Odczytuje metryki konta region i bazy danych. |
> | action | Microsoft.DocumentDB/databaseAccounts/restore/action | Przesyłanie żądania przywracania |
> | action | Microsoft.DocumentDB/databaseAccounts/usages/read | Odczytuje bazę danych użycia konta. |
> | action | Microsoft.DocumentDB/databaseAccounts/write | Aktualizowanie kont bazy danych. |
> | action | Microsoft.DocumentDB/locations/asyncoperations/read | Odczytuje stan operacji asynchronicznych |
> | action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft.DocumentDB, że sieć wirtualna lub podsieć jest usuwana |
> | action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Odczyt stanu deleteVirtualNetworkOrSubnets operacji asynchronicznej |
> | action | Microsoft.DocumentDB/operationResults/read | Odczytaj stan operacji asynchronicznej |
> | action | Microsoft.DocumentDB/operations/read | Dostępne operacje odczytu dla Microsoft DocumentDB  |
> | action | Microsoft.DocumentDB/register/action |  Zarejestruj dostawcę zasobów Microsoft DocumentDB dla subskrypcji |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.DomainRegistration/checkDomainAvailability/Action | Sprawdź, czy domena jest dostępna do zakupu |
> | action | Microsoft.DomainRegistration/domains/Delete | Usuwanie istniejącej domeny. |
> | action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Usuń identyfikator własności |
> | action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Identyfikatory własność listy |
> | action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Pobierz identyfikator własności |
> | action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Utwórz lub zaktualizuj identyfikator |
> | action | Microsoft.DomainRegistration/domains/operationresults/Read | Operacja pobrania domeny |
> | action | Microsoft.DomainRegistration/domains/Read | Pobieranie listy domen |
> | action | Microsoft.DomainRegistration/domains/Read | Pobierz domeny |
> | action | Microsoft.DomainRegistration/domains/renew/Action | Odnów istniejącej domeny. |
> | action | Microsoft.DomainRegistration/domains/Write | Dodaj nową domenę, lub zaktualizuj istniejącą |
> | action | Microsoft.DomainRegistration/generateSsoRequest/Action | Generuje żądanie logowania się do centrum kontroli domeny. |
> | action | Microsoft.DomainRegistration/listDomainRecommendations/Action | Pobierz listę zaleceń dotyczących domeny, na podstawie słów kluczowych |
> | action | Microsoft.DomainRegistration/operations/Read | Lista wszystkich operacji z rejestracji domeny usługi aplikacji |
> | action | Microsoft.DomainRegistration/register/action | Zarejestruj dostawcę zasobów Domains firmy Microsoft dla subskrypcji |
> | action | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Lista akcji umowy |
> | action | Microsoft.DomainRegistration/topLevelDomains/Read | Pobrać toplevel domen |
> | action | Microsoft.DomainRegistration/topLevelDomains/Read | Pobierz toplevel domeny |
> | action | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Sprawdź poprawność obiektu zakupu domeny bez przesyłania |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.EventGrid/domains/delete | Usuwanie domeny |
> | action | Microsoft.EventGrid/domains/listKeys/action | Wyświetl listę kluczy dla domeny |
> | action | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla domen |
> | action | Microsoft.EventGrid/domains/read | Przeczytaj domeny |
> | action | Microsoft.EventGrid/domains/regenerateKey/action | Ponowne generowanie klucza dla domeny |
> | action | Microsoft.EventGrid/domains/topics/read | Zapoznaj się z tematem domeny |
> | action | Microsoft.EventGrid/domains/write | Utwórz lub zaktualizuj domenę |
> | action | Microsoft.EventGrid/eventSubscriptions/delete | Usuń eventSubscription |
> | action | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Uzyskaj pełny adres url dla subskrypcji zdarzeń |
> | action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla eventSubscriptions |
> | action | Microsoft.EventGrid/eventSubscriptions/read | Przeczytaj eventSubscription |
> | action | Microsoft.EventGrid/eventSubscriptions/write | Utwórz lub zaktualizuj eventSubscription |
> | action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla tematów |
> | action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla tematów |
> | action | Microsoft.EventGrid/extensionTopics/read | Przeczytaj extensionTopic. |
> | action | Microsoft.EventGrid/locations/eventSubscriptions/read | Subskrypcje zdarzeń regionalnych listy |
> | action | Microsoft.EventGrid/locations/operationResults/read | Przeczytaj wynik operacji regionalne |
> | action | Microsoft.EventGrid/locations/operationsStatus/read | Odczytaj stan operacji regionalne |
> | action | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Wyświetl subskrypcje zdarzeń regionalnych przez topictype |
> | action | Microsoft.EventGrid/operationResults/read | Wynik operacji odczytu |
> | action | Microsoft.EventGrid/operations/read | Wyświetl listę operacji EventGrid. |
> | action | Microsoft.EventGrid/operationsStatus/read | Odczytaj stan operacji |
> | action | Microsoft.EventGrid/register/action | Rejestruje subskrypcję dostawcy zasobów EventGrid. |
> | action | Microsoft.EventGrid/topics/delete | Usuwanie tematu |
> | action | Microsoft.EventGrid/topics/listKeys/action | Wyświetl listę kluczy dla tematu |
> | action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla tematów |
> | action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla tematów |
> | action | Microsoft.EventGrid/topics/read | Zapoznaj się z tematem |
> | action | Microsoft.EventGrid/topics/regenerateKey/action | Wygeneruj ponownie klucz dla tematu |
> | action | Microsoft.EventGrid/topics/write | Utwórz lub zaktualizuj tematu |
> | action | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Subskrypcje zdarzeń globalnej listy według typu tematu |
> | action | Microsoft.EventGrid/topictypes/eventtypes/read | Przeczytaj eventtypes obsługiwane przez topictype |
> | action | Microsoft.EventGrid/topictypes/read | Przeczytaj topictype |
> | action | Microsoft.EventGrid/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.EventHub/checkNameAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | action | Microsoft.EventHub/checkNamespaceAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailability. |
> | action | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryki klastra opisów zasobów |
> | action | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Usuwa reguły sieci wirtualnej w dostawcy zasobów usługi EventHub dla określonej sieci wirtualnej |
> | action | Microsoft.EventHub/namespaces/authorizationRules/action | Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest przestarzały. Można zaktualizować reguły autoryzacji Namespace zamiast niego użyj wywołania PUT... Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | action | Microsoft.EventHub/namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć domyślnej reguły autoryzacji Namespace.  |
> | action | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia z przestrzenią nazw |
> | action | Microsoft.EventHub/namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | action | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | action | Microsoft.EventHub/namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie Namespace i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | action | Microsoft.EventHub/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobierz klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | action | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. Tę operację można wywołać tylko za pomocą podstawowej przestrzeni nazw. |
> | action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje tryb pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operacja aktualizowania usługi EventHub. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operacja usuwania reguł autoryzacji Centrum zdarzeń |
> | action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Pobierz parametry połączenia Centrum zdarzeń |
> | action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Pobierz listę reguł autoryzacji Centrum zdarzeń |
> | action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Utwórz reguły autoryzacji Centrum zdarzeń i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operacja usuwania zasobu grupy konsumentów |
> | action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Pobierz listę opisów zasobów grupy konsumentów |
> | action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Tworzenie lub właściwości grupy konsumentów aktualizacji. |
> | action | Microsoft.EventHub/namespaces/eventhubs/Delete | Operacja usuwania zasobów usługi EventHub |
> | action | Microsoft.EventHub/namespaces/eventhubs/read | Pobierz listę opisów zasobów Centrum zdarzeń |
> | action | Microsoft.EventHub/namespaces/eventhubs/write | Tworzenie lub właściwości aktualizacji usługi Event Hub. |
> | action | Microsoft.EventHub/namespaces/ipFilterRules/delete | Usuń zasób filtru adresów IP |
> | action | Microsoft.EventHub/namespaces/ipFilterRules/read | Pobierz zasób filtru adresów IP |
> | action | Microsoft.EventHub/namespaces/ipFilterRules/write | Utwórz zasób filtru adresów IP |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Odbieranie wiadomości |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Wysyłanie wiadomości |
> | action | Microsoft.EventHub/namespaces/messagingPlan/read | Pobiera Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | action | Microsoft.EventHub/namespaces/messagingPlan/write | Aktualizuje Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | action | Microsoft.EventHub/namespaces/networkrulesets/delete | Usuń zasób reguły sieci wirtualnej |
> | action | Microsoft.EventHub/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | action | Microsoft.EventHub/namespaces/networkrulesets/write | Utwórz zasób reguły sieci wirtualnej |
> | action | Microsoft.EventHub/namespaces/operationresults/read | Pobierz stan operacji przestrzeni nazw |
> | action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz listę opisów zasobów ustawień diagnostycznych Namespace |
> | action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Pobierz listę opisów zasobów ustawień diagnostycznych Namespace |
> | action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Pobierz listę dzienników Namespace opisów zasobów |
> | action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryk Namespace opisów zasobów |
> | action | Microsoft.EventHub/namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | action | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw usługi ACS |
> | action | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Usuń zasób reguły sieci wirtualnej |
> | action | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Pobiera zasób reguły sieci wirtualnej |
> | action | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Utwórz zasób reguły sieci wirtualnej |
> | action | Microsoft.EventHub/namespaces/write | Tworzenie zasobu Namespace i zaktualizuj jego właściwości. Tagi i pojemność Namespace są właściwości, które mogą być aktualizowane. |
> | action | Microsoft.EventHub/operations/read | Pobierz operacje |
> | action | Microsoft.EventHub/register/action | Rejestruje subskrypcję dostawcy zasobów usługi EventHub i włącza funkcję tworzenia zasobów usługi EventHub |
> | action | Microsoft.EventHub/sku/read | Pobierz listę opisów zasobów jednostki Sku |
> | action | Microsoft.EventHub/sku/regions/read | Pobierz listę opisów zasobów SkuRegions |
> | action | Microsoft.EventHub/unregister/action | Rejestruje dostawcę zasobów usługi EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Features/features/read | Pobiera funkcje subskrypcji. |
> | action | Microsoft.Features/operations/read | Pobiera listę operacji. |
> | action | Microsoft.Features/providers/features/read | Pobiera funkcję subskrypcji danego dostawcy zasobów. |
> | action | Microsoft.Features/providers/features/register/action | Rejestruje funkcję subskrypcji danego dostawcy zasobów. |
> | action | Microsoft.Features/providers/features/unregister/action | Wyrejestrowuje funkcję subskrypcji danego dostawcy zasobów. |
> | action | Microsoft.Features/register/action | Rejestruje funkcję subskrypcji. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Przypisanie konfiguracji gościa GET. |
> | action | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Raport o Get gościa konfiguracji przypisania. |
> | action | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Utwórz nowe przypisanie konfiguracji gościa. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.HDInsight/clusters/applications/delete | Usuń aplikację dla klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/applications/read | Pobieranie aplikacji w przypadku klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/applications/write | Utwórz lub zaktualizuj aplikację dla klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/changerdpsetting/action | Zmień ustawienie połączenia RDP dla klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/configurations/action | Zaktualizuj konfigurację klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/configurations/action | Pobierz konfiguracje klastrów HDInsight |
> | action | Microsoft.HDInsight/clusters/configurations/read | Pobierz konfiguracje klastrów HDInsight |
> | action | Microsoft.HDInsight/clusters/delete | Usuń klaster HDInsight |
> | action | Microsoft.HDInsight/clusters/getGatewaySettings/action | Pobieranie ustawień bramy dla klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/read | Szczegółowe informacje o klastrze HDInsight |
> | action | Microsoft.HDInsight/clusters/roles/resize/action | Zmień rozmiar klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aktualizowanie ustawień bramy dla klastra HDInsight |
> | action | Microsoft.HDInsight/clusters/write | Utwórz lub zaktualizuj klaster HDInsight |
> | action | Microsoft.HDInsight/locations/capabilities/read | Skorzystaj z możliwości subskrypcji |
> | action | Microsoft.HDInsight/locations/checkNameAvailability/read | Sprawdź dostępność nazwy |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ImportExport/jobs/delete | Usuwa istniejące zadanie. |
> | action | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Pobiera klucze funkcji BitLocker dla określonego zadania. |
> | action | Microsoft.ImportExport/jobs/read | Pobiera właściwości dla określonego zadania lub zwraca listę wszystkich zadań. |
> | action | Microsoft.ImportExport/jobs/write | Tworzy zadanie z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego zadania. |
> | action | Microsoft.ImportExport/locations/read | Pobiera właściwości dla określonej lokalizacji lub zwraca listę lokalizacji. |
> | action | Microsoft.ImportExport/register/action | Rejestruje subskrypcję dostawcy zasobów importu/eksportu i umożliwia tworzenie zadania importu/eksportu. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Insights/ActionGroups/Delete | Usuń grupę akcji |
> | action | Microsoft.Insights/ActionGroups/Read | Odczytaj grupę akcji |
> | action | Microsoft.Insights/ActionGroups/Write | Utwórz lub zaktualizuj grupę akcji |
> | action | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktywowano alert dotyczący dziennika aktywności |
> | action | Microsoft.Insights/ActivityLogAlerts/Delete | Usuń alert dotyczący dziennika aktywności |
> | action | Microsoft.Insights/ActivityLogAlerts/Read | Odczytaj alert dotyczący dziennika aktywności |
> | action | Microsoft.Insights/ActivityLogAlerts/Write | Utwórz lub zaktualizuj alert dotyczący dziennika aktywności |
> | action | Microsoft.Insights/AlertRules/Activated/Action | Aktywowano alert dotyczący metryki klasycznej |
> | action | Microsoft.Insights/AlertRules/Delete | Usuń alert dotyczący metryki klasycznej |
> | action | Microsoft.Insights/AlertRules/Incidents/Read | Odczytaj zdarzenie alertu dotyczącego metryki klasycznej |
> | action | Microsoft.Insights/AlertRules/Read | Odczytaj alert dotyczący metryki klasycznej |
> | action | Microsoft.Insights/AlertRules/Resolved/Action | Rozwiązano alert dotyczący metryki klasycznej |
> | action | Microsoft.Insights/AlertRules/Throttled/Action | Ograniczono regułę alertu dotyczącego metryki klasycznej |
> | action | Microsoft.Insights/AlertRules/Write | Utwórz lub zaktualizuj alert dotyczący metryki klasycznej |
> | action | Microsoft.Insights/AutoscaleSettings/Delete | Usuń ustawienie autoskalowania |
> | action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Przeczytaj definicje dzienników |
> | action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Przeczytaj definicje metryk |
> | action | Microsoft.Insights/AutoscaleSettings/Read | Odczytaj ustawienie autoskalowania |
> | action | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Autoskalowanie — zainicjowano skalowanie w dół |
> | action | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Autoskalowanie — zakończono skalowanie w dół |
> | action | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Autoskalowanie — zainicjowano skalowanie w górę |
> | action | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Autoskalowanie — zakończono skalowanie w górę |
> | action | Microsoft.Insights/AutoscaleSettings/Write | Utwórz lub zaktualizuj ustawienie autoskalowania |
> | action | Microsoft.Insights/Baseline/Read | Odczyt punktu odniesienia metryki (wersja zapoznawcza) |
> | action | Microsoft.Insights/CalculateBaseline/Read | Obliczanie punktu odniesienia dla wartości metryk (wersja zapoznawcza) |
> | action | Microsoft.Insights/Components/AnalyticsItems/Delete | Usuwanie elementu analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/AnalyticsItems/Read | Odczytywanie elementu analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/AnalyticsItems/Write | Zapisywanie elementu analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/AnalyticsTables/Action | Akcja tabeli analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/AnalyticsTables/Delete | Usuwanie schematu tabeli analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/AnalyticsTables/Read | Odczytywanie schematu tabeli analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/AnalyticsTables/Write | Zapisywanie schematu tabeli analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/Annotations/Delete | Usuwanie adnotacji w usłudze Application Insights |
> | action | Microsoft.Insights/Components/Annotations/Read | Odczytywanie adnotacji w usłudze Application Insights |
> | action | Microsoft.Insights/Components/Annotations/Write | Zapisywanie adnotacji w usłudze Application Insights |
> | action | Microsoft.Insights/Components/Api/Read | Odczytywanie interfejsu API danych składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/ApiKeys/Action | Generowanie klucza interfejsu API usługi Application Insights |
> | action | Microsoft.Insights/Components/ApiKeys/Delete | Usuwanie klucza interfejsu API usługi Application Insights |
> | action | Microsoft.Insights/Components/ApiKeys/Read | Odczytywanie klucza interfejsu API usługi Application Insights |
> | action | Microsoft.Insights/Components/BillingPlanForComponent/Read | Odczytywanie planu rozliczeniowego dla składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Odczytywanie bieżących funkcji rozliczeń dla składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Zapisywanie bieżących funkcji rozliczeń dla składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Odczytywanie domyślnej konfiguracji integracji z zarządzaniem cyklem życia aplikacji w usłudze Application Insights |
> | action | Microsoft.Insights/Components/Delete | Usuwanie konfiguracji składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/Events/Read | Pobierz dzienniki usługi Application Insights, używając formatu zapytań OData |
> | action | Microsoft.Insights/Components/ExportConfiguration/Action | Akcja ustawień eksportu usługi Application Insights |
> | action | Microsoft.Insights/Components/ExportConfiguration/Delete | Usuwanie ustawień eksportu usługi Application Insights |
> | action | Microsoft.Insights/Components/ExportConfiguration/Read | Odczytywanie ustawień eksportu usługi Application Insights |
> | action | Microsoft.Insights/Components/ExportConfiguration/Write | Zapisywanie ustawień eksportu usługi Application Insights |
> | action | Microsoft.Insights/Components/ExtendQueries/Read | Odczytywanie rozszerzonych wyników zapytania składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/Favorites/Delete | Usuwanie ulubionego elementu w usłudze Application Insights |
> | action | Microsoft.Insights/Components/Favorites/Read | Odczytywanie ulubionego elementu w usłudze Application Insights |
> | action | Microsoft.Insights/Components/Favorites/Write | Zapisywanie ulubionego elementu w usłudze Application Insights |
> | action | Microsoft.Insights/Components/FeatureCapabilities/Read | Odczytywanie możliwości funkcji składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Odczytywanie dostępnych funkcji rozliczeń składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/GetToken/Read | Odczytywanie tokenu składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/MetricDefinitions/Read | Odczytywanie definicji metryk składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/Metrics/Read | Odczytywanie metryk składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/Move/Action | Przenieś składnik usługi Application Insights do innej grupy zasobów lub subskrypcji |
> | action | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Usuwanie osobistego elementu analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/MyAnalyticsItems/Read | Odczytywanie osobistego elementu analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/MyAnalyticsItems/Write | Zapisywanie osobistego elementu analizy usługi Application Insights |
> | action | Microsoft.Insights/Components/MyFavorites/Read | Odczytywanie osobistego ulubionego elementu w usłudze Application Insights |
> | action | Microsoft.Insights/Components/Operations/Read | Pobierz stan długotrwałej operacji w usłudze Application Insights |
> | action | Microsoft.Insights/Components/PricingPlans/Read | Odczytywanie planu cenowego składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/PricingPlans/Write | Zapisywanie planu cenowego składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Odczytywanie konfiguracji wykrywania proaktywnego usługi Application Insights |
> | action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Zapisywanie konfiguracji wykrywania proaktywnego usługi Application Insights |
> | action | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Przeczytaj definicje metryk |
> | action | Microsoft.Insights/Components/Purge/Action | Czyszczenie danych z usługi Application Insights |
> | action | Microsoft.Insights/Components/Query/Read | Uruchom zapytania o dzienniki usługi Application Insights |
> | action | Microsoft.Insights/Components/QuotaStatus/Read | Odczytywanie stanu przydziału składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/Read | Odczytywanie konfiguracji składnika usługi Application Insights |
> | action | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Odczytywanie lokalizacji testu internetowego usługi Application Insights |
> | action | Microsoft.Insights/Components/Webtests/Read | Odczytywanie konfiguracji testu internetowego |
> | action | Microsoft.Insights/Components/WorkItemConfigs/Delete | Usuwanie konfiguracji integracji z zarządzaniem cyklem życia aplikacji w usłudze Application Insights |
> | action | Microsoft.Insights/Components/WorkItemConfigs/Read | Odczytywanie konfiguracji integracji z zarządzaniem cyklem życia aplikacji w usłudze Application Insights |
> | action | Microsoft.Insights/Components/WorkItemConfigs/Write | Zapisywanie konfiguracji integracji z zarządzaniem cyklem życia aplikacji w usłudze Application Insights |
> | action | Microsoft.Insights/Components/Write | Zapisywanie w konfiguracji składnika usługi Application Insights |
> | action | Microsoft.Insights/DiagnosticSettings/Delete | Usuń ustawienie diagnostyczne zasobu |
> | action | Microsoft.Insights/DiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | action | Microsoft.Insights/DiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | action | Microsoft.Insights/EventCategories/Read | Odczytaj dostępne kategorie zdarzeń dziennika aktywności |
> | action | Microsoft.Insights/eventtypes/digestevents/Read | Typ zdarzeń zarządzania — odczytaj podsumowanie |
> | action | Microsoft.Insights/eventtypes/values/Read | Odczytaj zdarzenia dziennika aktywności |
> | action | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Usuń ustawienie diagnostyczne dzienników przepływu sieci |
> | action | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne dziennika przepływu sieci |
> | action | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne dziennika przepływu sieci |
> | action | Microsoft.Insights/ListMigrationDate/Action | Odzyskaj datę migracji subskrypcji |
> | action | Microsoft.Insights/ListMigrationDate/Read | Odzyskaj datę migracji subskrypcji |
> | action | Microsoft.Insights/LogDefinitions/Read | Przeczytaj definicje dzienników |
> | action | Microsoft.Insights/LogProfiles/Delete | Usuń profil rejestrowania dziennika aktywności |
> | action | Microsoft.Insights/LogProfiles/Read | Odczytaj profil rejestrowania dziennik aktywności |
> | action | Microsoft.Insights/LogProfiles/Write | Utwórz lub zaktualizuj profil rejestrowania dla dziennika aktywności |
> | action | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/ADReplicationResult/Read | Odczytaj dane z tabeli ADReplicationResult |
> | action | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/Alert/Read | Odczytaj dane z tabeli Alert |
> | action | Microsoft.Insights/Logs/AlertHistory/Read | Odczytaj dane z tabeli AlertHistory |
> | action | Microsoft.Insights/Logs/ApplicationInsights/Read | Odczytaj dane z tabeli ApplicationInsights |
> | action | Microsoft.Insights/Logs/AzureActivity/Read | Odczytaj dane z tabeli AzureActivity |
> | action | Microsoft.Insights/Logs/AzureMetrics/Read | Odczytaj dane z tabeli AzureMetrics |
> | action | Microsoft.Insights/Logs/BoundPort/Read | Odczytaj dane z tabeli BoundPort |
> | action | Microsoft.Insights/Logs/CommonSecurityLog/Read | Odczytaj dane z tabeli CommonSecurityLog |
> | action | Microsoft.Insights/Logs/ComputerGroup/Read | Odczytaj dane z tabeli ComputerGroup |
> | action | Microsoft.Insights/Logs/ConfigurationChange/Read | Odczytaj dane z tabeli ConfigurationChange |
> | action | Microsoft.Insights/Logs/ConfigurationData/Read | Odczytaj dane z tabeli ConfigurationData |
> | action | Microsoft.Insights/Logs/ContainerImageInventory/Read | Odczytaj dane z tabeli ContainerImageInventory |
> | action | Microsoft.Insights/Logs/ContainerInventory/Read | Odczytaj dane z tabeli ContainerInventory |
> | action | Microsoft.Insights/Logs/ContainerLog/Read | Odczytaj dane z tabeli ContainerLog |
> | action | Microsoft.Insights/Logs/ContainerServiceLog/Read | Odczytaj dane z tabeli ContainerServiceLog |
> | action | Microsoft.Insights/Logs/DeviceAppCrash/Read | Odczytaj dane z tabeli DeviceAppCrash |
> | action | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Odczytaj dane z tabeli DeviceAppLaunch |
> | action | Microsoft.Insights/Logs/DeviceCalendar/Read | Odczytaj dane z tabeli DeviceCalendar |
> | action | Microsoft.Insights/Logs/DeviceCleanup/Read | Odczytaj dane z tabeli DeviceCleanup |
> | action | Microsoft.Insights/Logs/DeviceConnectSession/Read | Odczytaj dane z tabeli DeviceConnectSession |
> | action | Microsoft.Insights/Logs/DeviceEtw/Read | Odczytaj dane z tabeli DeviceEtw |
> | action | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | action | Microsoft.Insights/Logs/DeviceHealth/Read | Odczytaj dane z tabeli DeviceHealth |
> | action | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Odczytaj dane z tabeli DeviceHeartbeat |
> | action | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | action | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | action | Microsoft.Insights/Logs/DeviceSleepState/Read | Odczytaj dane z tabeli DeviceSleepState |
> | action | Microsoft.Insights/Logs/DHAppFailure/Read | Odczytaj dane z tabeli DHAppFailure |
> | action | Microsoft.Insights/Logs/DHAppReliability/Read | Odczytaj dane z tabeli DHAppReliability |
> | action | Microsoft.Insights/Logs/DHDriverReliability/Read | Odczytaj dane z tabeli DHDriverReliability |
> | action | Microsoft.Insights/Logs/DHLogonFailures/Read | Odczytaj dane z tabeli DHLogonFailures |
> | action | Microsoft.Insights/Logs/DHLogonMetrics/Read | Odczytaj dane z tabeli DHLogonMetrics |
> | action | Microsoft.Insights/Logs/DHOSCrashData/Read | Odczytaj dane z tabeli DHOSCrashData |
> | action | Microsoft.Insights/Logs/DHOSReliability/Read | Odczytaj dane z tabeli DHOSReliability |
> | action | Microsoft.Insights/Logs/DHWipAppLearning/Read | Odczytaj dane z tabeli DHWipAppLearning |
> | action | Microsoft.Insights/Logs/DnsEvents/Read | Odczytaj dane z tabeli DnsEvents |
> | action | Microsoft.Insights/Logs/DnsInventory/Read | Odczytaj dane z tabeli DnsInventory |
> | action | Microsoft.Insights/Logs/ETWEvent/Read | Odczytaj dane z tabeli ETWEvent |
> | action | Microsoft.Insights/Logs/Event/Read | Odczytaj dane z tabeli Event |
> | action | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/Heartbeat/Read | Odczytaj dane z tabeli Heartbeat |
> | action | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/InboundConnection/Read | Odczytaj dane z tabeli InboundConnection |
> | action | Microsoft.Insights/Logs/KubeNodeInventory/Read | Odczytaj dane z tabeli KubeNodeInventory |
> | action | Microsoft.Insights/Logs/KubePodInventory/Read | Odczytaj dane z tabeli KubePodInventory |
> | action | Microsoft.Insights/Logs/LinuxAuditLog/Read | Odczytaj dane z tabeli LinuxAuditLog |
> | action | Microsoft.Insights/Logs/MAApplication/Read | Odczytaj dane z tabeli MAApplication |
> | action | Microsoft.Insights/Logs/MAApplicationHealth/Read | Odczytaj dane z tabeli MAApplicationHealth |
> | action | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | action | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | action | Microsoft.Insights/Logs/MAApplicationInstance/Read | Odczytaj dane z tabeli MAApplicationInstance |
> | action | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | action | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Odczytaj dane z tabeli MAApplicationReadiness |
> | action | Microsoft.Insights/Logs/MADeploymentPlan/Read | Odczytaj dane z tabeli MADeploymentPlan |
> | action | Microsoft.Insights/Logs/MADevice/Read | Odczytaj dane z tabeli MADevice |
> | action | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Odczytaj dane z tabeli MADevicePnPHealth |
> | action | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | action | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | action | Microsoft.Insights/Logs/MADeviceReadiness/Read | Odczytaj dane z tabeli MADeviceReadiness |
> | action | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | action | Microsoft.Insights/Logs/MADriverReadiness/Read | Odczytaj dane z tabeli MADriverReadiness |
> | action | Microsoft.Insights/Logs/MAOfficeAddin/Read | Odczytaj dane z tabeli MAOfficeAddin |
> | action | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Odczytaj dane z tabeli MAOfficeAddinHealth |
> | action | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Odczytaj dane z tabeli MAOfficeAddinHealthIssues |
> | action | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Odczytaj dane z tabeli MAOfficeAddinInstance |
> | action | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinInstanceReadiness |
> | action | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinReadiness |
> | action | Microsoft.Insights/Logs/MAOfficeApp/Read | Odczytaj dane z tabeli MAOfficeApp |
> | action | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Odczytaj dane z tabeli MAOfficeAppHealth |
> | action | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Odczytaj dane z tabeli MAOfficeAppInstance |
> | action | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Odczytaj dane z tabeli MAOfficeAppReadiness |
> | action | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Odczytaj dane z tabeli MAOfficeBuildInfo |
> | action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessment |
> | action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | action | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | action | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | action | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | action | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | action | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | action | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | action | Microsoft.Insights/Logs/MAOfficeSuite/Read | Odczytaj dane z tabeli MAOfficeSuite |
> | action | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | action | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | action | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | action | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | action | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | action | Microsoft.Insights/Logs/NetworkMonitoring/Read | Odczytaj dane z tabeli NetworkMonitoring |
> | action | Microsoft.Insights/Logs/OfficeActivity/Read | Odczytaj dane z tabeli OfficeActivity |
> | action | Microsoft.Insights/Logs/Operation/Read | Odczytaj dane z tabeli Operation |
> | action | Microsoft.Insights/Logs/OutboundConnection/Read | Odczytaj dane z tabeli OutboundConnection |
> | action | Microsoft.Insights/Logs/Perf/Read | Odczytaj dane z tabeli Perf |
> | action | Microsoft.Insights/Logs/ProtectionStatus/Read | Odczytaj dane z tabeli ProtectionStatus |
> | action | Microsoft.Insights/Logs/Read | Odczytywanie danych ze wszystkich dzienników |
> | action | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | action | Microsoft.Insights/Logs/ReservedCommonFields/Read | Odczytaj dane z tabeli ReservedCommonFields |
> | action | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/SecurityAlert/Read | Odczytaj dane z tabeli SecurityAlert |
> | action | Microsoft.Insights/Logs/SecurityBaseline/Read | Odczytaj dane z tabeli SecurityBaseline |
> | action | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | action | Microsoft.Insights/Logs/SecurityDetection/Read | Odczytaj dane z tabeli SecurityDetection |
> | action | Microsoft.Insights/Logs/SecurityEvent/Read | Odczytaj dane z tabeli SecurityEvent |
> | action | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | action | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | action | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | action | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Odczytaj dane z tabeli SQLQueryPerformance |
> | action | Microsoft.Insights/Logs/Syslog/Read | Odczytaj dane z tabeli Syslog |
> | action | Microsoft.Insights/Logs/SysmonEvent/Read | Odczytaj dane z tabeli SysmonEvent |
> | action | Microsoft.Insights/Logs/UAApp/Read | Odczytaj dane z tabeli UAApp |
> | action | Microsoft.Insights/Logs/UAComputer/Read | Odczytaj dane z tabeli UAComputer |
> | action | Microsoft.Insights/Logs/UAComputerRank/Read | Odczytaj dane z tabeli UAComputerRank |
> | action | Microsoft.Insights/Logs/UADriver/Read | Odczytaj dane z tabeli UADriver |
> | action | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Odczytaj dane z tabeli UADriverProblemCodes |
> | action | Microsoft.Insights/Logs/UAFeedback/Read | Odczytaj dane z tabeli UAFeedback |
> | action | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Odczytaj dane z tabeli UAHardwareSecurity |
> | action | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | action | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Odczytaj dane z tabeli UAOfficeAddIn |
> | action | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Odczytaj dane z tabeli UAProposedActionPlan |
> | action | Microsoft.Insights/Logs/UASysReqIssue/Read | Odczytaj dane z tabeli UASysReqIssue |
> | action | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Odczytaj dane z tabeli UAUpgradedComputer |
> | action | Microsoft.Insights/Logs/Update/Read | Odczytaj dane z tabeli Update |
> | action | Microsoft.Insights/Logs/UpdateRunProgress/Read | Odczytaj dane z tabeli UpdateRunProgress |
> | action | Microsoft.Insights/Logs/UpdateSummary/Read | Odczytaj dane z tabeli UpdateSummary |
> | action | Microsoft.Insights/Logs/Usage/Read | Odczytaj dane z tabeli Usage |
> | action | Microsoft.Insights/Logs/W3CIISLog/Read | Odczytaj dane z tabeli W3CIISLog |
> | action | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | action | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | action | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | action | Microsoft.Insights/Logs/WDAVStatus/Read | Odczytaj dane z tabeli WDAVStatus |
> | action | Microsoft.Insights/Logs/WDAVThreat/Read | Odczytaj dane z tabeli WDAVThreat |
> | action | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/WindowsFirewall/Read | Odczytaj dane z tabeli WindowsFirewall |
> | action | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | action | Microsoft.Insights/Logs/WireData/Read | Odczytaj dane z tabeli WireData |
> | action | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | action | Microsoft.Insights/Logs/WUDOStatus/Read | Odczytaj dane z tabeli WUDOStatus |
> | action | Microsoft.Insights/MetricAlerts/Delete | Usuń alert dotyczący metryki |
> | action | Microsoft.Insights/MetricAlerts/Read | Odczytaj alert dotyczący metryki |
> | action | Microsoft.Insights/MetricAlerts/Status/Read | Stanu alertu metryki odczytu |
> | action | Microsoft.Insights/MetricAlerts/Write | Utwórz lub zaktualizuj alert dotyczący metryki |
> | action | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Przeczytaj definicje metryk |
> | action | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Przeczytaj definicje metryk |
> | action | Microsoft.Insights/MetricDefinitions/Read | Przeczytaj definicje metryk |
> | action | Microsoft.Insights/Metricnamespaces/Read | Odczytaj przestrzenie nazw metryki |
> | action | Microsoft.Insights/Metrics/Action | Metryki akcji |
> | action | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Odczytaj metryki |
> | action | Microsoft.Insights/Metrics/providers/Metrics/Read | Odczytaj metryki |
> | action | Microsoft.Insights/Metrics/Read | Odczytaj metryki |
> | DataAction | Microsoft.Insights/Metrics/Write | Metryki zapisu |
> | action | Microsoft.Insights/MigrateToNewpricingModel/Action | Migruj subskrypcję do nowego modelu cen |
> | action | Microsoft.Insights/Operations/Read | Operacje odczytu |
> | action | Microsoft.Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | action | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Wycofaj subskrypcję do starszego modelu cen |
> | action | Microsoft.Insights/ScheduledQueryRules/Delete | Usuwanie reguły zaplanowanego zapytania |
> | action | Microsoft.Insights/ScheduledQueryRules/Read | Odczytywanie reguły zaplanowanego zapytania |
> | action | Microsoft.Insights/ScheduledQueryRules/Write | Zapisywanie reguły zaplanowanego zapytania |
> | action | Microsoft.Insights/Tenants/Register/Action | Inicjuje dostawcę usługi Microsoft Insights |
> | action | Microsoft.Insights/Unregister/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | action | Microsoft.Insights/Webtests/Delete | Usuwanie konfiguracji testu internetowego |
> | action | Microsoft.Insights/Webtests/GetToken/Read | Odczytywanie tokenu testu internetowego |
> | action | Microsoft.Insights/Webtests/MetricDefinitions/Read | Odczytywanie definicji metryk testu internetowego |
> | action | Microsoft.Insights/Webtests/Metrics/Read | Odczytywanie metryk testu internetowego |
> | action | Microsoft.Insights/Webtests/Read | Odczytywanie konfiguracji testu internetowego |
> | action | Microsoft.Insights/Webtests/Write | Zapisywanie w konfiguracji testu internetowego |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Intune/diagnosticsettings/delete | Usuwanie ustawienia diagnostyczne |
> | action | Microsoft.Intune/diagnosticsettings/read | Odczytywanie ustawienie diagnostyczne |
> | action | Microsoft.Intune/diagnosticsettings/write | Zapisywanie ustawienia diagnostyczne |
> | action | Microsoft.Intune/diagnosticsettingscategories/read | Odczytywanie kategorii ustawienie diagnostyczne |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.IoTCentral/appTemplates/action | Pobiera wszystkie szablony aplikacji dostępnych w usłudze Azure IoT Central |
> | action | Microsoft.IoTCentral/checkNameAvailability/action | Sprawdza, czy nazwa IoT Central aplikacji jest dostępna |
> | action | Microsoft.IoTCentral/checkSubdomainAvailability/action | Sprawdza, czy poddomeny IoT Central aplikacji jest dostępna |
> | action | Microsoft.IoTCentral/IoTApps/delete | Usuwa aplikacje IoT Central |
> | action | Microsoft.IoTCentral/IoTApps/read | Pobiera pojedynczą aplikacją centralną IoT |
> | action | Microsoft.IoTCentral/IoTApps/write | Tworzy lub aktualizuje aplikacje IoT Central |
> | action | Microsoft.IoTCentral/operations/read | Pobiera dostępne operacje dotyczące IoT Central aplikacji |
> | action | Microsoft.IoTCentral/register/action | Zarejestruj subskrypcję dostawcy zasobów usługi Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.IoTSpaces/Graph/delete | Usuwa zasób grafu Microsoft.IoTSpaces |
> | action | Microsoft.IoTSpaces/Graph/read | Pobiera zasoby Microsoft.IoTSpaces wykresu |
> | action | Microsoft.IoTSpaces/Graph/write | Utwórz zasób Microsoft.IoTSpaces wykresu |
> | action | Microsoft.IoTSpaces/register/action | Rejestruj subskrypcję dostawcy zasobów programu Microsoft.IoTSpaces Graph umożliwiające tworzenie zasobów |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.KeyVault/checkNameAvailability/read | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa oraz czy została już użyta |
> | action | Microsoft.KeyVault/deletedVaults/read | Wyświetl właściwości usuniętych nietrwale magazynów kluczy |
> | action | Microsoft.KeyVault/hsmPools/delete | Usuń pulę modułów HSM |
> | action | Microsoft.KeyVault/hsmPools/joinVault/action | Dołącz magazyn kluczy do puli modułów HSM |
> | action | Microsoft.KeyVault/hsmPools/read | Wyświetl właściwości puli modułów HSM |
> | action | Microsoft.KeyVault/hsmPools/write | Utwórz nową pulę modułów HSM lub zaktualizuj właściwości istniejącej puli modułów HSM |
> | action | Microsoft.KeyVault/locations/deletedVaults/purge/action | Przeczyść usunięty nietrwale magazyn kluczy |
> | action | Microsoft.KeyVault/locations/deletedVaults/read | Wyświetl właściwości usuniętego nietrwale magazynu kluczy |
> | action | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia element Microsoft.KeyVault o usuwaniu podsieci lub sieci wirtualnej |
> | action | Microsoft.KeyVault/locations/operationResults/read | Sprawdź wynik operacji długiego uruchamiania |
> | action | Microsoft.KeyVault/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.KeyVault |
> | action | Microsoft.KeyVault/register/action | Rejestruje subskrypcję |
> | action | Microsoft.KeyVault/unregister/action | Wyrejestrowuje subskrypcję |
> | action | Microsoft.KeyVault/vaults/accessPolicies/write | Aktualizowanie istniejących zasad dostępu przez scalanie lub zastępowanie albo dodawanie nowych zasad dostępu do magazynu. |
> | action | Microsoft.KeyVault/vaults/delete | Usuń magazyn kluczy |
> | action | Microsoft.KeyVault/vaults/deploy/action | Zapewnia dostęp do wpisów tajnych w magazynie kluczy podczas wdrażania zasobów platformy Azure |
> | action | Microsoft.KeyVault/vaults/eventGridFilters/delete | Powiadamia magazyn Microsoft.KeyVault o trwającym usuwaniu subskrypcji EventGrid dla magazynu kluczy |
> | action | Microsoft.KeyVault/vaults/eventGridFilters/read | Powiadamia magazyn Microsoft.KeyVault o wyświetlaniu subskrypcji EventGrid dla magazynu kluczy |
> | action | Microsoft.KeyVault/vaults/eventGridFilters/write | Powiadamia magazyn Microsoft.KeyVault o trwającym tworzeniu nowej subskrypcji EventGrid dla magazynu kluczy |
> | action | Microsoft.KeyVault/vaults/read | Wyświetl właściwości magazynu kluczy |
> | action | Microsoft.KeyVault/vaults/secrets/read | Wyświetl właściwości wpisu tajnego, ale nie jego wartość |
> | action | Microsoft.KeyVault/vaults/secrets/write | Utwórz nowy wpis tajny lub zaktualizuj wartość istniejącego wpisu tajnego |
> | action | Microsoft.KeyVault/vaults/write | Utwórz nowy magazyn kluczy lub zaktualizuj właściwości istniejącego magazynu kluczy |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Kusto/Clusters/Activate/action | Uruchomienie klastra. |
> | action | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Służy do sprawdzania dostępności nazwy klastra. |
> | action | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Dodaje podmiotów zabezpieczeń bazy danych. |
> | action | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Służy do sprawdzania dostępności nazwy dla danego typu. |
> | action | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Usuwa zasób połączenia danych. |
> | action | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Odczytuje zasób połączenia danych. |
> | action | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Zapisuje zasób połączenia usługi danych. |
> | action | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Sprawdza poprawność danych połączenia z bazą danych. |
> | action | Microsoft.Kusto/Clusters/Databases/delete | Usuwa zasób bazy danych. |
> | action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Usuwa zasób połączenia Centrum zdarzeń. |
> | action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Odczytuje zasób połączenia Centrum zdarzeń. |
> | action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Zapisuje zasób połączenia Centrum zdarzeń. |
> | action | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Sprawdzanie poprawności połączenia z bazą danych Centrum zdarzeń. |
> | action | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Wyświetla bazy danych jednostki. |
> | action | Microsoft.Kusto/Clusters/Databases/read | Odczytuje zasobów bazy danych. |
> | action | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Usuwa bazy danych jednostki. |
> | action | Microsoft.Kusto/Clusters/Databases/write | Zapisuje zasobów bazy danych. |
> | action | Microsoft.Kusto/Clusters/Deactivate/action | Zatrzymuje klastra. |
> | action | Microsoft.Kusto/Clusters/delete | Usuwa zasób klastra. |
> | action | Microsoft.Kusto/Clusters/read | Odczytuje zasób klastra. |
> | action | Microsoft.Kusto/Clusters/SKUs/read | Odczytuje jednostki SKU zasobu klastra. |
> | action | Microsoft.Kusto/Clusters/Start/action | Uruchomienie klastra. |
> | action | Microsoft.Kusto/Clusters/Stop/action | Zatrzymuje klastra. |
> | action | Microsoft.Kusto/Clusters/write | Zapisuje zasobu klastra. |
> | action | Microsoft.Kusto/Locations/CheckNameAvailability/action | Sprawdza dostępność nazwy zasobu. |
> | action | Microsoft.Kusto/locations/operationresults/read | Odczyty operacji zasobów |
> | action | Microsoft.Kusto/Operations/read | Odczyty operacji zasobów |
> | action | Microsoft.Kusto/Register/action | Rejestruje subskrypcję dostawcy zasobów Kusto. |
> | action | Microsoft.Kusto/SKUs/read | Odczytuje zasób jednostki SKU. |
> | action | Microsoft.Kusto/Unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.LabServices/labAccounts/CreateLab/action | Tworzenie laboratorium na koncie laboratorium. |
> | action | Microsoft.LabServices/labAccounts/delete | Usuwanie kont w laboratorium. |
> | action | Microsoft.LabServices/labAccounts/galleryImages/delete | Usuwanie obrazów w galerii. |
> | action | Microsoft.LabServices/labAccounts/galleryImages/read | Przeczytaj galerii obrazów. |
> | action | Microsoft.LabServices/labAccounts/galleryImages/write | Dodawanie lub modyfikowanie galerii obrazów. |
> | action | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Uzyskaj informacje o dostępności regionalnej dla każdej kategorii rozmiar skonfigurowany w ramach konta laboratorium |
> | action | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Dodawanie użytkowników do laboratorium |
> | action | Microsoft.LabServices/labAccounts/labs/delete | Usuń labs. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Oświadczenia losowe środowiska użytkownika w ustawieniach środowiska |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Usuń ustawienia środowiska. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Oświadczenia środowiska, a następnie przypisuje go do użytkownika |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Usuwanie środowisk. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Odczyt środowisk. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Resetuje hasło użytkownika w środowisku |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Uruchamia środowisko poprzez uruchomienie wszystkich zasobów w środowisku. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Dodaje lub modyfikuje środowisk. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Przepisy/deprovisions wymagane zasoby do ustawienia środowiska na podstawie bieżącego stanu ustawienia/w środowisku laboratoryjnym. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Odczytaj ustawienia środowiska. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Zapisuje bieżący obraz szablonu do galerii udostępnionych w ramach konta laboratorium |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Usuwanie harmonogramów. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Przeczytaj harmonogramów. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Uruchamia szablonu przez uruchomienie wszystkich zasobów w obrębie szablonu. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Zatrzymuje szablonu przez zatrzymanie wszystkich zasobów w obrębie szablonu. |
> | action | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Dodaje lub modyfikuje ustawienia środowiska. |
> | action | Microsoft.LabServices/labAccounts/labs/read | Przeczytaj labs. |
> | action | Microsoft.LabServices/labAccounts/labs/Register/action | Zarejestruj się w laboratorium zarządzanych. |
> | action | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Wyślij wiadomość e-mail z linkiem rejestracji do laboratorium |
> | action | Microsoft.LabServices/labAccounts/labs/users/delete | Usuwanie użytkowników. |
> | action | Microsoft.LabServices/labAccounts/labs/users/read | Przeczytaj użytkowników. |
> | action | Microsoft.LabServices/labAccounts/labs/users/write | Dodawanie lub modyfikowanie użytkowników. |
> | action | Microsoft.LabServices/labAccounts/labs/write | Dodawanie lub modyfikowanie laboratoriów. |
> | action | Microsoft.LabServices/labAccounts/read | Przeczytaj kontami laboratorium. |
> | action | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Usuń sharedgalleries. |
> | action | Microsoft.LabServices/labAccounts/sharedGalleries/read | Przeczytaj sharedgalleries. |
> | action | Microsoft.LabServices/labAccounts/sharedGalleries/write | Dodaje lub modyfikuje sharedgalleries. |
> | action | Microsoft.LabServices/labAccounts/sharedImages/delete | Usuń sharedimages. |
> | action | Microsoft.LabServices/labAccounts/sharedImages/read | Przeczytaj sharedimages. |
> | action | Microsoft.LabServices/labAccounts/sharedImages/write | Dodaje lub modyfikuje sharedimages. |
> | action | Microsoft.LabServices/labAccounts/write | Dodaje lub modyfikuje kontami laboratorium. |
> | action | Microsoft.LabServices/locations/operations/read | Operacje odczytu. |
> | action | Microsoft.LabServices/register/action | Rejestruje subskrypcję |
> | action | Microsoft.LabServices/users/GetEnvironment/action | Pobiera szczegóły maszyny wirtualnej |
> | action | Microsoft.LabServices/users/GetOperationBatchStatus/action | Pobierz stan operacji dla usługi batch |
> | action | Microsoft.LabServices/users/GetOperationStatus/action | Pobiera stan operacji długotrwałej |
> | action | Microsoft.LabServices/users/GetPersonalPreferences/action | Pobiera preferencje osobiste użytkownika |
> | action | Microsoft.LabServices/users/ListAllEnvironments/action | Wyświetlić listę wszystkich środowisk dla użytkownika |
> | action | Microsoft.LabServices/users/ListEnvironments/action | Wyświetlić listy środowisk dla użytkownika |
> | action | Microsoft.LabServices/users/ListLabs/action | Wyświetl listę labs dla użytkownika. |
> | action | Microsoft.LabServices/users/Register/action | Rejestrowanie użytkownika do zarządzanego laboratorium |
> | action | Microsoft.LabServices/users/ResetPassword/action | Resetuje hasło użytkownika w środowisku |
> | action | Microsoft.LabServices/users/StartEnvironment/action | Uruchamia środowisko poprzez uruchomienie wszystkich zasobów w środowisku. |
> | action | Microsoft.LabServices/users/StopEnvironment/action | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | DataAction | Microsoft.LogAnalytics/logs/ADAssessmentRecommendation/read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/ADReplicationResult/read | Odczytaj dane z tabeli ADReplicationResult |
> | DataAction | Microsoft.LogAnalytics/logs/ADSecurityAssessmentRecommendation/read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/Alert/read | Odczytaj dane z tabeli Alert |
> | DataAction | Microsoft.LogAnalytics/logs/AlertHistory/read | Odczytaj dane z tabeli AlertHistory |
> | DataAction | Microsoft.LogAnalytics/logs/AppCenterError/read | Odczyt danych z tabeli AppCenterError |
> | DataAction | Microsoft.LogAnalytics/logs/ApplicationInsights/read | Odczytaj dane z tabeli ApplicationInsights |
> | DataAction | Microsoft.LogAnalytics/logs/AuditLogs/read | Odczyt danych z tabeli AuditLogs |
> | DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | Odczytaj dane z tabeli AzureActivity |
> | DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | Odczytaj dane z tabeli AzureMetrics |
> | DataAction | Microsoft.LogAnalytics/logs/BoundPort/read | Odczytaj dane z tabeli BoundPort |
> | DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | Odczytaj dane z tabeli CommonSecurityLog |
> | DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | Odczytaj dane z tabeli ComputerGroup |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationChange/read | Odczytaj dane z tabeli ConfigurationChange |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationData/read | Odczytaj dane z tabeli ConfigurationData |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerImageInventory/read | Odczytaj dane z tabeli ContainerImageInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerInventory/read | Odczytaj dane z tabeli ContainerInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerLog/read | Odczytaj dane z tabeli ContainerLog |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerServiceLog/read | Odczytaj dane z tabeli ContainerServiceLog |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppCrash/read | Odczytaj dane z tabeli DeviceAppCrash |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppLaunch/read | Odczytaj dane z tabeli DeviceAppLaunch |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCalendar/read | Odczytaj dane z tabeli DeviceCalendar |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCleanup/read | Odczytaj dane z tabeli DeviceCleanup |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceConnectSession/read | Odczytaj dane z tabeli DeviceConnectSession |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceEtw/read | Odczytaj dane z tabeli DeviceEtw |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHardwareHealth/read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHealth/read | Odczytaj dane z tabeli DeviceHealth |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHeartbeat/read | Odczytaj dane z tabeli DeviceHeartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeHeartbeat/read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeSignIn/read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSleepState/read | Odczytaj dane z tabeli DeviceSleepState |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppFailure/read | Odczytaj dane z tabeli DHAppFailure |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppReliability/read | Odczytaj dane z tabeli DHAppReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHDriverReliability/read | Odczytaj dane z tabeli DHDriverReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonFailures/read | Odczytaj dane z tabeli DHLogonFailures |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonMetrics/read | Odczytaj dane z tabeli DHLogonMetrics |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSCrashData/read | Odczytaj dane z tabeli DHOSCrashData |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSReliability/read | Odczytaj dane z tabeli DHOSReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHWipAppLearning/read | Odczytaj dane z tabeli DHWipAppLearning |
> | DataAction | Microsoft.LogAnalytics/logs/DnsEvents/read | Odczytaj dane z tabeli DnsEvents |
> | DataAction | Microsoft.LogAnalytics/logs/DnsInventory/read | Odczytaj dane z tabeli DnsInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | Odczytaj dane z tabeli ETWEvent |
> | DataAction | Microsoft.LogAnalytics/logs/Event/read | Odczytaj dane z tabeli Event |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeAssessmentRecommendation/read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | Odczytaj dane z tabeli Heartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/IISAssessmentRecommendation/read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/InboundConnection/read | Odczytaj dane z tabeli InboundConnection |
> | DataAction | Microsoft.LogAnalytics/logs/KubeEvents/read | Odczyt danych z tabeli KubeEvents |
> | DataAction | Microsoft.LogAnalytics/logs/KubeNodeInventory/read | Odczytaj dane z tabeli KubeNodeInventory |
> | DataAction | Microsoft.LogAnalytics/logs/KubePodInventory/read | Odczytaj dane z tabeli KubePodInventory |
> | DataAction | Microsoft.LogAnalytics/logs/KubeServices/read | Odczyt danych z tabeli KubeServices |
> | DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | Odczytaj dane z tabeli LinuxAuditLog |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplication/read | Odczytaj dane z tabeli MAApplication |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealth/read | Odczytaj dane z tabeli MAApplicationHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthAlternativeVersions/read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthIssues/read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstance/read | Odczytaj dane z tabeli MAApplicationInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstanceReadiness/read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationReadiness/read | Odczytaj dane z tabeli MAApplicationReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADeploymentPlan/read | Odczytaj dane z tabeli MADeploymentPlan |
> | DataAction | Microsoft.LogAnalytics/logs/MADevice/read | Odczytaj dane z tabeli MADevice |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceNotEnrolled/read | Odczyt danych z tabeli MADeviceNotEnrolled |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealth/read | Odczytaj dane z tabeli MADevicePnPHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthAlternativeVersions/read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthIssues/read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceReadiness/read | Odczytaj dane z tabeli MADeviceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverInstanceReadiness/read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverReadiness/read | Odczytaj dane z tabeli MADriverReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddin/read | Odczytaj dane z tabeli MAOfficeAddin |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealth/read | Odczytaj dane z tabeli MAOfficeAddinHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealthIssues/read | Odczytaj dane z tabeli MAOfficeAddinHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstance/read | Odczytaj dane z tabeli MAOfficeAddinInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstanceReadiness/read | Odczytaj dane z tabeli MAOfficeAddinInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinReadiness/read | Odczytaj dane z tabeli MAOfficeAddinReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeApp/read | Odczytaj dane z tabeli MAOfficeApp |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppHealth/read | Odczytaj dane z tabeli MAOfficeAppHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppInstance/read | Odczytaj dane z tabeli MAOfficeAppInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppReadiness/read | Odczytaj dane z tabeli MAOfficeAppReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeBuildInfo/read | Odczytaj dane z tabeli MAOfficeBuildInfo |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessment/read | Odczytaj dane z tabeli MAOfficeCurrencyAssessment |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessmentDailyCounts/read | Odczytaj dane z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeDeploymentStatus/read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealth/read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealthIssues/read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueInstanceReadiness/read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueReadiness/read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroSummary/read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuite/read | Odczytaj dane z tabeli MAOfficeSuite |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuiteInstance/read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAProposedPilotDevices/read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsBuildInfo/read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessment/read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessmentDailyCounts/read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsDeploymentStatus/read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsSysReqInstanceReadiness/read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/NetworkMonitoring/read | Odczytaj dane z tabeli NetworkMonitoring |
> | DataAction | Microsoft.LogAnalytics/logs/OfficeActivity/read | Odczytaj dane z tabeli OfficeActivity |
> | DataAction | Microsoft.LogAnalytics/logs/Operation/read | Odczytaj dane z tabeli Operation |
> | DataAction | Microsoft.LogAnalytics/logs/OutboundConnection/read | Odczytaj dane z tabeli OutboundConnection |
> | DataAction | Microsoft.LogAnalytics/logs/Perf/read | Odczytaj dane z tabeli Perf |
> | DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | Odczytaj dane z tabeli ProtectionStatus |
> | action | Microsoft.LogAnalytics/logs/read | Odczytywanie danych ze wszystkich dzienników |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | Odczytaj dane z tabeli ReservedCommonFields |
> | DataAction | Microsoft.LogAnalytics/logs/SCCMAssessmentRecommendation/read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SCOMAssessmentRecommendation/read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | Odczytaj dane z tabeli SecurityAlert |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | Odczytaj dane z tabeli SecurityBaseline |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | Odczytaj dane z tabeli SecurityDetection |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | Odczytaj dane z tabeli SecurityEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | DataAction | Microsoft.LogAnalytics/logs/SfBAssessmentRecommendation/read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SfBOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SharePointOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SigninLogs/read | Odczyt danych z tabeli SigninLogs |
> | DataAction | Microsoft.LogAnalytics/logs/SPAssessmentRecommendation/read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SQLAssessmentRecommendation/read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SQLQueryPerformance/read | Odczytaj dane z tabeli SQLQueryPerformance |
> | DataAction | Microsoft.LogAnalytics/logs/Syslog/read | Odczytaj dane z tabeli Syslog |
> | DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | Odczytaj dane z tabeli SysmonEvent |
> | DataAction | Microsoft.LogAnalytics/logs/Tables.Custom/read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | DataAction | Microsoft.LogAnalytics/logs/UAApp/read | Odczytaj dane z tabeli UAApp |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputer/read | Odczytaj dane z tabeli UAComputer |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputerRank/read | Odczytaj dane z tabeli UAComputerRank |
> | DataAction | Microsoft.LogAnalytics/logs/UADriver/read | Odczytaj dane z tabeli UADriver |
> | DataAction | Microsoft.LogAnalytics/logs/UADriverProblemCodes/read | Odczytaj dane z tabeli UADriverProblemCodes |
> | DataAction | Microsoft.LogAnalytics/logs/UAFeedback/read | Odczytaj dane z tabeli UAFeedback |
> | DataAction | Microsoft.LogAnalytics/logs/UAHardwareSecurity/read | Odczytaj dane z tabeli UAHardwareSecurity |
> | DataAction | Microsoft.LogAnalytics/logs/UAIESiteDiscovery/read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | DataAction | Microsoft.LogAnalytics/logs/UAOfficeAddIn/read | Odczytaj dane z tabeli UAOfficeAddIn |
> | DataAction | Microsoft.LogAnalytics/logs/UAProposedActionPlan/read | Odczytaj dane z tabeli UAProposedActionPlan |
> | DataAction | Microsoft.LogAnalytics/logs/UASysReqIssue/read | Odczytaj dane z tabeli UASysReqIssue |
> | DataAction | Microsoft.LogAnalytics/logs/UAUpgradedComputer/read | Odczytaj dane z tabeli UAUpgradedComputer |
> | DataAction | Microsoft.LogAnalytics/logs/Update/read | Odczytaj dane z tabeli Update |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateRunProgress/read | Odczytaj dane z tabeli UpdateRunProgress |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | Odczytaj dane z tabeli UpdateSummary |
> | DataAction | Microsoft.LogAnalytics/logs/Usage/read | Odczytaj dane z tabeli Usage |
> | DataAction | Microsoft.LogAnalytics/logs/VMBoundPort/read | Odczyt danych z tabeli VMBoundPort |
> | DataAction | Microsoft.LogAnalytics/logs/VMConnection/read | Odczyt danych z tabeli VMConnection |
> | DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | Odczytaj dane z tabeli W3CIISLog |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSDeploymentStatus/read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSInsiderStatus/read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSUpdateStatus/read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVStatus/read | Odczytaj dane z tabeli WDAVStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVThreat/read | Odczytaj dane z tabeli WDAVThreat |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsClientAssessmentRecommendation/read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsEvent/read | Odczyt danych z tabeli WindowsEvent |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | Odczytaj dane z tabeli WindowsFirewall |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsServerAssessmentRecommendation/read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/WireData/read | Odczytaj dane z tabeli WireData |
> | DataAction | Microsoft.LogAnalytics/logs/WorkloadMonitoringPerf/read | Odczyt danych z tabeli WorkloadMonitoringPerf |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOAggregatedStatus/read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOStatus/read | Odczytaj dane z tabeli WUDOStatus |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Logic/integrationAccounts/agreements/delete | Usuwa umowę na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości umowy na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/agreements/read | Odczytuje umowę na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/agreements/write | Tworzy lub aktualizuje umowę na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/assemblies/delete | Usuwa zestaw na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości zestawu na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/assemblies/read | Odczytuje zestaw na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/assemblies/write | Tworzy lub aktualizuje zestaw na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Usuwa konfigurację partii na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Odczytuje konfigurację partii na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Tworzy lub aktualizuje konfigurację partii na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/certificates/delete | Usuwa certyfikat na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/certificates/read | Odczytuje certyfikat na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/certificates/write | Tworzy lub aktualizuje certyfikat na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/delete | Usuwa konto integracji. |
> | action | Microsoft.Logic/integrationAccounts/join/action | Dołączanie konta integracji. |
> | action | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla konta integracji. |
> | action | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Pobiera klucze w magazynie kluczy. |
> | action | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Rejestruje zdarzenia śledzenia na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/maps/delete | Usuwa mapowanie na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości mapowania na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/maps/read | Odczytuje mapowanie na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/maps/write | Tworzy lub aktualizuje mapowanie na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/partners/delete | Usuwa partnera na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości partnera na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/partners/read | Odczytuje partnera na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/partners/write | Tworzy lub aktualizuje partnera na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Odczytuje definicje dziennika konta integracji. |
> | action | Microsoft.Logic/integrationAccounts/read | Odczytuje konto integracji. |
> | action | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Generuje ponownie wpisy tajne klucza dostępu. |
> | action | Microsoft.Logic/integrationAccounts/schemas/delete | Usuwa schemat na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości schematu na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/schemas/read | Odczytuje schemat na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/schemas/write | Tworzy lub aktualizuje schemat na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/sessions/delete | Usuwa sesję na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/sessions/read | Odczytuje konfigurację partii na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/sessions/write | Tworzy lub aktualizuje sesję na koncie integracji. |
> | action | Microsoft.Logic/integrationAccounts/write | Tworzy lub aktualizuje konto integracji. |
> | action | Microsoft.Logic/integrationServiceEnvironments/delete | Usuwa środowisko usługi integracji. |
> | action | Microsoft.Logic/integrationServiceEnvironments/join/action | Dołącza środowisko usługi integracji. |
> | action | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Odczytuje operację zarządzanego interfejsu API środowiska usługi integracji. |
> | action | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Odczytuje zarządzany interfejs API środowiska usługi integracji. |
> | action | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Odczytuje stany operacji środowiska usługi integracji. |
> | action | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje definicje metryk środowiska usługi integracji. |
> | action | Microsoft.Logic/integrationServiceEnvironments/read | Odczytuje środowisko usługi integracji. |
> | action | Microsoft.Logic/integrationServiceEnvironments/write | Tworzy lub aktualizuje środowisko usługi integracji. |
> | action | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Pobiera grupy operacji polecenia przepływu pracy. |
> | action | Microsoft.Logic/locations/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | action | Microsoft.Logic/operations/read | Pobiera operację. |
> | action | Microsoft.Logic/register/action | Rejestruje dostawcę zasobów Microsoft.Logic dla danej subskrypcji. |
> | action | Microsoft.Logic/workflows/accessKeys/delete | Usuwa klucz dostępu. |
> | action | Microsoft.Logic/workflows/accessKeys/list/action | Wyświetla wpisy tajne klucza dostępu. |
> | action | Microsoft.Logic/workflows/accessKeys/read | Odczytuje klucz dostępu. |
> | action | Microsoft.Logic/workflows/accessKeys/regenerate/action | Generuje ponownie wpisy tajne klucza dostępu. |
> | action | Microsoft.Logic/workflows/accessKeys/write | Tworzy lub aktualizuje klucz dostępu. |
> | action | Microsoft.Logic/workflows/delete | Usuwa przepływ pracy. |
> | action | Microsoft.Logic/workflows/disable/action | Wyłącza przepływ pracy. |
> | action | Microsoft.Logic/workflows/enable/action | Włącza przepływ pracy. |
> | action | Microsoft.Logic/workflows/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla przepływu pracy. |
> | action | Microsoft.Logic/workflows/listSwagger/action | Pobiera definicje struktury Swagger przepływu pracy. |
> | action | Microsoft.Logic/workflows/move/action | Przenosi przepływ pracy z jego obecnego identyfikatora subskrypcji, grupy zasobów i/lub nazwy do innego identyfikatora subskrypcji, grupy zasobów i/lub nazwy. |
> | action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Odczytuje ustawienia diagnostyki przepływu pracy. |
> | action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub modyfikuje ustawienie diagnostyki przepływu pracy. |
> | action | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Odczytuje definicje dzienników przepływu pracy. |
> | action | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje definicje metryk przepływu pracy. |
> | action | Microsoft.Logic/workflows/read | Odczytuje przepływ pracy. |
> | action | Microsoft.Logic/workflows/regenerateAccessKey/action | Generuje ponownie wpisy tajne klucza dostępu. |
> | action | Microsoft.Logic/workflows/run/action | Uruchamia przebieg przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Pobiera ślady wyrażenia akcji uruchamiania przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/actions/read | Odczytuje akcję przebiegu przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Pobiera ślady wyrażenia powtórzenia akcji przebiegu przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/actions/repetitions/read | Odczytuje powtórzenie akcji przebiegu przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Odczytuje historię żądań akcji powtórzenia przebiegu przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Odczytuje historię żądań akcji przebiegu przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Odczytuje powtórzenie zakresu akcji przebiegu przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/cancel/action | Anuluje przebieg przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/delete | Usuwa przebieg przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/operations/read | Odczytuje stan operacji przebiegu przepływu pracy. |
> | action | Microsoft.Logic/workflows/runs/read | Odczytuje przebieg przepływu pracy. |
> | action | Microsoft.Logic/workflows/suspend/action | Wstrzymuje przepływ pracy. |
> | action | Microsoft.Logic/workflows/triggers/histories/read | Odczytuje historie wyzwalacza. |
> | action | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Przesyła ponownie wyzwalacz przepływu pracy. |
> | action | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | action | Microsoft.Logic/workflows/triggers/read | Odczytuje wyzwalacz. |
> | action | Microsoft.Logic/workflows/triggers/reset/action | Resetuje wyzwalacz. |
> | action | Microsoft.Logic/workflows/triggers/run/action | Uruchamia wyzwalacz. |
> | action | Microsoft.Logic/workflows/triggers/setState/action | Ustawia stan wyzwalacza. |
> | action | Microsoft.Logic/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | action | Microsoft.Logic/workflows/versions/read | Odczytuje wersję przepływu pracy. |
> | action | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | action | Microsoft.Logic/workflows/write | Tworzy lub modyfikuje przepływ pracy. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Przenoszenie maszyn uczenia skojarzenia Plan zobowiązania |
> | action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Przeczytaj maszyn uczenia skojarzenia Plan zobowiązania |
> | action | Microsoft.MachineLearning/commitmentPlans/delete | Usuń maszynę, wszelkie zobowiązania Plan nauki |
> | action | Microsoft.MachineLearning/commitmentPlans/join/action | Dołącz do dowolnej maszyny zobowiązania Plan nauki |
> | action | Microsoft.MachineLearning/commitmentPlans/read | Przeczytaj maszyn zobowiązania Plan nauki |
> | action | Microsoft.MachineLearning/commitmentPlans/write | Utwórz lub zaktualizuj wszelkie planu zobowiązania nauki maszyny |
> | action | Microsoft.MachineLearning/locations/operationresults/read | Pobierz wynik operacji Machine Learning |
> | action | Microsoft.MachineLearning/locations/operationsstatus/read | Pobierz stan trwająca operacja Machine Learning |
> | action | Microsoft.MachineLearning/operations/read | Pobierz operacje uczenia maszynowego |
> | action | Microsoft.MachineLearning/register/action | Rejestruje subskrypcję dostawcy zasobów usługi sieci web usługi uczenia maszynowego i umożliwia tworzenie usług sieci web. |
> | action | Microsoft.MachineLearning/skus/read | Pobierz jednostki SKU planu zobowiązania uczenia maszynowego |
> | action | Microsoft.MachineLearning/webServices/action | Utwórz regionalnych właściwości obsługiwane regiony usługi sieci Web |
> | action | Microsoft.MachineLearning/webServices/delete | Usuń wszystkie usługi internetowej Machine Learning |
> | action | Microsoft.MachineLearning/webServices/listkeys/read | Pobierz klucze do usługi internetowej Machine Learning |
> | action | Microsoft.MachineLearning/webServices/read | Przeczytaj dowolnej usługi internetowej Machine Learning |
> | action | Microsoft.MachineLearning/webServices/write | Utwórz lub zaktualizuj wszelkie usługi internetowej Machine Learning |
> | action | Microsoft.MachineLearning/Workspaces/delete | Usuń dowolną maszynę uczenia obszaru roboczego |
> | action | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Klucze listy dla obszaru roboczego uczenia maszynowego |
> | action | Microsoft.MachineLearning/Workspaces/read | Przeczytaj maszyn uczenia obszaru roboczego |
> | action | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Ponowna synchronizacja kluczy skonfigurowano kont magazynu dla obszaru roboczego uczenia maszynowego |
> | action | Microsoft.MachineLearning/Workspaces/write | Utwórz lub zaktualizuj dowolną maszynę uczenia obszaru roboczego |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Sprawdź, czy aktualizacje są dostępne dla systemu usług dla klastra operacjonalizacji |
> | action | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Usuń wszelkie konta hostingu |
> | action | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Utwórz listę kluczy skojarzone z klastra operacjonalizacji |
> | action | Microsoft.MachineLearningCompute/operationalizationClusters/read | Odczytaj wszelkie hostingu konto |
> | action | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Aktualizowanie usługi systemowe w klastrze operacjonalizacji |
> | action | Microsoft.MachineLearningCompute/operationalizationClusters/write | Utwórz lub zaktualizuj dowolne konto hostingu |
> | action | Microsoft.MachineLearningCompute/register/action | Rejestruje identyfikator subskrypcji do dostawcy zasobów i włącza funkcję tworzenia zasobów obliczeniowych uczenia maszynowego |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.MachineLearningModelManagement/accounts/delete | Usuń wszelkie konta hostingu |
> | action | Microsoft.MachineLearningModelManagement/accounts/read | Odczytaj wszelkie hostingu konto |
> | action | Microsoft.MachineLearningModelManagement/accounts/write | Utwórz lub zaktualizuj dowolne konto hostingu |
> | action | Microsoft.MachineLearningModelManagement/register/action | Rejestruje identyfikator subskrypcji do dostawcy zasobów i włącza funkcję tworzenia konta hostingu |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Pobiera stan operacji obliczeniowych określoną |
> | action | Microsoft.MachineLearningServices/locations/usages/read | Raport użycia dla aml obliczenia zasobów w ramach subskrypcji |
> | action | Microsoft.MachineLearningServices/locations/vmsizes/read | Pobierz rozmiarów maszyn wirtualnych obsługiwanych |
> | action | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Pobiera stan operacji określonego obszaru roboczego |
> | action | Microsoft.MachineLearningServices/register/action | Rejestruje subskrypcję dostawcy zasobów Machine Learning usług |
> | action | Microsoft.MachineLearningServices/workspaces/computes/delete | Usuwa zasoby obliczeniowe w obszarach roboczych programu Machine Learning Services |
> | action | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Listy wpisów tajnych dla zasobów obliczeniowych w obszaru roboczego usługi Machine Learning Services |
> | action | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Lista węzłów dla zasobów obliczeniowych w obszaru roboczego usługi Machine Learning Services |
> | action | Microsoft.MachineLearningServices/workspaces/computes/read | Pobiera zasoby obliczeniowe w obszarach roboczych programu Machine Learning Services |
> | action | Microsoft.MachineLearningServices/workspaces/computes/write | Tworzy lub aktualizuje zasobów obliczeniowych w obszarach roboczych programu Machine Learning Services |
> | action | Microsoft.MachineLearningServices/workspaces/delete | Usuwa obszary robocze usługi uczenia maszynowego |
> | action | Microsoft.MachineLearningServices/workspaces/listKeys/action | Utwórz listę kluczy tajnych dla obszaru roboczego usługi uczenia maszynowego |
> | action | Microsoft.MachineLearningServices/workspaces/read | Pobiera obszarów roboczych usługi uczenia maszynowego |
> | action | Microsoft.MachineLearningServices/workspaces/write | Tworzy lub aktualizuje obszarów roboczych serwera Machine Learning usług |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ManagedIdentity/register/action | Rejestruje subskrypcję dostawcy zasobów tożsamości zarządzanej |
> | action | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Akcja RBAC do przypisywania istniejącego użytkownika tożsamości przypisanej do zasobu |
> | action | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Usuwa istniejące tożsamości przypisanych przez użytkownika |
> | action | Microsoft.ManagedIdentity/userAssignedIdentities/read | Pobiera istniejące tożsamości przypisanych przez użytkownika |
> | action | Microsoft.ManagedIdentity/userAssignedIdentities/write | Tworzy nowy tożsamości przypisanych przez użytkownika lub aktualizuje do tagów skojarzonych z istniejących tożsamości przypisanych przez użytkownika |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ManagedLab/labAccounts/CreateLab/action | Tworzenie laboratorium na koncie laboratorium. |
> | action | Microsoft.ManagedLab/labAccounts/delete | Usuwanie kont w laboratorium. |
> | action | Microsoft.ManagedLab/labAccounts/labs/delete | Usuń labs. |
> | action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Usuń ustawienia środowiska. |
> | action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Usuwanie środowisk. |
> | action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Odczyt środowisk. |
> | action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Dodaje lub modyfikuje środowisk. |
> | action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Odczytaj ustawienia środowiska. |
> | action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Dodaje lub modyfikuje ustawienia środowiska. |
> | action | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Usuwanie maszyn wirtualnych laboratorium. |
> | action | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Przeczytaj maszyn wirtualnych laboratorium. |
> | action | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Dodaje lub modyfikuje maszyn wirtualnych laboratorium. |
> | action | Microsoft.ManagedLab/labAccounts/labs/read | Przeczytaj labs. |
> | action | Microsoft.ManagedLab/labAccounts/labs/write | Dodawanie lub modyfikowanie laboratoriów. |
> | action | Microsoft.ManagedLab/labAccounts/read | Przeczytaj kontami laboratorium. |
> | action | Microsoft.ManagedLab/labAccounts/write | Dodaje lub modyfikuje kontami laboratorium. |
> | action | Microsoft.ManagedLab/locations/operations/read | Operacje odczytu. |
> | action | Microsoft.ManagedLab/register/action | Rejestruje subskrypcję |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Management/checkNameAvailability/action | Sprawdza, czy nazwa grupy zarządzania określony prawidłowy i unikatowy. |
> | action | Microsoft.Management/getEntities/action | Wyświetl listę wszystkich jednostek (grup zarządzania, subskrypcji itp.) dla tego uwierzytelnionego użytkownika. |
> | action | Microsoft.Management/managementGroups/delete | Usuwanie grupy zarządzania. |
> | action | Microsoft.Management/managementGroups/read | Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika. |
> | action | Microsoft.Management/managementGroups/subscriptions/delete | Kojarzy anulować subskrypcję z grupy zarządzania. |
> | action | Microsoft.Management/managementGroups/subscriptions/write | Kojarzy istniejącej subskrypcji z grupą zarządzania. |
> | action | Microsoft.Management/managementGroups/write | Utwórz lub zaktualizuj grupę zarządzania. |
> | action | Microsoft.Management/register/action | Zarejestrowanie określonej subskrypcji Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Daje dostęp do odczytu danych na konto usługi maps. |
> | action | Microsoft.Maps/accounts/delete | Usuwanie konta usługi Maps. |
> | action | Microsoft.Maps/accounts/eventGridFilters/delete | Usuń filtr usługi Event Grid |
> | action | Microsoft.Maps/accounts/eventGridFilters/read | Pobierz filtr usługi Event Grid |
> | action | Microsoft.Maps/accounts/eventGridFilters/write | Utwórz lub zaktualizuj filtr usługi Event Grid |
> | action | Microsoft.Maps/accounts/listKeys/action | Wyświetl klucze konta usługi Maps |
> | action | Microsoft.Maps/accounts/read | Uzyskaj konto usługi Maps. |
> | action | Microsoft.Maps/accounts/regenerateKey/action | Generuj nowy klucz podstawowy lub pomocniczy konta usługi Maps |
> | action | Microsoft.Maps/accounts/write | Utwórz lub zaktualizuj konto usługi Maps. |
> | action | Microsoft.Maps/operations/read | Odczytaj operacje dostawcy |
> | action | Microsoft.Maps/register/action | Zarejestruj dostawcę |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Zwraca umowę. |
> | action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Akceptuje podpisaną umowę. |
> | action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importuje obraz do usługi ACR użytkownika końcowego. |
> | action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Zwraca konfigurację. |
> | action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Zapisuje konfigurację. |
> | action | Microsoft.Marketplace/register/action | Rejestruje dostawcę zasobów Microsoft.Marketplace w ramach subskrypcji. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.MarketplaceApps/ClassicDevServices/delete | Wykonuje operację usuwania do zasobu usługi dev klasycznego. |
> | action | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Pobiera dev klasycznej usługi zasobu zarządzania kluczami. |
> | action | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Pobiera pojedynczy znak na adres URL usługi dev klasycznego. |
> | action | Microsoft.MarketplaceApps/ClassicDevServices/read | Wykonuje operację pobierania w usłudze classic deweloperów. |
> | action | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generuje klucze zarządzania zasobów klasycznych deweloperów usługi. |
> | action | Microsoft.MarketplaceApps/Operations/read | Odczytaj operacje dla wszystkich typów zasobów. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Anuluj umowę do danego handlowa |
> | action | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Zwróć umowę do danego handlowa |
> | action | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Podpisać umowę o podanej handlowa |
> | action | Microsoft.MarketplaceOrdering/agreements/read | Zwróć wszystkie umowy w ramach danej subskrypcji |
> | action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Pobierz umowę do danej maszyny wirtualnej handlowa |
> | action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Zaloguj się lub Anuluj umowę do danej maszyny wirtualnej handlowa |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Media/checknameavailability/action | Sprawdza, czy nazwa konta usługi Media Services jest dostępna |
> | action | Microsoft.Media/locations/checkNameAvailability/action | Sprawdza, czy nazwa konta usługi Media Services jest dostępna |
> | action | Microsoft.Media/mediaservices/accountfilters/delete | Usuń filtr dowolnego konta |
> | action | Microsoft.Media/mediaservices/accountfilters/read | Przeczytaj dowolny filtr konta |
> | action | Microsoft.Media/mediaservices/accountfilters/write | Utwórz lub zaktualizuj dowolny filtr konta |
> | action | Microsoft.Media/mediaservices/assets/assetfilters/delete | Usuń dowolny filtr zasobu |
> | action | Microsoft.Media/mediaservices/assets/assetfilters/read | Przeczytaj dowolny filtr zasobu |
> | action | Microsoft.Media/mediaservices/assets/assetfilters/write | Utwórz lub zaktualizuj dowolny filtr zasobu |
> | action | Microsoft.Media/mediaservices/assets/delete | Usuń zasoby |
> | action | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Pobierz klucz szyfrowania zawartości |
> | action | Microsoft.Media/mediaservices/assets/listContainerSas/action | Zasób kontenerów sygnatury dostępu Współdzielonego adresy URL |
> | action | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Lokalizatory przesyłania strumieniowego listy dla zasobu |
> | action | Microsoft.Media/mediaservices/assets/read | Odczytaj wszelkie zasób |
> | action | Microsoft.Media/mediaservices/assets/write | Utwórz lub zaktualizuj wszelkie zasób |
> | action | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Usuń wszelkie zasady klucza zawartości |
> | action | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Pobierz właściwości zasad przy użyciu kluczy tajnych |
> | action | Microsoft.Media/mediaservices/contentKeyPolicies/read | Przeczytaj żadnych zasad kluczy zawartości |
> | action | Microsoft.Media/mediaservices/contentKeyPolicies/write | Utwórz lub zaktualizuj wszelkie zasady klucza zawartości |
> | action | Microsoft.Media/mediaservices/delete | Usuń wszystkie konta usługi Media Services |
> | action | Microsoft.Media/mediaservices/eventGridFilters/delete | Usuń dowolny filtr siatki zdarzeń |
> | action | Microsoft.Media/mediaservices/eventGridFilters/read | Przeczytaj dowolny filtr siatki zdarzeń |
> | action | Microsoft.Media/mediaservices/eventGridFilters/write | Utwórz lub zaktualizuj dowolny filtr siatki zdarzeń |
> | action | Microsoft.Media/mediaservices/liveEventOperations/read | Wszystkie zdarzenia na żywo operacja odczytu |
> | action | Microsoft.Media/mediaservices/liveEvents/delete | Usuń wszystkie wydarzenia na żywo |
> | action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Usuń wszelkie na żywo dane wyjściowe |
> | action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Przeczytaj żadnego wyniku na żywo |
> | action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Utwórz lub zaktualizuj wszelkie na żywo dane wyjściowe |
> | action | Microsoft.Media/mediaservices/liveEvents/read | Odczytywać wszystkie wydarzenia na żywo |
> | action | Microsoft.Media/mediaservices/liveEvents/reset/action | Resetuj każdej operacji, zdarzenia na żywo |
> | action | Microsoft.Media/mediaservices/liveEvents/start/action | Uruchom dowolną operację zdarzenia na żywo |
> | action | Microsoft.Media/mediaservices/liveEvents/stop/action | Zatrzymaj operację wszystkie wydarzenia na żywo |
> | action | Microsoft.Media/mediaservices/liveEvents/write | Utwórz lub zaktualizuj wszystkie wydarzenia na żywo |
> | action | Microsoft.Media/mediaservices/liveOutputOperations/read | Wszelkie dane wyjściowe na żywo operacja odczytu |
> | action | Microsoft.Media/mediaservices/read | Przeczytaj dowolnego konta usługi Media Services |
> | action | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Odczyt każdej operacji, punkt końcowy przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingEndpoints/delete | Usuwanie dowolnego punktu końcowego przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingEndpoints/read | Przeczytaj dowolnego punktu końcowego przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Skalowanie każdej operacji, punkt końcowy przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Rozpocznij każdej operacji, punkt końcowy przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Zatrzymaj każdej operacji, punkt końcowy przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingEndpoints/write | Utwórz lub zaktualizuj wszelkie punkt końcowy przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingLocators/delete | Usuń wszelkie Lokalizator przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Wyświetl listę kluczy zawartości |
> | action | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Lista ścieżek |
> | action | Microsoft.Media/mediaservices/streamingLocators/read | Przeczytaj wszelkie Lokalizator przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingLocators/write | Utwórz lub zaktualizuj wszelkie Lokalizator przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingPolicies/delete | Usuń wszelkie zasady przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingPolicies/read | Przeczytaj wszelkie zasady przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/streamingPolicies/write | Utwórz lub zaktualizuj wszelkie zasady przesyłania strumieniowego |
> | action | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronizuj klucze magazynu dla konta usługi Azure Storage dołączone |
> | action | Microsoft.Media/mediaservices/transforms/delete | Usuń wszystkie przekształcenia |
> | action | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Anuluj zadanie |
> | action | Microsoft.Media/mediaservices/transforms/jobs/delete | Usuń wszystkie zadania |
> | action | Microsoft.Media/mediaservices/transforms/jobs/read | Odczytaj wszelkie zadanie |
> | action | Microsoft.Media/mediaservices/transforms/jobs/write | Utwórz lub zaktualizuj wszystkie zadania |
> | action | Microsoft.Media/mediaservices/transforms/read | Przeczytaj transformację |
> | action | Microsoft.Media/mediaservices/transforms/write | Utwórz lub zaktualizuj wszelkie przekształcenia |
> | action | Microsoft.Media/mediaservices/write | Utwórz lub zaktualizuj wszelkie konta usługi Media Services |
> | action | Microsoft.Media/operations/read | Pobierz dostępne operacje |
> | action | Microsoft.Media/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Media Services i włącza funkcję tworzenia konta usługi Media Services |
> | action | Microsoft.Media/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Media Services |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Migrate/locations/assessmentOptions/read | Pobiera opcje oceny, które są dostępne w danej lokalizacji |
> | action | Microsoft.Migrate/locations/checknameavailability/action | Służy do sprawdzania dostępności nazwy zasobów w danej subskrypcji w danej lokalizacji |
> | action | Microsoft.Migrate/migrateprojects/read | Pobiera właściwości projektu migracji. |
> | action | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Pobiera konfigurację rozwiązania projektu migracji |
> | action | Microsoft.Migrate/migrateprojects/solutions/read | Pobiera właściwości rozwiązania projektu migracji. |
> | action | Microsoft.Migrate/Operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.Migrate |
> | action | Microsoft.Migrate/projects/assessments/read | Wyświetla listę ocen w projekcie |
> | action | Microsoft.Migrate/projects/delete | Usuwa projekt |
> | action | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Pobierz właściwości ocenionej maszyny |
> | action | Microsoft.Migrate/projects/groups/assessments/delete | Usuwa ocenę |
> | action | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Pobiera adres URL raportu oceny |
> | action | Microsoft.Migrate/projects/groups/assessments/read | Pobiera właściwości oceny |
> | action | Microsoft.Migrate/projects/groups/assessments/write | Tworzy nową ocenę lub aktualizuje istniejącą |
> | action | Microsoft.Migrate/projects/groups/delete | Usuwa grupę |
> | action | Microsoft.Migrate/projects/groups/read | Pobierz właściwości grupy |
> | action | Microsoft.Migrate/projects/groups/write | Tworzy nową grupę lub aktualizuje istniejącą |
> | action | Microsoft.Migrate/projects/keys/action | Pobiera klucze wspólne dla projektu |
> | action | Microsoft.Migrate/projects/machines/read | Pobiera właściwości maszyny |
> | action | Microsoft.Migrate/projects/read | Pobiera właściwości projektu |
> | action | Microsoft.Migrate/projects/write | Tworzy nowy projekt lub aktualizuje istniejący |
> | action | Microsoft.Migrate/register/action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft.Migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.MixedReality/register/action | Rejestruje subskrypcję dostawcy zasobów rzeczywistość mieszana. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Tworzyć kotwic przestrzenne |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Usuń kotwic przestrzenne |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Wykrywanie pobliskich kotwic przestrzenne |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Pobierz właściwości kotwic przestrzenne |
> | action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla Microsoft.MixedReality/SpatialAnchorsAccounts |
> | action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla Microsoft.MixedReality/SpatialAnchorsAccounts |
> | action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla Microsoft.MixedReality/SpatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Znajdź kotwic przestrzenne |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Przesyłanie danych diagnostycznych, aby poprawić jakość usług Azure przestrzenne kotwic |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aktualizacja właściwości kotwic przestrzenne |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.NetApp/locations/operationresults/read | Odczytuje zasób wynik operacji. |
> | action | Microsoft.NetApp/locations/read | Odczyty na dostępność Sprawdź zasób. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Usuwa puli zasobów. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/read | Odczytuje puli zasobów. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Usuwa zasób woluminu. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Odczytuje zasób docelowy instalacji. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Odczytuje zasobu woluminu. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Usuwa zasób migawki. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Odczytuje zasób migawki. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Zapisuje zasobów migawki. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Zapisuje zasobu woluminu. |
> | action | Microsoft.NetApp/netAppAccounts/capacityPools/write | Zapisuje puli zasobów. |
> | action | Microsoft.NetApp/netAppAccounts/delete | Usuwa zasób konta. |
> | action | Microsoft.NetApp/netAppAccounts/read | Odczytuje zasób konta. |
> | action | Microsoft.NetApp/netAppAccounts/write | Zapisuje konta zasobu. |
> | action | Microsoft.NetApp/Operations/read | Odczytuje operacją zasobów. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Pobierz dostępne nagłówków żądań bramy Application Gateway |
> | action | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Pobierz dostępne nagłówek odpowiedzi usługa Application Gateway |
> | action | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Pobierz dostępne zmiennych serwera bramy Application Gateway |
> | action | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Ssl bramy aplikacji wstępnie zdefiniowane zasady |
> | action | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Dostępne opcje protokołu Ssl bramy aplikacji |
> | action | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Pobiera bramy aplikacji, która ustawia dostępne reguły zapory aplikacji sieci Web |
> | action | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Dołącza do puli adresów zaplecza bramy aplikacji. Nie dla. |
> | action | Microsoft.Network/applicationGateways/backendhealth/action | Pobiera stan zaplecza bramy aplikacji |
> | action | Microsoft.Network/applicationGateways/delete | Usuwa bramy aplikacji |
> | action | Microsoft.Network/applicationGateways/read | Pobiera bramy aplikacji |
> | action | Microsoft.Network/applicationGateways/start/action | Uruchamia bramy aplikacji |
> | action | Microsoft.Network/applicationGateways/stop/action | Zatrzymuje bramy aplikacji |
> | action | Microsoft.Network/applicationGateways/write | Utworzenie bramy aplikacji lub aktualizacji bramy aplikacji |
> | action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Usuwa zasadę zapory aplikacji sieci Web dla bramy aplikacji |
> | action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Pobiera zasady brama aplikacji zapory aplikacji internetowych |
> | action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Tworzy zasady zapory aplikacji sieci Web dla bramy aplikacji lub aktualizacji zasad usługi Application Gateway zapory aplikacji internetowych |
> | action | Microsoft.Network/applicationSecurityGroups/delete | Usuwa grupę zabezpieczeń aplikacji |
> | action | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Dołącza do konfiguracji adresu IP do grup zabezpieczeń aplikacji. Nie dla. |
> | action | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Dołącza reguły zabezpieczeń do grup zabezpieczeń aplikacji. Nie dla. |
> | action | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Konfiguracje adresów IP listy w ApplicationSecurityGroup |
> | action | Microsoft.Network/applicationSecurityGroups/read | Pobiera identyfikator grupy zabezpieczeń aplikacji. |
> | action | Microsoft.Network/applicationSecurityGroups/write | Tworzy grupę zabezpieczeń aplikacji lub aktualizacji istniejącej grupy zabezpieczeń aplikacji. |
> | action | Microsoft.Network/azureFirewallFqdnTags/read | Pobiera tagi FQDN zaporę platformy Azure |
> | action | Microsoft.Network/azurefirewalls/delete | Usuwanie zapory platformy Azure |
> | action | Microsoft.Network/azurefirewalls/read | Pobierz zaporę platformy Azure |
> | action | Microsoft.Network/azurefirewalls/write | Tworzy lub aktualizuje zapory usługi Azure |
> | action | Microsoft.Network/bastionHosts/delete | Usuwa hostem bastionu |
> | action | Microsoft.Network/bastionHosts/read | Pobiera hostem bastionu |
> | action | Microsoft.Network/bastionHosts/write | Utwórz lub zaktualizuj hostem bastionu |
> | action | Microsoft.Network/bgpServiceCommunities/read | Pobierz usługę protokołu Bgp społeczności |
> | action | Microsoft.Network/checkFrontDoorNameAvailability/action | Sprawdza, czy nazwa drzwiami frontowymi jest dostępna |
> | action | Microsoft.Network/checkTrafficManagerNameAvailability/action | Służy do sprawdzania dostępności nazwy DNS względem usługi Traffic Manager. |
> | action | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | action | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | action | Microsoft.Network/connections/revoke/action | Oznacza stan połączenia usługi Expressroute jako odwołane |
> | action | Microsoft.Network/connections/sharedkey/action | Get VirtualNetworkGatewayConnection SharedKey |
> | action | Microsoft.Network/connections/sharedKey/read | Gets VirtualNetworkGatewayConnection SharedKey |
> | action | Microsoft.Network/connections/sharedKey/write | Tworzy lub aktualizuje istniejące VirtualNetworkGatewayConnection SharedKey |
> | action | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Pobiera konfigurację VirtualNetworkGatewayConnection urządzenia sieci Vpn |
> | action | Microsoft.Network/connections/write | Tworzy lub aktualizuje istniejące VirtualNetworkGatewayConnection |
> | action | Microsoft.Network/ddosCustomPolicies/delete | Usuwa przed atakami DDoS dostosowane zasady |
> | action | Microsoft.Network/ddosCustomPolicies/read | Pobiera przed atakami DDoS dostosowanej definicji. Definicja zasad |
> | action | Microsoft.Network/ddosCustomPolicies/write | Tworzy zasady przed atakami DDoS dostosowany lub aktualizuje istniejące zasady przed atakami DDoS dostosowane |
> | action | Microsoft.Network/ddosProtectionPlans/delete | Usuwa planu ochrony przed atakami DDoS |
> | action | Microsoft.Network/ddosProtectionPlans/join/action | Dołącza do planu ochrony przed atakami DDoS. Nie dla. |
> | action | Microsoft.Network/ddosProtectionPlans/read | Pobiera Plan ochrony przed atakami DDoS |
> | action | Microsoft.Network/ddosProtectionPlans/write | Tworzy Plan ochrony przed atakami DDoS lub aktualizuje Plan ochrony przed atakami DDoS  |
> | action | Microsoft.Network/dnsoperationresults/read | Pobiera wyniki operacji DNS |
> | action | Microsoft.Network/dnsoperationstatuses/read | Pobiera stan operacji DNS  |
> | action | Microsoft.Network/dnszones/A/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "A", ze strefy DNS. |
> | action | Microsoft.Network/dnszones/A/read | Pobierz zestaw rekordów typu "A", w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | action | Microsoft.Network/dnszones/A/write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | action | Microsoft.Network/dnszones/AAAA/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "AAAA" ze strefy DNS. |
> | action | Microsoft.Network/dnszones/AAAA/read | Pobierz zestaw rekordów typu "AAAA" w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | action | Microsoft.Network/dnszones/AAAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | action | Microsoft.Network/dnszones/all/read | Pobiera zestawy rekordów DNS w przypadku typów |
> | action | Microsoft.Network/dnszones/CAA/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "CAA, ze strefy DNS. |
> | action | Microsoft.Network/dnszones/CAA/read | Pobierz zestaw rekordów typu "CAA" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element etag. |
> | action | Microsoft.Network/dnszones/CAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "CAA" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | action | Microsoft.Network/dnszones/CNAME/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "CNAME, ze strefy DNS. |
> | action | Microsoft.Network/dnszones/CNAME/read | Pobierz zestaw rekordów typu "CNAME" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element etag. |
> | action | Microsoft.Network/dnszones/CNAME/write | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | action | Microsoft.Network/dnszones/delete | Usunięcie strefy DNS, w formacie JSON. Właściwości strefy obejmują tagów, element etag, numberOfRecordSets i maxNumberOfRecordSets. |
> | action | Microsoft.Network/dnszones/MX/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "MX", ze strefy DNS. |
> | action | Microsoft.Network/dnszones/MX/read | Pobierz zestaw rekordów typu "MX", w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | action | Microsoft.Network/dnszones/MX/write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | action | Microsoft.Network/dnszones/NS/delete | Usuwa zestaw rekordów DNS typu NS |
> | action | Microsoft.Network/dnszones/NS/read | Pobiera zestaw rekordów DNS typu NS |
> | action | Microsoft.Network/dnszones/NS/write | Tworzy lub aktualizuje zestaw rekordów DNS typu NS |
> | action | Microsoft.Network/dnszones/PTR/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "PTR" ze strefy DNS. |
> | action | Microsoft.Network/dnszones/PTR/read | Pobierz zestaw rekordów typu "PTR", w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | action | Microsoft.Network/dnszones/PTR/write | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | action | Microsoft.Network/dnszones/read | Uzyskiwanie strefy DNS, w formacie JSON. Właściwości strefy obejmują tagów, element etag, numberOfRecordSets i maxNumberOfRecordSets. Należy pamiętać, że to polecenie nie pobiera pożądanych zestawów rekordów znajdujących się w strefie. |
> | action | Microsoft.Network/dnszones/recordsets/read | Pobiera zestawy rekordów DNS w przypadku typów |
> | action | Microsoft.Network/dnszones/SOA/read | Pobiera zestaw rekordów DNS typu SOA |
> | action | Microsoft.Network/dnszones/SOA/write | Tworzy lub aktualizuje zestaw rekordów DNS typu SOA |
> | action | Microsoft.Network/dnszones/SRV/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "SRV" ze strefy DNS. |
> | action | Microsoft.Network/dnszones/SRV/read | Pobierz zestaw rekordów typu "SRV" w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | action | Microsoft.Network/dnszones/SRV/write | Utwórz lub zaktualizuj zestaw rekordów typu SRV |
> | action | Microsoft.Network/dnszones/TXT/delete | Usuń zestaw rekordów o określonej nazwie, a następnie wpisz "TXT" ze strefy DNS. |
> | action | Microsoft.Network/dnszones/TXT/read | Pobierz zestaw rekordów typu "TXT", w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | action | Microsoft.Network/dnszones/TXT/write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | action | Microsoft.Network/dnszones/write | Utwórz lub zaktualizuj strefę DNS w grupie zasobów.  Używany do aktualizacji tagów w zasobie strefy DNS. Należy pamiętać, że to polecenie nie można utworzyć lub zaktualizować zestawów rekordów w strefie. |
> | action | Microsoft.Network/expressRouteCircuits/authorizations/delete | Usuwa ExpressRouteCircuit autoryzacji |
> | action | Microsoft.Network/expressRouteCircuits/authorizations/read | Pobiera ExpressRouteCircuit autoryzacji |
> | action | Microsoft.Network/expressRouteCircuits/authorizations/write | Tworzy lub aktualizuje istniejące autoryzacji ExpressRouteCircuit |
> | action | Microsoft.Network/expressRouteCircuits/delete | Usuwa obwodu usługi Expressroute |
> | action | Microsoft.Network/expressRouteCircuits/join/action | Dołącza do obwodu usługi Express Route. Nie dla. |
> | action | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Pobiera ExpressRouteCircuit ArpTable komunikacji równorzędnej |
> | action | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Usuwa połączenie ExpressRouteCircuit |
> | action | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Pobiera połączenie ExpressRouteCircuit |
> | action | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Tworzy lub aktualizuje istniejący zasób połączenia ExpressRouteCircuit |
> | action | Microsoft.Network/expressRouteCircuits/peerings/delete | Usuwa ExpressRouteCircuit komunikacji równorzędnej |
> | action | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Pobiera elementu równorzędnego połączenia obwodu usługi Express Route |
> | action | Microsoft.Network/expressRouteCircuits/peerings/read | Pobiera ExpressRouteCircuit komunikacji równorzędnej |
> | action | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Pobiera ExpressRouteCircuit RouteTable komunikacji równorzędnej |
> | action | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Pobiera ExpressRouteCircuit komunikacji równorzędnej RouteTable podsumowanie |
> | action | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Pobiera ExpressRouteCircuit komunikacji równorzędnej Stat |
> | action | Microsoft.Network/expressRouteCircuits/peerings/write | Tworzy lub aktualizuje istniejącą ExpressRouteCircuit komunikację równorzędną |
> | action | Microsoft.Network/expressRouteCircuits/read | Pobierz obwodu usługi Expressroute |
> | action | Microsoft.Network/expressRouteCircuits/stats/read | Pobiera ExpressRouteCircuit Stat |
> | action | Microsoft.Network/expressRouteCircuits/write | Tworzy lub aktualizuje istniejące obwodu usługi Expressroute |
> | action | Microsoft.Network/expressRouteCrossConnections/join/action | Sprzężenia moduł Express Route krzyżowe połączenia. Nie dla. |
> | action | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Pobiera Expressroute między tabeli protokołu Arp komunikacji równorzędnej połączenie |
> | action | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Usuwa Expressroute między połączenia komunikacji równorzędnej |
> | action | Microsoft.Network/expressRouteCrossConnections/peerings/read | Pobiera Expressroute między połączenia komunikacji równorzędnej |
> | action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Pobiera Expressroute między połączenia komunikacji równorzędnej Route Table |
> | action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Pobiera Expressroute między podsumowanie tabeli trasy połączenia komunikacji równorzędnej |
> | action | Microsoft.Network/expressRouteCrossConnections/peerings/write | Tworzy wiele połączeń komunikacji równorzędnej usługi Expressroute lub aktualizuje istniejące Cross połączenia komunikacji równorzędnej usługi Expressroute |
> | action | Microsoft.Network/expressRouteCrossConnections/read | Pobierz Expressroute między połączenia |
> | action | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Usuwa połączenia usługi Express Route |
> | action | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Pobiera połączenia usługi Express Route |
> | action | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Połączenia Expressroute tworzy lub aktualizuje istniejące połączenia usługi Expressroute |
> | action | Microsoft.Network/expressRouteGateways/join/action | Dołącza do bramy usługi Expressroute. Nie dla. |
> | action | Microsoft.Network/expressRouteGateways/read | Pobierz brama usługi Express Route |
> | action | Microsoft.Network/expressRoutePorts/delete | Deletes ExpressRoutePorts |
> | action | Microsoft.Network/expressRoutePorts/join/action | Dołącza do portów Express Route. Nie dla. |
> | action | Microsoft.Network/expressRoutePorts/links/read | Gets ExpressRouteLink |
> | action | Microsoft.Network/expressRoutePorts/read | Gets ExpressRoutePorts |
> | action | Microsoft.Network/expressRoutePorts/write | Tworzy lub aktualizuje ExpressRoutePorts |
> | action | Microsoft.Network/expressRoutePortsLocations/read | Lokalizacje Get Express Route portów |
> | action | Microsoft.Network/expressRouteServiceProviders/read | Pobiera dostawców usługi Express Route |
> | action | Microsoft.Network/frontDoors/backendPools/delete | Usuwa puli zaplecza |
> | action | Microsoft.Network/frontDoors/backendPools/read | Pobiera puli zaplecza |
> | action | Microsoft.Network/frontDoors/backendPools/write | Tworzy lub aktualizuje puli zaplecza |
> | action | Microsoft.Network/frontDoors/delete | Usuwa drzwi |
> | action | Microsoft.Network/frontDoors/frontendEndpoints/delete | Usuwa punkt końcowy serwera sieci Web |
> | action | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Powoduje wyłączenie protokołu HTTPS w punkcie końcowym serwera sieci Web |
> | action | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Włącza protokół HTTPS dla punktu końcowego serwera sieci Web |
> | action | Microsoft.Network/frontDoors/frontendEndpoints/read | Pobiera punkt końcowy serwera sieci Web |
> | action | Microsoft.Network/frontDoors/frontendEndpoints/write | Tworzy lub aktualizuje punktu końcowego serwera sieci Web |
> | action | Microsoft.Network/frontDoors/healthProbeSettings/delete | Usuwa ustawienia sondy kondycji |
> | action | Microsoft.Network/frontDoors/healthProbeSettings/read | Pobiera ustawienia sondy kondycji |
> | action | Microsoft.Network/frontDoors/healthProbeSettings/write | Tworzy lub aktualizuje ustawienia sondy kondycji |
> | action | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | action | Microsoft.Network/frontDoors/loadBalancingSettings/read | Pobiera obciążenia równoważenia ustawień |
> | action | Microsoft.Network/frontDoors/loadBalancingSettings/write | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | action | Microsoft.Network/frontDoors/purge/action | Przeczyszczanie zawartości w pamięci podręcznej z drzwi |
> | action | Microsoft.Network/frontDoors/read | Pobiera drzwi |
> | action | Microsoft.Network/frontDoors/routingRules/delete | Usuwa regułę routingu |
> | action | Microsoft.Network/frontDoors/routingRules/read | Pobiera regułę routingu |
> | action | Microsoft.Network/frontDoors/routingRules/write | Tworzy lub aktualizuje regułę routingu |
> | action | Microsoft.Network/frontDoors/validateCustomDomain/action | Sprawdza poprawność punktu końcowego serwera sieci Web dla drzwi |
> | action | Microsoft.Network/frontDoors/write | Tworzy lub aktualizuje drzwi |
> | action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Usuwa zasady zapory aplikacji sieci Web |
> | action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Pobiera zasady zapory aplikacji sieci Web |
> | action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Tworzy lub aktualizuje zasady zapory aplikacji sieci Web |
> | action | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Dołącza do puli adresów zaplecza modułu równoważenia obciążenia. Nie dla. |
> | action | Microsoft.Network/loadBalancers/backendAddressPools/read | Pobiera definicji puli adresów wewnętrznej bazy danych modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/delete | Usuwa moduł równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Dołącza do konfiguracji adresu IP frontonu modułu równoważenia obciążenia. Nie dla. |
> | action | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Pobiera definicję konfiguracji IP frontonu modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Dołącza modułu równoważenia obciążenia dla ruchu przychodzącego translatora adresów Sieciowych puli. Nie dla. |
> | action | Microsoft.Network/loadBalancers/inboundNatPools/read | Pobiera moduł równoważenia obciążenia dla ruchu przychodzącego translatora adresów sieciowych definicji puli |
> | action | Microsoft.Network/loadBalancers/inboundNatRules/delete | Usuwa regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Dołącza do reguły nat dla ruchu przychodzącego modułu równoważenia obciążenia. Nie dla. |
> | action | Microsoft.Network/loadBalancers/inboundNatRules/read | Pobiera moduł równoważenia obciążenia dla ruchu przychodzącego definicji reguły translatora adresów sieciowych |
> | action | Microsoft.Network/loadBalancers/inboundNatRules/write | Tworzy regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia lub aktualizuje istniejącą regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/loadBalancingRules/read | Pobiera równoważenia definicji reguły obciążenia modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/networkInterfaces/read | Pobiera odwołania do wszystkich interfejsów sieciowych w ramach modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/outboundRules/read | Pobiera definicję reguły ruchu wychodzącego modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/probes/join/action | Umożliwia użycie sond modułu równoważenia obciążenia. Na przykład z tą właściwością healthProbe uprawnień dla usługi VM scale set odwoływać się do sondy. Nie dla. |
> | action | Microsoft.Network/loadBalancers/probes/read | Pobiera sondy modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/read | Pobiera definicji modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w ramach modułu równoważenia obciążenia |
> | action | Microsoft.Network/loadBalancers/write | Tworzy moduł równoważenia obciążenia lub aktualizuje istniejącego modułu równoważenia obciążenia |
> | action | Microsoft.Network/localnetworkgateways/delete | Deletes LocalNetworkGateway |
> | action | Microsoft.Network/localnetworkgateways/read | Gets LocalNetworkGateway |
> | action | Microsoft.Network/localnetworkgateways/write | Tworzy lub aktualizuje istniejące LocalNetworkGateway |
> | action | Microsoft.Network/locations/availableDelegations/read | Pobiera dostępne delegacji |
> | action | Microsoft.Network/locations/availablePrivateEndpointResources/read | Pobiera zasoby dostępne prywatne punktu końcowego |
> | action | Microsoft.Network/locations/bareMetalTenants/action | Alokuje lub weryfikuje dzierżawy bez systemu operacyjnego |
> | action | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Sprawdza, czy wsparcia Accelerated Networking |
> | action | Microsoft.Network/locations/checkDnsNameAvailability/read | Sprawdza, czy etykieta dns jest dostępna w określonej lokalizacji |
> | action | Microsoft.Network/locations/operationResults/read | Pobiera wynik operacji asynchronicznej POST lub operacja usuwania |
> | action | Microsoft.Network/locations/operations/read | Pobiera zasób operacji, który reprezentuje stan operacji asynchronicznej |
> | action | Microsoft.Network/locations/serviceTags/read | Pobierz tagi usługi |
> | action | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Pobiera obsługiwane rozmiary maszyn wirtualnych |
> | action | Microsoft.Network/locations/usages/read | Pobiera zasoby metryki użycia |
> | action | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Pobiera listę dostępnych usług punktu końcowego sieci wirtualnej |
> | action | Microsoft.Network/networkIntentPolicies/delete | Usuwa zasady intencji sieci |
> | action | Microsoft.Network/networkIntentPolicies/read | Pobiera opis elementu Intent zasad sieciowych |
> | action | Microsoft.Network/networkIntentPolicies/write | Tworzy zasady sieciowe przeznaczenie lub aktualizuje istniejące zasady sieciowe przeznaczenie |
> | action | Microsoft.Network/networkInterfaces/delete | Usuwa interfejs sieciowy |
> | action | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Pobieranie grup zabezpieczeń sieci skonfigurowane w sieci interfejsu maszyny wirtualnej |
> | action | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Pobierz tabelę trasy skonfigurowane dla interfejsu sieciowego maszyny wirtualnej |
> | action | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Dołącza do konfiguracji adresu IP interfejsu sieciowego. Nie dla. |
> | action | Microsoft.Network/networkInterfaces/ipconfigurations/read | Pobiera definicję konfiguracji adresu ip interfejsu sieci.  |
> | action | Microsoft.Network/networkInterfaces/join/action | Dołącza do maszyny wirtualnej do interfejsu sieciowego. Nie dla. |
> | action | Microsoft.Network/networkInterfaces/loadBalancers/read | Pobiera wszystkie moduły równoważenia obciążenia, które interfejs sieciowy jest częścią |
> | action | Microsoft.Network/networkInterfaces/read | Pobiera definicja interfejsu sieciowego.  |
> | action | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Usuwa konfigurację wzorca Tap interfejsu sieciowego. |
> | action | Microsoft.Network/networkInterfaces/tapConfigurations/read | Pobiera konfigurację wzorca Tap interfejsu sieciowego. |
> | action | Microsoft.Network/networkInterfaces/tapConfigurations/write | Tworzy konfiguracji naciśnij interfejsu sieciowego lub aktualizacji istniejącej konfiguracji interfejsu sieciowego naciśnij. |
> | action | Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | action | Microsoft.Network/networkProfiles/delete | Usuwa profil sieciowy |
> | action | Microsoft.Network/networkProfiles/read | Pobiera profil sieciowy |
> | action | Microsoft.Network/networkProfiles/removeContainers/action | Usuwa kontenery |
> | action | Microsoft.Network/networkProfiles/setContainers/action | Ustawia kontenerów |
> | action | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Ustawia kontener interfejsów sieciowych |
> | action | Microsoft.Network/networkProfiles/write | Tworzy lub aktualizuje profil sieciowy |
> | action | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Pobiera domyślną definicją reguły zabezpieczeń |
> | action | Microsoft.Network/networkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń |
> | action | Microsoft.Network/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń. Nie dla. |
> | action | Microsoft.Network/networkSecurityGroups/read | Pobiera definicję grupy zabezpieczeń sieci |
> | action | Microsoft.Network/networkSecurityGroups/securityRules/delete | Usuwa regułę zabezpieczeń |
> | action | Microsoft.Network/networkSecurityGroups/securityRules/read | Pobiera definicji reguły zabezpieczeń |
> | action | Microsoft.Network/networkSecurityGroups/securityRules/write | Tworzy regułę zabezpieczeń lub aktualizuje istniejącą regułę zabezpieczeń |
> | action | Microsoft.Network/networkSecurityGroups/write | Tworzy sieciową grupę zabezpieczeń lub aktualizacji istniejącej sieciowej grupy zabezpieczeń |
> | action | Microsoft.Network/networkWatchers/availableProvidersList/action | Zwraca wszystkie dostępne internet dostawcy usług dla określonego regionu platformy Azure. |
> | action | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Zwraca oceny opóźnienia względnego dla Internetu dostawcy usług z określonej lokalizacji do regionów platformy Azure. |
> | action | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfiguruje rejestrowanie przepływu dla zasobu docelowego. |
> | action | Microsoft.Network/networkWatchers/connectionMonitors/delete | Usuwa monitora połączeń |
> | action | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Monitorowanie łączności między punktami końcowymi określonego zapytania |
> | action | Microsoft.Network/networkWatchers/connectionMonitors/read | Pobierz szczegóły monitora połączeń |
> | action | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Rozpocznij monitorowanie łączności między punktami końcowymi określonego |
> | action | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Monitorowanie łączności między punktami końcowymi określonego Stop/wstrzymywany |
> | action | Microsoft.Network/networkWatchers/connectionMonitors/write | Tworzy monitora połączeń |
> | action | Microsoft.Network/networkWatchers/connectivityCheck/action | Sprawdza możliwość nawiązywania bezpośredniego połączenia TCP z maszyny wirtualnej do danego punktu końcowego, łącznie z innej maszyny Wirtualnej lub dowolnego serwera zdalnego. |
> | action | Microsoft.Network/networkWatchers/delete | Usuwa usługi network watcher |
> | action | Microsoft.Network/networkWatchers/ipFlowVerify/action | Zwraca, czy pakiet może wchodzić lub wychodzić do / z określonej lokalizacji docelowej. |
> | action | Microsoft.Network/networkWatchers/lenses/delete | Usuwa obiektywu |
> | action | Microsoft.Network/networkWatchers/lenses/query/action | Zapytanie monitorowania ruchu sieciowego w określonym punkcie końcowym |
> | action | Microsoft.Network/networkWatchers/lenses/read | Pobierz szczegóły obiektywu |
> | action | Microsoft.Network/networkWatchers/lenses/start/action | Rozpocznij monitorowanie ruchu sieciowego w określonym punkcie końcowym |
> | action | Microsoft.Network/networkWatchers/lenses/stop/action | Zatrzymaj/wstrzymywany monitorowania ruchu sieciowego w określonym punkcie końcowym |
> | action | Microsoft.Network/networkWatchers/lenses/write | Tworzy obiektywu |
> | action | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostyka konfiguracji sieci. |
> | action | Microsoft.Network/networkWatchers/nextHop/action | Dla określonego obiektu docelowego i docelowy adres IP zwracać typ następnego przeskoku, a następnie nadzieję, że adres IP. |
> | action | Microsoft.Network/networkWatchers/packetCaptures/delete | Usuwa przechwytywania pakietów |
> | action | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Pobiera informacje o właściwości i stan zasobów przechwytywania pakietów. |
> | action | Microsoft.Network/networkWatchers/packetCaptures/read | Pobierz definicję przechwytywania pakietów |
> | action | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Zatrzymania uruchomionej sesji przechwytywania pakietów. |
> | action | Microsoft.Network/networkWatchers/packetCaptures/write | Tworzy przechwytywania pakietów |
> | action | Microsoft.Network/networkWatchers/pingMeshes/delete | Usuwa PingMesh |
> | action | Microsoft.Network/networkWatchers/pingMeshes/read | Pobierz szczegóły PingMesh |
> | action | Microsoft.Network/networkWatchers/pingMeshes/start/action | Rozpocznij PingMesh między określonym maszyn wirtualnych |
> | action | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Zatrzymaj PingMesh między określonym maszyn wirtualnych |
> | action | Microsoft.Network/networkWatchers/pingMeshes/write | Tworzy PingMesh |
> | action | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Zapytanie usługi Batch, monitorowanie łączności między punktami końcowymi określonego |
> | action | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Pobiera stan przepływu logowania się do zasobu. |
> | action | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Pobiera wynik rozwiązywania problemów z poprzednio uruchomionymi lub obecnie uruchomiona operacja rozwiązywania problemów. |
> | action | Microsoft.Network/networkWatchers/read | Pobierz definicję obserwatora sieciowego |
> | action | Microsoft.Network/networkWatchers/securityGroupView/action | Wyświetl skonfigurowane i obowiązujące reguły sieciowych grup zabezpieczeń stosowane na maszynie Wirtualnej. |
> | action | Microsoft.Network/networkWatchers/topology/action | Pobiera widok poziomu sieci, zasobów i ich wzajemne relacje w grupie zasobów. |
> | action | Microsoft.Network/networkWatchers/troubleshoot/action | Uruchamia Rozwiązywanie problemów w zasobie sieci na platformie Azure. |
> | action | Microsoft.Network/networkWatchers/write | Usługi network watcher tworzy lub aktualizuje istniejące usługi network watcher |
> | action | Microsoft.Network/operations/read | Pobierz dostępne operacje |
> | action | Microsoft.Network/p2sVpnGateways/delete | Usuwa P2SVpnGateway. |
> | action | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generowanie profil sieci Vpn dla P2SVpnGateway |
> | action | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Pobiera P2SVpnGateway kondycji połączenia sieci VPN typu P2S |
> | action | Microsoft.Network/p2sVpnGateways/read | Pobiera P2SVpnGateway. |
> | action | Microsoft.Network/p2sVpnGateways/write | Umieszcza P2SVpnGateway. |
> | action | Microsoft.Network/privateEndpoints/delete | Usuwa zasób prywatnych punktów końcowych. |
> | action | Microsoft.Network/privateEndpoints/read | Pobiera zasób prywatnych punktów końcowych. |
> | action | Microsoft.Network/privateEndpoints/write | Tworzy nowy punkt końcowy prywatnej lub aktualizuje istniejące prywatnych punktów końcowych. |
> | action | Microsoft.Network/privateLinkServices/delete | Usuwa zasób usługi prywatnego linku. |
> | action | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Usuwa połączenie prywatnych punktów końcowych. |
> | action | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Pobiera definicji połączenia prywatnych punktów końcowych. |
> | action | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Tworzy nowe połączenie prywatnych punktów końcowych lub aktualizuje istniejące połączenie prywatnych punktów końcowych. |
> | action | Microsoft.Network/privateLinkServices/read | Pobiera zasób usługi prywatnego linku. |
> | action | Microsoft.Network/privateLinkServices/write | Tworzy nową usługę prywatnego linku lub aktualizuje istniejącą usługę prywatnego linku. |
> | action | Microsoft.Network/publicIPAddresses/delete | Usuwa publicznego adresu Ip. |
> | action | Microsoft.Network/publicIPAddresses/join/action | Dołącza do publicznego adresu ip. Nie dla. |
> | action | Microsoft.Network/publicIPAddresses/read | Pobiera definicji adres publiczny adres ip. |
> | action | Microsoft.Network/publicIPAddresses/write | Tworzy publiczny adres Ip lub aktualizuje istniejące publiczny adres Ip.  |
> | action | Microsoft.Network/publicIPPrefixes/delete | Usuwa publiczny prefiks adresu Ip |
> | action | Microsoft.Network/publicIPPrefixes/join/action | Tworzy sprzężenie PublicIPPrefix. Nie dla. |
> | action | Microsoft.Network/publicIPPrefixes/read | Pobiera definicji publiczny prefiks adresu Ip |
> | action | Microsoft.Network/publicIPPrefixes/write | Tworzy publiczny prefiks adresu Ip lub aktualizuje istniejące publiczny prefiks adresu Ip |
> | action | Microsoft.Network/register/action | Rejestruje subskrypcję |
> | action | Microsoft.Network/routeFilters/delete | Usuwa definicję filtru tras |
> | action | Microsoft.Network/routeFilters/join/action | Dołącza do filtru trasy. Nie dla. |
> | action | Microsoft.Network/routeFilters/read | Pobiera definicji filtru tras |
> | action | Microsoft.Network/routeFilters/routeFilterRules/delete | Usuwa definicję reguły filtru trasy |
> | action | Microsoft.Network/routeFilters/routeFilterRules/read | Pobiera definicji reguły filtru trasy |
> | action | Microsoft.Network/routeFilters/routeFilterRules/write | Tworzy reguły filtru trasy lub aktualizuje istniejącą regułę filtru trasy |
> | action | Microsoft.Network/routeFilters/write | Tworzy filtru tras lub aktualizuje istniejący filtr tras |
> | action | Microsoft.Network/routeTables/delete | Usuwa definicję tabeli tras |
> | action | Microsoft.Network/routeTables/join/action | Tworzy sprzężenie tabeli tras. Nie dla. |
> | action | Microsoft.Network/routeTables/read | Pobiera definicję tabeli tras |
> | action | Microsoft.Network/routeTables/routes/delete | Usuwa definicję trasy |
> | action | Microsoft.Network/routeTables/routes/read | Pobiera definicję trasy |
> | action | Microsoft.Network/routeTables/routes/write | Tworzy trasę lub aktualizuje istniejącą trasę |
> | action | Microsoft.Network/routeTables/write | Tworzy tabelę tras lub aktualizuje istniejącą tabelę tras |
> | action | Microsoft.Network/securegateways/delete | Usuwanie bramy zabezpieczeń |
> | action | Microsoft.Network/securegateways/read | Pobierz bramy zabezpieczeń |
> | action | Microsoft.Network/securegateways/write | Tworzy lub aktualizuje bramę zabezpieczeń |
> | action | Microsoft.Network/serviceEndpointPolicies/delete | Usuwa zasad punktów końcowych usługi |
> | action | Microsoft.Network/serviceEndpointPolicies/join/action | Dołącza do zasad punktów końcowych usługi. Nie dla. |
> | action | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Dołącza podsieci do zasad punktów końcowych usługi. Nie dla. |
> | action | Microsoft.Network/serviceEndpointPolicies/read | Pobiera opis zasad punktu końcowego usługi |
> | action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Usuwa definicją zasad punktów końcowych usługi |
> | action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Pobiera opis definicji zasad punktu końcowego usługi |
> | action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Tworzy definicją zasad punktów końcowych usługi lub aktualizuje istniejące definicją zasad punktów końcowych usługi |
> | action | Microsoft.Network/serviceEndpointPolicies/write | Tworzy zasad punktów końcowych usługi lub aktualizuje istniejące zasady punktu końcowego usługi |
> | action | Microsoft.Network/trafficManagerGeographicHierarchies/read | Pobiera hierarchia geograficzna usługi Traffic Manager zawierający regiony, do których mogą być używane z metody geograficznego routingu ruchu |
> | action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Usuwa punkt końcowy platformy Azure z istniejącego profilu usługi Traffic Manager. Usługa Traffic Manager przestanie routing ruchu na usunięto punkt końcowy platformy Azure. |
> | action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Pobiera punkt końcowy platformy Azure, która należy do profilu usługi Traffic Manager, w tym wszystkich właściwości tego punktu końcowego usługi Azure. |
> | action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Dodaj nowy punkt końcowy platformy Azure w istniejący profil usługi Traffic Manager, lub zaktualizuj właściwości istniejącego punktu końcowego platformy Azure, w tym profilu usługi Traffic Manager. |
> | action | Microsoft.Network/trafficManagerProfiles/delete | Usuń profil usługi Traffic Manager. Wszystkie ustawienia skojarzone z profilem usługi Traffic Manager zostaną utracone, a profil, który nie jest już może służyć do kierowania ruchu. |
> | action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Usuwa zewnętrzny punkt końcowy z istniejącego profilu usługi Traffic Manager. Usługa Traffic Manager przestanie routingu ruchu do usuniętego zewnętrzny punkt końcowy. |
> | action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Pobiera zewnętrzny punkt końcowy, który należy do profilu usługi Traffic Manager, w tym wszystkie właściwości zewnętrzny punkt końcowy. |
> | action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Dodawanie nowego zewnętrznego punktu końcowego w istniejący profil usługi Traffic Manager, lub zaktualizuj właściwości istniejącego zewnętrzny punkt końcowy w profilu usługi Traffic Manager. |
> | action | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Pobiera Mapa cieplna usługi Traffic Manager dla danego profilu usługi Traffic Manager, który zawiera dane liczby i czas oczekiwania zapytania według lokalizacji i źródłowych adresów IP. |
> | action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Usuwa punkt końcowy zagnieżdżonych z istniejącego profilu usługi Traffic Manager. Usługa Traffic Manager przestanie routing ruchu do usuniętego zagnieżdżone punktu końcowego. |
> | action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Pobiera zagnieżdżone punkt końcowy, który należy do profilu usługi Traffic Manager, w tym wszystkich właściwości tego punktu końcowego zagnieżdżonych. |
> | action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Dodaj nowy punkt końcowy zagnieżdżone w istniejący profil usługi Traffic Manager, lub zaktualizuj właściwości istniejącego zagnieżdżone punktu końcowego w tym profilu usługi Traffic Manager. |
> | action | Microsoft.Network/trafficManagerProfiles/read | Pobieranie konfiguracji profilu usługi Traffic Manager. W tym ustawienia DNS, ustawienia routingu ruchu, ustawienia monitorowania punktu końcowego i listy punktów końcowych kierowany przez ten profil usługi Traffic Manager. |
> | action | Microsoft.Network/trafficManagerProfiles/write | Tworzenie profilu usługi Traffic Manager, lub zmodyfikować konfigurację istniejącego profilu usługi Traffic Manager.<br>Obejmuje to włączenie lub wyłączenie profilu i modyfikowania ustawień DNS, ustawienia routingu ruchu lub ustawień monitorowania punktu końcowego.<br>Punkty końcowe kierowany przez profil usługi Traffic Manager może dodać, usunąć, włączona lub wyłączona. |
> | action | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Usuwa klucz poziom subskrypcji użytych na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | action | Microsoft.Network/trafficManagerUserMetricsKeys/read | Pobiera klucz poziom subskrypcji użytych na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | action | Microsoft.Network/trafficManagerUserMetricsKeys/write | Tworzy nowy klucz poziom subskrypcji, który ma być używany dla kolekcji metryki użytkowników w czasie rzeczywistym. |
> | action | Microsoft.Network/unregister/action | Wyrejestrowuje subskrypcję |
> | action | Microsoft.Network/virtualHubs/delete | Usuwa koncentrator wirtualny |
> | action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Usuwa HubVirtualNetworkConnection |
> | action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Utwórz lub zaktualizuj HubVirtualNetworkConnection |
> | action | Microsoft.Network/virtualHubs/read | Pobierz koncentrator wirtualny |
> | action | Microsoft.Network/virtualHubs/write | Utwórz lub zaktualizuj koncentrator wirtualny |
> | action | microsoft.network/virtualnetworkgateways/connections/read | Get VirtualNetworkGatewayConnection |
> | action | Microsoft.Network/virtualNetworkGateways/delete | Usuwa element virtualNetworkGateway |
> | action | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Generowanie pakietu klienta VPN dla bramy virtualNetworkGateway |
> | action | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Wygeneruj pakiet profilu VpnProfile dla bramy VirtualNetworkGateway |
> | action | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Pobiera virtualNetworkGateway anonsowane trasy |
> | action | Microsoft.Network/virtualnetworkgateways/getbgppeerstatus/Action | Pobiera stan elementu równorzędnego protokołu bgp bramy virtualNetworkGateway |
> | action | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Pobiera virtualnetworkgateway rozpoznane trasy |
> | action | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Get Per Vpn Client Connection Health for VirtualNetworkGateway |
> | action | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Uzyskać Ipsec klienta VPN parametrów VirtualNetworkGateway P2S klienta. |
> | action | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Pobiera adres URL pakietu profilu vpn wstępnie wygenerowanego klienta |
> | action | Microsoft.Network/virtualNetworkGateways/read | Gets a VirtualNetworkGateway |
> | action | microsoft.network/virtualnetworkgateways/reset/action | Resetuje element virtualNetworkGateway |
> | action | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Zresetuj klucz współużytkowany klienta VPN VirtualNetworkGateway P2S klienta. |
> | action | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Konfigurowanie klienta VPN Ipsec parametrów VirtualNetworkGateway P2S klienta. |
> | action | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Listy obsługiwanych urządzeń sieci Vpn |
> | action | Microsoft.Network/virtualNetworkGateways/write | Tworzy lub aktualizuje element VirtualNetworkGateway |
> | action | Microsoft.Network/virtualNetworks/BastionHosts/action | Pobiera hostem bastionu odwołań w sieci wirtualnej. |
> | action | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Pobiera hostem bastionu odwołań w sieci wirtualnej. |
> | action | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Sprawdź, czy adres Ip jest dostępne w określonej sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/delete | Usuwa sieć wirtualną |
> | action | Microsoft.Network/virtualNetworks/peer/action | Łączy równorzędnie sieć wirtualną z inną siecią wirtualną |
> | action | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/subnets/delete | Usuwa podsieć sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/subnets/join/action | Łączy sieci wirtualnej. Nie dla. |
> | action | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza do zasobów, takich jak konto magazynu lub bazy danych SQL do podsieci. Nie dla. |
> | action | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Przygotowuje podsieci, stosując niezbędne zasad sieciowych |
> | action | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicji podsieci sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w podsieci sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/subnets/write | Tworzy podsieci sieci wirtualnej lub aktualizuje istniejącą podsieć sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/usages/read | Pobierz użycia adresów IP dla każdej podsieci sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Usuwa wirtualnej sieci równorzędnej |
> | action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Pobiera definicję komunikacji równorzędnej sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Tworzy komunikację równorzędną sieci wirtualnej lub aktualizuje istniejące komunikacji równorzędnej sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworks/write | Tworzy sieć wirtualną lub aktualizuje istniejącą sieć wirtualną |
> | action | Microsoft.Network/virtualNetworkTaps/delete | Usuń naciśnij sieci wirtualnej |
> | action | Microsoft.Network/virtualNetworkTaps/join/action | Łączy to w ramach sieci wirtualnej. Nie dla. |
> | action | Microsoft.Network/virtualNetworkTaps/read | Get Virtual Network Tap |
> | action | Microsoft.Network/virtualNetworkTaps/write | Utwórz lub zaktualizuj naciśnij sieci wirtualnej |
> | action | Microsoft.Network/virtualWans/delete | Usuwa wirtualnej sieci Wan |
> | action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Usuwa wirtualnej P2SVpnServerConfiguration sieci Wan |
> | action | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Pobiera wirtualnego P2SVpnServerConfiguration sieci Wan |
> | action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Tworzy wirtualny P2SVpnServerConfiguration sieci Wan lub aktualizuje istniejący wirtualny P2SVpnServerConfiguration sieci Wan |
> | action | Microsoft.Network/virtualWans/read | Uzyskaj wirtualne sieci Wan |
> | action | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Pobiera obsługiwane VirtualWan dostawców zabezpieczeń. |
> | action | Microsoft.Network/virtualWans/virtualHubs/read | Pobiera wszystkie wirtualne centra odwołujące się do wirtualnej sieci Wan. |
> | action | Microsoft.Network/virtualwans/vpnconfiguration/action | Pobiera konfigurację sieci Vpn |
> | action | Microsoft.Network/virtualWans/vpnSites/read | Pobiera wszystkie lokacje sieci VPN, odwołujące się do wirtualnej sieci Wan. |
> | action | Microsoft.Network/virtualWans/write | Utwórz lub zaktualizuj wirtualne sieci Wan |
> | action | Microsoft.Network/vpnGateways/delete | Usuwa bramy VpnGateway. |
> | action | microsoft.network/vpngateways/listvpnconnectionshealth/action | Kondycja połączenia pobiera wszystkie lub podzbiór połączenia dla bramy VpnGateway |
> | action | Microsoft.Network/vpnGateways/read | Pobiera bramy VpnGateway. |
> | action | microsoft.network/vpngateways/reset/action | Resets a VpnGateway |
> | action | microsoft.network/vpnGateways/vpnConnections/delete | Deletes a VpnConnection. |
> | action | microsoft.network/vpnGateways/vpnConnections/read | Pobiera obiekt VpnConnection. |
> | action | microsoft.network/vpnGateways/vpnConnections/write | Puts a VpnConnection. |
> | action | Microsoft.Network/vpnGateways/write | Umieszcza bramy VpnGateway. |
> | action | Microsoft.Network/vpnsites/delete | Usuwa zasób lokacji sieci Vpn. |
> | action | Microsoft.Network/vpnsites/read | Pobiera zasób lokacji sieci Vpn. |
> | action | Microsoft.Network/vpnsites/write | Tworzy lub aktualizuje zasób lokacji sieci Vpn. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Sprawdza, czy dana nazwa zasobu przestrzeni nazw jest dostępna w usłudze NotificationHub. |
> | action | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | action | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć domyślnej reguły autoryzacji Namespace.  |
> | action | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia z przestrzenią nazw |
> | action | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | action | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Reguła autoryzacji przestrzeni nazw: wygeneruj ponownie klucz podstawowy/pomocniczy. Określ klucz do ponownego wygenerowania |
> | action | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie Namespace i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | action | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Sprawdza, czy dana nazwa usługi NotificationHub jest dostępna w przestrzeni nazw. |
> | action | Microsoft.NotificationHubs/Namespaces/Delete | Usuń zasób przestrzeni nazw |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Pobierz listę reguł autoryzacji centrum powiadomień |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Usuń reguły autoryzacji centrum powiadomień |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Pobierz parametry połączenia z centrum powiadomień |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Pobierz listę reguł autoryzacji centrum powiadomień |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Reguła autoryzacji centrum powiadomień: wygeneruj ponownie klucz główny/pomocniczy. Określ klucz do ponownego wygenerowania |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Utwórz reguły autoryzacji Centrum powiadomień i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Wyślij testowe powiadomienie wypychane. |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Usuń zasób centrum powiadomień |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Pobierz listę metryk Namespace opisów zasobów |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Pobierz wszystkie poświadczenia systemu powiadomień platformy Centrum powiadomień. Obejmuje to, poświadczenia usługi WNS, MPNS, APNS, GCM i Baidu |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Pobierz listę opisów zasobów centrum powiadomień |
> | action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Tworzenie Centrum powiadomień i zaktualizuj jego właściwości. Jego właściwości obejmują głównie poświadczenia systemu powiadomień platformy. Reguły autoryzacji i czas wygaśnięcia |
> | action | Microsoft.NotificationHubs/Namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | action | Microsoft.NotificationHubs/Namespaces/write | Tworzenie zasobu Namespace i zaktualizuj jego właściwości. Tagi i pojemność Namespace są właściwości, które mogą być aktualizowane. |
> | action | Microsoft.NotificationHubs/operationResults/read | Zwraca wyniki operacji dla dostawcy usługi Notification Hubs |
> | action | Microsoft.NotificationHubs/operations/read | Zwraca listę obsługiwanych operacji dla dostawcy usługi Notification Hubs |
> | action | Microsoft.NotificationHubs/register/action | Rejestruje subskrypcję dostawcy zasobów NotificationHubs i włącza funkcję tworzenia przestrzeni nazw i NotificationHubs |
> | action | Microsoft.NotificationHubs/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów NotificationHubs i włącza funkcję tworzenia przestrzeni nazw i NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.OffAzure/HyperVSites/clusters/read | Pobiera właściwości klastra funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/clusters/write | Tworzy lub aktualizuje klastra funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/delete | Usuwa lokacji funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/hosts/read | Pobiera właściwości hosta funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/hosts/write | Tworzy lub aktualizuje hosta funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/jobs/read | Pobiera właściwości zadania funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/machines/read | Pobiera właściwości maszyn Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/machines/start/action | Uruchom maszyny Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/machines/stop/action | Zatrzymuje maszyny Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Pobiera właściwości stan operacji dla funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/read | Pobiera właściwości lokacji funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/refresh/action | Odświeża obiektów w lokacji funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Pobiera właściwości konta Uruchom jako funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/usage/read | Pobiera użycia witryny funkcji Hyper-V |
> | action | Microsoft.OffAzure/HyperVSites/write | Tworzy lub aktualizuje lokacji funkcji Hyper-V |
> | action | Microsoft.OffAzure/Operations/read | Odczytuje ujawnione operacje |
> | action | Microsoft.OffAzure/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.OffAzure |
> | action | Microsoft.OffAzure/VMwareSites/delete | Usuwa lokacja programu VMware |
> | action | Microsoft.OffAzure/VMwareSites/jobs/read | Pobiera właściwości zadania programu VMware |
> | action | Microsoft.OffAzure/VMwareSites/machines/read | Pobiera właściwości maszyny VMware |
> | action | Microsoft.OffAzure/VMwareSites/machines/start/action | Uruchomienie maszyn VMware |
> | action | Microsoft.OffAzure/VMwareSites/machines/stop/action | Zatrzymuje maszyny VMware |
> | action | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Pobiera właściwości stan operacji dla oprogramowania VMware |
> | action | Microsoft.OffAzure/VMwareSites/read | Pobiera właściwości lokacji programu VMware |
> | action | Microsoft.OffAzure/VMwareSites/refresh/action | Odświeża obiektów w lokacji programu VMware |
> | action | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Pobiera właściwości konta Uruchom jako VMware |
> | action | Microsoft.OffAzure/VMwareSites/usage/read | Pobiera użycia lokacja programu VMware |
> | action | Microsoft.OffAzure/VMwareSites/vcenters/read | Pobiera właściwości VMware vCenter |
> | action | Microsoft.OffAzure/VMwareSites/vcenters/write | Tworzy lub aktualizuje VMware vCenter |
> | action | Microsoft.OffAzure/VMwareSites/write | Tworzy lub aktualizuje lokacja programu VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.OperationalInsights/linkTargets/read | Wyświetla listę istniejących kont, które nie są skojarzone z subskrypcją platformy Azure. Aby połączyć tej subskrypcji platformy Azure do obszaru roboczego, użyj identyfikatora klienta zwróconego przez tę operację we właściwości identyfikatora klienta operacji Tworzenie obszaru roboczego. |
> | action | Microsoft.operationalinsights/Operations/Read | Wyświetla listę wszystkich dostępnych operacji interfejsu API Rest OperationalInsights. |
> | action | Microsoft.operationalinsights/Register/Action | Rergisters subskrypcji. |
> | action | Microsoft.OperationalInsights/register/action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | action | Microsoft.operationalinsights/unregister/Action | Wyrejestrowuje subskrypcję. |
> | action | Microsoft.OperationalInsights/workspaces/analytics/query/action | Wyszukaj przy użyciu nowego aparatu. |
> | action | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Pobierz schemat wyszukiwania w wersji 2. |
> | action | Microsoft.OperationalInsights/workspaces/api/query/action | Wyszukaj przy użyciu nowego aparatu. |
> | action | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Pobierz schemat wyszukiwania w wersji 2. |
> | action | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Usuń zakres konfiguracji |
> | action | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Pobierz zakres konfiguracji |
> | action | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Ustaw zakres konfiguracji |
> | action | Microsoft.OperationalInsights/workspaces/datasources/delete | Usuń źródła danych w obszarze roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/datasources/read | Pobierz źródła danych w obszarze roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/datasources/write | Utwórz/zaktualizuj źródła danych w obszarze roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/delete | Usuwa obszar roboczy. Jeśli obszar roboczy został połączony z istniejącego obszaru roboczego w czasie tworzenia obszaru roboczego, który został połączony z nie zostanie usunięty. |
> | action | Microsoft.OperationalInsights/workspaces/gateways/delete | Usuwa bramę skonfigurowaną dla obszaru roboczego. |
> | action | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Generuje certyfikat rejestracji dla obszaru roboczego. Ten certyfikat służy do łączenia programu Microsoft System Center Operations Manager z obszarem roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Wyłącza pakiet intelligence pack dla danego obszaru roboczego. |
> | action | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Włącza pakiet intelligence pack dla danego obszaru roboczego. |
> | action | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Wyświetla listę wszystkich pakietów intelligence Pack, które są widoczne dla danego obszaru roboczego i zawiera również, czy pakiet jest włączony / wyłączony dla danego obszaru roboczego. |
> | action | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Usuń połączone usługi w podanym obszarze roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/linkedServices/read | Pobierz połączone usługi w podanym obszarze roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/linkedServices/write | Utwórz/zaktualizuj połączone usługi w podanym obszarze roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/listKeys/action | Pobiera klucze listy dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/listKeys/read | Pobiera klucze listy dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/managementGroups/read | Pobiera nazwy i metadane dla grupy zarządzania programu System Center Operations Manager połączony z tym obszarem roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Pobierz definicje metryki w obszarze roboczym |
> | action | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Usuń ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | action | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Pobierz ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | action | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Ustaw ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | action | Microsoft.operationalinsights/workspaces/Operations/Read | Pobiera stan operacji obszaru roboczego OperationalInsights. |
> | action | Microsoft.OperationalInsights/workspaces/purge/action | Usuń określone dane z obszaru roboczego |
> | action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Odczyt danych z tabeli AADDomainServicesAccountLogon |
> | action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Odczyt danych z tabeli AADDomainServicesLogonLogoff |
> | action | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Odczytaj dane z tabeli ADReplicationResult |
> | action | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/Alert/read | Odczytaj dane z tabeli Alert |
> | action | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Odczytaj dane z tabeli AlertHistory |
> | action | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Odczyt danych z tabeli AppCenterError |
> | action | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Odczytaj dane z tabeli ApplicationInsights |
> | action | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Odczyt danych z tabeli AuditLogs |
> | action | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Odczyt danych z tabeli AutoscaleEvaluationsLog |
> | action | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Odczyt danych z tabeli AutoscaleScaleActionsLog |
> | action | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Odczyt danych z tabeli AWSCloudTrail |
> | action | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Odczytaj dane z tabeli AzureActivity |
> | action | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Odczytaj dane z tabeli AzureMetrics |
> | action | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Odczytaj dane z tabeli BoundPort |
> | action | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Odczytaj dane z tabeli CommonSecurityLog |
> | action | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Odczytaj dane z tabeli ComputerGroup |
> | action | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Odczytaj dane z tabeli ConfigurationChange |
> | action | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Odczytaj dane z tabeli ConfigurationData |
> | action | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Odczytaj dane z tabeli ContainerImageInventory |
> | action | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Odczytaj dane z tabeli ContainerInventory |
> | action | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Odczytaj dane z tabeli ContainerLog |
> | action | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Odczyt danych z tabeli ContainerNodeInventory |
> | action | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Odczytaj dane z tabeli ContainerServiceLog |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Odczyt danych z tabeli DatabricksAccounts |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Odczyt danych z tabeli DatabricksClusters |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Odczyt danych z tabeli DatabricksDBFS |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Odczyt danych z tabeli DatabricksJobs |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Odczyt danych z tabeli DatabricksNotebook |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Odczyt danych z tabeli DatabricksSecrets |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Odczyt danych z tabeli DatabricksSQLPermissions |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Odczyt danych z tabeli DatabricksSSH |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Odczyt danych z tabeli DatabricksTables |
> | action | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Odczyt danych z tabeli DatabricksWorkspace |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Odczytaj dane z tabeli DeviceAppCrash |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Odczytaj dane z tabeli DeviceAppLaunch |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Odczytaj dane z tabeli DeviceCalendar |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Odczytaj dane z tabeli DeviceCleanup |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Odczytaj dane z tabeli DeviceConnectSession |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Odczytaj dane z tabeli DeviceEtw |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Odczytaj dane z tabeli DeviceHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Odczytaj dane z tabeli DeviceHeartbeat |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | action | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Odczytaj dane z tabeli DeviceSleepState |
> | action | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Odczytaj dane z tabeli DHAppFailure |
> | action | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Odczytaj dane z tabeli DHAppReliability |
> | action | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Odczytaj dane z tabeli DHDriverReliability |
> | action | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Odczytaj dane z tabeli DHLogonFailures |
> | action | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Odczytaj dane z tabeli DHLogonMetrics |
> | action | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Odczytaj dane z tabeli DHOSCrashData |
> | action | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Odczytaj dane z tabeli DHOSReliability |
> | action | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Odczytaj dane z tabeli DHWipAppLearning |
> | action | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Odczytaj dane z tabeli DnsEvents |
> | action | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Odczytaj dane z tabeli DnsInventory |
> | action | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Odczytaj dane z tabeli ETWEvent |
> | action | Microsoft.OperationalInsights/workspaces/query/Event/read | Odczytaj dane z tabeli Event |
> | action | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Odczytaj dane z tabeli Heartbeat |
> | action | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Odczyt danych z tabeli HuntingBookmark |
> | action | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Odczytaj dane z tabeli InboundConnection |
> | action | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Odczyt danych z tabeli InsightsMetrics |
> | action | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Odczyt danych z tabeli IntuneAuditLogs |
> | action | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Odczyt danych z tabeli IntuneOperationalLogs |
> | action | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Odczyt danych z tabeli KubeEvents |
> | action | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Odczytaj dane z tabeli KubeNodeInventory |
> | action | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Odczytaj dane z tabeli KubePodInventory |
> | action | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Odczyt danych z tabeli KubeServices |
> | action | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Odczytaj dane z tabeli LinuxAuditLog |
> | action | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Odczytaj dane z tabeli MAApplication |
> | action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Odczytaj dane z tabeli MAApplicationHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Odczytaj dane z tabeli MAApplicationInstance |
> | action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Odczytaj dane z tabeli MAApplicationReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Odczytaj dane z tabeli MADeploymentPlan |
> | action | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Odczytaj dane z tabeli MADevice |
> | action | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Odczyt danych z tabeli MADeviceNotEnrolled |
> | action | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Odczyt danych z tabeli MADeviceNRT |
> | action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Odczytaj dane z tabeli MADevicePnPHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | action | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Odczytaj dane z tabeli MADeviceReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Odczytaj dane z tabeli MADriverReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Odczytaj dane z tabeli MAOfficeAddin |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Odczyt danych z tabeli MAOfficeAddinEntityHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Odczytaj dane z tabeli MAOfficeAddinHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Odczyt danych z tabeli MAOfficeAddinHealthEventNRT |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Odczytaj dane z tabeli MAOfficeAddinHealthIssues |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Odczytaj dane z tabeli MAOfficeAddinInstance |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Odczytaj dane z tabeli MAOfficeAddinInstanceReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Odczytaj dane z tabeli MAOfficeAddinReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Odczytaj dane z tabeli MAOfficeApp |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Odczyt danych z tabeli MAOfficeAppCrashesNRT |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Odczytaj dane z tabeli MAOfficeAppHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Odczytaj dane z tabeli MAOfficeAppInstance |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Odczyt danych z tabeli MAOfficeAppInstanceHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Odczytaj dane z tabeli MAOfficeAppReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Odczyt danych z tabeli MAOfficeAppSessionsNRT |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Odczytaj dane z tabeli MAOfficeBuildInfo |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Odczytaj dane z tabeli MAOfficeCurrencyAssessment |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Odczytaj dane z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Odczyt danych z tabeli MAOfficeDeploymentStatusNRT |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Odczyt danych z tabeli MAOfficeMacroErrorNRT |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Odczyt danych z tabeli MAOfficeMacroGlobalHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Odczytaj dane z tabeli MAOfficeSuite |
> | action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | action | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | action | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Odczyt danych z tabeli MAWindowsDeploymentStatusNRT |
> | action | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebW3CLog/read | Odczyt danych z tabeli MicrosoftWebW3CLog |
> | action | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Odczytaj dane z tabeli NetworkMonitoring |
> | action | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Odczytaj dane z tabeli OfficeActivity |
> | action | Microsoft.OperationalInsights/workspaces/query/Operation/read | Odczytaj dane z tabeli Operation |
> | action | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Odczytaj dane z tabeli OutboundConnection |
> | action | Microsoft.OperationalInsights/workspaces/query/Perf/read | Odczytaj dane z tabeli Perf |
> | action | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Odczytaj dane z tabeli ProtectionStatus |
> | action | Microsoft.OperationalInsights/workspaces/query/read | Uruchamianie zapytań względem danych w obszarze roboczym |
> | action | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | action | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Odczytaj dane z tabeli ReservedCommonFields |
> | action | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Odczytaj dane z tabeli SecurityAlert |
> | action | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Odczytaj dane z tabeli SecurityBaseline |
> | action | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | action | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Odczytaj dane z tabeli SecurityDetection |
> | action | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Odczytaj dane z tabeli SecurityEvent |
> | action | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Odczyt danych z tabeli SecurityIoTRawEvent |
> | action | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Odczyt danych z tabeli SecurityRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | action | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Odczyt danych z tabeli SigninLogs |
> | action | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Odczytaj dane z tabeli SQLQueryPerformance |
> | action | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Odczyt danych z tabeli SqlThreatProtectionLoginAudits |
> | action | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Odczyt danych z tabeli SqlVulnerabilityAssessmentResult |
> | action | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Odczytaj dane z tabeli Syslog |
> | action | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Odczytaj dane z tabeli SysmonEvent |
> | action | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | action | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Odczyt danych z tabeli ThreatIntelligenceIndicator |
> | action | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Odczytaj dane z tabeli UAApp |
> | action | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Odczytaj dane z tabeli UAComputer |
> | action | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Odczytaj dane z tabeli UAComputerRank |
> | action | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Odczytaj dane z tabeli UADriver |
> | action | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Odczytaj dane z tabeli UADriverProblemCodes |
> | action | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Odczytaj dane z tabeli UAFeedback |
> | action | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Odczytaj dane z tabeli UAHardwareSecurity |
> | action | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | action | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Odczytaj dane z tabeli UAOfficeAddIn |
> | action | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Odczytaj dane z tabeli UAProposedActionPlan |
> | action | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Odczytaj dane z tabeli UASysReqIssue |
> | action | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Odczytaj dane z tabeli UAUpgradedComputer |
> | action | Microsoft.OperationalInsights/workspaces/query/Update/read | Odczytaj dane z tabeli Update |
> | action | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Odczytaj dane z tabeli UpdateRunProgress |
> | action | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Odczytaj dane z tabeli UpdateSummary |
> | action | Microsoft.OperationalInsights/workspaces/query/Usage/read | Odczytaj dane z tabeli Usage |
> | action | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Odczyt danych z tabeli VMBoundPort |
> | action | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Odczyt danych z tabeli VMConnection |
> | action | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Odczytaj dane z tabeli W3CIISLog |
> | action | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | action | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | action | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | action | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Odczytaj dane z tabeli WDAVStatus |
> | action | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Odczytaj dane z tabeli WDAVThreat |
> | action | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Odczyt danych z tabeli WindowsEvent |
> | action | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Odczytaj dane z tabeli WindowsFirewall |
> | action | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | action | Microsoft.OperationalInsights/workspaces/query/WireData/read | Odczytaj dane z tabeli WireData |
> | action | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Odczyt danych z tabeli WorkloadMonitoringPerf |
> | action | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | action | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Odczytaj dane z tabeli WUDOStatus |
> | action | Microsoft.OperationalInsights/workspaces/read | Pobiera istniejący obszar roboczy |
> | action | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Ponownie generuje klucz współużytkowany określony obszar roboczy |
> | action | microsoft.operationalinsights/workspaces/rules/read | Pobieranie wszystkich reguł alertów. |
> | action | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Usuwa zapisane zapytanie wyszukiwania |
> | action | Microsoft.OperationalInsights/workspaces/savedSearches/read | Pobiera zapisane zapytanie wyszukiwania |
> | action | microsoft.operationalinsights/workspaces/savedsearches/results/read | Pobierz zapisać wyniki wyszukiwania. Przestarzałe |
> | action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Usuwanie zaplanowanego wyszukiwania akcji. |
> | action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Pobierz akcje wyszukiwania według harmonogramu. |
> | action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Utwórz lub zaktualizuj operacje wyszukiwania według harmonogramu. |
> | action | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Usuwanie zaplanowanych wyszukiwań. |
> | action | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Uzyskaj zaplanowanych wyszukiwań. |
> | action | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Utwórz lub zaktualizuj zaplanowanych wyszukiwań. |
> | action | Microsoft.OperationalInsights/workspaces/savedSearches/write | Tworzy zapytanie zapisanego wyszukiwania |
> | action | Microsoft.OperationalInsights/workspaces/schema/read | Pobiera schemat wyszukiwania dla obszaru roboczego.  Schemat wyszukiwania zawiera widoczne pola i ich typy. |
> | action | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | action | microsoft.operationalinsights/workspaces/search/read | Uzyskiwanie wyników wyszukiwania. Przestarzałe. |
> | action | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Pobiera klucze wspólne dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Pobiera klucze wspólne dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Usuwa konfigurację magazynu. Spowoduje to zatrzymanie usługi Microsoft Operational Insights na podstawie odczytu danych z konta magazynu. |
> | action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Pobiera konfigurację magazynu. |
> | action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Tworzy nową konfigurację magazynu. Te konfiguracje są używane do pobierania danych z lokalizacji na istniejące konto magazynu. |
> | action | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Pobierz dziennik błędów tłumaczenia uaktualnienia wyszukiwania dla obszaru roboczego |
> | action | Microsoft.OperationalInsights/workspaces/usages/read | Pobiera dane użycia dla obszaru roboczego, w tym ilość danych odczytanych przez obszar roboczy. |
> | action | Microsoft.OperationalInsights/workspaces/write | Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącym obszarem roboczym. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.OperationsManagement/managementAssociations/delete | Usuń istniejące skojarzenie zarządzania |
> | action | Microsoft.OperationsManagement/managementAssociations/read | Pobieranie istniejącego skojarzenia zarządzania |
> | action | Microsoft.OperationsManagement/managementAssociations/write | Utwórz nowe skojarzenie zarządzania |
> | action | Microsoft.OperationsManagement/managementConfigurations/delete | Usuń istniejącą konfigurację zarządzania |
> | action | Microsoft.OperationsManagement/managementConfigurations/read | Pobieranie istniejącej konfiguracji zarządzania |
> | action | Microsoft.OperationsManagement/managementConfigurations/write | Utwórz nową konfigurację zarządzania |
> | action | Microsoft.OperationsManagement/register/action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | action | Microsoft.OperationsManagement/solutions/delete | Usuń istniejące rozwiązanie OMS |
> | action | Microsoft.OperationsManagement/solutions/read | Pobierz istniejące rozwiązanie OMS |
> | action | Microsoft.OperationsManagement/solutions/write | Utwórz nowe rozwiązanie OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.PolicyInsights/asyncOperationResults/read | Pobiera wynik operacji asynchronicznej. |
> | action | Microsoft.PolicyInsights/operations/read | Pobiera obsługiwane operacje na Microsoft.PolicyInsights w przestrzeni nazw |
> | action | Microsoft.PolicyInsights/policyEvents/queryResults/action | Wykonaj zapytanie o informacje dotyczące zdarzeń zasad. |
> | action | Microsoft.PolicyInsights/policyEvents/queryResults/read | Wykonaj zapytanie o informacje dotyczące zdarzeń zasad. |
> | action | Microsoft.PolicyInsights/policyStates/queryResults/action | Wykonaj zapytanie o informacje dotyczące stanów zasad. |
> | action | Microsoft.PolicyInsights/policyStates/queryResults/read | Wykonaj zapytanie o informacje dotyczące stanów zasad. |
> | action | Microsoft.PolicyInsights/policyStates/summarize/action | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych stanów zasad. |
> | action | Microsoft.PolicyInsights/policyStates/summarize/read | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych stanów zasad. |
> | action | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Wyzwala nową ocenę zgodności dla wybranego zakresu. |
> | action | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Wykonaj zapytania o informacje na temat zasobów wymaganych przez zasady DeployIfNotExists. |
> | action | Microsoft.PolicyInsights/register/action | Rejestruje dostawcę zasobów szczegółowych informacji o zasadach firmy Microsoft i umożliwia operacje na nim. |
> | action | Microsoft.PolicyInsights/remediations/cancel/action | Anuluj korygowania funkcję Microsoft Policy w toku. |
> | action | Microsoft.PolicyInsights/remediations/delete | Usuń korygowania zasad. |
> | action | Microsoft.PolicyInsights/remediations/listDeployments/read | Wyświetla listę wdrożeń wymaganych przez korygowanie zasad. |
> | action | Microsoft.PolicyInsights/remediations/read | Pobierz korygowania zasad. |
> | action | Microsoft.PolicyInsights/remediations/write | Utwórz lub zaktualizuj korygowania Microsoft Policy funkcję. |
> | action | Microsoft.PolicyInsights/unregister/action | Wyrejestrowuje dostawcę zasobów szczegółowych informacji o zasadach firmy Microsoft. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Portal/consoles/delete | Usuwa wystąpienie usługi Cloud Shell. |
> | action | Microsoft.Portal/consoles/read | Odczytuje wystąpienie usługi Cloud Shell. |
> | action | Microsoft.Portal/consoles/write | Utwórz lub zaktualizuj wystąpienie usługi Cloud Shell. |
> | action | Microsoft.Portal/dashboards/delete | Usuwa pulpit nawigacyjny z subskrypcji. |
> | action | Microsoft.Portal/dashboards/read | Odczytuje pulpity nawigacyjne z subskrypcji. |
> | action | Microsoft.Portal/dashboards/write | Dodaj pulpit nawigacyjny do subskrypcji lub zmodyfikuj go. |
> | action | Microsoft.Portal/register/action | Zarejestruj w portalu |
> | action | Microsoft.Portal/usersettings/delete | Usuwa ustawienia użytkownika usługi Cloud Shell. |
> | action | Microsoft.Portal/usersettings/read | Odczytuje ustawienia użytkownika usługi Cloud Shell. |
> | action | Microsoft.Portal/usersettings/write | Utwórz lub zaktualizuj ustawienie użytkownika usługi Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.PowerBIDedicated/capacities/delete | Usuwa usługi Power BI w wersji dedykowanej pojemności. |
> | action | Microsoft.PowerBIDedicated/capacities/read | Pobiera informacje o określonej pojemności usługi Power BI w wersji dedykowanej. |
> | action | Microsoft.PowerBIDedicated/capacities/resume/action | Wznawia pojemności. |
> | action | Microsoft.PowerBIDedicated/capacities/skus/read | Pobierz dostępne informacje o jednostce SKU pojemności |
> | action | Microsoft.PowerBIDedicated/capacities/suspend/action | Wstrzymuje działanie pojemności. |
> | action | Microsoft.PowerBIDedicated/capacities/write | Tworzy lub aktualizuje określoną w wersji dedykowanej pojemności usługi Power BI. |
> | action | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Sprawdza, czy nazwa podana w wersji dedykowanej pojemności usługi Power BI jest prawidłowa i nie jest używany. |
> | action | Microsoft.PowerBIDedicated/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | action | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | action | Microsoft.PowerBIDedicated/operations/read | Pobiera informacje o operacjach |
> | action | Microsoft.PowerBIDedicated/register/action | Rejestruje dostawcę zasobów usługi Power BI w wersji dedykowanej. |
> | action | Microsoft.PowerBIDedicated/skus/read | Pobiera informacje o jednostkach SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | action | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest wewnętrzną operacją używaną przez usługę |
> | action | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | action | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Utwórz chroniony element kopii zapasowej |
> | action | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | action | Microsoft.RecoveryServices/Locations/backupStatus/action | Sprawdź stan kopii zapasowej dla magazynów usługi Recovery Services |
> | action | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Weryfikuj funkcje |
> | action | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Sprawdź dostępność nazwy zasobu to interfejs API, aby sprawdzić, czy nazwa zasobu jest dostępna |
> | action | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | action | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | action | Microsoft.RecoveryServices/register/action | Rejestry subskrypcji dla podanego dostawcy zasobów |
> | action | Microsoft.RecoveryServices/Vaults/backupconfig/read | Zwraca konfigurację dla odzyskiwania usług magazynu. |
> | action | Microsoft.RecoveryServices/Vaults/backupconfig/write | Aktualizuje konfigurację odzyskiwania usług magazynu. |
> | action | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Usuń opcję ochrony kopii zapasowej |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Pobierz opcję ochrony kopii zapasowej |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Utwórz opcję ochrony kopii zapasowej |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery z możliwością objęcia ochroną |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Usuwa zarejestrowany kontener |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Wykonuje kopię zapasową elementu chronionego. |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Usuwa chroniony element |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Ustanów natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Przywróć punkty odzyskiwania dla elementów chronionych. |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Wycofaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Utwórz chroniony element kopii zapasowej |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Tworzy zarejestrowany kontener |
> | action | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | action | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Anuluj zadanie |
> | action | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zadania. |
> | action | Microsoft.RecoveryServices/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadań |
> | action | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportuj zadania |
> | action | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Zwraca wynik operacji tworzenia kopii zapasowej magazynu usług Recovery Services. |
> | action | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca operacji tworzenia kopii zapasowej odzyskiwania stanu usługi magazynu. |
> | action | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Usuwa zasady ochrony |
> | action | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | action | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | action | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | action | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Tworzy zasady ochrony |
> | action | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Zwraca listę wszystkich elementów podlegających ochronie |
> | action | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | action | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | action | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listę wszystkich kopii zapasowych opcjami ochrony |
> | action | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Zabezpieczający numer PIN zwraca informacje dotyczące odzyskiwania usług magazynu. |
> | action | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Zwraca konfigurację magazynu dla odzyskiwania usług magazynu. |
> | action | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Aktualizuje konfigurację magazynu dla odzyskiwania usług magazynu. |
> | action | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dotyczące chronionych elementów i serwerów chronionych usług Recovery Services. |
> | action | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Weryfikowanie operacji chronionego elementu |
> | action | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | action | Microsoft.RecoveryServices/Vaults/delete | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "Magazyn" |
> | action | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | action | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | action | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery services. |
> | action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Pobiera konfigurację powiadomień magazynu usługi Recovery services. |
> | action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfiguruje powiadomienia pocztą e-mail do magazynu usługi Recovery services. |
> | action | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | action | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Operacja Wyrejestruj kontener umożliwia wyrejestrowanie kontenera. |
> | action | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyników operacji przesłanej asynchronicznie |
> | action | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Pobierz kontenery operacji umożliwia pobranie kontenerów zarejestrowanych dla zasobu. |
> | action | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operacja Rejestruj kontener usługi umożliwia rejestrowanie kontenera za pomocą usługi odzyskiwania. |
> | action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Odczytuje żadnych ustawień alertów |
> | action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Utwórz lub zaktualizuj wszelkie ustawienia alertów |
> | action | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczyt zdarzeń |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność sieci szkieletowej |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Usuń wszystkie sieci szkieletowe |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Wdróż obraz serwera przetwarzania |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrowanie sieci szkieletowej do usługi AAD |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Przeczytaj żadnych sieci szkieletowych |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skojarz ponownie bramę |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Usuń sieć szkieletową |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Odnów certyfikat sieci szkieletowej |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Przeczytaj sieci logicznych |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Odczyt wszystkich sieci |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Usuń wszystkie mapowania sieci |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytywać wszystkie mapowania sieci |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Utwórz lub zaktualizuj wszelkie mapowania sieci |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Odnajdywanie elementów podlegających ochronie |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytywać wszystkie kontenery ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Usuń kontener ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Usuń wszystkie elementy migracji |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrowanie elementu |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Odczytywać wszystkie punkty odzyskiwania migracji |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Odczytuj wszystkie elementy migracji |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Migrowanie testów |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Test Migrowanie oczyszczania |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Utwórz lub zaktualizuj wszystkie elementy migracji |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczyt wszystkich elementów podlegających ochronie |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Dodawanie dysków |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Zastosuj punkt odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Usuń wszystkie chronione elementy |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover Commit |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowane przełączenie w tryb failover |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczyt wszystkich chronionych elementów |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytywać wszystkie punkty odzyskiwania replikacji |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Usuń chroniony element |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Usuwanie dysków |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Napraw replikację |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Włącz ponownie ochronę chronionego elementu |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Prześlij opinię |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Przeczytaj dowolnej docelowej rozmiarów wystąpień obliczeniowych |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test pracy w trybie failover |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Czyszczenie testowego przełączania do trybu Failover |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizacja usługi mobilności |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Utwórz lub zaktualizuj wszystkie chronione elementy |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Usuń wszystkie mapowania kontenera ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytywać wszystkie mapowania kontenera ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Usuń mapowanie kontenera ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Utwórz lub zaktualizuj wszelkie mapowania kontenera ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Przełącz kontener ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Utwórz lub zaktualizuj wszelkie kontenery ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Usuwanie wszystkich dostawców usług odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczyt wszystkich dostawców usług odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Odśwież dostawcę |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Usuń dostawcę usługi Recovery Services |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Utwórz lub zaktualizuj wszelkie dostawców usług odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytywać wszystkie klasyfikacje magazynów |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Usuń wszystkie mapowania klasyfikacji magazynów |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytywać wszystkie mapowania klasyfikacji magazynów |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Utwórz lub zaktualizuj wszelkie mapowania klasyfikacji magazynów |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Usuń wszelkie vCenters |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Przeczytaj wszelkie vCenters |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Utwórz lub zaktualizuj wszelkie vCenters |
> | action | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Utwórz lub zaktualizuj wszystkie sieci szkieletowe |
> | action | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Anuluj zadanie |
> | action | Microsoft.RecoveryServices/vaults/replicationJobs/read | Odczytać wszystkie zadania |
> | action | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Uruchom ponownie zadanie |
> | action | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Wznów zadanie |
> | action | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Odczytuj wszystkie elementy migracji |
> | action | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Odczytywać wszystkie mapowania sieci |
> | action | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Odczyt wszystkich sieci |
> | action | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Usuń wszystkie zasady |
> | action | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Odczytywać wszystkie zasady |
> | action | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Utwórz lub zaktualizuj wszystkie zasady |
> | action | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Odczyt wszystkich chronionych elementów |
> | action | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Odczytywać wszystkie mapowania kontenera ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Odczytywać wszystkie kontenery ochrony |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Usuń wszelkie plany odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan odzyskiwania zatwierdzania trybu failover |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan planowanego trybu Failover odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Odczyt wszystkich planów odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Ponownie włączyć ochronę planu odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan odzyskiwania do trybu Failover testu |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan odzyskiwania systemu testowego przełączania w tryb Failover |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania do trybu failover |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Utwórz lub zaktualizuj wszelkie plany odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Odczyt wszystkich dostawców usług odzyskiwania |
> | action | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Odczytywać wszystkie mapowania klasyfikacji magazynów |
> | action | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Odczytywać wszystkie klasyfikacje magazynów |
> | action | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Odczytać dowolny  |
> | action | Microsoft.RecoveryServices/vaults/replicationUsages/read | Przeczytaj wszelkie użycia replikacji magazynu |
> | action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Przeczytaj wszelkie kondycję replikacji magazynu |
> | action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Odśwież kondycję magazynu |
> | action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Odczytać dowolny  |
> | action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Utwórz lub zaktualizuj dowolny  |
> | action | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Przeczytaj wszelkie vCenters |
> | action | Microsoft.RecoveryServices/vaults/usages/read | Przeczytaj wszelkie użycia magazynu |
> | action | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | action | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja Token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | action | Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Relay/checkNameAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | action | Microsoft.Relay/checkNamespaceAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailability. |
> | action | Microsoft.Relay/namespaces/authorizationRules/action | Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest przestarzały. Można zaktualizować reguły autoryzacji Namespace zamiast niego użyj wywołania PUT... Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | action | Microsoft.Relay/namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć domyślnej reguły autoryzacji Namespace.  |
> | action | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia z przestrzenią nazw |
> | action | Microsoft.Relay/namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | action | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | action | Microsoft.Relay/namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie Namespace i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | action | Microsoft.Relay/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobierz klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | action | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. Tę operację można wywołać tylko za pomocą podstawowej przestrzeni nazw. |
> | action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje tryb pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operacja aktualizowania HybridConnection. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operacja usuwania reguł autoryzacji HybridConnection |
> | action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Pobierz parametry połączenia HybridConnection |
> | action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Pobierz listę reguł autoryzacji HybridConnection |
> | action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Utwórz reguły autoryzacji HybridConnection i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | action | Microsoft.Relay/namespaces/HybridConnections/Delete | Operacja usuwania zasobu HybridConnection |
> | action | Microsoft.Relay/namespaces/HybridConnections/read | Pobierz listę opisów zasobów HybridConnection |
> | action | Microsoft.Relay/namespaces/HybridConnections/write | Tworzenie lub aktualizacja HybridConnection właściwości. |
> | action | Microsoft.Relay/namespaces/messagingPlan/read | Pobiera Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | action | Microsoft.Relay/namespaces/messagingPlan/write | Aktualizuje Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | action | Microsoft.Relay/namespaces/operationresults/read | Pobierz stan operacji przestrzeni nazw |
> | action | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryk Namespace opisów zasobów |
> | action | Microsoft.Relay/namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | action | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw usługi ACS |
> | action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operacja aktualizowania WcfRelay. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operacja usuwania reguł autoryzacji WcfRelay |
> | action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Pobierz parametry połączenia WcfRelay |
> | action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Pobierz listę reguł autoryzacji WcfRelay |
> | action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Utwórz reguły autoryzacji WcfRelay i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | action | Microsoft.Relay/namespaces/WcfRelays/Delete | Operacja usuwania zasobu WcfRelay |
> | action | Microsoft.Relay/namespaces/WcfRelays/read | Pobierz listę opisów zasobów WcfRelay |
> | action | Microsoft.Relay/namespaces/WcfRelays/write | Tworzenie lub aktualizacja WcfRelay właściwości. |
> | action | Microsoft.Relay/namespaces/write | Tworzenie zasobu Namespace i zaktualizuj jego właściwości. Tagi i pojemność Namespace są właściwości, które mogą być aktualizowane. |
> | action | Microsoft.Relay/operations/read | Pobierz operacje |
> | action | Microsoft.Relay/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i włącza funkcję tworzenia zasobów usługi Relay |
> | action | Microsoft.Relay/unregister/action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i włącza funkcję tworzenia zasobów usługi Relay |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Pobiera stan dostępności określonego zasobu |
> | action | Microsoft.ResourceHealth/AvailabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | action | Microsoft.ResourceHealth/events/read | Pobierz zdarzenia usługi Service Health dla danej subskrypcji |
> | action | Microsoft.Resourcehealth/healthevent/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | action | Microsoft.Resourcehealth/healthevent/Activated/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | action | Microsoft.Resourcehealth/healthevent/InProgress/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | action | Microsoft.Resourcehealth/healthevent/Pending/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | action | Microsoft.Resourcehealth/healthevent/Resolved/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | action | Microsoft.Resourcehealth/healthevent/Updated/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | action | Microsoft.ResourceHealth/impactedResources/read | Pobierz zasoby objęte wpływem dla danej subskrypcji |
> | action | Microsoft.ResourceHealth/Operations/read | Pobierz operacje dostępne dla usługi Microsoft ResourceHealth |
> | action | Microsoft.ResourceHealth/register/action | Rejestruje subskrypcję na potrzeby usługi Microsoft Resource Health |
> | action | Microsoft.ResourceHealth/unregister/action | Wyrejestrowuje subskrypcję z usługi Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Resources/checkPolicyCompliance/action | Sprawdź stan zgodności danego zasobu wobec zasad zasobów. |
> | action | Microsoft.Resources/checkResourceName/action | Sprawdź poprawność nazwy zasobu. |
> | action | Microsoft.Resources/deployments/cancel/action | Anuluje wdrożenie. |
> | action | Microsoft.Resources/deployments/delete | Usuwa wdrożenie. |
> | action | Microsoft.Resources/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
> | action | Microsoft.Resources/deployments/read | Pobiera wdrożenia lub wyświetla ich listę. |
> | action | Microsoft.Resources/deployments/validate/action | Weryfikuje wdrożenie. |
> | action | Microsoft.Resources/deployments/whatIf/action | Przewiduje zmiany dotyczące wdrożenia szablonu. |
> | action | Microsoft.Resources/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | action | Microsoft.Resources/links/delete | Usuwa link do zasobu. |
> | action | Microsoft.Resources/links/read | Pobiera linki do zasobów lub wyświetla ich listę. |
> | action | Microsoft.Resources/links/write | Tworzy lub aktualizuje link do zasobu. |
> | action | Microsoft.Resources/marketplace/purchase/action | Pozwala kupić zasób w portalu Marketplace. |
> | action | Microsoft.Resources/providers/read | Pobiera listę dostawców. |
> | action | Microsoft.Resources/resources/read | Pobiera listę zasobów zgodnie z filtrami. |
> | action | Microsoft.Resources/subscriptions/locations/read | Pobiera listę obsługiwanych lokalizacji. |
> | action | Microsoft.Resources/subscriptions/operationresults/read | Pobiera wyniki operacji subskrypcji. |
> | action | Microsoft.Resources/subscriptions/providers/read | Pobiera dostawców zasobów lub wyświetla ich listę. |
> | action | Microsoft.Resources/subscriptions/read | Pobiera listę subskrypcji. |
> | action | Microsoft.Resources/subscriptions/resourceGroups/delete | Usuwa grupę zasobów i wszystkie zasoby w tej grupie. |
> | action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
> | action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Pobiera stany operacji wdrażania lub wyświetla ich listę. |
> | action | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Pobiera wdrożenia lub wyświetla ich listę. |
> | action | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | action | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Przenosi zasoby z jednej grupy zasobów do innej. |
> | action | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | action | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Pobiera zasoby grupy zasobów. |
> | action | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Waliduj przeniesienie zasobów między grupami zasobów. |
> | action | Microsoft.Resources/subscriptions/resourceGroups/write | Tworzy lub aktualizuje grupę zasobów. |
> | action | Microsoft.Resources/subscriptions/resources/read | Pobiera zasoby subskrypcji. |
> | action | Microsoft.Resources/subscriptions/tagNames/delete | Usuwa tag subskrypcji. |
> | action | Microsoft.Resources/subscriptions/tagNames/read | Pobiera tagi subskrypcji lub wyświetla ich listę. |
> | action | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Usuwa wartość tagu subskrypcji. |
> | action | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Pobiera wartości tagów lub wyświetla ich listę. |
> | action | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Dodaje wartość tagu subskrypcji. |
> | action | Microsoft.Resources/subscriptions/tagNames/write | Dodaje tag subskrypcji. |
> | action | Microsoft.Resources/tags/delete | Usuwa wszystkie tagi do zasobu. |
> | action | Microsoft.Resources/tags/read | Pobiera wszystkie tagi do zasobu. |
> | action | Microsoft.Resources/tags/write | Aktualizuje tagi do zasobu, zastępowanie lub scalanie istniejące tagi z nowym zestawem tagów lub usuwając istniejące tagi. |
> | action | Microsoft.Resources/tenants/read | Pobiera listę dzierżawców. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Scheduler/jobcollections/delete | Usuwa kolekcję zadań. |
> | action | Microsoft.Scheduler/jobcollections/disable/action | Wyłącza kolekcję zadań. |
> | action | Microsoft.Scheduler/jobcollections/enable/action | Włącza kolekcję zadań. |
> | action | Microsoft.Scheduler/jobcollections/jobs/delete | Usuwa zadanie. |
> | action | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Generuje definicję aplikacji logiki na podstawie zadania usługi Scheduler. |
> | action | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Pobiera historię zadania. |
> | action | Microsoft.Scheduler/jobcollections/jobs/read | Pobiera zadanie. |
> | action | Microsoft.Scheduler/jobcollections/jobs/run/action | Uruchamia zadanie. |
> | action | Microsoft.Scheduler/jobcollections/jobs/write | Tworzy lub aktualizuje zadanie. |
> | action | Microsoft.Scheduler/jobcollections/read | Pobierz kolekcję zadań |
> | action | Microsoft.Scheduler/jobcollections/write | Tworzy lub aktualizuje kolekcję zadań. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Search/checkNameAvailability/action | Służy do sprawdzania dostępności nazwy usługi. |
> | action | Microsoft.Search/operations/read | Wyświetla listę wszystkich dostępnych operacji dostawcy Microsoft.Search. |
> | action | Microsoft.Search/register/action | Rejestruje subskrypcję dostawcy zasobów wyszukiwania i umożliwia tworzenie usługi wyszukiwania. |
> | action | Microsoft.Search/searchServices/createQueryKey/action | Tworzy klucz zapytania. |
> | action | Microsoft.Search/searchServices/delete | Usuwa usługę wyszukiwania. |
> | action | Microsoft.Search/searchServices/deleteQueryKey/delete | Usuwa klucz zapytania. |
> | action | Microsoft.Search/searchServices/listAdminKeys/action | Odczytuje klucze administratora. |
> | action | Microsoft.Search/searchServices/listQueryKeys/read | Zwraca listę kluczy interfejsu API zapytań dla danej usługi Azure Search. |
> | action | Microsoft.Search/searchServices/read | Odczytuje usługi wyszukiwania. |
> | action | Microsoft.Search/searchServices/regenerateAdminKey/action | Ponownie generuje klucz administratora. |
> | action | Microsoft.Search/searchServices/start/action | Uruchamia usługę wyszukiwania. |
> | action | Microsoft.Search/searchServices/stop/action | Zatrzymuje usługę wyszukiwania. |
> | action | Microsoft.Search/searchServices/write | Tworzy lub aktualizuje usługę wyszukiwania. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Wymusza ruch danego wzmacniania ochrony reguły przez utworzenie reguł zabezpieczeń dopasowania na danym sieciowe grupy zabezpieczeń |
> | action | Microsoft.Security/adaptiveNetworkHardenings/read | Pobiera zalecenia adaptacyjne ograniczania funkcjonalności sieci platformy Azure chronionych zasobów |
> | action | Microsoft.Security/advancedThreatProtectionSettings/read | Pobiera Zaawansowane ustawienia ochrony przed zagrożeniami dla zasobu |
> | action | Microsoft.Security/advancedThreatProtectionSettings/write | Aktualizuje Zaawansowane ustawienia ochrony przed zagrożeniami dla zasobu |
> | action | Microsoft.Security/alerts/read | Pobiera wszystkie alerty zabezpieczeń dostępne |
> | action | Microsoft.Security/applicationWhitelistings/read | Pobiera whitelistings aplikacji |
> | action | Microsoft.Security/applicationWhitelistings/write | Tworzy nowe listy dozwolonych aplikacji lub aktualizuje istniejący zestaw |
> | action | Microsoft.Security/complianceResults/read | Pobiera wyniki sprawdzania zgodności dla zasobu |
> | action | Microsoft.Security/informationProtectionPolicies/read | Pobiera informacje o zasadach ochrony dla zasobu |
> | action | Microsoft.Security/informationProtectionPolicies/write | Aktualizacje z zasadami ochrony informacji dla zasobu |
> | action | Microsoft.Security/locations/alerts/activate/action | Uaktywnij alert zabezpieczeń |
> | action | Microsoft.Security/locations/alerts/dismiss/action | Odrzuć alert zabezpieczeń |
> | action | Microsoft.Security/locations/alerts/read | Pobiera wszystkie alerty zabezpieczeń dostępne |
> | action | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Usuwa zasady dostępu do sieci just-in-time |
> | action | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicjuje żądanie zasad dostępu just in time sieci |
> | action | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Pobiera zasady dostępu just in time sieci |
> | action | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Tworzy nowe zasady dostępu do sieci just in time lub aktualizuje istniejący zestaw |
> | action | Microsoft.Security/locations/read | Pobiera lokalizację danych zabezpieczeń |
> | action | Microsoft.Security/locations/tasks/activate/action | Aktywuj zalecenie dotyczące zabezpieczeń |
> | action | Microsoft.Security/locations/tasks/dismiss/action | Odrzuć zalecenie dotyczące zabezpieczeń |
> | action | Microsoft.Security/locations/tasks/read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | action | Microsoft.Security/locations/tasks/resolve/action | Rozwiąż zalecenie dotyczące zabezpieczeń |
> | action | Microsoft.Security/locations/tasks/start/action | Rozpocznij zalecenie dotyczące zabezpieczeń |
> | action | Microsoft.Security/policies/read | Pobiera zasady zabezpieczeń |
> | action | Microsoft.Security/policies/write | Aktualizuje zasady zabezpieczeń |
> | action | Microsoft.Security/pricings/delete | Usuwa ustawienia cen dla zakresu |
> | action | Microsoft.Security/pricings/read | Pobiera ustawienia cen dla zakresu |
> | action | Microsoft.Security/pricings/write | Aktualizacje cen ustawienia dla zakresu |
> | action | Microsoft.Security/register/action | Rejestruje subskrypcję dla usługi Azure Security Center |
> | action | Microsoft.Security/securityContacts/delete | Usuwa kontakt zabezpieczeń |
> | action | Microsoft.Security/securityContacts/read | Pobiera kontaktu zabezpieczeń |
> | action | Microsoft.Security/securityContacts/write | Aktualizacje zabezpieczeń skontaktuj się z |
> | action | Microsoft.Security/securitySolutions/delete | Usuwa rozwiązanie w zakresie zabezpieczeń |
> | action | Microsoft.Security/securitySolutions/read | Pobiera rozwiązania zabezpieczeń |
> | action | Microsoft.Security/securitySolutions/write | Tworzy nowe rozwiązanie zabezpieczeń lub aktualizuje istniejący zestaw |
> | action | Microsoft.Security/securitySolutionsReferenceData/read | Pobiera dane referencyjne rozwiązania zabezpieczeń |
> | action | Microsoft.Security/securityStatuses/read | Pobiera stany kondycji zabezpieczeń zasobów platformy Azure |
> | action | Microsoft.Security/securityStatusesSummaries/read | Pobiera podsumowania stanów w zakresie zabezpieczeń |
> | action | Microsoft.Security/settings/read | Pobiera ustawienia dla zakresu |
> | action | Microsoft.Security/settings/write | Aktualizuje ustawienia dla zakresu |
> | action | Microsoft.Security/tasks/read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | action | Microsoft.Security/unregister/action | Wyrejestrowuje subskrypcję w usłudze Azure Security Center |
> | action | Microsoft.Security/webApplicationFirewalls/delete | Usuwa zapory aplikacji sieci web |
> | action | Microsoft.Security/webApplicationFirewalls/read | Pobiera zapory aplikacji sieci web |
> | action | Microsoft.Security/webApplicationFirewalls/write | Nowa Zapora aplikacji sieci web tworzy lub aktualizuje istniejący zestaw |
> | action | Microsoft.Security/workspaceSettings/connect/action | Zmienianie obszaru roboczego ustawienia ponownego łączenia |
> | action | Microsoft.Security/workspaceSettings/delete | Usuwa ustawienia obszaru roboczego |
> | action | Microsoft.Security/workspaceSettings/read | Pobiera ustawienia obszaru roboczego |
> | action | Microsoft.Security/workspaceSettings/write | Aktualizuje ustawienia obszaru roboczego |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.SecurityGraph/diagnosticsettings/delete | Usuwanie ustawienia diagnostyczne |
> | action | Microsoft.SecurityGraph/diagnosticsettings/read | Odczytywanie ustawienie diagnostyczne |
> | action | Microsoft.SecurityGraph/diagnosticsettings/write | Zapisywanie ustawienia diagnostyczne |
> | action | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Odczytywanie kategorii ustawienie diagnostyczne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ServiceBus/checkNameAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | action | Microsoft.ServiceBus/checkNamespaceAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailability. |
> | action | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Usuwa reguły sieci wirtualnej w dostawcy zasobów usługi ServiceBus dla określonej sieci wirtualnej |
> | action | Microsoft.ServiceBus/namespaces/authorizationRules/action | Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest przestarzały. Można zaktualizować reguły autoryzacji Namespace zamiast niego użyj wywołania PUT... Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | action | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć domyślnej reguły autoryzacji Namespace.  |
> | action | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia z przestrzenią nazw |
> | action | Microsoft.ServiceBus/namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | action | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | action | Microsoft.ServiceBus/namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie Namespace i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | action | Microsoft.ServiceBus/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobierz klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | action | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. Tę operację można wywołać tylko za pomocą podstawowej przestrzeni nazw. |
> | action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje tryb pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | action | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Usuwa filtr usługi Event Grid skojarzony z przestrzenią nazw. |
> | action | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Pobiera filtr usługi Event Grid skojarzony z przestrzenią nazw. |
> | action | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Tworzy lub aktualizuje filtr usługi Event Grid skojarzony z przestrzenią nazw. |
> | action | Microsoft.ServiceBus/namespaces/eventhubs/read | Pobierz listę opisów zasobów Centrum zdarzeń |
> | action | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Usuń zasób filtru adresów IP |
> | action | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Pobierz zasób filtru adresów IP |
> | action | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Utwórz zasób filtru adresów IP |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/browse/action | Przeglądaj wiadmości |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/defer/action | Odroczenie wiadomości |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Odbieranie wiadomości |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/schedule/action | Shedule wiadomości |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Wysyłanie wiadomości |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/setstate/action | Ustaw stan sesji |
> | action | Microsoft.ServiceBus/namespaces/messagingPlan/read | Pobiera Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | action | Microsoft.ServiceBus/namespaces/messagingPlan/write | Aktualizuje Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | action | Microsoft.ServiceBus/namespaces/migrate/action | Operacja migracji przestrzeni nazw |
> | action | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Usuwa konfigurację migracji. |
> | action | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Pobiera konfigurację migracji wskazującą stan migracji oraz oczekujące operacje replikacji |
> | action | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Przywraca migrację przestrzeni nazw z warstwy Standardowa do warstwy Premium |
> | action | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Przypisuje serwer DNS skojarzony z przestrzenią nazw warstwy Standardowa do przestrzeni nazw warstwy Premium, co powoduje ukończenie migracji oraz zatrzymanie synchronizacji zasobów z przestrzeni nazw warstwy Standardowa do przestrzeni nazw warstwy Premium. |
> | action | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Tworzy lub migracji aktualizacje konfiguracji. Spowoduje to uruchomienie synchronizacji zasobów od planu standard do przestrzeni nazw premium |
> | action | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Usuń zasób reguły sieci wirtualnej |
> | action | Microsoft.ServiceBus/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | action | Microsoft.ServiceBus/namespaces/networkrulesets/write | Utwórz zasób reguły sieci wirtualnej |
> | action | Microsoft.ServiceBus/namespaces/operationresults/read | Pobierz stan operacji przestrzeni nazw |
> | action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz listę opisów zasobów ustawień diagnostycznych Namespace |
> | action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Pobierz listę opisów zasobów ustawień diagnostycznych Namespace |
> | action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Pobierz listę dzienników Namespace opisów zasobów |
> | action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryk Namespace opisów zasobów |
> | action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operacja zaktualizowania kolejki. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operacja usuwania reguł autoryzacji kolejki |
> | action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Pobieranie parametrów połączenia do kolejki |
> | action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Pobierz listę reguł autoryzacji kolejki |
> | action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Utwórz reguły autoryzacji kolejki i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | action | Microsoft.ServiceBus/namespaces/queues/Delete | Operacja usuwania zasobu kolejki |
> | action | Microsoft.ServiceBus/namespaces/queues/read | Pobierz listę opisów zasobów kolejki |
> | action | Microsoft.ServiceBus/namespaces/queues/write | Tworzenie lub właściwości kolejki aktualizacji. |
> | action | Microsoft.ServiceBus/namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | action | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw usługi ACS |
> | action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operacja aktualizowania tematu. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operacja usuwania reguł autoryzacji tematu |
> | action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Pobieranie parametrów połączenia do tematu |
> | action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Pobierz listę reguł autoryzacji tematu |
> | action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Utwórz reguły autoryzacji z tematu i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | action | Microsoft.ServiceBus/namespaces/topics/Delete | Operacja usuwania zasobu tematu |
> | action | Microsoft.ServiceBus/namespaces/topics/read | Pobierz listę opisów zasobów tematu |
> | action | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operacja usuwania zasobu TopicSubscription |
> | action | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Pobierz listę opisów zasobów TopicSubscription |
> | action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operacja usuwania zasobu reguły |
> | action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Pobierz listę opisów zasobów reguły |
> | action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Tworzenie lub właściwości reguły aktualizacji. |
> | action | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Tworzenie lub aktualizacja TopicSubscription właściwości. |
> | action | Microsoft.ServiceBus/namespaces/topics/write | Tworzenie lub właściwości aktualizacji tematu. |
> | action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Usuń zasób reguły sieci wirtualnej |
> | action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Pobiera zasób reguły sieci wirtualnej |
> | action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Utwórz zasób reguły sieci wirtualnej |
> | action | Microsoft.ServiceBus/namespaces/write | Tworzenie zasobu Namespace i zaktualizuj jego właściwości. Tagi i pojemność Namespace są właściwości, które mogą być aktualizowane. |
> | action | Microsoft.ServiceBus/operations/read | Pobierz operacje |
> | action | Microsoft.ServiceBus/register/action | Rejestruje subskrypcję dostawcy zasobów usługi ServiceBus i włącza funkcję tworzenia zasobów usługi ServiceBus |
> | action | Microsoft.ServiceBus/sku/read | Pobierz listę opisów zasobów jednostki Sku |
> | action | Microsoft.ServiceBus/sku/regions/read | Pobierz listę opisów zasobów SkuRegions |
> | action | Microsoft.ServiceBus/unregister/action | Rejestruje subskrypcję dostawcy zasobów usługi ServiceBus i włącza funkcję tworzenia zasobów usługi ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.ServiceFabric/clusters/applications/delete | Usuń dowolną aplikację |
> | action | Microsoft.ServiceFabric/clusters/applications/read | Odczytaj dowolną aplikację |
> | action | Microsoft.ServiceFabric/clusters/applications/services/delete | Usuń dowolną usługę |
> | action | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Odczytaj dowolną partycję |
> | action | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Odczytaj dowolną replikę |
> | action | Microsoft.ServiceFabric/clusters/applications/services/read | Odczytaj dowolną usługę |
> | action | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Odczytaj stan dowolnej usługi |
> | action | Microsoft.ServiceFabric/clusters/applications/services/write | Utwórz lub zaktualizuj dowolną usługę |
> | action | Microsoft.ServiceFabric/clusters/applications/write | Utwórz lub zaktualizuj dowolną aplikację |
> | action | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Usuń dowolny typ aplikacji |
> | action | Microsoft.ServiceFabric/clusters/applicationTypes/read | Odczytaj dowolny typ aplikacji |
> | action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Usuń dowolną wersję typu aplikacji |
> | action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Odczytaj dowolną wersję typu aplikacji |
> | action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Utwórz lub zaktualizuj dowolną wersję typu aplikacji |
> | action | Microsoft.ServiceFabric/clusters/applicationTypes/write | Utwórz lub zaktualizuj dowolny typ aplikacji |
> | action | Microsoft.ServiceFabric/clusters/delete | Usuń dowolny klaster |
> | action | Microsoft.ServiceFabric/clusters/nodes/read | Odczytaj dowolny węzeł |
> | action | Microsoft.ServiceFabric/clusters/read | Odczytaj dowolny klaster |
> | action | Microsoft.ServiceFabric/clusters/statuses/read | Odczytaj stan dowolnego klastra |
> | action | Microsoft.ServiceFabric/clusters/write | Utwórz lub zaktualizuj dowolny klaster |
> | action | Microsoft.ServiceFabric/locations/clusterVersions/read | Odczytaj wersję dowolnego klastra |
> | action | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Odczytaj dowolną wersję klastra dla określonego środowiska |
> | action | Microsoft.ServiceFabric/locations/operationresults/read | Odczytaj dowolne wyniki operacji |
> | action | Microsoft.ServiceFabric/locations/operations/read | Odczytaj dowolne operacje według lokalizacji |
> | action | Microsoft.ServiceFabric/operations/read | Odczytaj dowolne dostępne operacje |
> | action | Microsoft.ServiceFabric/register/action | Zarejestruj dowolną akcję |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.SignalRService/locations/checknameavailability/action | Sprawdza, czy nazwa jest dostępna do użycia z nową usługą SignalR |
> | action | Microsoft.SignalRService/locations/operationresults/signalr/read | Kwerenda o stan operacji asynchorous |
> | action | Microsoft.SignalRService/locations/operationstatus/operationId/read |  |
> | action | Microsoft.SignalRService/locations/usages/read | Pobierz użycie limitu przydziału dla usługi Azure SignalR service |
> | action | Microsoft.SignalRService/operationresults/read | Kwerenda o stan operacji asynchorous |
> | action | Microsoft.SignalRService/operationstatus/read |  |
> | action | Microsoft.SignalRService/register/action | Rejestruje dostawcę zasobów "Microsoft.SignalRService" z subskrypcji |
> | action | Microsoft.SignalRService/SignalR/delete | Usuń całą usługę SignalR |
> | action | Microsoft.SignalRService/SignalR/listFeatures/read | Lista dostosowana FeatureFlags usługi SignalR. Domyślnie wyłączone z nich. |
> | action | Microsoft.SignalRService/SignalR/listkeys/action | Wyświetl wartości kluczy dostępu do biblioteki SignalR, w portalu zarządzania lub za pośrednictwem interfejsu API |
> | action | Microsoft.SignalRService/SignalR/read | Wyświetl ustawienia i konfiguracje SignalR, w portalu zarządzania lub za pośrednictwem interfejsu API |
> | action | Microsoft.SignalRService/SignalR/regeneratekey/action | Zmień wartość kluczy dostępu do biblioteki SignalR w portalu zarządzania lub za pośrednictwem interfejsu API |
> | action | Microsoft.SignalRService/SignalR/restart/action | Ponowne uruchomienie usługi Azure SignalR service w portalu zarządzania lub za pośrednictwem interfejsu API. Będzie pewnych przestojów. |
> | action | Microsoft.SignalRService/SignalR/switchFeatures/action | Przełącz włączenia/wyłączenia SignalR FeatureFlags z obsługiwanych właściwości w portalu zarządzania lub za pośrednictwem interfejsu API. |
> | action | Microsoft.SignalRService/SignalR/write | Zmodyfikuj ustawienia i konfiguracje w portalu zarządzania lub za pośrednictwem interfejsu API SignalR |
> | action | Microsoft.SignalRService/unregister/action | Wyrejestrowuje dostawcę zasobów "Microsoft.SignalRService" z subskrypcji |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Zwraca listę artefaktów aplikacji definicji aplikacji. |
> | action | Microsoft.Solutions/applicationDefinitions/delete | Usuwa definicję aplikacji. |
> | action | Microsoft.Solutions/applicationDefinitions/read | Pobiera listę definicji aplikacji. |
> | action | Microsoft.Solutions/applicationDefinitions/write | Dodaj lub zmodyfikuj definicję aplikacji. |
> | action | Microsoft.Solutions/applications/applicationArtifacts/read | Zwraca listę artefaktów aplikacji. |
> | action | Microsoft.Solutions/applications/delete | Usuwa aplikację. |
> | action | Microsoft.Solutions/applications/read | Pobiera listę aplikacji. |
> | action | Microsoft.Solutions/applications/refreshPermissions/action | Odświeża uprawnień aplikacji. |
> | action | Microsoft.Solutions/applications/updateAccess/action | Aktualizuje dostęp do aplikacji. |
> | action | Microsoft.Solutions/applications/write | Tworzy aplikację. |
> | action | Microsoft.Solutions/jitRequests/delete | Usuń żądanie JitRequest |
> | action | Microsoft.Solutions/jitRequests/read | Pobiera listę żądań JitRequest |
> | action | Microsoft.Solutions/jitRequests/write | Tworzy żądanie JitRequest |
> | action | Microsoft.Solutions/locations/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | action | Microsoft.Solutions/register/action | Zarezerwuj w rozwiązaniach. |
> | action | Microsoft.Solutions/unregister/action | Wyrejestrowuje z rozwiązania. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Sql/checkNameAvailability/action | Sprawdź, czy nazwa danego serwera jest dostępne do alokowania na całym świecie, w ramach danej subskrypcji. |
> | action | Microsoft.Sql/instancePools/delete | Usuwa z puli wystąpienia |
> | action | Microsoft.Sql/instancePools/read | Pobiera wystąpienie puli |
> | action | Microsoft.Sql/instancePools/usages/read | Pobiera informacje o użycie puli wystąpienia |
> | action | Microsoft.Sql/instancePools/write | Tworzy lub aktualizuje pulę wystąpień |
> | action | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Pobieranie wyników blob serwera rozszerzonej inspekcji operacji zestawu zasad |
> | action | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Pobieranie wyników blob server inspekcji operacji zestawu zasad |
> | action | Microsoft.Sql/locations/capabilities/read | Pobiera możliwości dla tej subskrypcji w danej lokalizacji |
> | action | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | action | Microsoft.Sql/locations/databaseOperationResults/read | Pobiera stan operacji bazy danych. |
> | action | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Pobiera w trakcie wykonywania operacji na usunięto serwer |
> | action | Microsoft.Sql/locations/deletedServerOperationResults/read | Pobiera w trakcie wykonywania operacji na usunięto serwer |
> | action | Microsoft.Sql/locations/deletedServers/read | Zwraca listę usuniętych serwerów lub pobiera właściwości dla określonego serwera usunięte. |
> | action | Microsoft.Sql/locations/deletedServers/recover/action | Odzyskaj usunięto serwer |
> | action | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Pobiera operacji asynchronicznej platformy azure dla operacji asynchronicznej puli elastycznej |
> | action | Microsoft.Sql/locations/elasticPoolOperationResults/read | Pobiera wynik operacji puli elastycznej. |
> | action | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption — funkcja ochrony szyfrowania |
> | action | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption — funkcja ochrony szyfrowania |
> | action | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Pobieranie wyników blob serwera rozszerzonej inspekcji operacji zestawu zasad |
> | action | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Pobieranie wyników blob serwera rozszerzonej inspekcji operacji zestawu zasad |
> | action | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Pobiera stan operacji reguły zapory. |
> | action | Microsoft.Sql/locations/firewallRulesOperationResults/read | Pobiera stan operacji reguły zapory. |
> | action | Microsoft.Sql/locations/instanceFailoverGroups/delete | Usuwa istniejącą grupę trybu failover wystąpienia. |
> | action | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Wykonuje planowanego trybu failover w istniejącej grupie trybu failover wystąpienia. |
> | action | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszonego trybu failover w istniejącej grupie trybu failover wystąpienia. |
> | action | Microsoft.Sql/locations/instanceFailoverGroups/read | Zwraca listę wystąpienia w tryb failover grupy lub pobiera właściwości dla grupy trybu failover określonym wystąpieniu. |
> | action | Microsoft.Sql/locations/instanceFailoverGroups/write | Tworzy wystąpienie grupy trybu failover z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego wystąpienia grupy trybu failover. |
> | action | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Pobiera stan operacji puli wystąpienia |
> | action | Microsoft.Sql/locations/instancePoolOperationResults/read | Pobiera wynik operacji puli wystąpienia |
> | action | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Zwraca szczegóły punktu końcowego określonego interfejsu operacji asynchronicznej platformy Azure |
> | action | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Zwraca szczegóły operacji profilu punktu końcowego określonego interfejsu |
> | action | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Pobiera stan operacji agenta zadania. |
> | action | Microsoft.Sql/locations/jobAgentOperationResults/read | Pobiera wynik operacji agenta zadania. |
> | action | Microsoft.Sql/locations/longTermRetentionBackups/read | Wyświetla listę długoterminowego przechowywania kopii zapasowych dla każdej bazy danych na każdym serwerze w lokalizacji |
> | action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Wyświetla listę długoterminowego przechowywania kopii zapasowych dla każdej bazy danych na serwerze |
> | action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Usuwa kopię zapasową przechowywania długoterminowego |
> | action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Wyświetla listę długoterminowego przechowywania kopii zapasowych bazy danych |
> | action | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Kończy operację przywracania zarządzanej bazy danych |
> | action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption wystąpienia zarządzanego — funkcja ochrony szyfrowania |
> | action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption wystąpienia zarządzanego — funkcja ochrony szyfrowania |
> | action | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Klucze wystąpienia zarządzane przez pobiera w trakcie wykonywania operacji na technologii transparent data encryption |
> | action | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Klucze wystąpienia zarządzane przez pobiera w trakcie wykonywania operacji na technologii transparent data encryption |
> | action | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Pobiera w trakcie wykonywania operacji na zarządzanej bazy danych przezroczyste szyfrowanie danych |
> | action | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Pobiera w trakcie wykonywania operacji na zarządzanej bazy danych przezroczyste szyfrowanie danych |
> | action | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Pobiera wynik operacji prywatnych punktów końcowych połączenia serwera proxy |
> | action | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Pobiera wynik operacji prywatnych punktów końcowych połączenia serwera proxy |
> | action | Microsoft.Sql/locations/read | Pobiera dostępne lokalizacje, w ramach danej subskrypcji |
> | action | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption serwera klucze |
> | action | Microsoft.Sql/locations/serverKeyOperationResults/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption serwera klucze |
> | action | Microsoft.Sql/locations/syncAgentOperationResults/read | Pobierz wynik operacji zasobów agenta synchronizacji |
> | action | Microsoft.Sql/locations/syncDatabaseIds/read | Pobieranie identyfikatorów bazy danych synchronizacji dla konkretnego regionu i subskrypcji |
> | action | Microsoft.Sql/locations/syncGroupOperationResults/read | Pobierz wynik operacji synchronizacji grupy zasobów |
> | action | Microsoft.Sql/locations/syncMemberOperationResults/read | Pobierz wynik operacji zasobów elementu członkowskiego synchronizacji |
> | action | Microsoft.Sql/locations/usages/read | Pobiera kolekcję metryki użycia dla tej subskrypcji w lokalizacji |
> | action | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Zwraca szczegóły reguły sieci wirtualnej określonej operacji asynchronicznej platformy azure  |
> | action | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Zwraca szczegóły operacji reguły określonej sieci wirtualnej  |
> | action | Microsoft.Sql/managedInstances/administrators/delete | Usuwa istniejącego administratora wystąpienia zarządzanego. |
> | action | Microsoft.Sql/managedInstances/administrators/read | Pobiera listę administratorów wystąpienia zarządzanego. |
> | action | Microsoft.Sql/managedInstances/administrators/write | Tworzy lub aktualizuje administratora wystąpienia zarządzanego z określonymi parametrami. |
> | action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Pobiera zasady przechowywania krótkoterminowych dla zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Aktualizuje zasady przechowywania krótkoterminowych dla zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/delete | Usuwa istniejące zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla bazy danych wystąpień zarządzanych |
> | action | Microsoft.Sql/managedInstances/databases/read | Pobiera istniejący zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/schemas/read | Pobierz schemat zarządzanej bazy danych. |
> | action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Pobierz kolumny zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Usuń etykieta poufności danej kolumny |
> | action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Wyłącz zalecenia czułości w danej kolumnie |
> | action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Włącz zalecenia czułości w danej kolumnie |
> | action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Pobierz etykieta poufności danej kolumny |
> | action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Utwórz lub zaktualizuj etykieta poufności danej kolumny |
> | action | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Pobierz tabelę zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń zarządzanej bazy danych skonfigurowane dla danego serwera |
> | action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń bazy danych dla danego zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/securityEvents/read | Pobiera zdarzenia zabezpieczeń zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Pobieranie szczegółów bazy danych przezroczyste szyfrowanie danych na danym zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Zmień bazę danych przezroczyste szyfrowanie danych dla danego zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Usuń oceny luk w zabezpieczeniach dla danej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na givendatabase |
> | action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Pobierz reguły oceny luk w zabezpieczeniach według planu bazowego dla danej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Zmień reguły oceny luk w zabezpieczeniach, według planu bazowego, określonej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Przekonwertować istniejące wyników skanowania ludzi czytelnym formacie. Jeśli istnieje już nic się dzieje |
> | action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Wykonywanie skanowania bazy danych oceny luk w zabezpieczeniach. |
> | action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Zwraca listę luk w zabezpieczeniach bazy danych rekordów skanowania oceny lub pobrać rekordu skanowania dla identyfikatora określonego skanowania. |
> | action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danej bazy danych |
> | action | Microsoft.Sql/managedInstances/databases/write | Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych. |
> | action | Microsoft.Sql/managedInstances/delete | Usuwa istniejącego wystąpienia zarządzanego. |
> | action | Microsoft.Sql/managedInstances/encryptionProtector/read | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonego serwera funkcja ochrony szyfrowania. |
> | action | Microsoft.Sql/managedInstances/encryptionProtector/write | Aktualizowanie właściwości dla określonego funkcja ochrony szyfrowania serwera. |
> | action | Microsoft.Sql/managedInstances/keys/delete | Usuwa istniejący klucz wystąpienia zarządzanego Azure SQL. |
> | action | Microsoft.Sql/managedInstances/keys/read | Zwraca listę wystąpienia zarządzanego kluczy lub pobiera właściwości dla klucza określonego wystąpienia zarządzanego. |
> | action | Microsoft.Sql/managedInstances/keys/write | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości bądź tagi dla klucza określonego wystąpienia zarządzanego. |
> | action | Microsoft.Sql/managedInstances/metricDefinitions/read | Pobierz definicje metryk wystąpienia zarządzanego |
> | action | Microsoft.Sql/managedInstances/metrics/read | Pobierz metryki dla wystąpienia zarządzanego |
> | action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla wystąpienia zarządzanego |
> | action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla wystąpienia zarządzanego |
> | action | Microsoft.Sql/managedInstances/read | Zwraca listę wystąpień zarządzanych lub pobiera właściwości dla określonego wystąpienia zarządzanego. |
> | action | Microsoft.Sql/managedInstances/recoverableDatabases/read | Zwraca listę możliwych do odzyskania, zarządzane bazy danych |
> | action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Pobiera zasady przechowywania krótkoterminowych porzuconych zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Aktualizuje zasady przechowywania krótkoterminowych dla porzuconych zarządzanej bazy danych |
> | action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Zwraca listę z możliwością przywrócenia porzucony, zarządzane bazy danych. |
> | action | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń serwera zarządzanego skonfigurowany dla danego serwera |
> | action | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera zarządzanego dla danego serwera zarządzanego |
> | action | Microsoft.Sql/managedInstances/tdeCertificates/action | Utwórz/zaktualizuj TDE certyfikatu |
> | action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Usuń oceny luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na danego wystąpienia zarządzanego |
> | action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | action | Microsoft.Sql/managedInstances/write | Tworzy wystąpienia zarządzanego z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego wystąpienia zarządzanego. |
> | action | Microsoft.Sql/operations/read | Pobiera dostępne operacje REST |
> | action | Microsoft.Sql/register/action | Rejestruje subskrypcję dostawcy zasobów bazy danych Microsoft SQL, a następnie włącza funkcję tworzenia bazy danych programu Microsoft SQL. |
> | action | Microsoft.Sql/servers/administratorOperationResults/read | Pobiera w trakcie wykonywania operacji na administratorów serwera |
> | action | Microsoft.Sql/servers/administrators/delete | Usuń administratora serwera |
> | action | Microsoft.Sql/servers/administrators/read | Pobieranie szczegółów administratora serwera |
> | action | Microsoft.Sql/servers/administrators/write | Utwórz lub zaktualizuj administratora serwera |
> | action | Microsoft.Sql/servers/advisors/read | Zwraca listę doradców dostępny dla serwera |
> | action | Microsoft.Sql/servers/advisors/recommendedActions/read | Zwraca listę zalecane akcje advisor określonego serwera |
> | action | Microsoft.Sql/servers/advisors/recommendedActions/write | Stosowanie zalecaną akcję na serwerze programu |
> | action | Microsoft.Sql/servers/advisors/write | Aktualizacje automatyczne execute stan doradcy na poziomie serwera. |
> | action | Microsoft.Sql/servers/auditingPolicies/read | Pobieranie szczegółów tabeli server domyślne zasady skonfigurowane na danym serwerze inspekcji |
> | action | Microsoft.Sql/servers/auditingPolicies/write | Zmień domyślny serwer inspekcji tabel dla danego serwera |
> | action | Microsoft.Sql/servers/auditingSettings/operationResults/read | Pobieranie wyników blob server inspekcji operacji zestawu zasad |
> | action | Microsoft.Sql/servers/auditingSettings/read | Pobieranie szczegółów zasad inspekcji obiektów blob serwera skonfigurowane na danym serwerze |
> | action | Microsoft.Sql/servers/auditingSettings/write | Zmień Inspekcja obiektów blob serwera dla danego serwera |
> | action | Microsoft.Sql/servers/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania serwera |
> | action | Microsoft.Sql/servers/automaticTuning/write | Aktualizuje ustawienia automatycznego dostrajania serwera i zwraca zaktualizowanych ustawień |
> | action | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Usuwa istniejący magazyn archiwizacji kopii zapasowej. |
> | action | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Ta operacja służy do Pobierz magazyn przechowywania kopii zapasowych, długoterminowe. Zwraca informacje o magazynie zarejestrowane na tym serwerze |
> | action | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ta operacja służy do rejestrowania przechowywania kopii zapasowych długoterminowym magazynie do serwera |
> | action | Microsoft.Sql/servers/communicationLinks/delete | Usuwa istniejące łącze komunikacji serwera. |
> | action | Microsoft.Sql/servers/communicationLinks/read | Zwraca listę linki komunikacyjne z określonego serwera. |
> | action | Microsoft.Sql/servers/communicationLinks/write | Utwórz lub zaktualizuj łącza komunikacyjnego serwera. |
> | action | Microsoft.Sql/servers/connectionPolicies/read | Zwraca listę zasad połączenia serwera z określonego serwera. |
> | action | Microsoft.Sql/servers/connectionPolicies/write | Utwórz lub zaktualizuj zasady połączeń serwera. |
> | action | Microsoft.Sql/servers/databases/advisors/read | Zwraca listę doradcy jest dostępna dla bazy danych |
> | action | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Zwraca listę zalecane działania określonego advisor dla bazy danych |
> | action | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Zastosuj Zalecana akcja w bazie danych |
> | action | Microsoft.Sql/servers/databases/advisors/write | Update-konaj stan doradcy na poziomie bazy danych. |
> | action | Microsoft.Sql/servers/databases/auditingPolicies/read | Pobieranie szczegółów zasad inspekcji tabeli skonfigurowany w danej bazie danych |
> | action | Microsoft.Sql/servers/databases/auditingPolicies/write | Zmień zasady inspekcji tabel dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/auditingSettings/read | Pobieranie szczegółów zasad inspekcji obiektów blob, skonfigurowany w danej bazie danych |
> | action | Microsoft.Sql/servers/databases/auditingSettings/write | Zmień zasady inspekcji obiektów blob dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów blob bazy danych |
> | action | Microsoft.Sql/servers/databases/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania dla bazy danych |
> | action | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualizuje ustawienia automatycznego dostrajania dla bazy danych i zwraca zaktualizowanych ustawień |
> | action | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Zwraca listę archiwizacji zasady tworzenia kopii zapasowych określonej bazy danych. |
> | action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Utwórz lub zaktualizuj zasady archiwizacji kopii zapasowych bazy danych. |
> | action | Microsoft.Sql/servers/databases/connectionPolicies/read | Pobieranie szczegółów zasad połączenia skonfigurowane w danej bazie danych |
> | action | Microsoft.Sql/servers/databases/connectionPolicies/write | Zmiana zasad połączenia dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | action | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Zwraca listę maskowania zasad danych bazy danych. |
> | action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Usuwanie reguły dla danej bazy danych maskowania danych |
> | action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Pobierz szczegóły reguły zasady skonfigurowane w danej bazie danych maskowania danych |
> | action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Zmień reguły dla danej bazy danych maskowania danych |
> | action | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Zmiany zasad dla danej bazy danych maskowania danych |
> | action | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Zwraca informacje o kroku zapytania rozproszonego zapytania magazynu danych dla Identyfikatora wybrany krok |
> | action | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Zwraca informacje zapytania dystrybucji magazynu danych dla Identyfikatora wybranego zapytania |
> | action | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Pobiera wystąpienia SQL Data Warehouse, co obejmuje zapytania uruchomionego i wstrzymania działań użytkownika |
> | action | Microsoft.Sql/servers/databases/delete | Usuwa istniejącą bazę danych. |
> | action | Microsoft.Sql/servers/databases/export/action | Eksportowanie bazy danych Azure SQL |
> | action | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Pobieranie szczegółów zasad inspekcji rozszerzonego obiektu blob skonfigurowany w danej bazie danych |
> | action | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Zmiana zasad inspekcji rozszerzonego obiektu blob dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/extensions/read | Pobiera kolekcję rozszerzeń dla bazy danych. |
> | action | Microsoft.Sql/servers/databases/extensions/write | Zmień rozszerzenie dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Pobieranie geograficznie zasady tworzenia kopii zapasowych dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Utwórz lub zaktualizuj zasady geobackup bazy danych |
> | action | Microsoft.Sql/servers/databases/importExportOperationResults/read | Pobiera w trakcie operacji importu/eksportu |
> | action | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Pobiera listę okna obsługi dostępne dla wybranej bazy danych. |
> | action | Microsoft.Sql/servers/databases/maintenanceWindows/read | Pobiera konserwacji ustawienia systemu windows dla wybranej bazy danych. |
> | action | Microsoft.Sql/servers/databases/maintenanceWindows/write | Ustawia konserwacji ustawienia systemu windows dla wybranej bazy danych. |
> | action | Microsoft.Sql/servers/databases/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | action | Microsoft.Sql/servers/databases/metrics/read | Zwróć metryki dla baz danych |
> | action | Microsoft.Sql/servers/databases/move/action | Zmień nazwę istniejącej bazy danych. |
> | action | Microsoft.Sql/servers/databases/operationResults/read | Pobiera stan operacji bazy danych. |
> | action | Microsoft.Sql/servers/databases/operations/cancel/action | Anuluje usługi Azure SQL Database oczekująca operacja asynchroniczna, która nie została jeszcze zakończona. |
> | action | Microsoft.Sql/servers/databases/operations/read | Zwraca listę operacji wykonywanych w bazie danych |
> | action | Microsoft.Sql/servers/databases/pause/action | Wstrzymywanie bazy danych z magazynu danych Azure SQL |
> | action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla baz danych |
> | action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | action | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Zwraca kolekcję tekstów zapytania, które odnoszą się do określonych parametrów. |
> | action | Microsoft.Sql/servers/databases/queryStore/read | Zwraca bieżące wartości ustawień Store kwerendy dla bazy danych. |
> | action | Microsoft.Sql/servers/databases/queryStore/write | Aktualizuje ustawienia Store kwerendy dla bazy danych |
> | action | Microsoft.Sql/servers/databases/read | Zwraca listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | action | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | action | Microsoft.Sql/servers/databases/replicationLinks/delete | Zakończenie relacji replikacji wymuszone i z potencjalną utratą danych |
> | action | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Trybu failover po zsynchronizowaniu wszystkich zmienia z serwera podstawowego, dzięki czemu tę bazę danych do relationship\u0027s replikacji podstawowej i wprowadzania zdalnego podstawowego do pomocniczego |
> | action | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Tryb failover bezpośrednio z potencjalną utratą danych, dzięki czemu tę bazę danych do relationship\u0027s replikacji podstawowej i wprowadzania zdalnego podstawowego do pomocniczego |
> | action | Microsoft.Sql/servers/databases/replicationLinks/read | Zwraca listę replikacji łącza lub pobiera właściwości dla wybranych łączach replikacji określony. |
> | action | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Zakończenie relacji replikacji wymuszone lub po synchronizacji z partnerem |
> | action | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Aktualizowanie trybu replikacji dla łącza do trybu synchroniczna lub asynchroniczna |
> | action | Microsoft.Sql/servers/databases/restorePoints/action | Tworzy nowy punkt przywracania |
> | action | Microsoft.Sql/servers/databases/restorePoints/delete | Usuwa punkt przywracania bazy danych. |
> | action | Microsoft.Sql/servers/databases/restorePoints/read | Zwraca przywrócić punktów dla bazy danych. |
> | action | Microsoft.Sql/servers/databases/resume/action | Wznawianie bazy danych z magazynu danych Azure SQL |
> | action | Microsoft.Sql/servers/databases/schemas/read | Pobierz schemat bazy danych. |
> | action | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Pobierz kolumny bazy danych. |
> | action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Usuń etykieta poufności danej kolumny |
> | action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Wyłącz zalecenia czułości w danej kolumnie |
> | action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Włącz zalecenia czułości w danej kolumnie |
> | action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Pobierz etykieta poufności danej kolumny |
> | action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Utwórz lub zaktualizuj etykieta poufności danej kolumny |
> | action | Microsoft.Sql/servers/databases/schemas/tables/read | Pobierz tabelę bazy danych. |
> | action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Pobieranie listy zalecenia dotyczące indeksu w bazie danych |
> | action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Zastosuj zalecenie dotyczące indeksu |
> | action | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń bazy danych skonfigurowane dla danego serwera |
> | action | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń bazy danych dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/securityMetrics/read | Pobiera kolekcję metryki zabezpieczeń bazy danych |
> | action | Microsoft.Sql/servers/databases/sensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | action | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Zwraca sugestii dotyczących skalowania bazy danych w górę lub w dół w oparciu statystyk wykonywania zapytań, aby zwiększyć wydajność i obniżyć koszty |
> | action | Microsoft.Sql/servers/databases/skus/read | Pobiera kolekcję dostępnych jednostek SKU dla bazy danych |
> | action | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Anuluj synchronizację grupy synchronizacji |
> | action | Microsoft.Sql/servers/databases/syncGroups/delete | Usuwa istniejącą grupą synchronizacji. |
> | action | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Zwraca listę synchronizacji schematy bazy danych Centrum |
> | action | Microsoft.Sql/servers/databases/syncGroups/logs/read | Zwraca listę dzienników grupy synchronizacji |
> | action | Microsoft.Sql/servers/databases/syncGroups/read | Zwraca listę synchronizacji grupy lub pobiera właściwości dla grupy synchronizacji określonej. |
> | action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Odśwież schemat bazy danych Centrum synchronizacji |
> | action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu Centrum synchronizacji |
> | action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Usuwa istniejącego elementu członkowskiego synchronizacji. |
> | action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Zwraca listę elementów członkowskich synchronizacji lub pobiera właściwości dla elementu członkowskiego synchronizacji określonej funkcji. |
> | action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Odśwież schemat elementu członkowskiego synchronizacji |
> | action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu elementu członkowskiego synchronizacji |
> | action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Zwraca listę synchronizacji schematy bazy danych elementu członkowskiego |
> | action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Tworzy element członkowski synchronizacji z określonymi parametrami lub aktualizuje właściwości dla elementu członkowskiego synchronizacji określonej. |
> | action | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Wyzwalacz Synchronizacja grupy synchronizacji |
> | action | Microsoft.Sql/servers/databases/syncGroups/write | Tworzy grupę synchronizacji z określonymi parametrami lub zaktualizować grupy synchronizacji określonej właściwości. |
> | action | Microsoft.Sql/servers/databases/topQueries/queryText/action | Zwraca tekst języka Transact-SQL dla Identyfikatora wybranego zapytania |
> | action | Microsoft.Sql/servers/databases/topQueries/read | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | action | Microsoft.Sql/servers/databases/topQueries/statistics/read | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | action | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption |
> | action | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Pobieranie stanu i szczegółowe informacje o technologii transparent data encryption zabezpieczeń funkcji określonej bazy danych |
> | action | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Zmień stan szyfrowania danych |
> | action | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Uaktualnianie bazy danych magazynu danych Azure SQL |
> | action | Microsoft.Sql/servers/databases/usages/read | Pobiera informacje o użycia usługi Azure SQL Database |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Usuń oceny luk w zabezpieczeniach dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na givendatabase |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Pobierz reguły oceny luk w zabezpieczeniach według planu bazowego dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Zmień reguły oceny luk w zabezpieczeniach, według planu bazowego, określonej bazy danych |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Przekonwertować istniejące wyników skanowania ludzi czytelnym formacie. Jeśli istnieje już nic się dzieje |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Wykonywanie skanowania bazy danych oceny luk w zabezpieczeniach. |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Zwraca listę luk w zabezpieczeniach bazy danych rekordów skanowania oceny lub pobrać rekordu skanowania dla identyfikatora określonego skanowania. |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Wykonywanie skanowania bazy danych oceny luk w zabezpieczeniach. |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Pobierz wynik skanowania oceny luk w zabezpieczeniach bazy danych operacji wykonywania |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Pobierz szczegóły oceny luk w zabezpieczeniach, skonfigurowany w danej bazie danych |
> | action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Zmiana oceny luk w zabezpieczeniach dla danej bazy danych |
> | action | Microsoft.Sql/servers/databases/write | Tworzy bazę danych z określonymi parametrami lub aktualizuje właściwości bądź tagi określonej bazy danych. |
> | action | Microsoft.Sql/servers/delete | Usuwa istniejącego serwera. |
> | action | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Usuwa istniejące konfiguracjami odzyskiwania po awarii dla danego serwera |
> | action | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Tryb failover DisasterRecoveryConfiguration |
> | action | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Wymuszenia trybu Failover DisasterRecoveryConfiguration |
> | action | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Pobiera kolekcję po awarii z konfiguracjami odzyskiwania, które zawierają ten serwer |
> | action | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Konfiguracja odzyskiwania po awarii serwera zmiany |
> | action | Microsoft.Sql/servers/elasticPoolEstimates/read | Zwraca listę już utworzone dla tego serwera oszacowania puli elastycznej |
> | action | Microsoft.Sql/servers/elasticPoolEstimates/write | Tworzy nowy szacunek puli elastycznej dla listy baz danych, pod warunkiem |
> | action | Microsoft.Sql/servers/elasticPools/advisors/read | Zwraca listę doradcy jest dostępna dla puli elastycznej |
> | action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Zwraca listę zalecane akcje advisor określonego dla puli elastycznej |
> | action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Zastosuj Zalecana akcja w puli elastycznej |
> | action | Microsoft.Sql/servers/elasticPools/advisors/write | Update-konaj stan doradcy na poziomie puli elastycznej. |
> | action | Microsoft.Sql/servers/elasticPools/databases/read | Pobiera listę baz danych dla puli elastycznej |
> | action | Microsoft.Sql/servers/elasticPools/delete | Usuwanie istniejącej puli elastycznej |
> | action | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Pobieranie działania i szczegółowe informacje na temat danego elastycznej puli baz danych |
> | action | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Pobieranie działania i szczegółowe informacje na temat danego bazy danych, który jest częścią elastycznej puli baz danych |
> | action | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla elastycznych pul baz danych |
> | action | Microsoft.Sql/servers/elasticPools/metrics/read | Zwróć metryki dla elastycznych pul baz danych |
> | action | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Anuluje elastyczną pulę Azure SQL oczekująca operacja asynchroniczna, która nie została jeszcze zakończona. |
> | action | Microsoft.Sql/servers/elasticPools/operations/read | Zwraca listę operacji wykonywanych na elastycznej puli |
> | action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla elastycznych pul baz danych |
> | action | Microsoft.Sql/servers/elasticPools/read | Pobierz szczegóły puli elastycznej na danym serwerze |
> | action | Microsoft.Sql/servers/elasticPools/skus/read | Pobiera kolekcję dostępnych jednostek SKU dla puli elastycznej |
> | action | Microsoft.Sql/servers/elasticPools/write | Utwórz nową lub zmienić właściwości istniejącej puli elastycznej |
> | action | Microsoft.Sql/servers/encryptionProtector/read | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonego serwera funkcja ochrony szyfrowania. |
> | action | Microsoft.Sql/servers/encryptionProtector/write | Aktualizowanie właściwości dla określonego funkcja ochrony szyfrowania serwera. |
> | action | Microsoft.Sql/servers/extendedAuditingSettings/read | Pobierz szczegóły obiektu blob serwera rozszerzonej skonfigurowane na danym serwerze zasady inspekcji |
> | action | Microsoft.Sql/servers/extendedAuditingSettings/write | Zmień serwer rozszerzonej inspekcji obiektów blob dla danego serwera |
> | action | Microsoft.Sql/servers/failoverGroups/delete | Usuwa istniejącą grupę trybu failover. |
> | action | Microsoft.Sql/servers/failoverGroups/failover/action | Wykonuje planowanego trybu failover w istniejącej grupie trybu failover. |
> | action | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszonego trybu failover w istniejącej grupie trybu failover. |
> | action | Microsoft.Sql/servers/failoverGroups/read | Zwraca listę trybu failover grupy lub pobiera właściwości dla grupy trybu failover określonym. |
> | action | Microsoft.Sql/servers/failoverGroups/write | Tworzy grupę trybu failover z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego trybu failover grupy. |
> | action | Microsoft.Sql/servers/firewallRules/delete | Usuwa istniejącą regułę zapory na serwerze. |
> | action | Microsoft.Sql/servers/firewallRules/read | Zwraca listę zapory serwera reguł lub pobiera właściwości dla określonego serwera, reguły zapory. |
> | action | Microsoft.Sql/servers/firewallRules/write | Tworzy regułę zapory na serwerze z określonymi parametrami, zaktualizować właściwości określonej reguły lub zastąpienie wszystkich istniejących reguł nowych reguł zapory serwera. |
> | action | Microsoft.Sql/servers/import/action | Tworzenie nowej bazy danych na serwerze i wdrożyć schemat i dane z pakietu DacPac |
> | action | Microsoft.Sql/servers/importExportOperationResults/read | Pobiera w trakcie operacji importu/eksportu |
> | action | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Usuwa profil punktu końcowego określonego interfejsu |
> | action | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Zwraca właściwości dla określonego interfejsu profilu punktu końcowego |
> | action | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Tworzy profil punktu końcowego interfejsu z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego interfejsu punktu końcowego |
> | action | Microsoft.Sql/servers/jobAgents/delete | Usuwa agenta zadań w usłudze Azure SQL DB |
> | action | Microsoft.Sql/servers/jobAgents/read | Pobiera agenta zadań w usłudze Azure SQL DB |
> | action | Microsoft.Sql/servers/jobAgents/write | Tworzy lub aktualizuje agenta zadań w usłudze Azure SQL DB |
> | action | Microsoft.Sql/servers/keys/delete | Usuwa istniejący klucz serwera. |
> | action | Microsoft.Sql/servers/keys/read | Zwraca listę server kluczy lub pobiera właściwości dla klucza określonego serwera. |
> | action | Microsoft.Sql/servers/keys/write | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości bądź tagi dla klucza określonego serwera. |
> | action | Microsoft.Sql/servers/operationResults/read | Pobiera w trakcie operacji serwera |
> | action | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Usuwa istniejący serwer proxy połączeń prywatnych punktów końcowych |
> | action | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Zwraca listę wszystkich prywatnych punktów końcowych połączenia serwerów proxy lub pobiera właściwości dla serwera proxy połączenia prywatnego określonym w punkcie końcowym. |
> | action | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Sprawdza poprawność prywatne połączenie z punktem końcowym tworzenie wywołania po stronie NRP |
> | action | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Tworzy połączenie serwera proxy prywatnych punktów końcowych z określonymi parametrami lub aktualizuje właściwości bądź tagi proxy połączenia określony prywatnych punktów końcowych usługi. |
> | action | Microsoft.Sql/servers/privateEndpointConnections/delete | Usuwa istniejące połączenie prywatnych punktów końcowych |
> | action | Microsoft.Sql/servers/privateEndpointConnections/read | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla połączenia prywatnego określonym w punkcie końcowym. |
> | action | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla serwerów |
> | action | Microsoft.Sql/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | action | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Pobierz metryki dla zalecane elastyczne pule baz danych dla danego serwera |
> | action | Microsoft.Sql/servers/recommendedElasticPools/read | Pobierz zalecenia dla elastycznych pul baz danych do zmniejszania kosztów lub zwiększenia wydajności na podstawie wykorzystania historycznych zasobów |
> | action | Microsoft.Sql/servers/recoverableDatabases/read | Ta operacja służy do odzyskiwania po awarii na żywo bazy danych do przywracania bazy danych Ostatnia znana dobra punktu kopii zapasowej. Zwraca informacje o ostatnim dobrej kopii zapasowej, ale doesn\u0027t faktycznie Przywracanie bazy danych. |
> | action | Microsoft.Sql/servers/replicationLinks/read | Zwraca listę replikacji łącza lub pobiera właściwości dla wybranych łączach replikacji określony. |
> | action | Microsoft.Sql/servers/restorableDroppedDatabases/read | Zostanie wyświetlona lista baz danych, które zostały usunięte na danym serwerze, które są nadal w ramach zasad przechowywania. |
> | action | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Pobierz wyniki operacji zapisu zasady wykrywania zagrożeń serwera |
> | action | Microsoft.Sql/servers/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń serwera skonfigurowany dla danego serwera |
> | action | Microsoft.Sql/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | action | Microsoft.Sql/servers/serviceObjectives/read | Pobieranie listy celów poziomu usług (znany także jako warstwy wydajności) dostępne na danym serwerze |
> | action | Microsoft.Sql/servers/syncAgents/delete | Usuwa istniejącego agenta synchronizacji. |
> | action | Microsoft.Sql/servers/syncAgents/generateKey/action | Wygeneruj klucz rejestracji agenta synchronizacji |
> | action | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Zwraca listę bazy danych połączone agenta synchronizacji |
> | action | Microsoft.Sql/servers/syncAgents/read | Zwraca listę agentów synchronizacji lub pobiera właściwości dla agenta synchronizacji określonej. |
> | action | Microsoft.Sql/servers/syncAgents/write | Tworzy agenta synchronizacji z określonymi parametrami lub aktualizuje właściwości dla agenta synchronizacji określonej. |
> | action | Microsoft.Sql/servers/tdeCertificates/action | Utwórz/zaktualizuj TDE certyfikatu |
> | action | Microsoft.Sql/servers/usages/read | Zwraca limit przydziału jednostek DTU serwera i bieżącej consuption jednostek DTU przez wszystkie bazy danych na serwerze |
> | action | Microsoft.Sql/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | action | Microsoft.Sql/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguł lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | action | Microsoft.Sql/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | action | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Usuń oceny luk w zabezpieczeniach dla danego serwera |
> | action | Microsoft.Sql/servers/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na danym serwerze |
> | action | Microsoft.Sql/servers/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danego serwera |
> | action | Microsoft.Sql/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |
> | action | Microsoft.Sql/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów bazy danych Microsoft SQL, a następnie włącza funkcję tworzenia bazy danych programu Microsoft SQL. |
> | action | Microsoft.Sql/virtualClusters/delete | Usuwa istniejący klaster wirtualny. |
> | action | Microsoft.Sql/virtualClusters/read | Zwraca listę klastrów wirtualnych lub pobiera właściwości dla określonego klastra wirtualnego. |
> | action | Microsoft.Sql/virtualClusters/write | Aktualizuje tagi klaster wirtualny. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Storage/checknameavailability/read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | action | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia element Microsoft.Storage o usuwaniu sieci wirtualnej lub podsieci |
> | action | Microsoft.Storage/locations/usages/read | Zwraca limit i bieżącą liczbę użyć dla zasobów w określonej subskrypcji |
> | action | Microsoft.Storage/operations/read | Sonduje stan operacji asynchronicznej. |
> | action | Microsoft.Storage/register/action | Rejestruje subskrypcję dostawcy zasobów magazynu i umożliwia tworzenie kont magazynu. |
> | action | Microsoft.Storage/skus/read | Wyświetla listę jednostek SKU obsługiwanych przez zasób Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Zwraca wynik dodania zawartości obiektu blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Zwraca wynik usunięcia obiektu blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Zwraca wynik usunięcia automatycznej migawki |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Zwraca listę obiektów blob w ramach konta ze zgodnym filtrem tagów |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Zwraca obiekt blob lub listę obiektów blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Zwraca wynik odczytywania tagów obiektów blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Zwraca wynik zapisywania tagów obiektów blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Zwraca wynik zapisania obiektu blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Usuń stan archiwizacji ze względów prawnych dla kontenera obiektów blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Zwraca wynik usunięcia kontenera |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Usuń zasady niezmienności kontenera obiektów blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Rozszerz zasady niezmienności kontenera obiektów blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Zablokuj zasady niezmienności kontenera obiektów blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Uzyskaj zasady niezmienności kontenera obiektów blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Dodaj zasady niezmienności kontenera obiektów blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Zwraca wynik dzierżawy kontenera obiektów blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/read | Zwraca kontener |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/read | Zwraca listę kontenerów |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ustaw stan archiwizacji ze względów prawnych dla kontenera obiektów blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/write | Zwraca wynik zastosowania poprawki do kontenera obiektów blob |
> | action | Microsoft.Storage/storageAccounts/blobServices/containers/write | Zwraca wynik umieszczania obiektu blob kontenera |
> | action | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla usługi Blob Service |
> | action | Microsoft.Storage/storageAccounts/blobServices/read | Zwraca statystyki lub wartości usługi Blob Service |
> | action | Microsoft.Storage/storageAccounts/blobServices/write | Zwraca wynik umieszczenia właściwości usługi obiektów blob |
> | action | Microsoft.Storage/storageAccounts/delete | Usuwa istniejące konto magazynu. |
> | action | Microsoft.Storage/storageAccounts/failover/action | Klient jest w stanie kontrolować przełączanie do trybu failover w przypadku problemów z dostępnością |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write |  |
> | action | Microsoft.Storage/storageAccounts/fileServices/read | Pobierz właściwości usługi File Service |
> | action | Microsoft.Storage/storageAccounts/listAccountSas/action | Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu. |
> | action | Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
> | action | Microsoft.Storage/storageAccounts/listServiceSas/action | Zwraca token sygnatury dostępu współdzielonego usługi dla określonego konta magazynu. |
> | action | Microsoft.Storage/storageAccounts/managementPolicies/delete | Usuń zasady zarządzania kontami magazynu |
> | action | Microsoft.Storage/storageAccounts/managementPolicies/read | Pobierz zasady konta zarządzania magazynem |
> | action | Microsoft.Storage/storageAccounts/managementPolicies/write | Umieść zasady zarządzania kontami magazynu |
> | action | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Zwraca wynik usunięcia kolejki |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Zwraca wynik dodania komunikatu |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Zwraca wynik usunięcia komunikatu |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Zwraca wynik przetworzenia komunikatu |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Zwraca komunikat |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Zwraca wynik zapisania komunikatu |
> | action | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | action | Microsoft.Storage/storageAccounts/queueServices/queues/write | Zwraca wynik zapisania kolejki |
> | action | Microsoft.Storage/storageAccounts/queueServices/read | Pobierz właściwości usługi Queue Service |
> | action | Microsoft.Storage/storageAccounts/queueServices/read | Zwraca statystyki lub wartości usługi kolejki. |
> | action | Microsoft.Storage/storageAccounts/queueServices/write | Zwraca wynik ustawienia właściwości usługi kolejki |
> | action | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
> | action | Microsoft.Storage/storageAccounts/regeneratekey/action | Generuje ponownie klucze dostępu dla podanego konta magazynu. |
> | action | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Odwołuje wszystkie klucze delegowania użytkowników dla określonego konta magazynu. |
> | action | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Utwórz lub zaktualizuj ustawienia diagnostyczne konta magazynu. |
> | action | Microsoft.Storage/storageAccounts/tableServices/read | Pobierz właściwości usługi Table Service |
> | action | Microsoft.Storage/storageAccounts/write | Tworzy konto magazynu z podanymi parametrami lub aktualizuje właściwości bądź tagi albo dodaje niestandardową domenę dla podanego konta magazynu. |
> | action | Microsoft.Storage/usages/read | Zwraca limit i bieżącą liczbę użyć dla zasobów w określonej subskrypcji |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | microsoft.storagesync/locations/checkNameAvailability/action | Sprawdza się, że nazwa usługi synchronizacji magazynu jest prawidłowa i nie jest używany. |
> | action | microsoft.storagesync/locations/workflows/operations/read | Pobiera stan operacji asynchronicznej |
> | action | microsoft.storagesync/storageSyncServices/delete | Usuń wszystkie usługi synchronizacji magazynu |
> | action | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla usługi synchronizacji magazynu |
> | action | microsoft.storagesync/storageSyncServices/read | Przeczytaj dowolnej usługi synchronizacji magazynu |
> | action | microsoft.storagesync/storageSyncServices/registeredServers/delete | Usuń wszelkie zarejestrowanego serwera |
> | action | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla zarejestrowanego serwera |
> | action | microsoft.storagesync/storageSyncServices/registeredServers/read | Przeczytaj dowolnego zarejestrowanego serwera |
> | action | microsoft.storagesync/storageSyncServices/registeredServers/write | Utwórz lub zaktualizuj wszelkie zarejestrowanego serwera |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Usuń wszystkie punkty końcowe w chmurze |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Pobiera stan operacji asynchronicznej/przywracania kopii zapasowej |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Wywołaj tę akcję po utworzeniu kopii zapasowej |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Wywołaj tę akcję po przywracaniu |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Wywołaj tę akcję przed kopią zapasową |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Wywołaj tę akcję przed przywróceniem |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Odczytywać wszystkie punkty końcowe w chmurze |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Przywróć pulsu |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Utwórz lub zaktualizuj wszystkie punkty końcowe w chmurze |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/delete | Usuń wszystkie grupy synchronizacji |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla grupy synchronizacji |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/read | Przeczytaj żadnych grup synchronizacji |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Usuń wszystkie punkty końcowe serwera |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla punkty końcowe serwera |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Odczytywać wszystkie punkty końcowe serwera |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Wywołaj tę akcję, aby odwołać pliki do serwera |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Utwórz lub zaktualizuj wszystkie punkty końcowe serwera |
> | action | microsoft.storagesync/storageSyncServices/syncGroups/write | Utwórz lub zaktualizuj żadnych grup synchronizacji |
> | action | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Pobiera stan operacji asynchronicznej |
> | action | microsoft.storagesync/storageSyncServices/workflows/operations/read | Pobiera stan operacji asynchronicznej |
> | action | microsoft.storagesync/storageSyncServices/workflows/read | Przeczytaj przepływów pracy |
> | action | microsoft.storagesync/storageSyncServices/write | Utwórz lub zaktualizuj wszelkie usługi synchronizacji magazynu |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.StorSimple/managers/accessControlRecords/delete | Usuwa rekordy kontroli dostępu |
> | action | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/accessControlRecords/read | Wyświetla lub pobiera rekordy kontroli dostępu |
> | action | Microsoft.StorSimple/managers/accessControlRecords/write | Utwórz lub zaktualizuj rekordy kontroli dostępu |
> | action | Microsoft.StorSimple/managers/alerts/read | Wyświetla lub pobiera alerty |
> | action | Microsoft.StorSimple/managers/backups/read | Wyświetla lub pobiera zestaw kopii zapasowych |
> | action | Microsoft.StorSimple/managers/bandwidthSettings/delete | Usuwa istniejące ustawienia przepustowości (8000 Series tylko) |
> | action | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/bandwidthSettings/read | Lista ustawień przepustowości (tylko w przypadku serii 8000) |
> | action | Microsoft.StorSimple/managers/bandwidthSettings/write | Tworzy nową lub aktualizuje ustawienia przepustowości (8000 Series tylko) |
> | action | Microsoft.StorSimple/managers/certificates/write | Tworzenie lub aktualizowanie certyfikatów |
> | action | Microsoft.StorSimple/Managers/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | action | Microsoft.StorSimple/managers/clearAlerts/action | Usuń zaznaczenie wszystkich alertów skojarzonych z Menedżerem urządzeń. |
> | action | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Wyświetl urządzenie w chmurze obsługiwane konfiguracje |
> | action | Microsoft.StorSimple/managers/configureDevice/action | Konfiguruje urządzenia |
> | action | Microsoft.StorSimple/managers/delete | Usuwa menedżerów urządzeń |
> | action | Microsoft.StorSimple/Managers/delete | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "Magazyn" |
> | action | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/alertSettings/read | Wyświetla lub pobiera ustawienia alertu |
> | action | Microsoft.StorSimple/managers/devices/alertSettings/write | Utwórz lub zaktualizuj ustawienia alertu |
> | action | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autoryzacja dla usługi Przerzucanie klucza szyfrowania urządzeń |
> | action | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Ręczna kopia zapasowa, aby utworzyć na żądanie należy wykonać kopię zapasową wszystkich woluminów, które są chronione przez zasady. |
> | action | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Usuwa istniejące zasady kopii zapasowych (8000 Series tylko) |
> | action | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/backupPolicies/read | Listy kopii zapasowej zasady (tylko w przypadku serii 8000) |
> | action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Usuwa istniejące harmonogramy |
> | action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Lista harmonogramy |
> | action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Tworzy nową lub aktualizuje harmonogramów |
> | action | Microsoft.StorSimple/managers/devices/backupPolicies/write | Tworzy nową lub aktualizacji zasad kopii zapasowych (8000 Series tylko) |
> | action | Microsoft.StorSimple/managers/devices/backups/delete | Usuwa zestaw kopii zapasowych |
> | action | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klonuj udział lub wolumin za pomocą elementu kopii zapasowej. |
> | action | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/backups/read | Wyświetla lub pobiera zestaw kopii zapasowych |
> | action | Microsoft.StorSimple/managers/devices/backups/restore/action | Przywróć wszystkie woluminy z zestawu kopii zapasowych. |
> | action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Usuwa grupy harmonogram tworzenia kopii zapasowych |
> | action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Wyświetla lub pobiera grupy harmonogramu kopii zapasowych |
> | action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Utwórz lub zaktualizuj grupy harmonogramu kopii zapasowych |
> | action | Microsoft.StorSimple/managers/devices/chapSettings/delete | Usuwa ustawienia protokołu Chap |
> | action | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/chapSettings/read | Wyświetla lub pobiera ustawienia protokołu Chap |
> | action | Microsoft.StorSimple/managers/devices/chapSettings/write | Utwórz lub zaktualizuj ustawienia protokołu Chap |
> | action | Microsoft.StorSimple/managers/devices/deactivate/action | Dezaktywuje urządzenia. |
> | action | Microsoft.StorSimple/managers/devices/delete | Usuwa urządzenia |
> | action | Microsoft.StorSimple/managers/devices/disks/read | Wyświetla lub pobiera dysków |
> | action | Microsoft.StorSimple/managers/devices/download/action | Pobierz aktualizacje dla urządzenia. |
> | action | Microsoft.StorSimple/managers/devices/failover/action | Tryb failover urządzenia. |
> | action | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/failoverTargets/read | Wyświetla lub pobiera obiekty docelowe trybu Failover urządzeń |
> | action | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Wykonaj kopię zapasową, serwera plików. |
> | action | Microsoft.StorSimple/managers/devices/fileservers/delete | Usuwa na serwerach plików |
> | action | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Wyświetla lub pobiera metryki |
> | action | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | action | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/fileservers/read | Wyświetla lub pobiera na serwerach plików |
> | action | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Usuwa akcji |
> | action | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Wyświetla lub pobiera metryki |
> | action | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | action | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Wyświetla lub pobiera akcje |
> | action | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Utwórz lub zaktualizuj akcji |
> | action | Microsoft.StorSimple/managers/devices/fileservers/write | Utwórz lub zaktualizuj serwery plików |
> | action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Zmiana stanu zasilania kontrolera grup składników sprzętowych |
> | action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Lista grup składników sprzętowych |
> | action | Microsoft.StorSimple/managers/devices/install/action | Zainstaluj aktualizacje na urządzeniu. |
> | action | Microsoft.StorSimple/managers/devices/installUpdates/action | Instaluje aktualizacje na urządzeniach (8000 Series tylko). |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Wykonaj kopię zapasową serwera iSCSI. |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Usuwa iSCSI serwerów |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Usuwa dysków |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Wyświetla lub pobiera metryki |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Wyświetla lub pobiera dysków |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Utwórz lub zaktualizuj dyski |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Wyświetla lub pobiera metryki |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/read | Wyświetla lub pobiera iSCSI serwerów |
> | action | Microsoft.StorSimple/managers/devices/iscsiservers/write | Utwórz lub zaktualizuj serwery iSCSI |
> | action | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Anuluj uruchomione zadanie |
> | action | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/jobs/read | Wyświetla lub pobiera zadania |
> | action | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Lista zestawów trybu failover dla istniejącego urządzenia (8000 Series tylko). |
> | action | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Wyświetl listę celów trybu failover urządzeń (8000 Series tylko). |
> | action | Microsoft.StorSimple/managers/devices/metrics/read | Wyświetla lub pobiera metryki |
> | action | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Potwierdza migracja zakończyła się powodzeniem, a następnie zatwierdzić. |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Lista upewnij się, stan migracji |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Pobierz stan potwierdzenia migracji. |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Pobierz stan zadania oceny migracji. |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Pobierz stan migracji. |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Zaimportować konfiguracje źródeł dla migracji |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Lista oszacowanie migracji |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Wyświetl stan migracji |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Rozpoczynanie migracji za pomocą źródło konfiguracji |
> | action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Uruchamianie zadania można oszacować, na czas trwania procesu migracji. |
> | action | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/networkSettings/read | Wyświetla lub pobiera ustawienia sieci |
> | action | Microsoft.StorSimple/managers/devices/networkSettings/write | Tworzy nową lub aktualizuje ustawienia sieci |
> | action | Microsoft.StorSimple/managers/devices/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Lista publicznego klucza szyfrowania dla Menedżera urządzeń |
> | action | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publikowanie pakietu dla pomocy technicznej dla istniejącego urządzenia. Pakiet dla pomocy technicznej usługi StorSimple jest mechanizm łatwy w użyciu, który gromadzi informacje o wszystkich dzienników istotnych uzyskanymi Microsoft Support rozwiązać wszelkie problemy z urządzeniem StorSimple. |
> | action | Microsoft.StorSimple/managers/devices/read | Wyświetla lub pobiera urządzenia |
> | action | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Skanowanie w poszukiwaniu aktualizacji w urządzeniu. |
> | action | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/securitySettings/read | Ustawienia zabezpieczeń listy |
> | action | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synchronizowanie certyfikatu zarządzania zdalnego dla urządzenia. |
> | action | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Aktualizowanie ustawień zabezpieczeń. |
> | action | Microsoft.StorSimple/managers/devices/securitySettings/write | Tworzy nową lub aktualizuje ustawienia zabezpieczeń |
> | action | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Wyślij wiadomość e-mail z alertem testu do adresatów wiadomości e-mail skonfigurowany. |
> | action | Microsoft.StorSimple/managers/devices/shares/read | Wyświetla lub pobiera akcje |
> | action | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/timeSettings/read | Wyświetla lub pobiera ustawienia czasu |
> | action | Microsoft.StorSimple/managers/devices/timeSettings/write | Tworzy nową lub aktualizuje ustawienia czasu |
> | action | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/updateSummary/read | Wyświetla lub pobiera Podsumowanie aktualizacji |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Usuwa istniejące kontenery woluminów (8000 Series tylko) |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Listy metryk |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Listę definicji metryk |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/read | Lista kontenerów woluminów (tylko w przypadku serii 8000) |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Usuwa istniejące woluminy |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Listy metryk |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Listę definicji metryk |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Wyświetl wyniki operacji |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Listę woluminów |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Tworzy nową lub aktualizuje woluminów |
> | action | Microsoft.StorSimple/managers/devices/volumeContainers/write | Tworzy nową lub aktualizuje kontenery woluminów (8000 Series tylko) |
> | action | Microsoft.StorSimple/managers/devices/volumes/read | Listę woluminów |
> | action | Microsoft.StorSimple/managers/devices/write | Utwórz lub zaktualizuj urządzenia |
> | action | Microsoft.StorSimple/managers/encryptionSettings/read | Wyświetla lub pobiera ustawienia szyfrowania |
> | action | Microsoft.StorSimple/managers/extendedInformation/delete | Usuwa informacje o rozszerzonych magazynu |
> | action | Microsoft.StorSimple/Managers/extendedInformation/delete | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | action | Microsoft.StorSimple/managers/extendedInformation/read | Wyświetla lub pobiera rozszerzone informacje o magazynie |
> | action | Microsoft.StorSimple/Managers/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | action | Microsoft.StorSimple/managers/extendedInformation/write | Utwórz lub zaktualizuj rozszerzonych informacji o magazynie |
> | action | Microsoft.StorSimple/Managers/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | action | Microsoft.StorSimple/managers/features/read | Lista funkcji |
> | action | Microsoft.StorSimple/managers/fileservers/read | Wyświetla lub pobiera na serwerach plików |
> | action | Microsoft.StorSimple/managers/getEncryptionKey/action | Pobierz klucz szyfrowania dla Menedżera urządzeń. |
> | action | Microsoft.StorSimple/managers/iscsiservers/read | Wyświetla lub pobiera iSCSI serwerów |
> | action | Microsoft.StorSimple/managers/jobs/read | Wyświetla lub pobiera zadania |
> | action | Microsoft.StorSimple/managers/listActivationKey/action | Pobiera klucz aktywacji Menedżer urządzeń StorSimple. |
> | action | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Utwórz listę kluczy publicznych szyfrowania dla Menedżera urządzeń StorSimple. |
> | action | Microsoft.StorSimple/managers/metrics/read | Wyświetla lub pobiera metryki |
> | action | Microsoft.StorSimple/managers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | action | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrowanie do usługi Resource Manager z Mangers klasyczny |
> | action | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Zwraca listę konfiguracji źródłowej migracji (tylko w przypadku serii 8000) |
> | action | Microsoft.StorSimple/managers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Tworzenie nowego urządzenia w chmurze. |
> | action | Microsoft.StorSimple/managers/read | Wyświetla lub pobiera menedżerów urządzeń |
> | action | Microsoft.StorSimple/Managers/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | action | Microsoft.StorSimple/managers/regenerateActivationKey/action | Wygeneruj ponownie klucz aktywacji dla istniejących Menedżer urządzeń StorSimple. |
> | action | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Usuwa poświadczenia konta magazynu |
> | action | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/storageAccountCredentials/read | Wyświetla lub pobiera poświadczenia konta magazynu |
> | action | Microsoft.StorSimple/managers/storageAccountCredentials/write | Utwórz lub zaktualizuj poświadczenia konta magazynu |
> | action | Microsoft.StorSimple/managers/storageDomains/delete | Usuwa domeny magazynu |
> | action | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | action | Microsoft.StorSimple/managers/storageDomains/read | Wyświetla lub pobiera domeny magazynu |
> | action | Microsoft.StorSimple/managers/storageDomains/write | Utwórz lub zaktualizuj domeny magazynu |
> | action | Microsoft.StorSimple/managers/write | Utwórz lub zaktualizuj menedżerów urządzeń |
> | action | Microsoft.StorSimple/Managers/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |
> | action | Microsoft.StorSimple/operations/read | Wyświetla lub pobiera operacje |
> | action | Microsoft.StorSimple/register/action | Register Provider Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.StreamAnalytics/locations/quotas/Read | Odczyt Stream Analytics — limit przydziału subskrypcji |
> | action | Microsoft.StreamAnalytics/operations/Read | Operacje odczytu w Stream Analytics |
> | action | Microsoft.StreamAnalytics/Register/action | Zarejestruj subskrypcję za pomocą dostawcy zasobów usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/Delete | Usuń zadanie usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Usuń funkcję zadania usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Odczytaj wyniki operacji na funkcję zadania usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Stanowisko do odczytu Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Pobierz definicję domyślną funkcję zadania Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Test Stream Analytics stanowiska |
> | action | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Zapis funkcję zadania usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Usuń Stream Analytics — dane wejściowe zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Odczytaj wyniki operacji na dane wejściowe zadania analizy Stream |
> | action | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Odczyt Stream Analytics — dane wejściowe zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Przykładowe Stream Analytics — dane wejściowe zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Dane wejściowe testu Stream Analytics zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Zapis Stream Analytics — dane wejściowe zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Przeczytaj definicje metryk |
> | action | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Odczytaj wyniki operacji na zadanie usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Usuń Stream Analytics — dane wyjściowe zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Odczytaj wyniki operacji dla Stream Analytics — dane wyjściowe zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Odczyt Stream Analytics — dane wyjściowe zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Dane wyjściowe testu Stream Analytics zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Zapis Stream Analytics — dane wyjściowe zadania |
> | action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Odczytaj ustawienie diagnostyczne. |
> | action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Zapisz ustawienia diagnostyczne. |
> | action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla streamingjobs |
> | action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla streamingjobs |
> | action | Microsoft.StreamAnalytics/streamingjobs/Read | Odczyt zadanie usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/Start/action | Uruchom zadanie usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Zatrzymaj zadanie usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Usuń transformacja zadania usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Transformacja zadania usługi Stream Analytics odczytu |
> | action | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Zapis transformacja zadania usługi Stream Analytics |
> | action | Microsoft.StreamAnalytics/streamingjobs/Write | Zapis zadanie usługi Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Subscription/CreateSubscription/action | Utwórz subskrypcję platformy Azure |
> | action | Microsoft.Subscription/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.Subscription |
> | action | Microsoft.Subscription/SubscriptionDefinitions/read | Pobierz definicję subskrypcji platformy Azure w grupie zarządzania. |
> | action | Microsoft.Subscription/SubscriptionDefinitions/write | Tworzenie definicji subskrypcji platformy Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.Support/register/action | Przeprowadza rejestrację u dostawcy zasobów pomocy technicznej |
> | action | Microsoft.Support/supportTickets/read | Pobiera szczegóły biletu pomocy technicznej (w tym stan, ważność, szczegóły dotyczące kontaktu i wiadomości) lub pobiera listę biletów pomocy technicznej dla różnych subskrypcji. |
> | action | Microsoft.Support/supportTickets/write | Tworzy lub aktualizuje bilet pomocy technicznej. Można utworzyć bilet pomocy technicznej dotyczący problemów technicznych, rozliczeń, limitów przydziału lub problemów związanych z zarządzaniem subskrypcją. Można także zaktualizować ważność, szczegóły dotyczące kontaktu i komunikacji dla istniejących biletów pomocy technicznej. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Usuwa zasady dostępu. |
> | action | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Pobierz właściwości zasad dostępu. |
> | action | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Tworzy nową zasadę dostępu dla środowiska, lub aktualizuje istniejące zasady dostępu. |
> | action | Microsoft.TimeSeriesInsights/environments/delete | Usuwa środowisko. |
> | action | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Usuwa źródła zdarzeń. |
> | action | Microsoft.TimeSeriesInsights/environments/eventsources/read | Pobierz właściwości źródła zdarzenia. |
> | action | Microsoft.TimeSeriesInsights/environments/eventsources/write | Tworzy nowe źródło zdarzeń dla środowiska, lub aktualizuje istniejące źródło zdarzeń. |
> | action | Microsoft.TimeSeriesInsights/environments/read | Pobierz właściwości środowiska. |
> | action | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Usuwa odwołanie do zestawu danych. |
> | action | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Pobierz właściwości zestawu danych referencyjnych. |
> | action | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Tworzy nowego zestawu danych referencyjnych dla środowiska, lub aktualizuje istniejący zestaw danych referencyjnych. |
> | action | Microsoft.TimeSeriesInsights/environments/status/read | Pobierz stan środowiska w stanie jego skojarzony operacje, takie jak ruch przychodzący. |
> | action | Microsoft.TimeSeriesInsights/environments/write | Tworzy nowe środowisko, lub aktualizuje istniejące środowisko. |
> | action | Microsoft.TimeSeriesInsights/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Time Series Insights oraz umożliwia tworzenie środowiska usługi Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.VisualStudio/Account/Delete | Usuń konto |
> | action | Microsoft.VisualStudio/Account/Extension/Read | Rozszerzenie Account odczytu |
> | action | Microsoft.VisualStudio/Account/Project/Read | Odczytaj konto/projektu |
> | action | Microsoft.VisualStudio/Account/Project/Write | Ustaw konto/projekt |
> | action | Microsoft.VisualStudio/Account/Read | Odczytaj konto |
> | action | Microsoft.VisualStudio/Account/Write | Ustaw konto |
> | action | Microsoft.VisualStudio/Extension/Delete | Usuń rozszerzenie |
> | action | Microsoft.VisualStudio/Extension/Read | Rozszerzenie odczytu |
> | action | Microsoft.VisualStudio/Extension/Write | Rozszerzenia zestawu |
> | action | Microsoft.VisualStudio/Project/Delete | Usuń projekt |
> | action | Microsoft.VisualStudio/Project/Read | Odczyt projektu |
> | action | Microsoft.VisualStudio/Project/Write | Ustaw projekt |
> | action | Microsoft.VisualStudio/Register/Action | Zarejestruj subskrypcję platformy Azure przy użyciu dostawcy Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | microsoft.web/apimanagementaccounts/apiacls/read | Pobierz interfejs Api zarządzania kontami Apiacls. |
> | action | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Usuwanie interfejsu Api zarządzania konta interfejsów API Apiacls. |
> | action | microsoft.web/apimanagementaccounts/apis/apiacls/read | Pobierz interfejs Api zarządzania konta interfejsów API Apiacls. |
> | action | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Write | Aktualizowanie interfejsu Api zarządzania konta interfejsów API Apiacls. |
> | action | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Pobierz interfejs Api zarządzania konta interfejsów API Connectionacls. |
> | action | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Upewnij się, zgody kodu interfejsu Api zarządzania konta interfejsów API połączeń. |
> | action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Usuwanie interfejsu Api zarządzania konta interfejsów API połączeń Connectionacls. |
> | action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Pobierz interfejs Api zarządzania konta interfejsów API połączeń Connectionacls. |
> | action | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Write | Aktualizowanie interfejsu Api zarządzania konta interfejsów API połączeń Connectionacls. |
> | action | Microsoft.Web/apimanagementaccounts/APIs/Connections/DELETE | Usuwanie interfejsu Api zarządzania konta interfejsów API połączeń. |
> | action | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Pobieranie linków wyrażania zgody dla interfejsu Api zarządzania konta interfejsów API połączeń. |
> | action | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Lista połączeń klucze połączenia interfejsu Api zarządzania konta interfejsów API. |
> | action | Microsoft.Web/apimanagementaccounts/APIs/Connections/listsecrets/Action | Lista kluczy tajnych połączenia interfejsu Api zarządzania konta interfejsów API. |
> | action | microsoft.web/apimanagementaccounts/apis/connections/read | Pobierz interfejs Api zarządzania konta interfejsów API połączeń. |
> | action | Microsoft.Web/apimanagementaccounts/APIs/Connections/Write | Aktualizowanie interfejsu Api zarządzania konta interfejsów API połączeń. |
> | action | Microsoft.Web/apimanagementaccounts/APIs/DELETE | Usuń interfejsów API usługi Api Management kont. |
> | action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Usuwanie usługi Api Management interfejsy API kont zlokalizowane definicje. |
> | action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Uzyskaj usługi Api Management interfejsy API kont zlokalizowane definicje. |
> | action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Aktualizacja interfejsów Api zarządzania kontami usługi API zlokalizowany definicje. |
> | action | microsoft.web/apimanagementaccounts/apis/read | Pobierz interfejsy API usługi Api Management kont. |
> | action | Microsoft.Web/apimanagementaccounts/APIs/Write | Zaktualizuj interfejsów API usługi Api Management kont. |
> | action | microsoft.web/apimanagementaccounts/connectionacls/read | Pobierz interfejs Api zarządzania kontami Connectionacls. |
> | action | microsoft.web/availablestacks/read | Pobierz dostępne stosów. |
> | action | Microsoft.Web/certificates/Delete | Usunięcie istniejącego certyfikatu. |
> | action | Microsoft.Web/certificates/Read | Pobierz listę certyfikatów. |
> | action | Microsoft.Web/certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejącą. |
> | action | microsoft.web/checknameavailability/read | Sprawdź, czy nazwa zasobu jest dostępny. |
> | action | microsoft.web/classicmobileservices/read | Uzyskiwanie klasycznego usług Mobile Services. |
> | action | Microsoft.Web/connectionGateways/Delete | Usuwa bramy połączenia. |
> | action | Microsoft.Web/connectionGateways/Join/Action | Dołącza do bramy połączenia. |
> | action | Microsoft.Web/connectionGateways/ListStatus/Action | Wyświetla stan bramy połączenia. |
> | action | Microsoft.Web/connectionGateways/Move/Action | Przenosi bramy połączenia. |
> | action | Microsoft.Web/connectionGateways/Read | Pobierz listę bram połączenia. |
> | action | Microsoft.Web/connectionGateways/Write | Tworzy lub aktualizuje bramy połączenia. |
> | action | Microsoft.Web/Connections/confirmconsentcode/Action | Upewnij się, kod wyrażania zgody połączenia. |
> | action | Microsoft.Web/connections/Delete | Usuwa połączenie. |
> | action | Microsoft.Web/connections/Join/Action | Dołącza do połączenia. |
> | action | microsoft.web/connections/listconsentlinks/action | Listy linków wyrażania zgody dla połączeń. |
> | action | Microsoft.Web/connections/Move/Action | Przenosi połączenia. |
> | action | Microsoft.Web/connections/Read | Pobierz listę połączeń. |
> | action | Microsoft.Web/connections/Write | Tworzy lub aktualizuje połączenia. |
> | action | Microsoft.Web/customApis/Delete | Usuwa niestandardowego interfejsu API. |
> | action | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Wyodrębnia definicji interfejsu API z WSDL. |
> | action | Microsoft.Web/customApis/Join/Action | Dołącza do niestandardowego interfejsu API. |
> | action | Microsoft.Web/customApis/listWsdlInterfaces/Action | Wyświetla listę interfejsów WSDL dla niestandardowego interfejsu API. |
> | action | Microsoft.Web/customApis/Move/Action | Przenosi niestandardowego interfejsu API. |
> | action | Microsoft.Web/customApis/Read | Pobierz listę niestandardowego interfejsu API. |
> | action | Microsoft.Web/customApis/Write | Tworzy lub aktualizuje niestandardowego interfejsu API. |
> | action | Microsoft.Web/deletedSites/Read | Pobierz właściwości aplikacji sieci Web usunięto |
> | action | microsoft.web/deploymentlocations/read | Uzyskanie lokalizacji wdrożenia. |
> | action | Microsoft.Web/geoRegions/Read | Pobierz listę regionów geograficznych. |
> | action | microsoft.web/hostingenvironments/capacities/read | Pobierz hostingu możliwości środowiska. |
> | action | Microsoft.Web/hostingEnvironments/Delete | Usuń środowisko App Service Environment |
> | action | microsoft.web/hostingenvironments/detectors/read | Pobierz hostingu detektory środowisk. |
> | action | microsoft.web/hostingenvironments/diagnostics/read | Pobierz hostingu środowiska diagnostyki. |
> | action | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Pobierz punkty końcowe sieci wszystkie zależności dla ruchu przychodzącego. |
> | action | Microsoft.Web/hostingEnvironments/Join/Action | Dołącza środowisko App Service Environment |
> | action | microsoft.web/hostingenvironments/metricdefinitions/read | Pobierz hostingu definicje metryk środowiska. |
> | action | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Pobierz hostingu definicje metryk pule pełniących wiele środowisk. |
> | action | microsoft.web/hostingenvironments/multirolepools/metrics/read | Pobierz hostingu środowisk pełniących wiele pul metryki. |
> | action | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Pobierz właściwości puli frontonu w środowisku usługi App Service |
> | action | microsoft.web/hostingenvironments/multirolepools/skus/read | Pobierz hostingu środowisk pełniących wiele pul w jednostki SKU. |
> | action | microsoft.web/hostingenvironments/multirolepools/usages/read | Pobierz hostingu środowisk pełniących wiele pul użycia. |
> | action | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Tworzenie nowej puli frontonu w środowisku usługi App Service lub zaktualizuj istniejącą |
> | action | microsoft.web/hostingenvironments/operations/read | Pobierz hostingu operacji środowiska. |
> | action | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Pobierz punkty końcowe sieci wszystkie zależności ruchu wychodzącego. |
> | action | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Połączenia prywatne punktu końcowego zatwierdzenia |
> | action | Microsoft.Web/hostingEnvironments/Read | Pobierz właściwości środowiska usługi App Service |
> | action | Microsoft.Web/hostingEnvironments/reboot/Action | Ponowne uruchomienie wszystkich maszyn w środowisku usługi App Service |
> | action | Microsoft.Web/hostingenvironments/resume/Action | Wznów, środowiskach hostingu. |
> | action | microsoft.web/hostingenvironments/serverfarms/read | Uzyskaj plany usługi App Service środowiska hostingu. |
> | action | microsoft.web/hostingenvironments/sites/read | Uzyskaj hostowanie aplikacji sieci Web środowiska. |
> | action | Microsoft.Web/hostingenvironments/suspend/Action | Wstrzymaj, środowiskach hostingu. |
> | action | microsoft.web/hostingenvironments/usages/read | Pobierz hostingu użycia środowiska. |
> | action | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Pobierz hostingu definicje metryki Workerpools środowisk. |
> | action | microsoft.web/hostingenvironments/workerpools/metrics/read | Pobierz hostingu środowiska Workerpools metryki. |
> | action | Microsoft.Web/hostingEnvironments/workerPools/Read | Pobierz właściwości puli procesów roboczych w środowisku usługi App Service |
> | action | microsoft.web/hostingenvironments/workerpools/skus/read | Pobierz hostingu jednostki SKU Workerpools środowisk. |
> | action | microsoft.web/hostingenvironments/workerpools/usages/read | Pobierz hostingu środowiska Workerpools użycia. |
> | action | Microsoft.Web/hostingEnvironments/workerPools/Write | Tworzenie nowej puli procesów roboczych w środowisku usługi App Service lub zaktualizuj istniejącą |
> | action | Microsoft.Web/hostingEnvironments/Write | Utwórz nowe środowisko App Service Environment lub zaktualizuj istniejącą grupę |
> | action | microsoft.web/ishostingenvironmentnameavailable/read | GET, jeśli nazwa środowiska hostingu jest dostępna. |
> | action | microsoft.web/ishostnameavailable/read | Sprawdź, czy nazwa hosta jest dostępny. |
> | action | microsoft.web/isusernameavailable/read | Sprawdź, czy nazwa użytkownika jest dostępna. |
> | action | Microsoft.Web/listSitesAssignedToHostName/Read | Pobierz nazwy lokacji przypisany do nazwy hosta. |
> | action | microsoft.web/locations/apioperations/read | Pobierz operacje lokalizacji interfejsu API. |
> | action | microsoft.web/locations/connectiongatewayinstallations/read | Rozpoczynanie instalacji bramy połączenia lokalizacji. |
> | action | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Sieć wirtualna lub podsieć powiadomienie usunięcia dla lokalizacji. |
> | action | microsoft.web/locations/extractapidefinitionfromwsdl/action | Prowadzenie definicji interfejsu Api WSDL dla lokalizacji. |
> | action | microsoft.web/locations/listwsdlinterfaces/action | Interfejsy WSDL listy dla lokalizacji. |
> | action | microsoft.web/locations/managedapis/apioperations/read | Pobierz operacje zarządzany interfejs API lokalizacji. |
> | action | Microsoft.Web/locations/managedapis/Join/Action | Tworzy sprzężenie zarządzany interfejs API. |
> | action | microsoft.web/locations/managedapis/read | Pobierz lokalizacje zarządzanych interfejsów API. |
> | action | microsoft.web/operations/read | Pobierz operacje. |
> | action | microsoft.web/publishingusers/read | Rozpoczynanie publikowania użytkowników. |
> | action | microsoft.web/publishingusers/write | Aktualizacja publikowanie użytkowników. |
> | action | Microsoft.Web/recommendations/Read | Pobierz listę zaleceń dla subskrypcji. |
> | action | Microsoft.Web/Register/Action | Rejestrowanie dostawcy zasobów Microsoft.Web dla subskrypcji. |
> | action | microsoft.web/resourcehealthmetadata/read | Pobierz metadane kondycji zasobu. |
> | action | microsoft.web/serverfarms/capabilities/read | Skorzystaj z możliwości planów usługi App Service. |
> | action | Microsoft.Web/serverfarms/Delete | Usuń istniejący Plan usługi App Service |
> | action | microsoft.web/serverfarms/firstpartyapps/settings/delete | Usuń ustawienia aplikacji innych firm pierwszy planów usługi App Service. |
> | action | microsoft.web/serverfarms/firstpartyapps/settings/read | Pobierz ustawienia aplikacji innych firm pierwszy planów usługi App Service. |
> | action | microsoft.web/serverfarms/firstpartyapps/settings/write | Aktualizowanie ustawień aplikacji innych firm pierwszy planów usługi App Service. |
> | action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Usuń usługi App Service plany hybrydowe połączenie przestrzeni nazw przekaźników. |
> | action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Uzyskaj usługi App Service plany hybrydowe połączenie przestrzeni nazw przekaźników. |
> | action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Uzyskaj hybrydowe planów usługi App Service połączenia aplikacje sieci Web przekaźniki przestrzeni nazw. |
> | action | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Uzyskaj limity planu połączeń hybrydowych planów usługi App Service. |
> | action | microsoft.web/serverfarms/hybridconnectionrelays/read | Uzyskaj przekaźniki połączenia hybrydowego planów usługi App Service. |
> | action | microsoft.web/serverfarms/metricdefinitions/read | Pobierz definicje metryk planów usługi App Service. |
> | action | microsoft.web/serverfarms/metrics/read | Pobierz metryki planów usługi App Service. |
> | action | microsoft.web/serverfarms/operationresults/read | Pobierz wyniki operacji planów usługi App Service. |
> | action | Microsoft.Web/serverfarms/Read | Pobiera właściwości planu usługi App Service |
> | action | Microsoft.Web/serverfarms/restartSites/Action | Uruchom ponownie wszystkie aplikacje sieci Web w planie usługi App Service |
> | action | microsoft.web/serverfarms/sites/read | Pobierz aplikacje sieci Web planów usługi App Service. |
> | action | microsoft.web/serverfarms/skus/read | Pobierz jednostki SKU planów usługi App Service. |
> | action | microsoft.web/serverfarms/usages/read | Pobierz użycia planów usługi App Service. |
> | action | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Zaktualizuj bramy połączenia sieci wirtualnej planów usługi App Service. |
> | action | microsoft.web/serverfarms/virtualnetworkconnections/read | Uzyskaj połączenia sieć wirtualna planów usługi App Service. |
> | action | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Usuń trasy połączenia sieć wirtualna planów usługi App Service. |
> | action | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Uzyskaj tras połączenia sieci wirtualnych planów usługi App Service. |
> | action | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Aktualizowanie tras połączenia sieci wirtualnych planów usługi App Service. |
> | action | microsoft.web/serverfarms/workers/reboot/action | Uruchom ponownie pracowników planów usługi App Service. |
> | action | Microsoft.Web/serverfarms/Write | Utwórz nowy Plan usługi App Service lub zaktualizuj istniejącą |
> | action | microsoft.web/sites/analyzecustomhostname/read | Analizuj niestandardową nazwę hosta. |
> | action | Microsoft.Web/sites/applySlotConfig/Action | Zastosuj konfigurację sieci web app miejsca w docelowym gnieździe w bieżącej aplikacji sieci web |
> | action | Microsoft.Web/sites/backup/Action | Utwórz nową kopię zapasową aplikacji sieci web |
> | action | Microsoft.Web/Sites/Backup/Read | Uzyskaj kopii zapasowej aplikacji sieci Web. |
> | action | microsoft.web/sites/backup/write | Aktualizacja kopii zapasowej aplikacji sieci Web. |
> | action | microsoft.web/sites/backups/action | Odnajduje istniejącą kopię zapasową aplikacji, którą można przywrócić z obiektu blob w usłudze Azure storage. |
> | action | microsoft.web/sites/backups/delete | Usuwanie kopii zapasowych aplikacji internetowych. |
> | action | microsoft.web/sites/backups/list/action | Wykaz kopii zapasowych aplikacji sieci Web. |
> | action | Microsoft.Web/sites/backups/Read | Pobierz właściwości elementu kopii zapasowej aplikacji sieci web |
> | action | Microsoft.Web/Sites/Backups/Restore/Action | Przywracanie kopii zapasowych aplikacji internetowych. |
> | action | microsoft.web/sites/backups/write | Aktualizowanie kopii zapasowych aplikacji internetowych. |
> | action | Microsoft.Web/Sites/config/DELETE | Usuwanie konfiguracji aplikacji sieci Web. |
> | action | Microsoft.Web/sites/config/list/Action | Lista aplikacji sieci Web poufne ustawienia zabezpieczeń, takich jak publikowanie poświadczeń, ustawienia aplikacji i parametrów połączenia |
> | action | Microsoft.Web/sites/config/Read | Pobieranie ustawień konfiguracji aplikacji sieci Web |
> | action | microsoft.web/sites/config/snapshots/read | Pobierz migawki konfiguracji aplikacji sieci Web. |
> | action | Microsoft.Web/sites/config/Write | Aktualizowanie ustawień konfiguracji aplikacji sieci Web |
> | action | Microsoft.Web/Sites/containerlogs/Action | Pobierz pliki z rozszerzeniem zip dzienniki kontenerów dla aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/containerlogs/Download/Action | Pobierz dzienniki kontenerów aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/continuouswebjobs/DELETE | Usuwanie zadania Webjob ciągłej aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/continuouswebjobs/Read | Pobierz zadania ciągłego sieci Web aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/continuouswebjobs/Start/Action | Uruchom zadania ciągłego sieci Web aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/continuouswebjobs/Stop/Action | Zatrzymywanie zadania Webjob ciągłej aplikacji sieci Web. |
> | action | Microsoft.Web/sites/Delete | Usuń istniejącą aplikację sieci Web |
> | action | microsoft.web/sites/deployments/delete | Usunięcie wdrożenia aplikacji sieci Web. |
> | action | microsoft.web/sites/deployments/log/read | Pobierz dziennik wdrożenia aplikacji sieci Web. |
> | action | microsoft.web/sites/deployments/read | Uzyskaj wdrożenia aplikacji sieci Web. |
> | action | microsoft.web/sites/deployments/write | Aktualizowanie wdrożenia aplikacji sieci Web. |
> | action | microsoft.web/sites/detectors/read | Uzyskaj detektory aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/analyses/execute/Action | Przeprowadź analizę Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/analyses/read | Uzyskaj analizy Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/aspnetcore/read | Uzyskiwanie diagnostyki aplikacji sieci Web dla aplikacji platformy ASP.NET Core. |
> | action | microsoft.web/sites/diagnostics/autoheal/read | Uzyskaj funkcji Autoheal diagnostyki aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/deployment/read | Pobierz wdrożenie diagnostyki aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/deployments/read | Uzyskaj wdrożeń Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/detectors/execute/Action | Uruchom wykrywanie diagnostyki aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/detectors/read | Uzyskaj wykrywanie diagnostyki aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Uzyskaj żądania sieci Web diagnostyki aplikacji nie powiodło się dla identyfikatora Uri. |
> | action | microsoft.web/sites/diagnostics/frebanalysis/read | Uzyskaj analizy FREB diagnostyki aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/loganalyzer/read | Uzyskaj analizator dzienników diagnostyki aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/read | Uzyskaj kategorie Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/runtimeavailability/read | Uzyskaj dostępność środowiska uruchomieniowego Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/servicehealth/read | Pobierz stan usługi Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Uzyskaj analizy użycia Procesora witryny Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/sitecrashes/read | Uzyskaj awarii lokacji Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/sitelatency/read | Uzyskaj czas oczekiwania witryny Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Uzyskaj analizy pamięci witryny Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Uzyskaj Aktualizacja ustawień ponowne uruchomienie witryny Diagnostyka aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Diagnostics/siterestartuserinitiated/Read | Uzyskaj sieci Web diagnostyki aplikacji witryny inicjowane przez ponowne uruchomienie użytkownika. |
> | action | microsoft.web/sites/diagnostics/siteswap/read | Uzyskaj wymiany lokacji Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/threadcount/read | Pobieranie liczby wątków Diagnostyka aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/workeravailability/read | Uzyskaj Workeravailability diagnostyki aplikacji sieci Web. |
> | action | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Uzyskaj odtworzenia procesu roboczego Diagnostyka aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/domainownershipidentifiers/Read | Pobierz identyfikatory własności domeny aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/domainownershipidentifiers/Write | Aktualizuj identyfikatory własności domeny aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Extensions/DELETE | Usuwanie rozszerzenia aplikacji sieci Web. |
> | action | microsoft.web/sites/extensions/read | Można pobierać rozszerzenia witryny aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Extensions/Write | Aktualizowanie rozszerzenia aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Functions/Action | Funkcje Web Apps. |
> | action | Microsoft.Web/Sites/Functions/DELETE | Usuwanie funkcji aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Functions/listsecrets/Action | Listy kluczy tajnych funkcji aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Functions/masterkey/Read | Uzyskaj Masterkey funkcje aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Functions/Read | Uzyskaj funkcje aplikacji sieci Web. |
> | action | microsoft.web/sites/functions/token/read | Token funkcje aplikacji sieci Web GET. |
> | action | Microsoft.Web/Sites/Functions/Write | Aktualizuj funkcje aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/hostnamebindings/DELETE | Usuwanie powiązania nazwy hosta aplikacji sieci Web. |
> | action | microsoft.web/sites/hostnamebindings/read | Uzyskaj powiązania nazwy hosta aplikacji sieci Web. |
> | action | microsoft.web/sites/hostnamebindings/write | Zaktualizuj powiązania nazwy hosta aplikacji sieci Web. |
> | action | microsoft.web/sites/hostruntime/functions/keys/read | Pobierz klucze funkcji Hostruntime aplikacji sieci Web. |
> | action | Microsoft.Web/sites/hostruntime/host/_master/read | Pobierz klucz główny w aplikacji funkcji dla operacji administratora |
> | action | Microsoft.Web/sites/hostruntime/host/action | Wykonywanie aplikacji funkcji środowiska uruchomieniowego akcji, takich jak synchronizowanie wyzwalaczy, Dodaj funkcje, wywoływać funkcje i usuwanie funkcji itp. |
> | action | Microsoft.Web/Sites/hostruntime/host/Read | Uzyskiwanie hosta Hostruntime aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/hybridconnection/DELETE | Usuwanie połączenia hybrydowego aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/hybridconnection/Read | Uzyskaj połączenie hybrydowe aplikacje sieci Web. |
> | action | Microsoft.Web/Sites/hybridconnection/Write | Zaktualizuj połączenie hybrydowe aplikacje sieci Web. |
> | action | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/DELETE | Usuń przekaźniki przestrzenie nazw połączenia hybrydowe aplikacje sieci Web. |
> | action | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Lista kluczy sieci Web aplikacji hybrydowych połączenia przestrzenie nazw przekaźników. |
> | action | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Uzyskaj przekaźniki przestrzenie nazw połączenia hybrydowe aplikacje sieci Web. |
> | action | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Zaktualizuj przekaźniki przestrzenie nazw połączenia hybrydowe aplikacje sieci Web. |
> | action | Microsoft.Web/Sites/hybridconnectionrelays/Read | Uzyskaj przekaźniki połączenia hybrydowe aplikacje sieci Web. |
> | action | Microsoft.Web/Sites/instances/Deployments/DELETE | Usuwanie wdrożenia wystąpienia aplikacji sieci Web. |
> | action | microsoft.web/sites/instances/deployments/read | Uzyskaj wdrożenia wystąpienia aplikacji sieci Web. |
> | action | microsoft.web/sites/instances/extensions/log/read | Pobierz dziennik rozszerzenia wystąpienia aplikacji sieci Web. |
> | action | microsoft.web/sites/instances/extensions/processes/read | Uzyskaj procesów rozszerzenia wystąpienia aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/instances/Extensions/Read | Można pobierać rozszerzenia wystąpienia aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/instances/processes/DELETE | Usuń procesy wystąpienia aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/instances/processes/Modules/Read | Pobierz moduły procesy wystąpienia aplikacji sieci Web. |
> | action | microsoft.web/sites/instances/processes/read | Uzyskaj procesy wystąpienia aplikacji sieci Web. |
> | action | microsoft.web/sites/instances/processes/threads/read | Rozpoczynanie wątków procesów wystąpienia aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/instances/Read | Pobierz wystąpienia aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/listsyncfunctiontriggerstatus/Action | Lista synchronizacji funkcji wyzwalacza stan aplikacji sieci Web. |
> | action | microsoft.web/sites/metricdefinitions/read | Pobierz definicje metryki aplikacji sieci Web. |
> | action | microsoft.web/sites/metrics/read | Pobierz metryki aplikacji sieci Web. |
> | action | microsoft.web/sites/metricsdefinitions/read | Pobierz definicje metryk aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/migratemysql/Action | Przeprowadź migrację aplikacji sieci Web MySql. |
> | action | microsoft.web/sites/migratemysql/read | Get Web Apps Migrate MySql. |
> | action | microsoft.web/sites/networktrace/action | Network Trace Web Apps. |
> | action | microsoft.web/sites/networktraces/operationresults/read | Pobierz wyniki operacji śledzenia aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/newpassword/Action | Newpassword Web Apps. |
> | action | Microsoft.Web/Sites/operationresults/Read | Pobierz wyniki operacji aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Operations/Read | Pobierz operacje aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/perfcounters/Read | Pobierz liczniki wydajności aplikacji sieci Web. |
> | action | microsoft.web/sites/premieraddons/delete | Usuń dodatków Premier aplikacji sieci Web. |
> | action | microsoft.web/sites/premieraddons/read | Uzyskaj dodatków Premier aplikacji sieci Web. |
> | action | microsoft.web/sites/premieraddons/write | Zaktualizuj dodatków Premier aplikacji sieci Web. |
> | action | microsoft.web/sites/privateaccess/read | Pobierz dane dotyczące włączania dostępu do prywatnej witryny i autoryzowanych sieci wirtualnych, które mogą uzyskać dostęp do witryny. |
> | action | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Połączenia prywatne punktu końcowego zatwierdzenia |
> | action | microsoft.web/sites/processes/modules/read | Pobierz moduły procesów aplikacji sieci Web. |
> | action | microsoft.web/sites/processes/read | Uzyskaj procesy aplikacji sieci Web. |
> | action | microsoft.web/sites/processes/threads/read | Rozpoczynanie wątków procesów aplikacji sieci Web. |
> | action | microsoft.web/sites/publiccertificates/delete | Usuń certyfikat publiczny aplikacji sieci Web. |
> | action | microsoft.web/sites/publiccertificates/read | Pobierz certyfikat publiczny aplikacji sieci Web. |
> | action | microsoft.web/sites/publiccertificates/write | Aktualizuj certyfikaty publiczne aplikacji sieci Web. |
> | action | Microsoft.Web/sites/publish/Action | Publikowanie aplikacji sieci Web |
> | action | Microsoft.Web/sites/publishxml/Action | Rozpoczynanie publikowania pliku xml profilu dla aplikacji sieci Web |
> | action | microsoft.web/sites/publishxml/read | Pobierać aplikacje sieci Web, publikowanie XML. |
> | action | Microsoft.Web/sites/Read | Pobierz właściwości aplikacji sieci Web |
> | action | microsoft.web/sites/recommendationhistory/read | Pobieranie historii zalecenie dotyczące aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/recommendations/disable/Action | Wyłącz zalecenia dotyczące aplikacji sieci Web. |
> | action | Microsoft.Web/sites/recommendations/Read | Pobierz listę zaleceń dotyczących aplikacji sieci web. |
> | action | Microsoft.Web/Sites/Recover/Action | Odzyskiwanie aplikacji sieci Web. |
> | action | Microsoft.Web/sites/resetSlotConfig/Action | Zresetuj konfigurację aplikacji sieci web |
> | action | Microsoft.Web/Sites/resourcehealthmetadata/Read | Pobierz metadane kondycji zasobów aplikacji sieci Web. |
> | action | Microsoft.Web/sites/restart/Action | Uruchom ponownie aplikację internetową |
> | action | microsoft.web/sites/restore/read | Rozpoczynanie przywracania aplikacji sieci Web. |
> | action | microsoft.web/sites/restore/write | Restore Web Apps. |
> | action | microsoft.web/sites/restorefrombackupblob/action | Przywracanie aplikacji internetowej z kopii zapasowej magazynu obiektów Blob. |
> | action | microsoft.web/sites/restorefromdeletedwebapp/action | Przywracanie aplikacji sieci Web z usunięto aplikację. |
> | action | Microsoft.Web/Sites/restoresnapshot/Action | Przywracanie migawki aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/siteextensions/DELETE | Usuwanie rozszerzenia aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/siteextensions/Read | Można pobierać rozszerzenia witryny aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/siteextensions/Write | Aktualizowanie rozszerzenia aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/analyzecustomhostname/read | Pobierz Web miejsc aplikacji analizowanie niestandardową nazwę hosta. |
> | action | Microsoft.Web/sites/slots/applySlotConfig/Action | Zastosuj konfigurację sieci web app miejsca w docelowym gnieździe do bieżącego miejsca. |
> | action | Microsoft.Web/sites/slots/backup/Action | Utwórz nową kopię zapasową miejsca aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/backup/read | Pobierz kopię zapasową miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/backup/write | Aktualizacja kopii zapasowej miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/backups/action | Wykryj kopie zapasowe miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/backups/delete | Usuwanie kopii zapasowych miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/backups/list/action | Lista miejsca kopii zapasowych aplikacji internetowych. |
> | action | Microsoft.Web/sites/slots/backups/Read | Pobierz właściwości kopia zapasowa miejsc aplikacji sieci web |
> | action | microsoft.web/sites/slots/backups/restore/action | Przywracanie kopii zapasowych miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/config/delete | Usuwanie konfiguracji miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/sites/slots/config/list/Action | Lista miejsca aplikacji sieci Web poufne ustawienia zabezpieczeń, takich jak publikowanie poświadczeń, ustawienia aplikacji i parametrów połączenia |
> | action | Microsoft.Web/sites/slots/config/Read | Pobieranie ustawień konfiguracji miejsca aplikacji sieci Web |
> | action | Microsoft.Web/sites/slots/config/Write | Aktualizowanie ustawień konfiguracji miejsca aplikacji sieci Web |
> | action | Microsoft.Web/Sites/Slots/containerlogs/Action | Pobierz pliki z rozszerzeniem zip dzienniki kontenera dla miejsca aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/containerlogs/download/action | Pobierz dzienniki kontenerów miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/continuouswebjobs/DELETE | Usuwanie sieci Web Apps miejsc ciągłe zadania Web Job. |
> | action | Microsoft.Web/Sites/Slots/continuouswebjobs/Read | Uzyskaj Web Apps miejsc ciągłe zadania Web Job. |
> | action | Microsoft.Web/Sites/Slots/continuouswebjobs/Start/Action | Uruchom Web Apps miejsc ciągłe zadania Web Job. |
> | action | Microsoft.Web/Sites/Slots/continuouswebjobs/Stop/Action | Zatrzymaj Web Apps miejsc ciągłe zadania Web Job. |
> | action | Microsoft.Web/sites/slots/Delete | Usuwanie istniejącego miejsca aplikacji sieci Web |
> | action | microsoft.web/sites/slots/deployments/delete | Usuwanie wdrożenia miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/deployments/log/read | Pobierz dziennik wdrożeń miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/deployments/read | Uzyskaj wdrożeń miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/deployments/write | Aktualizowanie wdrożenia miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/detectors/read | Uzyskaj detektory miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Uruchom analizę diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/analyses/read | Uzyskaj analizy diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Uzyskiwanie diagnostyki miejsc aplikacji sieci Web dla aplikacji platformy ASP.NET Core. |
> | action | microsoft.web/sites/slots/diagnostics/autoheal/read | Uzyskaj Web Apps miejsc diagnostyki w funkcji Autoheal. |
> | action | microsoft.web/sites/slots/diagnostics/deployment/read | Rozpoczynanie wdrażanie diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/deployments/read | Uzyskaj wdrożeń diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Uruchom wykrywanie diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/detectors/read | Uzyskaj wykrywanie diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Uzyskaj Web Apps miejsc diagnostyki FREB analizy. |
> | action | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Uzyskaj sieci Web Apps miejsc diagnostyki dziennika analizatora. |
> | action | microsoft.web/sites/slots/diagnostics/read | Pobierz diagnostykę miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Uzyskaj dostępność diagnostyki gniazda sieci Web aplikacji w czasie wykonywania. |
> | action | microsoft.web/sites/slots/diagnostics/servicehealth/read | Pobierz stan usługi Diagnostyka miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Uzyskaj analizy Procesora witryny diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Uzyskaj sieci Web Apps miejsc Diagnostyka witryny awarie. |
> | action | microsoft.web/sites/slots/diagnostics/sitelatency/read | Uzyskaj czas oczekiwania witryny diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Uzyskaj analizy pamięci witryny diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Uzyskaj miejsc aplikacji sieci Web diagnostyki Aktualizacja ustawień ponowne uruchomienie witryny. |
> | action | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Uzyskaj sieci Web Apps miejsc diagnostyki lokacji ponowne uruchomienie inicjowane przez użytkownika. |
> | action | microsoft.web/sites/slots/diagnostics/siteswap/read | Uzyskaj Web Apps miejsc diagnostyki lokacji wymiany. |
> | action | microsoft.web/sites/slots/diagnostics/threadcount/read | Pobieranie liczby wątków diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/workeravailability/read | Uzyskaj Workeravailability diagnostyki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Uzyskaj odtworzenia procesu procesu roboczego diagnostyki miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/domainownershipidentifiers/Read | Uzyskaj sieci Web Apps miejsc domeny własność identyfikatorów. |
> | action | Microsoft.Web/Sites/Slots/Functions/listsecrets/Action | Funkcji miejsc listy wpisów tajnych w sieci Web aplikacji. |
> | action | microsoft.web/sites/slots/functions/read | Uzyskaj funkcji miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/hostnamebindings/delete | Usuwanie powiązania nazwy hosta miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/hostnamebindings/read | Uzyskaj powiązania nazwy hosta miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/hostnamebindings/write | Zaktualizuj powiązania nazwy hosta miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/hybridconnection/DELETE | Usuwanie połączenia hybrydowego miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/hybridconnection/Read | Uzyskaj połączenie hybrydowe miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/hybridconnection/Write | Aktualizowanie połączenia hybrydowego miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Usuwanie sieci Web aplikacji miejsc hybrydowe połączenie przestrzeni nazw przekaźników. |
> | action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Aktualizowanie sieci Web aplikacji miejsc hybrydowe połączenie przestrzeni nazw przekaźników. |
> | action | microsoft.web/sites/slots/hybridconnectionrelays/read | Uzyskaj sieci Web Apps miejsc hybrydowego połączenia przekaźników. |
> | action | microsoft.web/sites/slots/instances/deployments/read | Uzyskaj wdrożenia wystąpienia miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/instances/processes/delete | Usuń procesy wystąpień miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/instances/processes/read | Uzyskaj procesy wystąpień miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/instances/read | Pobierz wystąpienia miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/metricdefinitions/read | Pobierz definicje metryk miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/metrics/read | Pobierz metryki miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/migratemysql/read | Pobierz Web miejsc aplikacji migracji MySql. |
> | action | microsoft.web/sites/slots/networktrace/action | Miejsc aplikacji sieci Web śledzenia sieci. |
> | action | microsoft.web/sites/slots/networktraces/operationresults/read | Pobierz wyniki operacji śledzenia sieci na miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/newpassword/Action | Miejsc aplikacji sieci Web Newpassword. |
> | action | microsoft.web/sites/slots/operationresults/read | Pobierz wyniki operacji miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/operations/read | Pobierz operacje miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/perfcounters/Read | Pobierz liczniki wydajności miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/phplogging/read | Uzyskaj Phplogging miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/premieraddons/delete | Usuń dodatków Premier miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/premieraddons/read | Uzyskaj dodatków Premier miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/premieraddons/write | Zaktualizuj dodatków Premier miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/processes/read | Uzyskaj procesy miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/publiccertificates/delete | Usuń certyfikaty publiczne miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/publiccertificates/read | Pobierz certyfikaty publiczne miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/publiccertificates/write | Tworzenie lub aktualizowanie certyfikatów publicznych miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/sites/slots/publish/Action | Publikowanie miejsca aplikacji sieci Web |
> | action | Microsoft.Web/sites/slots/publishxml/Action | Rozpoczynanie publikowania pliku xml profilu dla miejsca aplikacji sieci Web |
> | action | Microsoft.Web/sites/slots/Read | Pobierz właściwości miejsce wdrożenia aplikacji sieci Web |
> | action | Microsoft.Web/Sites/Slots/Recover/Action | Odzyskaj miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/sites/slots/resetSlotConfig/Action | Resetowanie konfiguracji miejsca aplikacji sieci web |
> | action | Microsoft.Web/Sites/Slots/resourcehealthmetadata/Read | Pobierz metadane kondycji zasobu miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/sites/slots/restart/Action | Uruchom ponownie miejsca aplikacji sieci Web |
> | action | microsoft.web/sites/slots/restore/read | Rozpoczynanie przywracania miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/restore/write | Przywróć miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/restorefrombackupblob/action | Przywróć miejsca aplikacji sieci Web z obiektów Blob kopii zapasowej. |
> | action | microsoft.web/sites/slots/restorefromdeletedwebapp/action | Przywróć miejsc aplikacji sieci Web z usunięto aplikację. |
> | action | Microsoft.Web/Sites/Slots/restoresnapshot/Action | Przywracanie migawki miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/siteextensions/DELETE | Usuń rozszerzenia witryny miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/siteextensions/read | Pobierz rozszerzenia witryny miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/siteextensions/Write | Aktualizowanie rozszerzeń witryny miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/sites/slots/slotsdiffs/Action | Pobierz różnic w konfiguracji aplikacji sieci web i gniazd |
> | action | Microsoft.Web/sites/slots/slotsswap/Action | Zamiana miejsc wdrożenia aplikacji sieci Web |
> | action | microsoft.web/sites/slots/snapshots/read | Pobierz migawki miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/sites/slots/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | action | Microsoft.Web/sites/slots/sourcecontrols/Read | Pobierz ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | action | Microsoft.Web/sites/slots/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | action | Microsoft.Web/sites/slots/start/Action | Rozpocznij miejsca aplikacji sieci Web |
> | action | Microsoft.Web/sites/slots/stop/Action | Zatrzymaj miejsca aplikacji sieci Web |
> | action | Microsoft.Web/Sites/Slots/Sync/Action | Synchronizacja miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/triggeredwebjobs/delete | Usuwanie zadania Webjob wyzwalane miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/triggeredwebjobs/read | Rozpoczynanie zadania Webjob wyzwalane miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/triggeredwebjobs/Run/Action | Uruchamianie zadania Webjob wyzwalane miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/usages/read | Pobierz użycia miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/virtualnetworkconnections/delete | Usuwanie połączeń sieci wirtualnej miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Zaktualizuj bramy połączenia sieci wirtualnej miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/virtualnetworkconnections/read | Uzyskaj połączeń sieci wirtualnej miejsc aplikacji sieci Web. |
> | action | microsoft.web/sites/slots/virtualnetworkconnections/write | Aktualizowanie połączenia sieć wirtualna miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/Slots/webjobs/Read | Rozpoczynanie zadania Webjob miejsc aplikacji sieci Web. |
> | action | Microsoft.Web/sites/slots/Write | Utwórz nowe miejsce aplikacji sieci Web lub zaktualizuj istniejącą |
> | action | Microsoft.Web/sites/slotsdiffs/Action | Pobierz różnic w konfiguracji aplikacji sieci web i gniazd |
> | action | Microsoft.Web/sites/slotsswap/Action | Zamiana miejsc wdrożenia aplikacji sieci Web |
> | action | microsoft.web/sites/snapshots/read | Pobierz migawki aplikacji sieci Web. |
> | action | Microsoft.Web/sites/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła dla aplikacji sieci Web |
> | action | Microsoft.Web/sites/sourcecontrols/Read | Pobieranie ustawień konfiguracji aplikacji sieci Web kontroli źródła |
> | action | Microsoft.Web/sites/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła dla aplikacji sieci Web |
> | action | Microsoft.Web/sites/start/Action | Uruchamiają aplikację sieci Web |
> | action | Microsoft.Web/sites/stop/Action | Zatrzymaj aplikację sieci Web |
> | action | Microsoft.Web/Sites/Sync/Action | Synchronizuj aplikacje sieci Web. |
> | action | Microsoft.Web/Sites/syncfunctiontriggers/Action | Wyzwalacze funkcji synchronizacji dla aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/triggeredwebjobs/DELETE | Usuń wyzwolone zadania Webjob w aplikacji sieci Web. |
> | action | microsoft.web/sites/triggeredwebjobs/history/read | Pobieranie historii wyzwolone zadania Webjob aplikacji sieci Web. |
> | action | microsoft.web/sites/triggeredwebjobs/read | Uzyskaj wyzwolone zadania Webjob w aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/triggeredwebjobs/Run/Action | Aplikacje sieci Web wykonywania wyzwolone zadania Webjob. |
> | action | microsoft.web/sites/usages/read | Pobierz użycia aplikacji sieci Web. |
> | action | microsoft.web/sites/virtualnetworkconnections/delete | Usuwanie połączenia sieć wirtualna aplikacji sieci Web. |
> | action | microsoft.web/sites/virtualnetworkconnections/gateways/read | Pobierz bramy połączenia sieci wirtualnej aplikacji sieci Web. |
> | action | microsoft.web/sites/virtualnetworkconnections/gateways/write | Zaktualizuj bramy połączenia sieci wirtualnej aplikacji sieci Web. |
> | action | microsoft.web/sites/virtualnetworkconnections/read | Uzyskaj połączenia sieć wirtualna aplikacji sieci Web. |
> | action | microsoft.web/sites/virtualnetworkconnections/write | Aktualizowanie połączenia sieć wirtualna aplikacji sieci Web. |
> | action | Microsoft.Web/Sites/webjobs/Read | Rozpoczynanie zadania Webjob aplikacji sieci Web. |
> | action | Microsoft.Web/sites/Write | Utwórz nową aplikację sieci Web lub zaktualizuj istniejącą |
> | action | microsoft.web/skus/read | Pobierz jednostki SKU. |
> | action | microsoft.web/sourcecontrols/read | Uzyskaj kontroli źródła. |
> | action | microsoft.web/sourcecontrols/write | Zaktualizuj kontrolki źródła. |
> | action | Microsoft.Web/unregister/Action | Wyrejestruj dostawcę zasobów Microsoft.Web dla subskrypcji. |
> | action | microsoft.web/validate/action | Sprawdzanie poprawności. |
> | action | microsoft.web/verifyhostingenvironmentvnet/action | Upewnij się, hostingu środowiska sieci wirtualnej. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | action | Microsoft.WorkloadMonitor/components/read | Pobiera składników dla zasobu |
> | action | Microsoft.WorkloadMonitor/componentsSummary/read | Pobiera podsumowanie składników |
> | action | Microsoft.WorkloadMonitor/monitorInstances/read | Pobiera wystąpienia monitorów dla zasobu |
> | action | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Pobiera podsumowanie wystąpień |
> | action | Microsoft.WorkloadMonitor/monitors/read | Pobiera monitorów dla zasobu |
> | action | Microsoft.WorkloadMonitor/monitors/write | Konfigurowanie monitorowania dla zasobu |
> | action | Microsoft.WorkloadMonitor/notificationSettings/read | Pobiera ustawienia powiadomień dla zasobu |
> | action | Microsoft.WorkloadMonitor/notificationSettings/write | Skonfiguruj ustawienia powiadomień dla zasobu |
> | action | Microsoft.WorkloadMonitor/operations/read | Pobiera obsługiwane operacje |

## <a name="next-steps"></a>Kolejne kroki

- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
