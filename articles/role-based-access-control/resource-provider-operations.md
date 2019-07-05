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
ms.date: 06/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 424995369ca0d993d78a5d5e4dd1e2b52a0d64d9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428331"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operacje dostawcy zasobów w usłudze Azure Resource Manager

W tym artykule wymieniono operacje dostępne dla każdego dostawcy zasobów usługi Azure Resource Manager. Te operacje mogą być używane w [ról niestandardowych](custom-roles.md) zapewnienie szczegółową [kontroli dostępu opartej na rolach (RBAC)](overview.md) do zasobów platformy Azure. Operacja ciągi mają następujący format: `{Company}.{ProviderName}/{resourceType}/{action}`

Zawsze ewoluuje operacji dostawcy zasobów. Aby uzyskać najnowsze operacji, użyj [Get AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) lub [lista operacji dostawcy az](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AAD/domainServices/delete | Delete Domain Service |
> | Akcja | Microsoft.AAD/domainServices/oucontainer/delete | Usuwanie kontenera jednostki organizacyjnej |
> | Akcja | Microsoft.AAD/domainServices/oucontainer/read | Odczytaj jednostki organizacyjnej kontenerów |
> | Akcja | Microsoft.AAD/domainServices/oucontainer/write | Zapis kontenera jednostki organizacyjnej |
> | Akcja | Microsoft.AAD/domainServices/read | Read Domain Services |
> | Akcja | Microsoft.AAD/domainServices/replicaSets/delete | Usuń lokację klastra |
> | Akcja | Microsoft.AAD/domainServices/replicaSets/read | Witryna klastra odczytu |
> | Akcja | Microsoft.AAD/domainServices/replicaSets/write | Zapis witryna klastra |
> | Akcja | Microsoft.AAD/domainServices/write | Write Domain Service |
> | Akcja | Microsoft.AAD/locations/operationresults/read |  |
> | Akcja | Microsoft.AAD/Operations/read |  |
> | Akcja | Microsoft.AAD/register/action | Register Domain Service |
> | Akcja | Microsoft.AAD/unregister/action | Unregister Domain Service |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.aadiam/diagnosticsettings/DELETE | Usuwanie ustawienia diagnostyczne |
> | Akcja | microsoft.aadiam/diagnosticsettings/read | Odczytywanie ustawienie diagnostyczne |
> | Akcja | microsoft.aadiam/diagnosticsettings/write | Zapisywanie ustawienia diagnostyczne |
> | Akcja | microsoft.aadiam/diagnosticsettingscategories/read | Odczytywanie kategorii ustawienie diagnostyczne |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Addons/operations/read | Pobiera obsługiwane operacje jednostki Uzależnionej. |
> | Akcja | Microsoft.Addons/register/action | Zarejestrowanie określonej subskrypcji Microsoft.Addons |
> | Akcja | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Wyświetla listę bieżących informacji planu pomocy technicznej dotyczących określonej subskrypcji. |
> | Akcja | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Usuwa planu określonego pomocy technicznej firmy Canonical |
> | Akcja | Microsoft.Addons/supportProviders/supportPlanTypes/read | Pobierz stan planu określonego pomocy technicznej firmy Canonical. |
> | Akcja | Microsoft.Addons/supportProviders/supportPlanTypes/write | Dodaje określony typ planu pomocy technicznej firmy Canonical. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/action | Utwórz nowy las dla dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Pobiera wszystkie serwery dla określonej nazwy usługi. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/alerts/read | Pobiera szczegóły alertów dla lasu, takich jak alertid alertu zgłoszone Data, ostatni wykryte, alertu opis alertu, ostatniego alertu, zaktualizowaną poziomu, alertów stanu, alertów, rozwiązywanie problemów z łączami itp. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/configuration/read | Pobiera konfigurację usługi w lesie. Przykład — nazwa lasu poziom funkcjonalności nazewnictwa domeny głównego roli FSMO, roli FSMO wzorca schematu itp. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/delete | Usuwa usługę i jej serwery oraz dane dotyczące kondycji. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Pobiera szczegóły domen i lokacji w lesie. Stan kondycji przykładu, aktywne alerty rozwiązane alerty, właściwości, takie jak poziom funkcjonalności domeny, lasu, wzorzec infrastruktury, kontrolera PDC, etc wzorca RID.  |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Pobiera ustawienia preferencji użytkownika dla tego lasu.<br>Przykład — MetricCounterName, takie jak ldapsuccessfulbinds ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Ustawienia interfejsu użytkownika wykresy itd. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Pobiera lasu podsumowania dla danego lasu, takie jak nazwa lasu, liczbę domen w tym lesie, liczbie lokacji oraz lokacji szczegóły itp. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Pobiera listę obsługiwanych metryk dla danej usługi.<br>Przykład ekstranetu blokady konta, łączna liczba nieudanych żądań oczekujących żądań tokenów (Proxy), żądań tokenów/s itp. usługi AD FS.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czas powiązania protokołu LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla ADDomainService.<br>Uruchom opóźnienie profilu, ustanowionych połączeń TCP, liczba bajtów prywatnych agentów szczegółowe informacje, Statystyka eksportu do usługi Azure AD dla usługi ADSync. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Biorąc pod uwagę usługę, ten interfejs API pobiera dane metryk.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Ten interfejs API pobiera listę wszystkich dołączono ADDomainServices dla dzierżawy w warstwie premium. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/read | Pobiera szczegóły usługi dla określonej nazwy usługi. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Pobiera szczegóły replikacji dla wszystkich serwerów dla określonej nazwy usługi. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Pobiera liczbę kontrolerów domeny i ich błędy replikacji, jeśli istnieje. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Pobiera ukończyć listy kontrolerów domeny wraz ze szczegółami replikacji dla podanego lasu. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Dodanie wystąpienia serwera do usługi. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Podczas rejestracji serwera ADDomainService ten interfejs api jest wywoływana, aby uzyskać poświadczenia dla dołączania nowych serwerów. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Usuwa serwer dla danej usługi i dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/write | Tworzy lub aktualizuje wystąpienia ADDomainService dla dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/configuration/action | Aktualizuje konfigurację dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/configuration/read | Odczytuje konfigurację dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/configuration/write | Tworzy konfigurację dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/logs/contents/read | Pobiera zawartość elementu instalacji agenta i dzienniki rejestracji, przechowywane w obiekcie blob. |
> | Akcja | Microsoft.ADHybridHealthService/logs/read | Pobiera dzienniki rejestracji i Instalacja agenta dla dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/operations/read | Pobiera listę operacji obsługiwanych przez system. |
> | Akcja | Microsoft.ADHybridHealthService/register/action | Rejestruje dostawcę zasobów usługi kondycji ADHybrid i włącza funkcję tworzenia zasobów ADHybrid kondycji usługi. |
> | Akcja | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Pobiera listę dostępnych regionów, używane przez DevOps do odbiorcy zdarzeń pomocy technicznej. |
> | Akcja | Microsoft.ADHybridHealthService/reports/badpassword/read | Pobiera listę nieudane próby wprowadzenia hasła dla wszystkich użytkowników w Active Directory Federation Services. |
> | Akcja | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Pobiera identyfikator URI sygnatury dostępu Współdzielonego obiektów Blob zawierający stan i ostateczną wynikiem nowo dodawanych do kolejki zadania raportowania dla częstotliwości nieprawidłowo podawali nazwę użytkownika/hasło prób na identyfikator użytkownika na adres IP na dzień dla danej dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Pobiera listę DevOps wyraził zgodę dzierżaw. Zwykle używane przez dział pomocy technicznej. |
> | Akcja | Microsoft.ADHybridHealthService/reports/isdevops/read | Pobiera wartość wskazującą, czy dzierżawcy jest wyraził zgodę metodyki DevOps, czy nie. |
> | Akcja | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Aktualizuje userid(objectid) dla dzierżawy ops wybranego deweloperów. |
> | Akcja | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Pobiera wybrane wdrożenie do danej dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Podany identyfikator dzierżawy pobiera dzierżawy lokalizacji magazynu. |
> | Akcja | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Pobiera lokalizacji geograficznej, z którego będzie można uzyskać dostępu do danych. |
> | Akcja | Microsoft.ADHybridHealthService/services/action | Aktualizuje wystąpienia usługi w dzierżawie. |
> | Akcja | Microsoft.ADHybridHealthService/services/alerts/read | Odczytuje alerty dla usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/alerts/read | Odczytuje alerty dla usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Daną funkcję nazwy sprawdza, czy usługa ma wszystko, co jest wymagane, aby użyć tej funkcji. |
> | Akcja | Microsoft.ADHybridHealthService/services/delete | Usuwa wystąpienie usługi w dzierżawie. |
> | Akcja | Microsoft.ADHybridHealthService/services/exporterrors/read | Pobiera błędów eksportu dla usługi w danym synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/exportstatus/read | Pobiera stan eksportu dla danej usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Pobiera alerty opinii dla danej usługi i serwera. |
> | Akcja | Microsoft.ADHybridHealthService/services/metricmetadata/read | Pobiera listę obsługiwanych metryk dla danej usługi.<br>Przykład ekstranetu blokady konta, łączna liczba nieudanych żądań oczekujących żądań tokenów (Proxy), żądań tokenów/s itp. usługi AD FS.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czas powiązania protokołu LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla ADDomainService.<br>Uruchom opóźnienie profilu, ustanowionych połączeń TCP, liczba bajtów prywatnych agentów szczegółowe informacje, Statystyka eksportu do usługi Azure AD dla usługi ADSync. |
> | Akcja | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Biorąc pod uwagę usługę, ten interfejs API pobiera średnia dla metryki dla danej usługi.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/metrics/groups/read | Biorąc pod uwagę usługę, ten interfejs API pobiera dane metryk.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Biorąc pod uwagę usługę, ten interfejs API pobiera zagregowany widok, aby uzyskać metryki dla danej usługi.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Dodawanie lub aktualizacji konfiguracji monitorowania dla usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Pobiera konfiguracji monitorowania dla danej usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Dodawanie lub aktualizacji konfiguracji monitorowania dla usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/premiumcheck/read | Ten interfejs API pobiera listę wszystkich usług dołączono dla dzierżawy w warstwie premium. |
> | Akcja | Microsoft.ADHybridHealthService/services/read | Odczytuje wystąpień usługi w dzierżawie. |
> | Akcja | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Pobiera wszystkie raport ryzykownych adresów IP, identyfikatorów URI dla ostatnich 7 dni. |
> | Akcja | Microsoft.ADHybridHealthService/services/reports/details/read | Raport pobiera 50 pierwszych użytkowników z błędami nieprawidłowego hasła z ostatnich 7 dni |
> | Akcja | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Generuje raport ryzykownych adresów IP i zwraca identyfikator URI wskazujących na niego. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/action | Tworzy wystąpienie serwera w usłudze. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Odczytuje alerty dla serwera. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Podczas rejestracji serwera ten interfejs api jest wywoływana, aby uzyskać poświadczenia dla dołączania nowych serwerów. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Dla danego serwera ten interfejs API pobiera listę typów danych, które są przekazywane przez serwery i ostatni czas dla każdego przekazywania. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/delete | Usuwa wystąpienie serwera w usłudze. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Pobiera szczegóły błędu eksportowania synchronizacji dla danej usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Biorąc pod uwagę usługę, ten interfejs API pobiera dane metryk.<br>Ten interfejs API można na przykład, aby uzyskać informacje dotyczące: Ekstranetu blokady konta, łączna liczba nieudanych żądań, żądań oczekujących tokenów (Proxy), żądań tokenów/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślne na sekundę, czasu powiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę, ATQ itp. Łączna liczba wątków dla usługi ADDomain.<br>Profil opóźnienie przebiegów, połączenia TCP nawiązane, Bajty prywatne agenta szczegółowe informacje, wyeksportuj statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Pobiera listę łączników i nazwy profilu uruchamiania dla danej usługi i element członkowski usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/read | Odczytuje wystąpienie serwera w usłudze. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Pobiera konfigurację usługi dla danej dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Pobiera stan umieszczania na białej liście funkcji dla danej dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/services/write | Tworzy wystąpienie usługi w dzierżawie. |
> | Akcja | Microsoft.ADHybridHealthService/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Service Health ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Advisor/configurations/read | Pobieranie konfiguracji |
> | Akcja | Microsoft.Advisor/configurations/write | Tworzy/aktualizuje konfigurację |
> | Akcja | Microsoft.Advisor/generateRecommendations/action | Wygeneruje zalecenia |
> | Akcja | Microsoft.Advisor/generateRecommendations/read | Pobiera Generowanie zaleceń dotyczących stanu |
> | Akcja | Microsoft.Advisor/metadata/read | Get Metadata |
> | Akcja | Microsoft.Advisor/operations/read | Pobiera operacje dla Advisor firmy Microsoft |
> | Akcja | Microsoft.Advisor/recommendations/available/action | Nowe zalecenie jest dostępna w programie Microsoft advisor |
> | Akcja | Microsoft.Advisor/recommendations/read | Odczytuje zalecenia |
> | Akcja | Microsoft.Advisor/recommendations/suppressions/delete | Usuwa pominięć |
> | Akcja | Microsoft.Advisor/recommendations/suppressions/read | Pobiera pominięć |
> | Akcja | Microsoft.Advisor/recommendations/suppressions/write | Tworzy aktualizacje pominięć |
> | Akcja | Microsoft.Advisor/register/action | Rejestruje subskrypcję usługi Advisor firmy Microsoft |
> | Akcja | Microsoft.Advisor/suppressions/delete | Usuwa pominięć |
> | Akcja | Microsoft.Advisor/suppressions/read | Pobiera pominięć |
> | Akcja | Microsoft.Advisor/suppressions/write | Tworzy aktualizacje pominięć |
> | Akcja | Microsoft.Advisor/unregister/action | Wyrejestrowuje subskrypcję usługi Advisor firmy Microsoft |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AlertsManagement/actionRules/delete | Usuń regułę akcji w ramach danej subskrypcji. |
> | Akcja | Microsoft.AlertsManagement/actionRules/read | Pobierz wszystkie reguły akcji dla filtrów wejściowych. |
> | Akcja | Microsoft.AlertsManagement/actionRules/write | Utwórz lub zaktualizuj reguły akcji w ramach danej subskrypcji |
> | Akcja | Microsoft.AlertsManagement/alerts/changestate/action | Zmień stan alertu. |
> | Akcja | Microsoft.AlertsManagement/alerts/diagnostics/read | Pobierz wszystkie diagnostyki dla alertu |
> | Akcja | Microsoft.AlertsManagement/alerts/history/read | Pobieranie historii alertu |
> | Akcja | Microsoft.AlertsManagement/alerts/read | Pobierz wszystkie alerty dla danych wejściowych filtrów. |
> | Akcja | Microsoft.AlertsManagement/alertsList/read | Pobierz wszystkie alerty dla danych wejściowych filtrów między subskrypcjami |
> | Akcja | Microsoft.AlertsManagement/alertsSummary/read | Pobierz podsumowanie alertów |
> | Akcja | Microsoft.AlertsManagement/alertsSummaryList/read | Pobierz podsumowanie alertów w subskrypcjach |
> | Akcja | Microsoft.AlertsManagement/Operations/read | Odczytuje operacji podano |
> | Akcja | Microsoft.AlertsManagement/register/action | Rejestruje subskrypcję usługi Microsoft zarządzania alertami |
> | Akcja | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Usuń regułę alertów wykrywanie inteligentne w ramach danej subskrypcji |
> | Akcja | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Pobieranie wszystkich reguł alertów inteligentne wykrywanie filtry wejściowe |
> | Akcja | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Utwórz lub zaktualizuj regułę alertu wykrywanie inteligentne w ramach danej subskrypcji |
> | Akcja | Microsoft.AlertsManagement/smartGroups/changestate/action | Zmień stan grupy inteligentne |
> | Akcja | Microsoft.AlertsManagement/smartGroups/history/read | Pobieranie historii grupy inteligentne |
> | Akcja | Microsoft.AlertsManagement/smartGroups/read | Pobierz wszystkie inteligentne grupy dla danych wejściowych filtrów |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Sprawdza, czy podany serwer Analysis Server Nazwa jest prawidłowa i nie jest używany. |
> | Akcja | Microsoft.AnalysisServices/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | Akcja | Microsoft.AnalysisServices/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | Akcja | Microsoft.AnalysisServices/operations/read | Pobiera informacje o operacjach |
> | Akcja | Microsoft.AnalysisServices/register/action | Rejestruje dostawcę zasobów usług Analysis Services. |
> | Akcja | Microsoft.AnalysisServices/servers/delete | Usuwa serwer Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Wyświetl stan bramy skojarzonej z serwerem. |
> | Akcja | Microsoft.AnalysisServices/servers/read | Pobiera informacje o konkretnym serwerze Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/servers/resume/action | Wznawia działanie serwera Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/servers/skus/read | Pobierz dostępne informacje o jednostkach SKU dla serwera |
> | Akcja | Microsoft.AnalysisServices/servers/suspend/action | Wstrzymuje działanie serwera Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/servers/write | Tworzy lub aktualizuje konkretny serwer Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/skus/read | Pobiera informacje o jednostkach SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ApiManagement/checkNameAvailability/read | Sprawdza, czy pod warunkiem, że nazwa usługi jest dostępny |
> | Akcja | Microsoft.ApiManagement/operations/read | Odczytywać wszystkie operacje interfejsu API dostępne dla zasobu Microsoft.ApiManagement |
> | Akcja | Microsoft.ApiManagement/register/action | Rejestruj subskrypcję dostawcy zasobów Microsoft.ApiManagement |
> | Akcja | Microsoft.ApiManagement/reports/read | Pobierz raporty agregowane przez okresy czasu, regionu geograficznego, deweloperów, produktów, interfejsów API, operacje, subskrypcji i byRequest. |
> | Akcja | Microsoft.ApiManagement/service/apis/delete | Usuwa określony interfejs API wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/delete | Usuwa określony diagnostyczne z interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/read | Wyświetla listę wszystkich diagnostyki interfejsu API. lub pobiera szczegóły diagnostyczne dla interfejsu API, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/write | Tworzy nowy diagnostyczne dla interfejsu API lub aktualizuje istniejący zestaw. lub aktualizacje szczegóły diagnostyczne dla interfejsu API określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Usuwa określony komentarz z problemu. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/attachments/read | Wyświetla wszystkie załączniki dla wydania skojarzone z określonego interfejsu API. lub pobiera szczegóły problemu załącznika dla interfejsu API, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/attachments/write | Tworzy nowy załącznik do problemu w interfejsie API, lub aktualizuje istniejący zestaw. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/comments/delete | Usuwa określony komentarz z problemu. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/comments/read | Wyświetla wszystkie komentarze do wydania skojarzone z określonego interfejsu API. lub pobiera szczegóły problemu komentarz dotyczący interfejsu API, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/comments/write | Tworzy nowego komentarza dotyczącego problemu w interfejsie API, lub aktualizuje istniejący zestaw. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/delete | Usuwa określony problem z interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/read | Wyświetla listę wszystkich problemów skojarzonych z określonym interfejsem API. lub pobiera szczegóły problemu dla interfejsu API, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/write | Tworzy nowy problem dla interfejsu API lub aktualizuje istniejący zestaw. lub aktualizacji istniejącej wydania dla interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/delete | Usuwa określonej operacji interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policies/delete | Usuwa konfigurację zasad w operacji interfejsu Api. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policies/read | Pobierz listę konfiguracji zasad na poziomie operacji interfejsu API. lub Konfiguracja zasad na poziomie operacji interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policies/write | Tworzy lub aktualizuje konfigurację zasad na poziomie operacji interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policy/delete | Usuwanie konfiguracji zasad na poziomie operacji |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policy/read | Pobierz konfigurację zasad na poziomie operacji |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policy/write | Tworzenie konfiguracji zasad na na operację |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/read | Wyświetla listę to kolekcja operacji dla określonego interfejsu API. lub pobiera szczegóły operacji interfejsu API, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/tags/delete | Odłącz tag z działania. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/tags/read | Wyświetla wszystkie znaczniki skojarzone z operacją. lub Pobierz tag skojarzone z operacją. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/tags/write | Przypisywanie tagów do operacji. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/write | Tworzy nową operację w interfejsie API, lub aktualizuje istniejący zestaw. lub aktualizacje szczegóły operacji w interfejsie API określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/operationsByTags/read | Zawiera kolekcję operacji skojarzonych z tagami. |
> | Akcja | Microsoft.ApiManagement/service/apis/policies/delete | Usuwa konfigurację zasad w interfejsie Api. |
> | Akcja | Microsoft.ApiManagement/service/apis/policies/read | Pobierz konfigurację zasad na poziomie interfejsu API. lub Konfiguracja zasad na poziomie interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/policies/write | Tworzy lub aktualizuje konfigurację zasad interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/policy/delete | Usuwanie konfiguracji zasad na poziomie interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/policy/read | Pobierz konfigurację zasad na poziomie interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/policy/write | Tworzenie konfiguracji zasad na na interfejs API |
> | Akcja | Microsoft.ApiManagement/service/apis/products/read | Wyświetla listę wszystkich produktów, które interfejs API jest częścią. |
> | Akcja | Microsoft.ApiManagement/service/apis/read | Wyświetla listę wszystkich interfejsów API wystąpienia usługi API Management. lub pobiera szczegółowe informacje o interfejsie API, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/releases/delete | Usuwa wszystkie wersje interfejsu API lub usuwa z określonej wersji w interfejsie API. |
> | Akcja | Microsoft.ApiManagement/service/apis/releases/read | Wyświetla listę wszystkich wersji interfejsu API.<br>Wersja interfejsu API jest tworzony podczas wykonywania bieżącej poprawki interfejsu API.<br>Wersje są również używane do wycofania do poprzedniej poprawki.<br>Wyniki będą stronicowane i może być ograniczona przez parametry $top i $skip.<br>lub zwraca szczegółowe informacje o interfejs API wersji. |
> | Akcja | Microsoft.ApiManagement/service/apis/releases/write | Tworzy nową wersję interfejsu API. lub aktualizacje, szczegółowe informacje o wersji interfejsu API określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/revisions/delete | Usuwa wszystkie poprawki interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/revisions/read | Wyświetla wszystkie poprawki interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/delete | Usuwa konfigurację schematu w interfejsie Api. |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/read | Uzyskaj konfiguracją w schemacie na poziomie interfejsu API. lub konfiguracją w schemacie na poziomie interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/write | Tworzy lub aktualizuje schemat konfiguracji dla interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Usuń tag opis interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Wyświetla wszystkie opisy tagów w zakresie interfejsu API. Model podobne do struktury swagger — tagDescription jest zdefiniowana na poziomie interfejsu API, ale tagu może być przypisana do operacji lub pobrać opisu tagu w zakresie interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Utwórz/zaktualizuj opis tagu w zakresie interfejsu Api. |
> | Akcja | Microsoft.ApiManagement/service/apis/tags/delete | Odłącz tag z interfejsu Api. |
> | Akcja | Microsoft.ApiManagement/service/apis/tags/read | Wyświetla wszystkie znaczniki skojarzone z interfejsem API. lub Pobierz tag skojarzonych z interfejsem API. |
> | Akcja | Microsoft.ApiManagement/service/apis/tags/write | Przypisywanie tagów do interfejsu Api. |
> | Akcja | Microsoft.ApiManagement/service/apis/write | Nowe tworzy lub aktualizuje istniejące API określonego wystąpienia usługi API Management. lub aktualizacji interfejsu API określonego wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/apisByTags/read | Wyświetla listę zbiór interfejsów API skojarzone z tagami. |
> | Akcja | Microsoft.ApiManagement/service/apiVersionSets/delete | Usuwa określone ustawiona wersja interfejsu Api. |
> | Akcja | Microsoft.ApiManagement/service/apiVersionSets/read | Zawiera kolekcję zestawów wersji interfejsu API w wystąpieniu określonej usługi. lub pobiera szczegółowe informacje o wersji interfejsu Api zestawu określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Pobierz listę jednostek wersji |
> | Akcja | Microsoft.ApiManagement/service/apiVersionSets/write | Tworzy lub aktualizuje zestaw wersji interfejsu Api. lub aktualizacje szczegóły VersionSet interfejsu Api, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Aktualizuje zasoby Microsoft.ApiManagement działać w sieci wirtualnej, aby pobrać zaktualizowane ustawienia sieciowe. |
> | Akcja | Microsoft.ApiManagement/service/authorizationServers/delete | Usuwa wystąpienie serwera określonego autoryzacji. |
> | Akcja | Microsoft.ApiManagement/service/authorizationServers/read | Wyświetla listę kolekcji serwerów autoryzacji zdefiniowane w ramach wystąpienia usługi. lub pobiera szczegóły serwera autoryzacji, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/authorizationServers/write | Tworzy nowy serwer autoryzacji lub aktualizuje istniejący serwer autoryzacji. lub aktualizacje szczegóły serwera autoryzacji, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/backends/delete | Usuwa określony wewnętrznej bazy danych. |
> | Akcja | Microsoft.ApiManagement/service/backends/read | Zawiera kolekcję zapleczy w wystąpieniu określonej usługi. lub pobiera szczegółowe informacje o wewnętrznej bazy danych określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/backends/reconnect/action | Powiadamia serwera proxy usługi APIM utworzyć nowe połączenie z zapleczem usługi po określonym czasie. Jeśli nie określono limitu czasu, limit czasu równy 2 minuty jest używany. |
> | Akcja | Microsoft.ApiManagement/service/backends/write | Tworzy lub aktualizuje zaplecza. lub aktualizuje istniejącego zaplecza. |
> | Akcja | Microsoft.ApiManagement/service/backup/action | Kopii zapasowych usługi API Management do określonego kontenera w użytkownika podane konto magazynu |
> | Akcja | Microsoft.ApiManagement/service/caches/delete | Usuwa określonych pamięci podręcznej. |
> | Akcja | Microsoft.ApiManagement/service/caches/read | Zawiera zbiór wszystkich zewnętrznych pamięci podręcznych w wystąpieniu określonej usługi. lub pobiera szczegółowe informacje o pamięci podręcznej, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/caches/write | Tworzy lub aktualizuje zewnętrzna pamięć podręczna, które zostaną użyte w wystąpienia usługi Api Management. lub aktualizacje szczegółowe informacje o pamięci podręcznej, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/certificates/delete | Usuwa określonego certyfikatu. |
> | Akcja | Microsoft.ApiManagement/service/certificates/read | Wyświetla listę kolekcji wszystkie certyfikaty w wystąpieniu określonej usługi. lub pobiera szczegóły certyfikatu określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/certificates/write | Tworzy lub aktualizuje certyfikat używany do uwierzytelniania przy użyciu wewnętrznej bazy danych. |
> | Akcja | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Usuwa określony element zawartości. |
> | Akcja | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Zwraca listę elementów zawartości lub zwraca szczegóły elementu zawartości |
> | Akcja | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Tworzy nowy element zawartości lub aktualizuje określony element zawartości |
> | Akcja | Microsoft.ApiManagement/service/contentTypes/read | Zwraca listę typów zawartości |
> | Akcja | Microsoft.ApiManagement/service/delete | Usuń wystąpienie usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/delete | Usuwa określony diagnostyki. |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/read | Wyświetla listę wszystkich diagnostykę wystąpienia usługi API Management. lub pobiera szczegóły diagnostyczne określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/write | Tworzy nowy diagnostyki lub aktualizuje istniejący zestaw. lub aktualizacje szczegóły diagnostyczne określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/getssotoken/action | Pobiera token logowania jednokrotnego, który może służyć do logowania do interfejsu API zarządzania usługi w starszej wersji portalu jako administrator |
> | Akcja | Microsoft.ApiManagement/service/groups/delete | Usuwa określoną grupę wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/groups/read | Zawiera kolekcję grup zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły grupy określonej przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/groups/users/delete | Usuwanie istniejącego użytkownika z istniejącej grupy. |
> | Akcja | Microsoft.ApiManagement/service/groups/users/read | Zawiera kolekcję obiektów użytkownika skojarzone z grupą. |
> | Akcja | Microsoft.ApiManagement/service/groups/users/write | Dodawanie istniejącego użytkownika do istniejącej grupy |
> | Akcja | Microsoft.ApiManagement/service/groups/write | Tworzy lub aktualizuje grupę. lub aktualizacje szczegóły grupy określonej przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/identityProviders/delete | Usuwa konfigurację dostawcy określonej tożsamości. |
> | Akcja | Microsoft.ApiManagement/service/identityProviders/read | Wyświetla listę kolekcji przez dostawcę tożsamości, które skonfigurowano w wystąpieniu określonej usługi. lub pobiera szczegóły konfiguracji dostawcy tożsamości zostały skonfigurowane w wystąpieniu określonej usługi. |
> | Akcja | Microsoft.ApiManagement/service/identityProviders/write | Tworzy lub aktualizuje konfigurację dostawca tożsamości. lub aktualizuje istniejącą konfigurację dostawca tożsamości. |
> | Akcja | Microsoft.ApiManagement/service/issues/read | Wyświetla listę kolekcji problemów w wystąpieniu określonej usługi. lub szczegółowe informacje o problemie pobiera z usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/locations/networkstatus/read | Pobiera stan dostępu do sieci, zasobów, od których zależy od usługi, w tym miejscu. |
> | Akcja | Microsoft.ApiManagement/service/loggers/delete | Usuwa określonego rejestratora. |
> | Akcja | Microsoft.ApiManagement/service/loggers/read | Zawiera kolekcję rejestratorów w wystąpieniu określonej usługi. lub pobiera szczegóły rejestratora określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/loggers/write | Tworzy lub aktualizuje rejestrator. lub aktualizuje istniejące rejestratora. |
> | Akcja | Microsoft.ApiManagement/service/managedeployments/action | Zmiana jednostki SKU/jednostek, dodawać i usuwać regionalnych wdrożeń usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/networkstatus/read | Pobiera stan dostępu do sieci, zasobów, od których zależy usługa. |
> | Akcja | Microsoft.ApiManagement/service/notifications/action | Wysyła powiadomienie do określonego użytkownika |
> | Akcja | Microsoft.ApiManagement/service/notifications/read | Zawiera kolekcję właściwości zdefiniowane w ramach wystąpienia usługi. lub pobiera szczegóły powiadomienia, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Usuwa wiadomości e-mail z listy powiadomień. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Pobiera listę adresatów wiadomości E-mail z powiadomieniem subskrypcję powiadomienia. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Dodaje adres E-mail do listy adresatów powiadomienia. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Usuwa użytkownika usługi API Management z listy powiadomień. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Pobiera listę użytkowników adresata powiadomień subskrypcję powiadomienia. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Dodaje użytkownika zarządzania interfejsu API do listy adresatów powiadomienia. |
> | Akcja | Microsoft.ApiManagement/service/notifications/write | Tworzenie lub powiadomienia o wydawcy usługi API Management Update. |
> | Akcja | Microsoft.ApiManagement/service/openidConnectProviders/delete | Usuwa określonego dostawcy OpenID Connect wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/openidConnectProviders/read | Wyświetla listę wszystkich dostawców uwierzytelniania OpenId Connect. lub pobiera określonego dostawcy OpenID Connect. |
> | Akcja | Microsoft.ApiManagement/service/openidConnectProviders/write | Tworzy lub aktualizuje dostawcy OpenID Connect. lub aktualizacje określonego dostawcy OpenID Connect. |
> | Akcja | Microsoft.ApiManagement/service/operationresults/read | Pobiera bieżący stan długotrwałej operacji |
> | Akcja | Microsoft.ApiManagement/service/policies/delete | Usuwa konfigurację globalnych zasad usługi Api Management. |
> | Akcja | Microsoft.ApiManagement/service/policies/read | Wyświetla listę wszystkich definicji globalnych zasad usługi Api Management. lub Pobierz definicję globalnych zasad usługi Api Management. |
> | Akcja | Microsoft.ApiManagement/service/policies/write | Tworzy lub aktualizuje konfigurację globalnych zasad usługi Api Management. |
> | Akcja | Microsoft.ApiManagement/service/policy/delete | Usuwanie konfiguracji zasad na poziomie dzierżawy |
> | Akcja | Microsoft.ApiManagement/service/policy/read | Pobierz konfigurację zasad na poziomie dzierżawy |
> | Akcja | Microsoft.ApiManagement/service/policy/write | Tworzenie konfiguracji zasad na w dzierżawie |
> | Akcja | Microsoft.ApiManagement/service/policySnippets/read | Wyświetla wszystkie fragmenty kodu zasad. |
> | Akcja | Microsoft.ApiManagement/service/portalsettings/read | Uzyskaj logowania w ustawieniach portalu lub logowania Odbierz Konfigurowanie ustawień portalu lub uzyskać ustawienia delegowania dla portalu. |
> | Akcja | Microsoft.ApiManagement/service/portalsettings/write | Aktualizowanie ustawień logowania. lub ustawienia tworzenia lub aktualizacji logowania. lub ustawienia aktualizacji Zarejestruj lub aktualizacji Zarejestruj ustawień lub ustawień delegowania aktualizacji. lub ustawienia tworzenia lub aktualizacji delegowania. |
> | Akcja | Microsoft.ApiManagement/service/products/apis/delete | Usuwa określony interfejs API z określonego produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/apis/read | Listy to zbiór interfejsów API skojarzone z produktem. |
> | Akcja | Microsoft.ApiManagement/service/products/apis/write | Dodaje interfejs API do określonego produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/delete | Usuń produkt. |
> | Akcja | Microsoft.ApiManagement/service/products/groups/delete | Usuwa skojarzenie między określonej grupy i produkt. |
> | Akcja | Microsoft.ApiManagement/service/products/groups/read | Zawiera kolekcję grup dla deweloperów, które skojarzony z określonym produktem. |
> | Akcja | Microsoft.ApiManagement/service/products/groups/write | Dodaje skojarzenie między grupą deweloperów określony za pomocą określonego produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/policies/delete | Usuwa konfigurację zasad w produkcie. |
> | Akcja | Microsoft.ApiManagement/service/products/policies/read | Pobierz konfigurację zasad na poziomie produktu. lub Konfiguracja zasad na poziomie produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/policies/write | Tworzy lub aktualizuje konfigurację zasad dla produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/policy/delete | Usuwanie konfiguracji zasad na poziomie produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policy/read | Pobierz konfigurację zasad na poziomie produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policy/write | Tworzenie konfiguracji zasad na produktu |
> | Akcja | Microsoft.ApiManagement/service/products/read | Wyświetla listę kolekcji produktów w wystąpieniu określonej usługi. lub pobiera szczegóły produktu, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/products/subscriptions/read | Wyświetla listę kolekcji w subskrypcji określonego produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/tags/delete | Odłącz tag z produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/tags/read | Wyświetla wszystkie znaczniki skojarzone z produktem. lub Pobierz tag skojarzony z produktem. |
> | Akcja | Microsoft.ApiManagement/service/products/tags/write | Przypisywanie tagu produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/write | Tworzy lub aktualizuje produktu. lub zaktualizować istniejące szczegółowe informacje. |
> | Akcja | Microsoft.ApiManagement/service/productsByTags/read | Zawiera kolekcję produkty powiązane z tagami. |
> | Akcja | Microsoft.ApiManagement/service/properties/delete | Usuwa określoną właściwość z wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/properties/read | Zawiera kolekcję właściwości zdefiniowane w ramach wystąpienia usługi. lub pobiera szczegółowe informacje o właściwości określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/properties/write | Tworzy lub aktualizuje właściwości. lub aktualizuje określoną właściwość. |
> | Akcja | Microsoft.ApiManagement/service/quotas/periods/read | Pobierz wartość licznika limitu przydziału dla okresu |
> | Akcja | Microsoft.ApiManagement/service/quotas/periods/write | Ustaw bieżącą wartość licznika limitu przydziału |
> | Akcja | Microsoft.ApiManagement/service/quotas/read | Pobieranie wartości do limitu przydziału |
> | Akcja | Microsoft.ApiManagement/service/quotas/write | Ustaw bieżącą wartość licznika limitu przydziału |
> | Akcja | Microsoft.ApiManagement/service/read | Odczytywanie metadanych dla wystąpienia usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/regions/read | Wyświetla listę wszystkich regionach platformy azure, w których usługa istnieje. |
> | Akcja | Microsoft.ApiManagement/service/reports/read | Pobierz raport agregowane przez okresów lub Pobierz raport agregowane według regionu geograficznego lub raport Get agregowane przez deweloperów.<br>lub Pobierz raport agregowane przez produktów.<br>lub Pobierz raport agregowane przez interfejsy API lub Pobierz raport agregowane przez operacje lub Pobierz raport agregowane według subskrypcji.<br>lub Pobierz żądań danych raportowania |
> | Akcja | Microsoft.ApiManagement/service/restore/action | Przywracanie usługi API Management z określonego kontenera użytkownika podane konto magazynu |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/delete | Usuwa określoną subskrypcję. |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/read | Wyświetla listę wszystkich subskrypcji wystąpienia usługi API Management. lub pobiera określonej jednostki subskrypcji. |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Generuje ponownie klucz podstawowy istniejącej subskrypcji wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Generuje ponownie klucz pomocniczy dla istniejącej subskrypcji wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/write | Tworzy lub aktualizuje subskrypcji określonego użytkownika z określonym produktem. lub aktualizacje Szczegóły subskrypcji, określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/tagResources/read | Zawiera kolekcję zasobów skojarzonych z tagami. |
> | Akcja | Microsoft.ApiManagement/service/tags/delete | Usuwa określony tag wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/tags/read | Zawiera kolekcję tagów zdefiniowany w ramach wystąpienia usługi. lub pobiera Szczegóły tagu określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/tags/write | Tworzy tag. lub aktualizacje Szczegóły tagu określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/templates/delete | Resetuj domyślny szablon wiadomości e-mail usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/templates/read | Pobiera wszystkie szablony wiadomości e-mail lub wiadomości e-mail są pobierane z usługi API Management Szczegóły szablonu |
> | Akcja | Microsoft.ApiManagement/service/templates/write | Tworzenie lub aktualizowanie szablonu wiadomości e-mail usługi API Management lub aktualizacji usługi API Management, szablon wiadomości e-mail |
> | Akcja | Microsoft.ApiManagement/service/tenant/delete | Usuń konfigurację zasad dla dzierżawy |
> | Akcja | Microsoft.ApiManagement/service/tenant/deploy/action | Uruchamia zadanie wdrożenia, aby zastosować zmiany z gałęzi git określony w konfiguracji w bazie danych. |
> | Akcja | Microsoft.ApiManagement/service/tenant/operationResults/read | Pobierz listę wyników operacji lub Pobierz wyniki operacji |
> | Akcja | Microsoft.ApiManagement/service/tenant/read | Uzyskaj definicję globalnych zasad usługi Api Management. lub Pobierz dzierżawy dostęp do informacji o szczegóły |
> | Akcja | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Ponowne generowanie podstawowego klucza dostępu |
> | Akcja | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Wygeneruj ponownie pomocniczy klucz dostępu |
> | Akcja | Microsoft.ApiManagement/service/tenant/save/action | Tworzy zatwierdzenie z migawki konfiguracji w określonej gałęzi w repozytorium |
> | Akcja | Microsoft.ApiManagement/service/tenant/syncState/read | Pobierz stan ostatniej synchronizacji narzędzia git |
> | Akcja | Microsoft.ApiManagement/service/tenant/validate/action | Sprawdza poprawność zmian z gałęzi określonej usługi git |
> | Akcja | Microsoft.ApiManagement/service/tenant/write | Ustawienia zasad konfiguracji dla dzierżawy lub szczegółowe informacje dotyczące aktualizacji dzierżawy dostępu |
> | Akcja | Microsoft.ApiManagement/service/updatecertificate/action | Przekaż certyfikat SSL dla usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/updatehostname/action | Konfigurowanie, zaktualizować lub usunąć niestandardowe nazwy domen dla usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/users/action | Rejestrowanie nowego użytkownika |
> | Akcja | Microsoft.ApiManagement/service/users/confirmations/send/action | Wysyła potwierdzenie z |
> | Akcja | Microsoft.ApiManagement/service/users/delete | Usuwa określonego użytkownika. |
> | Akcja | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Pobiera adres URL przekierowania zawierający token uwierzytelniania dla danego użytkownika logowania się do portalu dla deweloperów. |
> | Akcja | Microsoft.ApiManagement/service/users/groups/read | Wyświetla listę wszystkich grup użytkowników. |
> | Akcja | Microsoft.ApiManagement/service/users/identities/read | Lista wszystkich tożsamości użytkownika. |
> | Akcja | Microsoft.ApiManagement/service/users/keys/read | Pobierz klucze skojarzone z użytkownikiem |
> | Akcja | Microsoft.ApiManagement/service/users/read | Zawiera kolekcję zarejestrowanych użytkowników w wystąpieniu określonej usługi. lub pobiera szczegóły użytkownika określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/users/subscriptions/read | Wyświetla listę kolekcji w subskrypcji określonego użytkownika. |
> | Akcja | Microsoft.ApiManagement/service/users/token/action | Pobiera Token autoryzacji dostępu współdzielonego dla użytkownika. |
> | Akcja | Microsoft.ApiManagement/service/users/write | Tworzy lub aktualizuje użytkownika. lub aktualizacje szczegóły użytkownika określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/write | Utwórz nowe wystąpienie usługi API Management |
> | Akcja | Microsoft.ApiManagement/unregister/action | Wyrejestruj subskrypcję dostawcy zasobów Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Authorization/classicAdministrators/delete | Usuwa administratora z subskrypcji. |
> | Akcja | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Pobiera administrator stany operacji subskrypcji. |
> | Akcja | Microsoft.Authorization/classicAdministrators/read | Odczytuje administratorów dla subskrypcji. |
> | Akcja | Microsoft.Authorization/classicAdministrators/write | Dodaje lub modyfikuje administratora dla subskrypcji. |
> | Akcja | Microsoft.Authorization/denyAssignments/delete | Usuń przypisanie Odmów w podanym zakresie. |
> | Akcja | Microsoft.Authorization/denyAssignments/read | Uzyskaj informacje o przypisaniu Odmów. |
> | Akcja | Microsoft.Authorization/denyAssignments/write | Tworzenie przypisania Odmów w podanym zakresie. |
> | Akcja | Microsoft.Authorization/elevateAccess/action | Udziela uprawnień obiektu wywołującego dostępu Administrator dostępu użytkowników w zakresie dzierżawy |
> | Akcja | Microsoft.Authorization/locks/delete | Usuwa blokady w podanym zakresie. |
> | Akcja | Microsoft.Authorization/locks/read | Pobiera blokady w podanym zakresie. |
> | Akcja | Microsoft.Authorization/locks/write | Dodaje blokady w podanym zakresie. |
> | Akcja | Microsoft.Authorization/operations/read | Pobiera listę operacji |
> | Akcja | Microsoft.Authorization/permissions/read | Wyświetla listę wszystkich uprawnień obiektu wywołującego w danym zakresie. |
> | Akcja | Microsoft.Authorization/policyAssignments/delete | Usuń przypisanie zasad w określonym zakresie. |
> | Akcja | Microsoft.Authorization/policyAssignments/read | Uzyskaj informacje o przypisaniu zasad. |
> | Akcja | Microsoft.Authorization/policyAssignments/write | Tworzenie przypisania zasad w określonym zakresie. |
> | Akcja | Microsoft.Authorization/policyDefinitions/delete | Usuń definicję zasad. |
> | Akcja | Microsoft.Authorization/policyDefinitions/read | Uzyskaj informacje o definicji zasad. |
> | Akcja | Microsoft.Authorization/policyDefinitions/write | Utwórz niestandardową definicję zasad. |
> | Akcja | Microsoft.Authorization/policySetDefinitions/delete | Usuń definicję zestawu zasad. |
> | Akcja | Microsoft.Authorization/policySetDefinitions/read | Pobierz informacje o definicji zestawu zasad. |
> | Akcja | Microsoft.Authorization/policySetDefinitions/write | Utwórz niestandardową definicję zestawu zasad. |
> | Akcja | Microsoft.Authorization/providerOperations/read | Pobierz operacje dla wszystkich dostawców zasobów, które mogą być używane w definicji ról. |
> | Akcja | Microsoft.Authorization/roleAssignments/delete | Usuń przypisanie roli w podanym zakresie. |
> | Akcja | Microsoft.Authorization/roleAssignments/read | Uzyskaj informacje o przypisaniu roli. |
> | Akcja | Microsoft.Authorization/roleAssignments/write | Utwórz przypisanie roli w podanym zakresie. |
> | Akcja | Microsoft.Authorization/roleDefinitions/delete | Usuń określoną niestandardową definicję roli. |
> | Akcja | Microsoft.Authorization/roleDefinitions/read | Pobierz informacje o definicji roli. |
> | Akcja | Microsoft.Authorization/roleDefinitions/write | Utwórz lub zaktualizuj niestandardową definicję roli z określonymi uprawnieniami i zakresy możliwe do przypisania. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Przeczytaj informacje o rejestracji usługi Azure Automation DSC firmy |
> | Akcja | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Zapisuje żądanie, aby ponownie wygenerować klucze usługi Azure Automation DSC |
> | Akcja | Microsoft.Automation/automationAccounts/certificates/delete | Usuwa zasób certyfikatu usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/certificates/getCount/action | Odczytuje liczba certyfikatów |
> | Akcja | Microsoft.Automation/automationAccounts/certificates/read | Pobiera zasób certyfikatu usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/certificates/write | Tworzy lub aktualizuje zasób certyfikatu usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/compilationjobs/read | Odczytuje usługi Azure Automation DSC w kompilacji |
> | Akcja | Microsoft.Automation/automationAccounts/compilationjobs/read | Odczytuje usługi Azure Automation DSC w kompilacji |
> | Akcja | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapisuje usługi Azure Automation DSC w kompilacji |
> | Akcja | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapisuje usługi Azure Automation DSC w kompilacji |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/content/read | Odczytuje zawartość nośnika konfiguracji |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/delete | Usuwa zawartość Azure DSC usługi Automation |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/getCount/action | Odczytuje liczba zawartości Azure DSC usługi Automation |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/read | Pobiera zawartość usługi Azure Automation DSC firmy |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/write | Zapisuje zawartość Azure DSC usługi Automation |
> | Akcja | Microsoft.Automation/automationAccounts/connections/delete | Usuwa zasób połączenia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/connections/getCount/action | Odczytuje liczba połączeń |
> | Akcja | Microsoft.Automation/automationAccounts/connections/read | Pobiera zasób połączenia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/connections/write | Tworzy lub aktualizuje zasób połączenia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/connectionTypes/delete | Usuwa zasób typu połączenia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/connectionTypes/read | Pobiera zasób typu połączenia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/connectionTypes/write | Tworzy zasób typu połączenia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/delete | Usuwa zasób poświadczenia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/getCount/action | Odczytuje liczba poświadczeń |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/read | Pobiera zasób poświadczenia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/write | Tworzy lub aktualizuje zasób poświadczenia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/delete | Usuwa konto usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Usuwa zasoby hybrydowego procesu roboczego elementu Runbook |
> | Akcja | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/output/read | Pobiera dane wyjściowe zadania |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Pobiera zawartość elementu runbook usługi Azure Automation w czasie wykonywania zadania |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/stop/action | Zatrzymuje zadanie usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobSchedules/delete | Usuwa harmonogram zadań usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobSchedules/read | Pobiera harmonogram zadań usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobSchedules/write | Tworzy harmonogram zadań usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem usługi automation |
> | Akcja | Microsoft.Automation/automationAccounts/listKeys/action | Odczytuje klucze konta usługi automation |
> | Akcja | Microsoft.Automation/automationAccounts/modules/activities/read | Pobiera działania usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/modules/delete | Usuwa moduł usługi Azure Automation Powershell |
> | Akcja | Microsoft.Automation/automationAccounts/modules/getCount/action | Pobiera liczbę modułów programu Powershell w ramach konta usługi Automation |
> | Akcja | Microsoft.Automation/automationAccounts/modules/read | Pobiera moduł usługi Azure Automation Powershell |
> | Akcja | Microsoft.Automation/automationAccounts/modules/write | Tworzy lub aktualizuje moduł usługi Azure Automation Powershell |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Usuwa konfigurację węzła DSC usługi Automation platformy Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Odczytuje zawartość konfiguracji węzła DSC usługi Automation platformy Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Odczytuje konfigurację węzła DSC usługi Automation platformy Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Zapisuje konfiguracji węzła DSC usługi Automation platformy Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodecounts/read | Odczytuje podsumowanie liczby węzłów dla określonego typu |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/delete | Usuwa węzłów usługi Azure Automation DSC |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/read | Odczytuje węzłów usługi Azure Automation DSC |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Odczytuje zawartość raportu w usłudze Azure Automation DSC |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/reports/read | Odczytuje raporty usługi Azure Automation DSC |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/write | Tworzy lub aktualizuje węzłów usługi Azure Automation DSC |
> | Akcja | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Pobiera TypeFields usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/python2Packages/delete | Usuwa pakiet w ramach usługi Azure Automation języka Python 2 |
> | Akcja | Microsoft.Automation/automationAccounts/python2Packages/read | Pobiera pakiet w ramach usługi Azure Automation języka Python 2 |
> | Akcja | Microsoft.Automation/automationAccounts/python2Packages/write | Tworzy lub aktualizuje pakiet w ramach usługi Azure Automation języka Python 2 |
> | Akcja | Microsoft.Automation/automationAccounts/python3Packages/delete | Usuwa pakiet usługi Azure Automation Python 3 |
> | Akcja | Microsoft.Automation/automationAccounts/python3Packages/read | Pobiera pakiet usługi Azure Automation Python 3 |
> | Akcja | Microsoft.Automation/automationAccounts/python3Packages/write | Tworzy lub aktualizuje pakiet usługi Azure Automation Python 3 |
> | Akcja | Microsoft.Automation/automationAccounts/read | Pobiera konto usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/content/read | Pobiera zawartość elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/delete | Usuwa element runbook usługi Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Tworzy zawartość wersji roboczej elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Pobiera wyniki operacji wersję roboczą elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/read | Pobiera wersję roboczą elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Pobiera zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Wznawia zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Zatrzymuje zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Wstrzymuje zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Tworzy zadanie testowe wersji roboczej elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Cofnij operacje edycji próbnej wersji elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Pobiera liczbę elementów runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publikuje wersję roboczą elementu runbook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element runbook usługi Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/write | Tworzy lub aktualizuje element runbook usługi Automation |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/delete | Usuwa zasób harmonogramu usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/getCount/action | Pobiera liczba harmonogramów usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/read | Pobiera zasób harmonogramu usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/write | Tworzy lub aktualizuje zasób harmonogramu usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Usuwa konfiguracji aktualizacji oprogramowania usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Pobiera konfiguracji aktualizacji oprogramowania usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Tworzy lub aktualizuje konfiguracji aktualizacji oprogramowania automatyzacji platformy Azure |
> | Akcja | Microsoft.Automation/automationAccounts/statistics/read | Pobiera statystyki usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Uruchom maszynę wdrażania aktualizacji usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Pobiera zadanie poprawki zarządzania aktualizacji usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/usages/read | Pobiera użycia usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/variables/delete | Usuwa zasób zmiennej usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/variables/read | Odczytuje zasób zmiennej usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/variables/write | Tworzy lub aktualizuje zasób zmiennej usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/delete | Usuń zadanie obserwatora usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/read | Pobiera zadanie obserwatora usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/start/action | Uruchom zadanie obserwatora usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/stop/action | Zatrzymaj zadanie obserwatora usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/streams/read | Pobiera strumień zadań usługi Azure Automation obserwatora |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Usuwanie akcji zadania obserwatora usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Pobiera zadania akcji obserwatora usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Tworzenie akcji zadania obserwatora usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/write | Tworzy zadanie obserwatora usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/webhooks/action | Generuje identyfikator URI dla elementu webhook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/webhooks/delete | Usuwa element webhook usługi Azure Automation  |
> | Akcja | Microsoft.Automation/automationAccounts/webhooks/read | Odczytuje element webhook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/webhooks/write | Tworzy lub aktualizuje element webhook usługi Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/write | Tworzy lub aktualizuje konto usługi Azure Automation |
> | Akcja | Microsoft.Automation/operations/read | Pobiera dostępne operacje dla zasobów usługi Azure Automation |
> | Akcja | Microsoft.Automation/register/action | Rejestruje subskrypcję usługi Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Usuń zasób katalogu B2C |
> | Akcja | Microsoft.AzureActiveDirectory/b2cDirectories/read | Wyświetl zasób katalogu B2C |
> | Akcja | Microsoft.AzureActiveDirectory/b2cDirectories/write | Utwórz lub zaktualizuj zasób katalogu B2C |
> | Akcja | Microsoft.AzureActiveDirectory/b2ctenants/read | Wyświetla listę wszystkich dzierżawach B2C, gdzie użytkownik jest członkiem |
> | Akcja | Microsoft.AzureActiveDirectory/operations/read | Odczytywać wszystkie operacje interfejsu API dostępne dla dostawcy zasobów Microsoft.AzureActiveDirectory |
> | Akcja | Microsoft.AzureActiveDirectory/register/action | Rejestruj subskrypcję dostawcy zasobów Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AzureStack/Operations/read | Pobiera właściwości operacji dostawcy zasobów |
> | Akcja | Microsoft.AzureStack/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.AzureStack |
> | Akcja | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Usuwa subskrypcję klienta usługi Azure Stack |
> | Akcja | Microsoft.AzureStack/registrations/customerSubscriptions/read | Pobiera właściwości subskrypcji usługi Azure Stack klienta |
> | Akcja | Microsoft.AzureStack/registrations/customerSubscriptions/write | Tworzy lub aktualizuje subskrypcją usługi Azure Stack klienta |
> | Akcja | Microsoft.AzureStack/registrations/delete | Usuwa rejestrację w usłudze Azure Stack |
> | Akcja | Microsoft.AzureStack/registrations/getActivationKey/action | Pobiera najnowszego klucza aktywacji usługi Azure Stack |
> | Akcja | Microsoft.AzureStack/registrations/products/listDetails/action | Pobiera rozszerzone szczegóły produktu usługi Azure Stack w portalu Marketplace |
> | Akcja | Microsoft.AzureStack/registrations/products/read | Pobiera właściwości produktu usługi Azure Stack w portalu Marketplace |
> | Akcja | Microsoft.AzureStack/registrations/read | Pobiera właściwości rejestracji w usłudze Azure Stack |
> | Akcja | Microsoft.AzureStack/registrations/write | Tworzy lub aktualizuje rejestracji w usłudze Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Batch/batchAccounts/applications/delete | Usuwa aplikację |
> | Akcja | Microsoft.Batch/batchAccounts/applications/read | Wyświetla listę aplikacji lub pobiera właściwości aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktywuje pakietu aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/delete | Usunięcie pakietu aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/read | Pobiera właściwości pakietu aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/write | Tworzy nowy pakiet aplikacji lub aktualizuje istniejący pakiet aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/write | Tworzy nową aplikację lub aktualizuje istniejącą aplikację |
> | Akcja | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Pobiera wyniki długotrwałej operacji certyfikat na koncie usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Anuluje nie powiodło się usunięcie certyfikatu na konto usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/delete | Usuwa certyfikat z kontem usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/read | Wyświetla listę certyfikatów na koncie usługi Batch lub pobiera właściwości certyfikatu |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/write | Tworzy nowy certyfikat na koncie usługi Batch lub aktualizuje istniejący certyfikat |
> | Akcja | Microsoft.Batch/batchAccounts/delete | Usuwa konto usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/listkeys/action | Listy dostępu do kluczy konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/operationResults/read | Pobiera wyniki długotrwałej operacji konto usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/poolOperationResults/read | Pobiera wyniki długotrwałej operacji puli na koncie usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/pools/delete | Usuwa puli z konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Wyłącza automatyczne skalowanie puli konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/pools/read | Wyświetla pule na konto usługi Batch lub pobiera właściwości puli |
> | Akcja | Microsoft.Batch/batchAccounts/pools/stopResize/action | Zatrzymuje na bieżące zmiany rozmiaru operacji w puli konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/pools/write | Tworzy nową pulę na koncie usługi Batch lub aktualizuje istniejącą pulę |
> | Akcja | Microsoft.Batch/batchAccounts/read | Wyświetla wszystkie konta usługi Batch lub pobiera właściwości konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/regeneratekeys/action | Odtwarza uzyskać dostęp do kluczy dla konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchronizuje klucze dostępu dla konta magazynu automatycznie skonfigurowany dla konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/write | Tworzy nowe konto usługi Batch lub aktualizuje istniejące konto usługi Batch |
> | Akcja | Microsoft.Batch/locations/accountOperationResults/read | Pobiera wyniki długotrwałej operacji konto usługi Batch |
> | Akcja | Microsoft.Batch/locations/checkNameAvailability/action | Sprawdza, czy nazwa konta jest prawidłowy, a nie w użyciu. |
> | Akcja | Microsoft.Batch/locations/quotas/read | Pobiera przydziały usługi Batch w określonej subskrypcji w określonym regionie platformy Azure |
> | Akcja | Microsoft.Batch/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.Batch |
> | Akcja | Microsoft.Batch/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Batch i włącza funkcję tworzenia konta usługi Batch |
> | Akcja | Microsoft.Batch/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Batch, co uniemożliwia tworzenie kont usługi Batch |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Billing/billingAccounts/departments/read | Lista wszystkich działach w ramach rozliczeń zakresu konta |
> | Akcja | Microsoft.Billing/billingAccounts/enrollmentAccounts/read | Wyświetlanie listy wszystkich kont rejestracji w ramach rozliczeń zakresu konta |
> | Akcja | Microsoft.Billing/billingAccounts/read | Wszystkie konta rozliczeniowego, użytkownika, który ma dostęp do listy |
> | Akcja | Microsoft.Billing/billingPeriods/read | Wyświetla listę dostępnych okresów rozliczeniowych |
> | Akcja | Microsoft.Billing/departments/read | Wszystkie działy, na którym użytkownik ma dostęp do listy |
> | Akcja | Microsoft.Billing/invoices/read | Wyświetla dostępne faktur |
> | Akcja | Microsoft.Billing/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.Billing |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.BingMaps/mapApis/Delete | Operacja usuwania |
> | Akcja | Microsoft.BingMaps/mapApis/listSecrets/action | Wyświetl klucze tajne |
> | Akcja | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Odczyt tokenu autoryzacji dla zasobu jednokrotnego |
> | Akcja | Microsoft.BingMaps/mapApis/Read | Operacja odczytu |
> | Akcja | Microsoft.BingMaps/mapApis/regenerateKey/action | Generuje ponownie klucz |
> | Akcja | Microsoft.BingMaps/mapApis/Write | Operacja zapisu |
> | Akcja | Microsoft.BingMaps/Operations/read | Opis operacji. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Blockchain/blockchainMembers/delete | Usuwa istniejącą składową łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Pobiera lub wyświetla ich listę istniejących kluczy interfejsu Api elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/read | Pobiera lub wyświetla ich listę istniejących członków łańcucha bloków. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Łączy do węzła transakcji składowej łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Usuwa istniejący węzeł transakcji składowej łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Pobiera lub wyświetla ich listę istniejących kluczy interfejsu Api węzła transakcji łańcucha bloków elementu członkowskiego. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Pobiera lub wyświetla ich listę istniejących węzłów transakcji składowej łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Tworzy lub aktualizuje węzła transakcji składowej łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/write | Tworzy lub aktualizuje element członkowski łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Pobiera wyniki operacji elementów członkowskich łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/locations/checkNameAvailability/action | Sprawdza, czy ta nazwa zasobu jest prawidłowa i nie jest używany. |
> | Akcja | Microsoft.Blockchain/operations/read | Wyświetl listę wszystkich operacji dostawcy zasobów usługi Microsoft łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/register/action | Rejestruje subskrypcję dostawcy zasobów łańcucha bloków. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Odczytaj wszelkie artefakty planu |
> | Akcja | Microsoft.Blueprint/blueprintAssignments/delete | Usuń wszelkie artefakty planu |
> | Akcja | Microsoft.Blueprint/blueprintAssignments/read | Odczytaj wszelkie artefakty planu |
> | Akcja | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Pobieranie obiektu jednostki usługi Azure schematy identyfikatora. |
> | Akcja | Microsoft.Blueprint/blueprintAssignments/write | Utwórz lub zaktualizuj wszelkie artefakty planu |
> | Akcja | Microsoft.Blueprint/blueprints/artifacts/delete | Usuń wszelkie artefakty planu |
> | Akcja | Microsoft.Blueprint/blueprints/artifacts/read | Odczytaj wszelkie artefakty planu |
> | Akcja | Microsoft.Blueprint/blueprints/artifacts/write | Utwórz lub zaktualizuj wszelkie artefakty planu |
> | Akcja | Microsoft.Blueprint/blueprints/delete | Usuń wszelkie plany |
> | Akcja | Microsoft.Blueprint/blueprints/read | Odczytaj wszelkie plany |
> | Akcja | Microsoft.Blueprint/blueprints/versions/artifacts/read | Odczytaj wszelkie artefakty planu |
> | Akcja | Microsoft.Blueprint/blueprints/versions/delete | Usuń wszelkie plany |
> | Akcja | Microsoft.Blueprint/blueprints/versions/read | Odczytaj wszelkie plany |
> | Akcja | Microsoft.Blueprint/blueprints/versions/write | Utwórz lub zaktualizuj wszelkie plany |
> | Akcja | Microsoft.Blueprint/blueprints/write | Utwórz lub zaktualizuj wszelkie plany |
> | Akcja | Microsoft.Blueprint/register/action | Rejestruje dostawcę zasobów platformy Azure plany |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.BotService/botServices/channels/delete | Usuwanie usługi Bot |
> | Akcja | Microsoft.BotService/botServices/channels/read | Usługa Bot odczytu |
> | Akcja | Microsoft.BotService/botServices/channels/write | Usługa Bot zapisu |
> | Akcja | Microsoft.BotService/botServices/connections/delete | Usuwanie usługi Bot |
> | Akcja | Microsoft.BotService/botServices/connections/read | Usługa Bot odczytu |
> | Akcja | Microsoft.BotService/botServices/connections/write | Usługa Bot zapisu |
> | Akcja | Microsoft.BotService/botServices/delete | Usuwanie usługi Bot |
> | Akcja | Microsoft.BotService/botServices/read | Usługa Bot odczytu |
> | Akcja | Microsoft.BotService/botServices/write | Usługa Bot zapisu |
> | Akcja | Microsoft.BotService/locations/operationresults/read | Odczytaj stan operacji asynchronicznej |
> | Akcja | Microsoft.BotService/Operations/read | Odczytaj operacje dla wszystkich typów zasobów |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Cache/checknameavailability/action | Sprawdza, czy nazwa jest dostępna do użycia z usługą Redis Cache |
> | Akcja | Microsoft.Cache/locations/operationresults/read | Pobiera wynik długotrwałej uruchomiona operacja, dla której nagłówek "Location" został wcześniej zwrócony do klienta |
> | Akcja | Microsoft.Cache/operations/read | Wyświetla listę operacji obsługiwanych przez dostawcę "Microsoft.Cache". |
> | Akcja | Microsoft.Cache/redis/delete | Usuń całą pamięć podręczną Redis |
> | Akcja | Microsoft.Cache/redis/export/action | Wyeksportuj dane usługi Redis do obiektów blob magazynu prefiksem w określonym formacie |
> | Akcja | Microsoft.Cache/redis/firewallRules/delete | Usuwanie reguły zapory IP pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/firewallRules/read | Pobierz reguły zapory IP pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/firewallRules/write | Edytuj reguły zapory IP pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/forceReboot/action | Wymuś ponowny rozruch wystąpienia pamięci podręcznej, nawet z utratą danych. |
> | Akcja | Microsoft.Cache/redis/import/action | Zaimportuj dane w podanym formacie z wielu obiektów blob do usługi Redis |
> | Akcja | Microsoft.Cache/redis/linkedservers/delete | Usuń podłączony serwer z pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/linkedservers/read | Pobierz połączone serwery skojarzona z pamięcią podręczną redis. |
> | Akcja | Microsoft.Cache/redis/linkedservers/write | Dodaj połączony serwer do pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/listKeys/action | Wyświetl wartości kluczy dostępu pamięci podręcznej Redis w portalu zarządzania |
> | Akcja | Microsoft.Cache/redis/listUpgradeNotifications/read | Lista najnowszych powiadomień o uaktualnieniach dla dzierżawy pamięci podręcznej. |
> | Akcja | Microsoft.Cache/redis/metricDefinitions/read | Pobiera dostępne metryki pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/patchSchedules/delete | Usuń harmonogram stosowania poprawek pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/patchSchedules/read | Pobiera harmonogram stosowania poprawek pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/patchSchedules/write | Modyfikuj harmonogram stosowania poprawek pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/read | Wyświetl ustawienia i konfigurację pamięci podręcznej Redis w portalu zarządzania |
> | Akcja | Microsoft.Cache/redis/regenerateKey/action | Zmień wartość kluczy dostępu pamięci podręcznej Redis w portalu zarządzania |
> | Akcja | Microsoft.Cache/redis/start/action | Uruchom wystąpienie pamięci podręcznej. |
> | Akcja | Microsoft.Cache/redis/stop/action | Zatrzymaj wystąpienie pamięci podręcznej. |
> | Akcja | Microsoft.Cache/redis/write | Zmodyfikuj ustawienia pamięci podręcznej Redis i konfiguracji w portalu zarządzania |
> | Akcja | Microsoft.Cache/register/action | Rejestruje dostawcę zasobów "Microsoft.Cache" z subskrypcji |
> | Akcja | Microsoft.Cache/unregister/action | Wyrejestrowuje dostawcę zasobów "Microsoft.Cache" z subskrypcji |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Capacity/appliedreservations/read | Odczyt wszystkich rezerwacji |
> | Akcja | Microsoft.Capacity/calculateexchange/action | Oblicza ilość programu exchange i ceny zakupu nowego i przywraca zasadom błędy. |
> | Akcja | Microsoft.Capacity/calculateprice/action | Oblicz cenę dowolnej rezerwacji |
> | Akcja | Microsoft.Capacity/catalogs/read | Wykaz odczytu rezerwacji |
> | Akcja | Microsoft.Capacity/checkoffers/action | Sprawdź wszystkie oferty subskrypcji |
> | Akcja | Microsoft.Capacity/checkscopes/action | Sprawdź dowolnej subskrypcji |
> | Akcja | Microsoft.Capacity/commercialreservationorders/read | Pobierz zamówienia rezerwacji utworzone w ramach dowolnej dzierżawy |
> | Akcja | Microsoft.Capacity/exchange/action | Exchange żadnych rezerwacji |
> | Akcja | Microsoft.Capacity/operations/read | Wszelkie operacja odczytu |
> | Akcja | Microsoft.Capacity/register/action | Rejestruje dostawcę zasobów pojemności i włącza funkcję tworzenia zasobów pojemności. |
> | Akcja | Microsoft.Capacity/reservationorders/action | Aktualizuj żadnych rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/availablescopes/action | Znajdź wszystkie dostępne zakresu |
> | Akcja | Microsoft.Capacity/reservationorders/calculaterefund/action | Oblicza ilość zwrot kosztów i ceny zakupu nowego i przywraca zasadom błędy. |
> | Akcja | Microsoft.Capacity/reservationorders/delete | Usuń wszelkie rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/merge/action | Scal wszystkie rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/read | Odczyt wszystkich rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/action | Aktualizuj żadnych rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/delete | Usuń wszelkie rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/read | Odczyt wszystkich rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/revisions/read | Odczyt wszystkich rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/write | Utwórz zastrzeżenie |
> | Akcja | Microsoft.Capacity/reservationorders/return/action | Zwróć wszystkie rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/split/action | Podziel żadnych rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/swap/action | Zamień wszystkie rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/write | Utwórz zastrzeżenie |
> | Akcja | Microsoft.Capacity/tenants/register/action | Zarejestruj dowolnej dzierżawy |
> | Akcja | Microsoft.Capacity/unregister/action | Wyrejestruj dowolnej dzierżawy |
> | Akcja | Microsoft.Capacity/validatereservationorder/action | Sprawdzanie poprawności dowolnego rezerwacji |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Akcja | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Akcja | Microsoft.Cdn/edgenodes/delete |  |
> | Akcja | Microsoft.Cdn/edgenodes/read |  |
> | Akcja | Microsoft.Cdn/edgenodes/write |  |
> | Akcja | Microsoft.Cdn/operationresults/delete |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Akcja | Microsoft.Cdn/operationresults/read |  |
> | Akcja | Microsoft.Cdn/operationresults/write |  |
> | Akcja | Microsoft.Cdn/operations/read |  |
> | Akcja | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Akcja | Microsoft.Cdn/profiles/delete |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/read |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/write |  |
> | Akcja | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Akcja | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Akcja | Microsoft.Cdn/profiles/read |  |
> | Akcja | Microsoft.Cdn/profiles/write |  |
> | Akcja | Microsoft.Cdn/register/action | Rejestruje subskrypcję dostawcy zasobów CDN i umożliwia tworzenie profilów CDN. |
> | Akcja | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Usuń istniejący certyfikat |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Pobierz listę certyfikatów |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejącą |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/Delete | Usuwanie istniejącego certyfikatu usługi App Service |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/Read | Pobierz listę zamówień certyfikatów |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Ponowne wystawianie zamówienia istniejącego certyfikatu |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Odnawianie istniejącego zamówienia certyfikatu |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Ponowne wysyłanie wiadomości e-mail z certyfikatu |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Wyślij ponownie żądanie z wiadomości e-mail do innego adresu e-mail |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Pobieranie pieczętowanie witryny dla wystawionego certyfikatu usługi App Service |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Pobieranie listy akcji certyfikatów |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Pobieranie historii wiadomości e-mail certyfikatu |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Weryfikowanie własności domeny |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/Write | Dodaj nowe zamówienia certyfikatu lub zaktualizuj istniejącą |
> | Akcja | Microsoft.CertificateRegistration/operations/Read | Lista wszystkich operacji z rejestracji certyfikatu usługi dla aplikacji |
> | Akcja | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Jednostki usługi aprowizacji dla nazwy głównej usługi aplikacji |
> | Akcja | Microsoft.CertificateRegistration/register/action | Zarejestruj dostawcę zasobów Certificates firmy Microsoft dla subskrypcji |
> | Akcja | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Sprawdź poprawność obiektu zakup certyfikatu bez przesyłania |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ClassicCompute/capabilities/read | Pokazuje możliwości |
> | Akcja | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Sprawdza dostępność danej nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Pobiera dostępność danej nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/active/write | Ustawia nazwę aktywnej domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Pokaż zestaw dostępności dla zasobu. |
> | Akcja | Microsoft.ClassicCompute/domainNames/capabilities/read | Pokazuje możliwości nazwy domeny |
> | Akcja | Microsoft.ClassicCompute/domainNames/delete | Usuń nazwy domen dla zasobów. |
> | Akcja | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Pokazuje miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Uzyskiwanie roli na miejsce wdrożenia nazwy domeny |
> | Akcja | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Pobierz wystąpienie roli dla roli w miejsce wdrożenia nazwy domeny |
> | Akcja | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Pobierz stan miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Dodaj stan miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Pobierz domeny uaktualnień dla miejsca wdrożenia od nazwy domeny |
> | Akcja | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Aktualizowanie domeny uaktualnień dla miejsca wdrożenia od nazwy domeny |
> | Akcja | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft.ClassicCompute/domainNames/extensions/delete | Usuń rozszerzenia nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Odczytuje stan operacji dla domeny rozszerzenia nazw. |
> | Akcja | Microsoft.ClassicCompute/domainNames/extensions/read | Zwraca wartość rozszerzenia nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/extensions/write | Dodaj rozszerzenia nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Usuń nowe wewnętrzną usługę równoważenia obciążenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Odczytuje stan operacji dla wewnętrznych modułów równoważenia obciążenia nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Pobiera wewnętrzne usługi równoważenia obciążenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Tworzy nowy wewnętrzną usługę równoważenia obciążenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Odczytuje stan operacji dla zestawy punktów końcowych z równoważeniem obciążenia nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Pobierz zestawy punktów końcowych ze zrównoważonym obciążeniem. |
> | Akcja | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Dodaj zestaw punktów końcowych ze zrównoważonym obciążeniem. |
> | Akcja | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Pobierz stan operacji dla nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Odczytuje stan operacji dla domeny rozszerzenia nazw. |
> | Akcja | Microsoft.ClassicCompute/domainNames/read | Zwraca nazwy domen dla zasobów. |
> | Akcja | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Usuń używane certyfikaty usługi. |
> | Akcja | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Odczytuje stan operacji dla domeny certyfikatów usługi nazw. |
> | Akcja | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Zwraca używane certyfikaty usługi. |
> | Akcja | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Dodaje lub modyfikuje używane certyfikaty usługi. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Przerywa migrację miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Zatwierdza migrację miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/delete | Usuwa dane miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Odczytuje stan operacji dla domeny gniazd nazw. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Przygotowuje migrację miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/read | Pokazuje miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Usuń odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Odczytuje stan operacji dla odwołań rozszerzenia ról w programie domeny nazw miejsc. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Zwraca odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Dodaje lub modyfikuje odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Pobierz metryki definicji roli dla nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Pobierz metryki roli dla nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Pobierz stan operacji dla roli miejsca nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Pobiera definicje metryk. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/read | Pobiera rolę dla miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Pobiera plik połączenia pulpitu zdalnego dla wystąpienia roli dla roli miejsca nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Pobiera stan operacji dla wystąpienia roli dla roli miejsca nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Pobiera wystąpienie roli. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Ponownie kompiluje wystąpienie roli. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Odtwarza z obrazu wystąpienia roli. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Uruchamia ponownie wystąpienia roli. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Uzyskaj sku roli miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/write | Dodaj rolę dla miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/start/action | Uruchamia miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Zmienia stan miejsca wdrożenia na zatrzymane. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Zmienia stan miejsca wdrożenia pracę. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/stop/action | Wstrzymuje miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Przewodnik uaktualnienia domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Weryfikuje migrację miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft.ClassicCompute/domainNames/swap/action | Zamienia miejsce przejściowe z miejscem produkcyjnym. |
> | Akcja | Microsoft.ClassicCompute/domainNames/write | Dodaje lub modyfikuje nazwy domen dla zasobów. |
> | Akcja | Microsoft.ClassicCompute/moveSubscriptionResources/action | Przenieś wszystkie zasoby klasyczne do innej subskrypcji. |
> | Akcja | Microsoft.ClassicCompute/operatingSystemFamilies/read | Wyświetla listę rodzin systemów operacyjnych gościa dostępnych na platformie Microsoft Azure i zawiera także listę wersji systemów operacyjnych dostępnych w ramach każdej rodziny. |
> | Akcja | Microsoft.ClassicCompute/operatingSystems/read | Lista wersji systemu operacyjnego gościa, które są obecnie dostępne w systemie Microsoft Azure. |
> | Akcja | Microsoft.ClassicCompute/operations/read | Pobiera listę operacji. |
> | Akcja | Microsoft.ClassicCompute/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.ClassicCompute/quotas/read | Zwiększ limit przydziału dla subskrypcji. |
> | Akcja | Microsoft.ClassicCompute/register/action | Zarejestruj się w usłudze Classic Compute |
> | Akcja | Microsoft.ClassicCompute/resourceTypes/skus/read | Pobiera listę jednostek Sku dla obsługiwanych typów zasobów. |
> | Akcja | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Sprawdź dostępność subskrypcji dla klasycznej operacji przenoszenia. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla maszyn wirtualnych skojarzonych z grupami zabezpieczeń sieci. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Pobiera dostępne operacje asynchroniczne |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Dołącza dysk danych do maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/capture/action | Przechwytywanie maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/delete | Usuwa maszyny wirtualne. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Odłącza dysk z danymi od maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Pobierz ustawienia diagnostyczne maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/disks/read | Pobiera listę dysków z danymi |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Pobiera plik RDP dla maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Odczytuje stan operacji dla rozszerzeń maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/extensions/read | Pobiera rozszerzenie maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/extensions/write | Umieszcza rozszerzenie maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Pobierz definicję metryki maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/metrics/read | Pobiera metryki. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Usuwa grupę zabezpieczeń sieci skojarzona z interfejsem sieciowym. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla maszyn wirtualnych skojarzonych z grupami zabezpieczeń sieci. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z interfejsem sieci. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Dodaje grupę zabezpieczeń sieci skojarzona z interfejsem sieciowym. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Odczytuje stan operacji dla maszyn wirtualnych. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Przeprowadza konserwację na maszynie wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Pobiera definicje metryk. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/read | Pobiera listę maszyn wirtualnych. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Ponownie wdraża maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/restart/action | Powoduje ponowne uruchomienie maszyn wirtualnych. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Zamknij maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/start/action | Uruchom maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/stop/action | Zatrzymuje maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/write | Dodaje lub modyfikuje maszyny wirtualne. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Uzyskaj expressroute między stan operacji dla połączenia. |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Usuń expressroute między połączenia komunikacji równorzędnej. |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Uzyskaj expressroute między stan operacji dla połączenia komunikacji równorzędnej. |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Uzyskaj expressroute między połączenia komunikacji równorzędnej. |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Dodaj expressroute między połączenia komunikacji równorzędnej. |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Pobierz express route wielu połączeń. |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Dodaj expressroute obejmujące wiele połączeń. |
> | Akcja | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Pobiera listę obsługiwanych urządzeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grupy zabezpieczeń sieci. |
> | Akcja | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne grup zabezpieczeń sieci |
> | Akcja | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne, ta operacja jest uzupełniana przez dostawcę zasobów szczegółowych informacji sieciowe grupy zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Pobiera zdarzenia dla sieciowej grupy zabezpieczeń |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Usuwa regułę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Odczytuje stan operacji dla reguł zabezpieczeń sieciowych grup zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Pobiera regułę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Dodaje lub aktualizuje regułę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/write | Dodaje nową sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/operations/read | Pobierz operacje klasycznej sieci. |
> | Akcja | Microsoft.ClassicNetwork/quotas/read | Zwiększ limit przydziału dla subskrypcji. |
> | Akcja | Microsoft.ClassicNetwork/register/action | Zarejestruj się w usłudze Classic Network |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/delete | Usuwanie zastrzeżonego adresu Ip. |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/join/action | Dołącz zarezerwowany adres Ip |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/link/action | Połącz zarezerwowany adres Ip |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Odczytuje stan operacji dla zarezerwowanych adresów IP. |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/read | Pobiera zastrzeżone adresy IP |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/write | Dodaj nowy zastrzeżony adres Ip |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Przerywa migrację sieci wirtualnej |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Pokazuje możliwości |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Sprawdza dostępność danego adresu IP w sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Zatwierdza migrację sieci wirtualnej |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/delete | Usuwa sieć wirtualną. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Odwołuje cofnięcie certyfikatu klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Przeczytaj odwołanych certyfikatów klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Cofa certyfikat klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Usuwa certyfikat klienta bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Pobiera certyfikat na podstawie odcisku palca. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Wyświetla pakiet certyfikatu bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Znajdź klienta programu certyfikatów głównych. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Przekazuje nowy certyfikat główny klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Łączy połączenie bramy między lokacjami. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Rozłącza połączenie bramy między lokacjami. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Pobiera listę połączeń. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testuje połączenie bramy między lokacjami. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Usuwa bramę sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Pobiera skrypt konfiguracji urządzenia. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Pobiera dane diagnostyczne bramy. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Pobiera klucz usługi obwodu. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Wyświetla pakiet bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Odczytuje stan operacji dla bram sieci wirtualnych. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Pobiera pakiet bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Pobiera bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Uruchamia diagnostyczne dla bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Zatrzymuje diagnostyczne dla bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Dodaje bramę sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/join/action | Łączy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Odczytuje stan operacji dla sieci wirtualnych. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Łączy równorzędnie sieć wirtualną z inną siecią wirtualną. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Przygotowuje migrację sieci wirtualnej |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/read | Pobierz sieć wirtualną. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Usuwa serwer proxy komunikacji równorzędnej zdalna sieć wirtualna. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Pobiera serwer proxy komunikacji równorzędnej zdalna sieć wirtualna. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Dodaje lub aktualizuje serwer proxy komunikacji równorzędnej zdalna sieć wirtualna. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla skojarzonej sieciowej grupy zabezpieczeń podsieci sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Weryfikuje migrację sieci wirtualnej |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Pobiera komunikację równorzędną sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/write | Dodaj nową sieć wirtualną. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ClassicStorage/capabilities/read | Pokazuje możliwości |
> | Akcja | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Sprawdza dostępność konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Uzyskaj dostępność konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/disks/read | Zwraca dysk konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/images/operationstatuses/read | Pobiera stan operacji dla obrazu. |
> | Akcja | Microsoft.ClassicStorage/images/read | Zwraca obraz. |
> | Akcja | Microsoft.ClassicStorage/operations/read | Pobiera operacje magazynu klasycznego |
> | Akcja | Microsoft.ClassicStorage/osImages/read | Zwraca obraz systemu operacyjnego. |
> | Akcja | Microsoft.ClassicStorage/osPlatformImages/read | Pobiera obraz platformy systemu operacyjnego. |
> | Akcja | Microsoft.ClassicStorage/publicImages/read | Pobiera publiczny obraz maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicStorage/quotas/read | Zwiększ limit przydziału dla subskrypcji. |
> | Akcja | Microsoft.ClassicStorage/register/action | Zarejestruj w magazynie klasycznym |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Przerywa migrację konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Zatwierdza migrację konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/delete | Usuń konto magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/delete | Usuwa dany dysk konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/read | Zwraca dysk konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/write | Dodaje dysk konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/images/delete | Usuwa obraz konta magazynu podanego. (Przestarzałe. Użyj "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Zwraca stan operacji obraz konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/images/read | Zwraca obraz konta magazynu. (Przestarzałe. Użyj "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla klucze dostępu dla kont magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Usuwa obraz systemu operacyjnego konta magazynu podanego. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/osImages/read | Zwraca obraz systemu operacyjnego konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/osImages/write | Dodaje obraz systemu operacyjnego konta magazynu podanego. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Przygotowuje migrację konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/read | Zwraca konto magazynu z podanym kontem. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Pobierz ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Pobiera definicje metryk. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Pobiera metryki. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/read | Pobierz dostępne usługi. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Weryfikuje migrację konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Usuwa dany obraz maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Pobiera stan operacji dla podanej maszyny wirtualnej obrazu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Zwraca obraz maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Dodaje dany obraz maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/write | Dodaje nowe konto magazynu. |
> | Akcja | Microsoft.ClassicStorage/vmImages/read | Wyświetla listę obrazów maszyn wirtualnych. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | Ta operacja wyodrębnia bogaty zestaw funkcji visual na podstawie zawartości obrazu.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Ta operacja zwraca pola ograniczeń wokół najważniejszych obszarów obrazu. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Ta operacja spowoduje wygenerowanie opis obrazu ludzi języka do odczytu z postaci kompletnych zdań.<br> Opis jest oparty na zbiór zawartości tagami, które również są zwracane przez operację.<br>Więcej niż jeden opis mogą być generowane dla każdego obrazu.<br> Opisy są uporządkowane według ich współczynnik ufności.<br>Wszystkie opisy są w języku angielskim. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Ta operacja wykonanie wykrywanie obiektów dla określonego obrazu.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Ta operacja generuje obraz miniatury przy użyciu określonych przez użytkownika szerokość i wysokość.<br> Domyślnie usługa analizuje obraz identyfikuje regionu zainteresowania (ROI) i generuje inteligentne współrzędne przycinania zwrot z inwestycji w oparciu.<br> Inteligentne przycinanie pomaga w określeniu współczynnik proporcji, która różni się od tego obrazu wejściowego |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Ta operacja rozpoznaje zawartości w ramach obrazu, stosując modelu specyficznego dla domeny.<br> Za pomocą żądania GET /models można pobrać listy modeli specyficznych dla domeny, które są obsługiwane przez interfejs API przetwarzania obrazów.<br> Obecnie interfejs API zapewnia następujące modeli specyficznych dla domeny: osobistości, charakterystycznych elementów krajobrazu. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Ta operacja zwraca listę wszystkich modeli specyficznych dla domeny, które są obsługiwane przez interfejs API przetwarzania obrazów.  Obecnie interfejs API obsługuje następujące modeli specyficznych dla domeny: aparat rozpoznawania osobistości, aparat rozpoznawania charakterystycznych elementów krajobrazu. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | Optyczne rozpoznawanie znaków (OCR) wykrywa tekst w obrazie i wyodrębnia rozpoznane znaki do strumienia znaków, można używać maszyny.    |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Pobierz wynik operacji rozpoznawanie tekstu za pomocą tego interfejsu. Korzystając z interfejsu rozpoznawanie tekstu odpowiedzi zawiera pole o nazwie "Lokalizacja operacji". Pole "Operacja lokalizacja" zawiera adres URL, do którego należy użyć dla operacji Pobierz rozpoznaje wynik operacji tekstu. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Ta operacja generuje listę słów lub tagi, które mają zastosowanie do zawartości podany obraz.<br>Interfejs API przetwarzania obrazów może być zwracać tagi na podstawie obiektów, życia żywych, scenerii lub znaleziono w obrazach akcji.<br>W przeciwieństwie do kategorii tagi nie są zorganizowane według systemu hierarchiczne klasyfikacji, ale odnoszą się do zawartości obrazu.<br>Tagi mogą zawierać wskazówki, aby uniknąć niejednoznaczności lub udostępnić kontekst, na przykład tag "cello" mogą towarzyszyć wskazówka "Instrumentacji w kompozycją".<br>Wszystkie tagi są w języku angielskim. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Ten interfejs jest używany w celu uzyskania rozpoznawanie tekstu, wynik operacji. Adres URL dla tego interfejsu powinny zostać pobrane z <b>"Operacja lokalizacja"</b> pola zwrócony z interfejsu rozpoznawanie tekstu. |
> | Akcja | Microsoft.CognitiveServices/accounts/delete | Usuwa konta interfejsów API |
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
> | Akcja | Microsoft.CognitiveServices/accounts/listKeys/action | Wyświetl listę kluczy |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Pobiera prognozowania opublikowanych punkt końcowy dla danego zapytania. |
> | Akcja | Microsoft.CognitiveServices/accounts/read | Odczytuje konta interfejsów API. |
> | Akcja | Microsoft.CognitiveServices/accounts/regenerateKey/action | Ponowne generowanie klucza |
> | Akcja | Microsoft.CognitiveServices/accounts/skus/read | Odczytuje dostępne jednostki SKU dla istniejącego zasobu. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | Interfejs API zwraca listę znanych jednostek oraz ogólne nazwane jednostek (\"osoby\", \"lokalizacji\", \"organizacji\" itp.) w danym dokumencie. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | Interfejs API zwraca listę ciągów wskazujących najważniejsze punkty w tekście wejściowym. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | Interfejs API zwraca wykryty język i wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 oznaczają 100% pewności, że określony język jest poprawny. Obsługiwanych jest 120 języków. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | Interfejs API zwraca wynik liczbowy z zakresu od 0 do 1.<br>Wyniki zbliżone do wartości 1 wskazują na pozytywną tonację, a wyniki zbliżone do wartości 0 wskazują na negatywną tonację.<br>Wynik 0,5 oznacza brak wskaźniki nastrojów klientów (np.<br>oświadczenie faktoid). |
> | Akcja | Microsoft.CognitiveServices/accounts/usages/read | Pobierz użycie przydziału dla istniejącego zasobu. |
> | Akcja | Microsoft.CognitiveServices/accounts/write | Zapisuje konta interfejsów API. |
> | Akcja | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Akcja | Microsoft.CognitiveServices/Operations/read | Lista wszystkich dostępnych operacji |
> | Akcja | Microsoft.CognitiveServices/register/action | Rejestruje subskrypcję dla usług Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Commerce/RateCard/read | Zwraca oferują danych, Miernik zasobów/metadanych i współczynniki dla danej subskrypcji. |
> | Akcja | Microsoft.Commerce/UsageAggregates/read | Pobiera Microsoft Azure — użytek przez subskrypcję. Wynik zawiera agreguje dane dotyczące użycia, subskrypcji i zasobów powiązane informacje w określonym czasie. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Compute/availabilitySets/delete | Usuwa zestaw dostępności |
> | Akcja | Microsoft.Compute/availabilitySets/read | Pobierz właściwości zestawu dostępności |
> | Akcja | Microsoft.Compute/availabilitySets/vmSizes/read | Wyświetl dostępne rozmiary, do tworzenia lub aktualizowania maszyny wirtualnej w zestawie dostępności |
> | Akcja | Microsoft.Compute/availabilitySets/write | Tworzy nowy zestaw dostępności lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/disks/beginGetAccess/action | Pobierz identyfikator URI sygnatury dostępu Współdzielonego dysku dla dostępu do obiektów blob |
> | Akcja | Microsoft.Compute/disks/delete | Usuwa dysk |
> | Akcja | Microsoft.Compute/disks/endGetAccess/action | Odwołanie identyfikatora URI sygnatury dostępu Współdzielonego dysku |
> | Akcja | Microsoft.Compute/disks/read | Pobierz właściwości dysku |
> | Akcja | Microsoft.Compute/disks/write | Tworzy nowy dysk lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/galleries/delete | Usuwa galerii |
> | Akcja | Microsoft.Compute/galleries/images/delete | Usuwa obraz z galerii |
> | Akcja | Microsoft.Compute/galleries/images/read | Pobiera właściwości Image z galerie |
> | Akcja | Microsoft.Compute/galleries/images/versions/delete | Usuwa wersję obrazu w galerii |
> | Akcja | Microsoft.Compute/galleries/images/versions/read | Pobiera właściwości wersja obrazu z galerii |
> | Akcja | Microsoft.Compute/galleries/images/versions/write | Tworzy nową wersję obrazu z galerii lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/galleries/images/write | Tworzy nowy obraz w galerii lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/galleries/read | Pobiera właściwości galerii |
> | Akcja | Microsoft.Compute/galleries/write | Tworzy nową galerię lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/hostGroups/delete | Usuwa grupy hostów |
> | Akcja | Microsoft.Compute/hostGroups/read | Pobierz właściwości grupy hostów |
> | Akcja | Microsoft.Compute/hostGroups/write | Tworzy nową grupę hostów lub aktualizuje istniejące grupy hostów |
> | Akcja | Microsoft.Compute/hosts/delete | Usuwa hosta |
> | Akcja | Microsoft.Compute/hosts/read | Pobierz właściwości hosta |
> | Akcja | Microsoft.Compute/hosts/write | Tworzy nowy host lub aktualizuje istniejący host |
> | Akcja | Microsoft.Compute/images/delete | Usuwa obraz |
> | Akcja | Microsoft.Compute/images/read | Pobierz właściwości obrazu |
> | Akcja | Microsoft.Compute/images/write | Tworzy nowy obraz lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/locations/capsOperations/read | Pobiera stan operacji asynchronicznej Caps |
> | Akcja | Microsoft.Compute/locations/diskOperations/read | Pobiera stan operacji asynchronicznej dysku |
> | Akcja | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Twórz dzienniki będzie wyświetlana łączna liczba żądań, przedział czasu do pomocy ograniczania diagnostyki. |
> | Akcja | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Twórz dzienniki, aby wyświetlić agregacje ograniczenia żądań, pogrupowanych według ResourceName, OperationName lub zastosowane zasady ograniczania. |
> | Akcja | Microsoft.Compute/locations/operations/read | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Pobierz właściwości oferty obrazu platformy |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Pobierz właściwości jednostki Sku obrazu platformy |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Pobierz właściwości wersji obrazu platformy |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Pobierz właściwości typu VMExtension |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Pobierz właściwości wersji VMExtension |
> | Akcja | Microsoft.Compute/locations/publishers/read | Pobierz właściwości wydawcy |
> | Akcja | Microsoft.Compute/locations/runCommands/read | Wyświetla listę dostępnych poleceń uruchamiania w lokalizacji |
> | Akcja | Microsoft.Compute/locations/usages/read | Pobiera limity usług i bieżącej ilości użycia zasobów obliczeniowych subskrypcji w lokalizacji |
> | Akcja | Microsoft.Compute/locations/vmSizes/read | Wyświetla dostępne rozmiary maszyny wirtualnej w lokalizacji |
> | Akcja | Microsoft.Compute/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.Compute |
> | Akcja | Microsoft.Compute/proximityPlacementGroups/delete | Usuwa grupę umieszczania odległości między elementami |
> | Akcja | Microsoft.Compute/proximityPlacementGroups/read | Pobierz właściwości grupy umieszczania odległości między elementami |
> | Akcja | Microsoft.Compute/proximityPlacementGroups/write | Tworzy nową grupę umieszczania sąsiedztwie lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.Compute |
> | Akcja | Microsoft.Compute/restorePointCollections/delete | Usuwa kolekcję punktów przywracania i zawiera punkty przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/read | Pobierz właściwości kolekcji punktów przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/restorePoints/delete | Usuwa punkt przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/restorePoints/read | Pobierz właściwości punktu przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Pobierz właściwości punktu przywracania z identyfikatorami URI SAS obiektów blob |
> | Akcja | Microsoft.Compute/restorePointCollections/restorePoints/write | Tworzy nowy punkt przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/write | Tworzy nową kolekcję punktów przywracania lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/sharedVMImages/delete | Usuwa element SharedVMImage |
> | Akcja | Microsoft.Compute/sharedVMImages/read | Pobierz właściwości elementu SharedVMImage |
> | Akcja | Microsoft.Compute/sharedVMImages/versions/delete | Usuń element SharedVMImageVersion |
> | Akcja | Microsoft.Compute/sharedVMImages/versions/read | Pobierz właściwości elementu SharedVMImageVersion |
> | Akcja | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replikuj element SharedVMImageVersion do regionów docelowych |
> | Akcja | Microsoft.Compute/sharedVMImages/versions/write | Utwórz nowy element SharedVMImageVersion lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Compute/sharedVMImages/write | Tworzy nowy element SharedVMImage lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/skus/read | Pobiera listę jednostek SKU Microsoft.Compute dostępnych dla Twojej subskrypcji |
> | Akcja | Microsoft.Compute/snapshots/beginGetAccess/action | Pobierz identyfikator URI sygnatury dostępu Współdzielonego migawki dla dostępu do obiektów blob |
> | Akcja | Microsoft.Compute/snapshots/delete | Usuń migawkę |
> | Akcja | Microsoft.Compute/snapshots/endGetAccess/action | Odwołanie identyfikatora URI sygnatury dostępu Współdzielonego migawki |
> | Akcja | Microsoft.Compute/snapshots/read | Pobierz właściwości migawki |
> | Akcja | Microsoft.Compute/snapshots/write | Utwórz nową migawkę lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Compute/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów Microsoft.Compute |
> | Akcja | Microsoft.Compute/virtualMachines/capture/action | Przechwytuje maszynę wirtualną przez skopiowanie wirtualnych dysków twardych oraz generuje szablon, który może służyć do tworzenie podobnych maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Konwertuje opartymi na obiektach blob dyski maszyny wirtualnej do usługi managed disks |
> | Akcja | Microsoft.Compute/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Akcja | Microsoft.Compute/virtualMachines/delete | Usuwa maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/extensions/delete | Usuwa rozszerzenie maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/extensions/read | Pobierz właściwości rozszerzenia maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/extensions/write | Tworzy nowe rozszerzenie maszyny wirtualnej lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/virtualMachines/generalize/action | Ustawia stan maszyny wirtualnej uogólniono i przygotowuje maszynę wirtualną do przechwycenia |
> | Akcja | Microsoft.Compute/virtualMachines/instanceView/read | Pobiera szczegółowy stan środowiska uruchomieniowego maszyny wirtualnej i jej zasobami |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykli użytkownicy |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Zaloguj się do maszyny wirtualnej za pomocą administratora Windows lub Linux uprawnień użytkownika root użytkownika |
> | Akcja | Microsoft.Compute/virtualMachines/performMaintenance/action | Wykonuje operację konserwacji na maszynie Wirtualnej. |
> | Akcja | Microsoft.Compute/virtualMachines/powerOff/action | Wyłącza maszynę wirtualną. Należy zauważyć, że maszyna wirtualna będzie w dalszym ciągu objęte rozliczeniami. |
> | Akcja | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/redeploy/action | Ponownie wdraża maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/reimage/action | Odtwarza z obrazu maszynę wirtualną używającą dysku różnicowego. |
> | Akcja | Microsoft.Compute/virtualMachines/restart/action | Powoduje ponowne uruchomienie maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/runCommand/action | Wykonuje wstępnie zdefiniowany skrypt na maszynie wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/start/action | Umożliwia uruchomienie maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/vmSizes/read | Wyświetla dostępne rozmiary, które można zaktualizować maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/write | Tworzy nową maszynę wirtualną lub aktualizuje istniejącą maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Wyłącza i zwalnia zasoby obliczeniowe dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/delete | Usuwa zestaw skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/delete/action | Usuwa wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Usuwa rozszerzenie zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Pobiera właściwości rozszerzenia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Nowej maszyny wirtualnej rozszerzenie zestawu skalowania tworzy lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Ręcznie Skontroluj domeny aktualizacji platformy usługi Service Fabric, zestawu skalowania maszyn wirtualnych, aby ukończyć oczekującą aktualizację, która się zablokowała |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Pobiera widok wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Ręcznie aktualizuje wystąpienia do najnowszego modelu zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Pobierz właściwości wszystkich interfejsów sieciowych zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Uruchamia uaktualnienia stopniowego, aby przenieść wszystkie wystąpienia zestawu skalowania maszyn wirtualnych do najnowszej dostępnej wersji systemu operacyjnego obrazu platformy. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Pobiera historię uaktualniania systemu operacyjnego dla zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Przeprowadza planowaną konserwację na wystąpieniach zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Wyłącza wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Pobierz właściwości wszystkich publicznych adresów IP zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/read | Pobierz właściwości zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Wdróż ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Odtwarza z obrazu wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dysków z danymi) dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/restart/action | Uruchamia ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Anuluje uaktualnienie stopniowe zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Pobierz najnowszy stan stopniowego uaktualniania dla zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/scale/action | Sprawdź, jeśli istniejącego zestawu skalowania maszyn wirtualnych można przeskalować w / skalowanie w poziomie do określonej liczby wystąpień |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/skus/read | Wyświetla prawidłowe jednostki SKU dla istniejącego zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/start/action | Uruchamia wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Wyłącza i zwalnia zasoby obliczeniowe dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Usuń określoną maszynę wirtualną w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Pobiera widok wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Pobierz właściwości publicznego adresu IP utworzonego za pomocą zestawu skalowania maszyn wirtualnych. Zestaw skalowania maszyn wirtualnych można utworzyć najwyżej jeden publiczny adres IP na konfigurację protokołu (prywatny adres IP) |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Pobierz właściwości jeden lub wszystkie konfiguracje protokołu IP interfejsu sieciowego utworzonego za pomocą zestawu skalowania maszyn wirtualnych. Konfiguracje protokołu IP reprezentują prywatne adresy IP |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Pobierz właściwości jednego lub wszystkich interfejsów sieciowych maszyny wirtualnej utworzone za pomocą zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Przeprowadza planowaną konserwację na wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Wystąpienie uprawnień maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Pobiera właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Wdraża ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Odtwarza z obrazu wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dysków z danymi) dla wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Ponowne uruchomienie wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Wykonuje wstępnie zdefiniowany skrypt na wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Uruchamia wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Aktualizuje właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Listę dostępnych rozmiarów do tworzenia lub aktualizowania maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/write | Tworzy zestaw skalowania maszyn wirtualnych lub aktualizuje istniejący zestaw |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Consumption/balances/read | Lista użycia podsumowania dla okresu rozliczeniowego dla grupy zarządzania. |
> | Akcja | Microsoft.Consumption/budgets/delete | Usuń budżety, subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/budgets/read | Wyświetl listę budżetów subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/budgets/write | Tworzy i aktualizowania budżetów subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/charges/read | Lista opłat |
> | Akcja | Microsoft.Consumption/credits/read | Lista środki na korzystanie z |
> | Akcja | Microsoft.Consumption/events/read | Wyświetl listę zdarzeń |
> | Akcja | Microsoft.Consumption/forecasts/read | Lista prognoz |
> | Akcja | Microsoft.Consumption/lots/read | Wiele list |
> | Akcja | Microsoft.Consumption/marketplaces/read | Lista szczegółów użycia zasobów witryny marketplace dla zakresu dla subskrypcji EA i webdirect, dla których. |
> | Akcja | Microsoft.Consumption/operationresults/read | Lista operationresults |
> | Akcja | Microsoft.Consumption/operations/read | Wyświetl listę wszystkich obsługiwanych operacji przez dostawcę zasobów Microsoft.Consumption. |
> | Akcja | Microsoft.Consumption/operationstatus/read | Lista operationstatus |
> | Akcja | Microsoft.Consumption/pricesheets/read | Lista danych Pricesheets dla subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/register/action | Zarejestruj się, aby użycie jednostki Uzależnionej |
> | Akcja | Microsoft.Consumption/reservationDetails/read | Lista szczegółów użycia wystąpienia zarezerwowane przez rezerwacji zamówienia lub zarządzania grupy. Jest szczegółowych danych na wystąpienie na poziomie dnia. |
> | Akcja | Microsoft.Consumption/reservationRecommendations/read | Lista jednego lub udostępnione zalecenia dotyczące wystąpień zarezerwowanych w ramach subskrypcji. |
> | Akcja | Microsoft.Consumption/reservationSummaries/read | Lista użycia podsumowanie wystąpienia zarezerwowane przez rezerwacji zamówienia lub zarządzania grupy. Podsumowanie danych może być na poziomie miesięcznej lub dziennego. |
> | Akcja | Microsoft.Consumption/reservationTransactions/read | Lista historii transakcji dla wystąpienia zarezerwowane przez grup zarządzania. |
> | Akcja | Microsoft.Consumption/tags/read | Tworzenie listy tagów dla subskrypcji i umowy EA. |
> | Akcja | Microsoft.Consumption/tenants/read | Wykaz dzierżaw |
> | Akcja | Microsoft.Consumption/tenants/register/action | Rejestrowanie akcji dla zakresu Microsoft.Consumption przez dzierżawcę. |
> | Akcja | Microsoft.Consumption/terms/read | Lista warunków dla subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/usageDetails/read | Wyświetl listę szczegóły użycia dla zakresu dla subskrypcji EA i webdirect, dla których. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContainerInstance/containerGroups/containers/exec/action | EXEC w określonym kontenerze. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Pobierz dzienniki dla określonego kontenera. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/delete | Usuń określoną grupę kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/read | Pobierz wszystkie grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/restart/action | Ponownie uruchamia określoną grupę kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/start/action | Uruchamia określoną grupę kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/stop/action | Zatrzymuje określoną grupę kontenerów. Obliczenia, będzie można cofnąć alokacji zasobów i rozliczanie zostanie zatrzymane. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/write | Utwórz lub zaktualizuj określoną grupę kontenerów. |
> | Akcja | Microsoft.ContainerInstance/locations/cachedImages/read | Pobiera pamięci podręcznej obrazów dla subskrypcji w regionie. |
> | Akcja | Microsoft.ContainerInstance/locations/capabilities/read | Dostęp do potrzebnych możliwości dla regionu. |
> | Akcja | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft.ContainerInstance, że sieć wirtualna lub podsieć jest usuwana. |
> | Akcja | Microsoft.ContainerInstance/locations/usages/read | Pobierz użycie dla określonego regionu. |
> | Akcja | Microsoft.ContainerInstance/register/action | Rejestruje subskrypcję dostawcy zasobów wystąpienia kontenera i włącza funkcję tworzenia grup kontenerów. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContainerRegistry/checkNameAvailability/read | Sprawdza, czy nazwa rejestru kontenerów jest dostępny do użytku. |
> | Akcja | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft.ContainerRegistry, że trwa usuwanie sieci wirtualnej lub podsieci |
> | Akcja | Microsoft.ContainerRegistry/locations/operationResults/read | Pobiera wynik operacji asynchronicznej |
> | Akcja | Microsoft.ContainerRegistry/operations/read | Wyświetla listę wszystkich dostępnych operacji API REST usługi Azure Container Registry |
> | Akcja | Microsoft.ContainerRegistry/register/action | Rejestruje subskrypcję dostawcy zasobów rejestru kontenera i włącza funkcję tworzenia rejestrów kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/artifacts/delete | Usuwanie artefaktów w rejestrze kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/builds/cancel/action | Anuluje istniejącą kompilację. |
> | Akcja | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Pobiera link do pobierania dzienników kompilacji. |
> | Akcja | Microsoft.ContainerRegistry/registries/builds/read | Pobiera właściwości określonej kompilacji lub wyświetla wszystkie kompilacje dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/builds/write | Aktualizuje kompilacji dla rejestru kontenerów przy użyciu określonych parametrów. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/delete | Usuwa zadanie kompilacji z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Zawiera listę właściwości kontroli źródła dla zadania kompilacji. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/read | Pobiera właściwości zadania określonej kompilacji lub wyświetla listę wszystkich zadań kompilacji dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Usuwa kroku budowania z zadania kompilacji. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Wyświetla listę argumentów kompilacji dla kroku kompilacji, łącznie z argumentami wpisu tajnego. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Pobiera właściwości kroku kompilacji określonego lub wyświetla ich listę wszystkich kroków kompilacji dla zadania określonej kompilacji. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Tworzy lub aktualizuje kroku kompilacji dla zadania kompilacji z określonymi parametrami. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/write | Tworzy lub aktualizuje zadanie kompilacji dla rejestru kontenerów przy użyciu określonych parametrów. |
> | Akcja | Microsoft.ContainerRegistry/registries/delete | Usuwa z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Usuwa filtr siatki zdarzeń z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Pobiera właściwości filtru siatki określonego zdarzenia lub wyświetla listę wszystkich filtrów siatki zdarzeń dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Tworzy lub aktualizuje filtr siatki zdarzeń dla rejestru kontenerów przy użyciu określonych parametrów. |
> | Akcja | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Pobiera lokalizację przekazywania dla użytkownika można było przekazać źródła. |
> | Akcja | Microsoft.ContainerRegistry/registries/importImage/action | Importuj obraz do rejestru kontenerów przy użyciu określonych parametrów. |
> | Akcja | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Uzyskiwanie źródła przekazywania lokalizacji adresu url dla rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/listCredentials/action | Wyświetla listę poświadczenia logowania dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/listPolicies/read | Wyświetla listę zasad dla określonego rejestru kontenerów |
> | Akcja | Microsoft.ContainerRegistry/registries/listUsages/read | Wyświetla użycie limitu przydziału dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/metadata/read | Pobiera metadane dla określonego repozytorium na potrzeby rejestru kontenerów |
> | Akcja | Microsoft.ContainerRegistry/registries/metadata/write | Aktualizuje metadane repozytorium dla rejestru kontenerów |
> | Akcja | Microsoft.ContainerRegistry/registries/operationStatuses/read | Pobiera stan operacji asynchronicznej dla rejestru |
> | Akcja | Microsoft.ContainerRegistry/registries/pull/read | Ściągnij lub pobieranie obrazów z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/push/write | Wypychanie lub zapisanie obrazów do rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/quarantineRead/read | Ściąganie lub uzyskać poddane kwarantannie obrazów z rejestru kontenerów |
> | Akcja | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Zapisu/modyfikowania stan kwarantanny obrazów poddane kwarantannie |
> | Akcja | Microsoft.ContainerRegistry/registries/queueBuild/action | Tworzy nową kompilację na podstawie parametrów żądania i dodać go do kolejki kompilacji. |
> | Akcja | Microsoft.ContainerRegistry/registries/read | Pobiera właściwości określonego rejestru kontenerów lub wyświetla ich listę rejestrów kontenerów w ramach określonej grupy zasobów lub subskrypcji. |
> | Akcja | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Ponownie generuje jednego poświadczenia logowania dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/delete | Usuwa replikacji z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Pobiera stan operacji asynchronicznej replikacji |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/read | Pobiera właściwości określonej replikacji lub wyświetla ich listę wszystkich replikacji dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/write | Tworzy lub aktualizuje replikacji dla rejestru kontenerów przy użyciu określonych parametrów. |
> | Akcja | Microsoft.ContainerRegistry/registries/runs/cancel/action | Anuluj przebieg istniejących. |
> | Akcja | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Pobiera dziennika adresu URL sygnatury dostępu Współdzielonego dla przebiegu. |
> | Akcja | Microsoft.ContainerRegistry/registries/runs/read | Pobiera właściwości wykonywania względem rejestru kontenerów lub listy przebiegów. |
> | Akcja | Microsoft.ContainerRegistry/registries/runs/write | Aktualizuje przebiegu. |
> | Akcja | Microsoft.ContainerRegistry/registries/scheduleRun/action | Planowanie wykonywania względem rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/sign/write | / Wypychania zawartości zaufania metadanych dla rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/tasks/delete | Usuwa zadanie dla rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Wyświetl listę wszystkich szczegółów zadania dla rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/tasks/read | Pobiera zadanie dla rejestru kontenerów lub listy wszystkich zadań. |
> | Akcja | Microsoft.ContainerRegistry/registries/tasks/write | Tworzy lub aktualizuje zadanie dla rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/updatePolicies/write | Aktualizuje zasady dla określonego rejestru kontenerów |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/delete | Usuwa element webhook z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Pobiera konfigurację identyfikator URI usługi i nagłówki niestandardowe dla elementu webhook. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Wyświetla listę najnowszych zdarzeń dla określonego elementu webhook. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Pobiera stan operacji asynchronicznej dla elementu webhook |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Wyzwala zdarzenie typu ping do wysłania do elementu webhook. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/read | Pobiera właściwości określonego elementu webhook lub wyświetla ich listę wszystkich elementów webhook dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/write | Tworzy lub aktualizuje element webhook usługi container registry przy użyciu określonych parametrów. |
> | Akcja | Microsoft.ContainerRegistry/registries/write | Tworzy lub aktualizuje rejestr kontenerów z określonymi parametrami. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContainerService/containerServices/delete | Usuwa z nich usługę kontenera |
> | Akcja | Microsoft.ContainerService/containerServices/read | Usługi kontenerów |
> | Akcja | Microsoft.ContainerService/containerServices/write | Tworzy nową usługę kontenera lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.ContainerService/locations/operationresults/read | Pobiera stan wynik operacji asynchronicznej |
> | Akcja | Microsoft.ContainerService/locations/operations/read | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft.ContainerService/locations/orchestrators/read | Wyświetla listę obsługiwanych koordynatorów |
> | Akcja | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Pobieranie profilu dostępu do zarządzanego klastra według nazwy roli przy użyciu poświadczeń listy |
> | Akcja | Microsoft.ContainerService/managedClusters/accessProfiles/read | Pobieranie profilu dostępu do zarządzanego klastra według nazwy roli |
> | Akcja | Microsoft.ContainerService/managedClusters/agentPools/delete | Usuwa pulę agentów |
> | Akcja | Microsoft.ContainerService/managedClusters/agentPools/read | Pobiera pulę agentów |
> | Akcja | Microsoft.ContainerService/managedClusters/agentPools/write | Tworzy nową pulę agentów lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.ContainerService/managedClusters/delete | Usuwa zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Lista poświadczeń clusterAdmin zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Lista poświadczeń clusterUser zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/read | Pobierz zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Resetowanie profilu usługi AAD zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Resetowanie profilu jednostki usługi z zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Pobiera profil uaktualnienia klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/write | Tworzy nowy klaster zarządzanego lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.ContainerService/openShiftClusters/delete | Usuwanie klastra usługi Open Shift |
> | Akcja | Microsoft.ContainerService/openShiftClusters/read | Pobierz klaster Open Shift |
> | Akcja | Microsoft.ContainerService/openShiftClusters/write | Tworzy nowy klaster Open Shift lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.ContainerService/openShiftManagedClusters/delete | Usunąć klaster zarządzanego Open Shift |
> | Akcja | Microsoft.ContainerService/openShiftManagedClusters/read | Pobierz Open Shift zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/openShiftManagedClusters/write | Tworzy nowy Open Shift zarządzany klaster lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.ContainerService/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.ContainerService |
> | Akcja | Microsoft.ContainerService/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.ContainerService |
> | Akcja | Microsoft.ContainerService/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów Microsoft.ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContentModerator/applications/delete | Operacja usuwania |
> | Akcja | Microsoft.ContentModerator/applications/listSecrets/action | Utwórz listę kluczy tajnych |
> | Akcja | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Odczyt jednokrotnego tokenów |
> | Akcja | Microsoft.ContentModerator/applications/read | Operacja odczytu |
> | Akcja | Microsoft.ContentModerator/applications/write | Operacja zapisu |
> | Akcja | Microsoft.ContentModerator/applications/write | Operacja zapisu |
> | Akcja | Microsoft.ContentModerator/listCommunicationPreference/action | Wyświetl Preferencje dotyczące komunikacji |
> | Akcja | Microsoft.ContentModerator/operations/read | operacje odczytu |
> | Akcja | Microsoft.ContentModerator/updateCommunicationPreference/action | Aktualizuj preferencje dotyczące komunikacji |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.CostManagement/cloudConnectors/delete | Usuń określoną wartość cloudConnector. |
> | Akcja | Microsoft.CostManagement/cloudConnectors/read | Wyświetl listę cloudConnectors dla tego uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.CostManagement/cloudConnectors/write | Utwórz lub zaktualizuj określoną wartość cloudConnector. |
> | Akcja | Microsoft.CostManagement/dimensions/read | Wyświetl listę wszystkich obsługiwanych wymiarów przez zakres. |
> | Akcja | Microsoft.CostManagement/exports/action | Uruchom określony eksportu. |
> | Akcja | Microsoft.CostManagement/exports/delete | Usuń eksport określony. |
> | Akcja | Microsoft.CostManagement/exports/read | Wyświetl listę eksportu w zakresie. |
> | Akcja | Microsoft.CostManagement/exports/run/action | Eksportuje przebiegu. |
> | Akcja | Microsoft.CostManagement/exports/write | Utwórz lub zaktualizuj określoną eksportu. |
> | Akcja | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Wyświetl listę externalSubscriptions w ramach externalBillingAccount dla tego uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.CostManagement/externalBillingAccounts/read | Wyświetl listę externalBillingAccounts dla tego uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.CostManagement/externalSubscriptions/read | Wyświetl listę externalSubscriptions dla tego uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.CostManagement/externalSubscriptions/write | Aktualizowanie grupy zarządzania skojarzony z externalSubscription |
> | Akcja | Microsoft.CostManagement/query/action | Wykonywanie zapytań o dane użycia przez zakres. |
> | Akcja | Microsoft.CostManagement/query/read | Wykonywanie zapytań o dane użycia przez zakres. |
> | Akcja | Microsoft.CostManagement/register/action | Rejestrowanie akcji dla zakresu Microsoft.CostManagement przez subskrypcję. |
> | Akcja | Microsoft.CostManagement/reports/action | Harmonogram raportów dotyczących danych użycia w ramach zakresu. |
> | Akcja | Microsoft.CostManagement/reports/read | Harmonogram raportów dotyczących danych użycia w ramach zakresu. |
> | Akcja | Microsoft.CostManagement/tenants/register/action | Rejestrowanie akcji dla zakresu Microsoft.CostManagement przez dzierżawcę. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.CustomerInsights/hubs/adobemetadata/action | Utwórz lub zaktualizuj wszelkie metadane Adobe Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/adobemetadata/read | Odczyt wszystkich metadanych, Adobe Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Usuwanie zasad sygnatury dostępu współdzielonego żadnych klientów platformy Azure szczegółowe informacje |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Odczytać dowolny Azure Customer Insights współużytkowane zasady sygnatury dostępu |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Wygeneruj ponownie klucz podstawowy Azure Customer Insights zasady dostępu współużytkowanego podpisu |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Wygeneruj ponownie klucz pomocniczy Azure Customer Insights zasady dostępu współużytkowanego podpisu |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Utwórz lub zaktualizuj żadnych zasad sygnatury dostępu współdzielonego Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktywuj każdy łącznik Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktywuj każdy łącznik Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/delete | Usunięcie łącznika Insights dowolnego klienta na platformie Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Pobierz stan czasu wykonywania żadnych łącznika usługi Azure Customer Insights |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Aktywuj żadnego mapowania łącznika Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Usuń mapowanie łącznika Insights dowolnego klienta na platformie Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Przeczytaj dowolny wynik operacji mapowanie łącznika szczegółowych informacji klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Przeczytaj żadnego mapowania łącznika Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Utwórz lub zaktualizuj wszelkie mapowanie łącznika Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/operations/read | Przeczytaj dowolny wynik operacji łącznika usługi Azure Customer Insights |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/read | Przeczytaj każdy łącznik Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Utwórz lub zaktualizuj wszelkie klientów platformy Azure Insights łącznika uwierzytelniania bramą |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/update/action | Zaktualizuj łącznik Insights dowolnego klienta na platformie Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/write | Utwórz lub zaktualizuj wszelkie łącznik Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/crmmetadata/action | Utwórz lub zaktualizuj wszelkie metadane Crm Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/crmmetadata/read | Odczyt wszystkich klientów platformy Azure Insights Crm metadanych |
> | Akcja | Microsoft.CustomerInsights/hubs/delete | Usunąć dowolne Centrum Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/gdpr/delete | Usuń wszelkie rodo Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/gdpr/read | Przeczytaj rodo Insights dowolnego klienta na platformie Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/gdpr/write | Utwórz lub zaktualizuj wszelkie rodo Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Uzyskaj środki na korzystanie z systemu klienta usługi Azure Insights Centrum rozliczeń |
> | Akcja | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Pobieranie historii rozliczeń Centrum Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/images/delete | Usuń dowolny obraz Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/images/read | Przeczytaj dowolnego obrazu Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/images/write | Utwórz lub zaktualizuj dowolny obraz Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/delete | Usuń wszelkie Insights interakcyjnymi platformy azure |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/operations/read | Przeczytaj dowolny wynik operacji Azure Customer Insights interakcji |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/read | Przeczytaj szczegółowe informacje interakcji z klientem platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Zaproponuj relacji łączy dla żadnych interakcji Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/write | Utwórz lub zaktualizuj interakcji z klientem platformy Azure Insights |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/delete | Usuń wszelkie klienta usługi Azure Insights kluczowy wskaźnik wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/operations/read | Przeczytaj dowolny wynik operacji usługi Azure Insights klienta kluczowych wskaźników wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/read | Przeczytaj dowolnego klienta usługi Azure Insights kluczowy wskaźnik wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Ponowne przetwarzanie dowolnego klienta na platformie Azure Insights kluczowych wskaźników wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/write | Utwórz lub zaktualizuj wszelkie klienta usługi Azure Insights kluczowy wskaźnik wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/links/delete | Usuń wszystkie linki do szczegółowych informacji klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/links/operations/read | Przeczytaj dowolny wynik operacji łącza Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/links/read | Odczytywać wszystkie linki do szczegółowych informacji klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/links/write | Utwórz lub zaktualizuj wszelkie linki klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/msemetadata/action | Utwórz lub zaktualizuj wszelkie metadane Mse Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/msemetadata/read | Odczyt wszystkich klientów platformy Azure Insights Mse metadanych |
> | Akcja | Microsoft.CustomerInsights/hubs/operationresults/read | Pobierz wyniki operacji centrum Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictions/delete | Usuń wszelkie prognozy Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictions/operations/read | Przeczytaj dowolny wynik operacji Azure Customer Insights prognozy |
> | Akcja | Microsoft.CustomerInsights/hubs/predictions/read | Przeczytaj prognozy Insights żadnych klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictions/write | Utwórz lub zaktualizuj wszelkie prognozy klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Usuń wszystkie zasady dopasowania Predykcyjnego Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Przeczytaj dowolny wynik operacji Azure klienta szczegółowych informacji predykcyjne dopasowania zasad |
> | Akcja | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Odczytywać wszystkie zasady dopasowania Predykcyjnego Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Utwórz lub zaktualizuj wszelkie zasady dopasowania Predykcyjnego Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/profiles/delete | Usuwanie profilu Insights żadnych klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/profiles/operations/read | Przeczytaj dowolny wynik operacji profil Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/profiles/read | Odczytuj profil Insights dowolnego klienta na platformie Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/profiles/write | Zapis żadnego profilu Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla zasobu |
> | Akcja | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla zasobu |
> | Akcja | Microsoft.CustomerInsights/hubs/read | Przeczytaj dowolne Centrum Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Usuń wszystkie linki relacji Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Przeczytaj dowolny wynik operacji łączy relacji szczegółowych informacji klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Odczytywać wszystkie linki relacji Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Utwórz lub zaktualizuj wszelkie linki relacji Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationships/delete | Usuń wszystkie relacje Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationships/operations/read | Przeczytaj dowolny wynik operacji szczegółowych informacji z klientami platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationships/read | Przeczytaj relacje Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationships/write | Utwórz lub zaktualizuj wszystkie relacje Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Usunąć wszystkie przypisania Rbac Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Przeczytaj dowolny wynik operacji przypisania Rbac Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/roleAssignments/read | Odczytywać wszystkie przypisania Rbac Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/roleAssignments/write | Utwórz lub zaktualizuj wszelkie przypisania Rbac Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/roles/read | Odczytywać wszystkie role Rbac Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Utwórz lub zaktualizuj wszelkie metadane SalesForce Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Odczytaj wszystkie metadane SalesForce szczegółowych informacji klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/delete | Usuń wszystkie segmenty Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Segmenty żadnych klientów platformy Azure szczegółowe informacje o dynamiczne zarządzanie |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/read | Przeczytaj szczegółowe informacje segmentów klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/static/action | Segmenty systemu zarządzania wszystkie statyczne Insight klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/write | Utwórz lub zaktualizuj segmentów klientów platformy Azure Insights |
> | Akcja | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Usuń wszelkie SqlConnectionStrings Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Przeczytaj SqlConnectionStrings Insights dowolnego klienta na platformie Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Utwórz lub zaktualizuj wszelkie SqlConnectionStrings Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Generowanie zasugerować schematu typu z przykładowych danych |
> | Akcja | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Zarządzaj ustawienia Centrum Azure Customer Insights |
> | Akcja | Microsoft.CustomerInsights/hubs/views/delete | Usuń dowolny widok App Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/views/read | Przeczytaj dowolny widok App Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/views/write | Utwórz lub zaktualizuj dowolny widok App Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/widgettypes/read | Wszystkie typy aplikacji widżetu szczegółowych informacji klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/write | Utwórz lub zaktualizuj dowolne Centrum Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/operations/read | Przeczytaj Metadatas interfejsu Api usługi Insights klientów platformy Azure |
> | Akcja | Microsoft.CustomerInsights/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Customer Insights i włącza funkcję tworzenia zasobów usługi Customer Insights |
> | Akcja | Microsoft.CustomerInsights/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Customer Insights |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Umożliwia zarezerwowanie odbioru zwrotnej. |
> | Akcja | Microsoft.DataBox/jobs/cancel/action | Anuluje zamówienie będące w toku. |
> | Akcja | Microsoft.DataBox/jobs/delete | Usuwanie zamówienia |
> | Akcja | Microsoft.DataBox/jobs/listCredentials/action | Wyświetla listę poświadczeń nieszyfrowanych, związanych z zamówieniem. |
> | Akcja | Microsoft.DataBox/jobs/read | Wyświetl listę lub Pobierz zamówienia |
> | Akcja | Microsoft.DataBox/jobs/write | Utwórz lub zaktualizuj zamówienia |
> | Akcja | Microsoft.DataBox/locations/availableSkus/action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | Akcja | Microsoft.DataBox/locations/operationResults/read | Wyświetl listę lub Pobierz wyniki operacji |
> | Akcja | Microsoft.DataBox/locations/validateAddress/action | Weryfikuje adres wysyłkowy i udostępnia alternatywne adresy, jeśli istnieje. |
> | Akcja | Microsoft.DataBox/register/action | Zarejestruj dostawcę Microsoft.Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Wyświetla lub pobiera alerty |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Wyświetla lub pobiera alerty |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Usuwa harmonogramy przepustowości |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Wyświetla lub pobiera harmonogramy przepustowości |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Wyświetla lub pobiera harmonogramy przepustowości |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Tworzy lub aktualizuje harmonogramy przepustowości |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Usuwa urządzenia brzegowe pole danych |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Pobieranie aktualizacji na urządzeniu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Pobiera zasób rozszerzone informacje |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalowanie aktualizacji na urządzeniu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Wyświetla lub pobiera zadania |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Wyświetla lub pobiera ustawienia sieciowe urządzenia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Wyświetla lub pobiera stan operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Usuwa zamówienia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Wyświetla lub pobiera zamówienia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Wyświetla lub pobiera zamówienia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Tworzy lub aktualizuje zamówienia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub pobiera urządzenia brzegowe pole danych |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub pobiera urządzenia brzegowe pole danych |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub pobiera urządzenia brzegowe pole danych |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Usuwa ról |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Wyświetla lub pobiera role |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Wyświetla lub pobiera role |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Tworzy lub aktualizuje ról |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Skanowanie w poszukiwaniu aktualizacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Aktualizowanie ustawień zabezpieczeń |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Usuwa akcji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Wyświetla lub pobiera akcje |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Wyświetla lub pobiera akcje |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Odświeżanie metadanych udostępniania, za pomocą danych z chmury |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Tworzy lub aktualizuje akcji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Usuwa poświadczenia konta magazynu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Wyświetla lub pobiera poświadczenia konta magazynu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Wyświetla lub pobiera poświadczenia konta magazynu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Tworzy lub aktualizuje poświadczenia konta magazynu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Usuwa wyzwalacze |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Wyświetla lub pobiera wyzwalacze |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Wyświetla lub pobiera wyzwalacze |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Tworzy lub aktualizuje wyzwalacze |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Wyświetla lub pobiera Podsumowanie aktualizacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Przekaż certyfikat na potrzeby rejestracji urządzeń |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Usuwa użytkownicy udziału |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Wyświetla lub pobiera użytkownicy udziału |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Wyświetla lub pobiera użytkownicy udziału |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Tworzy lub aktualizuje użytkownicy udziału |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Tworzy lub aktualizuje urządzenia brzegowe pole danych |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Tworzy lub aktualizuje urządzenia brzegowe pole danych |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Databricks/register/action | Zarejestruj się w usłudze Databricks. |
> | Akcja | Microsoft.Databricks/workspaces/delete | Usuwa obszar roboczy usługi Databricks. |
> | Akcja | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Zestawy dostępne ustawienia diagnostyki dla obszaru roboczego usługi Databricks |
> | Akcja | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | Akcja | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Pobiera definicje dzienników dostępnych dla obszaru roboczego usługi Databricks |
> | Akcja | Microsoft.Databricks/workspaces/read | Pobiera listę obszarów roboczych usługi Databricks. |
> | Akcja | Microsoft.Databricks/workspaces/write | Tworzy obszar roboczy usługi Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataCatalog/catalogs/delete | Usuń zasób katalogi dla dostawcy zasobów usługi Data Catalog. |
> | Akcja | Microsoft.DataCatalog/catalogs/read | Odczytaj zasób katalogi dla dostawcy zasobów usługi Data Catalog. |
> | Akcja | Microsoft.DataCatalog/catalogs/write | Napisz wykazów zasobów dla dostawcy zasobów usługi Data Catalog. |
> | Akcja | Microsoft.DataCatalog/checkNameAvailability/read | Sprawdź dostępność nazwy wykazu dla dostawcy zasobów usługi Data Catalog. |
> | Akcja | Microsoft.DataCatalog/datacatalogs/delete | Usuń zasób DataCatalog dla dostawcy zasobów usługi Data Catalog. |
> | Akcja | Microsoft.DataCatalog/datacatalogs/read | Odczytaj zasób DataCatalog dla dostawcy zasobów usługi Data Catalog. |
> | Akcja | Microsoft.DataCatalog/datacatalogs/write | Napisz DataCatalog zasobów dla dostawcy zasobów usługi Data Catalog. |
> | Akcja | Microsoft.DataCatalog/operations/read | Odczytuje wszystkie operacje dostępne w dostawcy zasobów usługi Data Catalog. |
> | Akcja | Microsoft.DataCatalog/register/action | Rejestruj subskrypcję dostawcy zasobów usługi Data Catalog |
> | Akcja | Microsoft.DataCatalog/unregister/action | Wyrejestruj subskrypcję dla dostawcy zasobów usługi Data Catalog |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Sprawdza, jeśli nazwa fabryki danych jest dostępne do użycia. |
> | Akcja | Microsoft.DataFactory/datafactories/activitywindows/read | Odczytuje Windows działanie w usłudze Data Factory przy użyciu określonych parametrów. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Odczytuje Windows działania dla działania potoku przy użyciu określonych parametrów. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Odczytuje Windows działania dla potoku przy użyciu określonych parametrów. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/delete | Usuwa dowolnym potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Wstrzymuje dowolnym potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/read | Odczytuje w dowolnym potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Wznawia dowolnym potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/update/action | Aktualizuje dowolnym potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/write | Tworzy lub aktualizuje dowolnym potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Odczytuje Windows działanie dla zestawu danych z określonymi parametrami. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/delete | Usuwa dowolnym zestawie danych. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/read | Odczytuje dowolnym zestawie danych. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Odczytuje uruchomienia wycinka danych dla danego zestawu danych o podanej godzinie. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/slices/read | Pobiera wycinki danych w danym okresie. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/slices/write | Aktualizuje stan wycinka danych. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/write | Tworzy lub aktualizuje dowolnym zestawie danych. |
> | Akcja | Microsoft.DataFactory/datafactories/delete | Usuwa z fabryką danych. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Odczytuje informacje o połączeniu dla żadnej bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/delete | Usuwa wszystkie bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Wyświetla listę kluczy uwierzytelniania dla żadnej bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/read | Odczytuje żadnej bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Ponownie generuje klucze uwierzytelniania dla żadnej bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/write | Tworzy lub aktualizuje żadnej bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/linkedServices/delete | Usuwa wszystkie połączonej usługi. |
> | Akcja | Microsoft.DataFactory/datafactories/linkedServices/read | Odczytuje wszystkie połączonej usługi. |
> | Akcja | Microsoft.DataFactory/datafactories/linkedServices/write | Tworzy lub aktualizuje wszystkie połączonej usługi. |
> | Akcja | Microsoft.DataFactory/datafactories/read | Odczytuje z fabryką danych. |
> | Akcja | Microsoft.DataFactory/datafactories/runs/loginfo/read | Odczytuje identyfikator URI sygnatury dostępu Współdzielonego kontenera obiektów blob zawierający dzienniki. |
> | Akcja | Microsoft.DataFactory/datafactories/tables/delete | Usuwa dowolnym zestawie danych. |
> | Akcja | Microsoft.DataFactory/datafactories/tables/read | Odczytuje dowolnym zestawie danych. |
> | Akcja | Microsoft.DataFactory/datafactories/tables/write | Tworzy lub aktualizuje dowolnym zestawie danych. |
> | Akcja | Microsoft.DataFactory/datafactories/write | Tworzy lub aktualizuje fabryki danych. |
> | Akcja | Microsoft.DataFactory/factories/cancelpipelinerun/action | Anuluje uruchomienie określonego przez identyfikator uruchomienia potoku |
> | Akcja | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Tworzy sesję debugowania przepływu danych. |
> | Akcja | Microsoft.DataFactory/factories/dataflows/delete | Usuwa przepływ danych. |
> | Akcja | Microsoft.DataFactory/factories/dataflows/read | Odczytuje przepływ danych. |
> | Akcja | Microsoft.DataFactory/factories/dataflows/write | Utwórz lub zaktualizuj przepływ danych |
> | Akcja | Microsoft.DataFactory/factories/datasets/delete | Usuwa dowolnym zestawie danych. |
> | Akcja | Microsoft.DataFactory/factories/datasets/read | Odczytuje dowolnym zestawie danych. |
> | Akcja | Microsoft.DataFactory/factories/datasets/write | Tworzy lub aktualizuje dowolnym zestawie danych. |
> | Akcja | Microsoft.DataFactory/factories/delete | Usuwa usługi Data Factory. |
> | Akcja | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Usuwa sesję debugowania przepływu danych. |
> | Akcja | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Pobiera dostęp do usługi ADF płaszczyzny danych. |
> | Akcja | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Odczyty dostęp do usługi ADF płaszczyzny danych. |
> | Akcja | Microsoft.DataFactory/factories/getFeatureValue/action | Pobierz wartość funkcji sterowania narażenia dla określonej lokalizacji. |
> | Akcja | Microsoft.DataFactory/factories/getFeatureValue/read | Odczytuje wartość funkcji sterowania narażenia dla określonej lokalizacji. |
> | Akcja | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Pobiera token dostępu usługi GitHub. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/delete | Usuwa wszystkie środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Odczytuje informacje o połączeniu ze środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Pobierz metadane środowiska SSIS Integration Runtime dla określonego środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Odczytuje stan środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Utwórz odwołanie do środowiska uruchomieniowego integracji połączone na określonym udostępnionego Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Wyświetla listę kluczy uwierzytelniania dla dowolnego środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Pobiera dane monitorowania dla dowolnego środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Usuwa węzła dla określonego środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Zwraca adres IP dla określonego węzła Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Odczytuje węzła dla określonego produktu Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Aktualizuje samodzielnie hostowany węzeł środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/read | Odczytuje wszystkie środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Odświeżanie środowiska SSIS Integration Runtime metadanych dla określonego środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Ponownie generuje klucze uwierzytelniania dla określonego środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Usuwa powiązane odwołania środowisko uruchomieniowe integracji z określonego produktu Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/start/action | Uruchamia wszystkie środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Zatrzymuje wszystkie środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Przeprowadza synchronizację poświadczeń dla określonego środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Uaktualnia określonego produktu Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/write | Tworzy lub aktualizuje wszystkie środowiska Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/linkedServices/delete | Usuwa połączoną usługę. |
> | Akcja | Microsoft.DataFactory/factories/linkedServices/read | Odczyty połączoną usługę. |
> | Akcja | Microsoft.DataFactory/factories/linkedServices/write | Utwórz lub zaktualizuj połączone usługi |
> | Akcja | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Odczytuje uruchomienia działania określony identyfikator uruchomienia potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Anuluje uruchomienie określonego przez identyfikator uruchomienia potoku |
> | Akcja | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Zapytania, których działanie zostanie uruchomione dla określonego potoku Uruchom identyfikatora. |
> | Akcja | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Odczytuje wynik działania zapytanie działa przez określony identyfikator uruchomienia potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelineruns/read | Odczytuje uruchomienia potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/createrun/action | Tworzy przebiegu potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/delete | Usuwa potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Pobiera postęp uruchomienia działania. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Odczytuje uruchomienia potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/read | Odczytuje potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/write | Utwórz lub zaktualizuj potoku |
> | Akcja | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Wysyła zapytanie do uruchomienia potoku debugowania. |
> | Akcja | Microsoft.DataFactory/factories/querypipelineruns/action | Wysyła zapytanie do uruchomienia potoku. |
> | Akcja | Microsoft.DataFactory/factories/querypipelineruns/read | Odczytuje wynik uruchomienia potoku zapytania. |
> | Akcja | Microsoft.DataFactory/factories/querytriggerruns/action | Wysyła zapytanie do uruchomienia wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/querytriggerruns/read | Odczytuje wynik uruchomienia wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/read | Odczytuje fabryki danych. |
> | Akcja | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Uruchamia sesję debugowania przepływu danych. |
> | Akcja | Microsoft.DataFactory/factories/triggerruns/read | Odczytuje uruchomienia wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/delete | Usuwa wszystkie wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/read | Odczytuje wszystkie wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/start/action | Uruchamia wszystkie wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/stop/action | Zatrzymuje wszystkie wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/triggerruns/read | Odczytuje uruchomienia wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/write | Tworzy lub aktualizuje dowolnego wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/write | Utwórz lub zaktualizuj usługi Data Factory |
> | Akcja | Microsoft.DataFactory/locations/configureFactoryRepo/action | Umożliwia skonfigurowanie repozytorium fabryki. |
> | Akcja | Microsoft.DataFactory/locations/getFeatureValue/action | Pobierz wartość funkcji sterowania narażenia dla określonej lokalizacji. |
> | Akcja | Microsoft.DataFactory/locations/getFeatureValue/read | Odczytuje wartość funkcji sterowania narażenia dla określonej lokalizacji. |
> | Akcja | Microsoft.DataFactory/operations/read | Odczytuje wszystkie operacje w dostawcy fabryki danych firmy Microsoft. |
> | Akcja | Microsoft.DataFactory/register/action | Rejestruje subskrypcję dostawcy zasobów fabryki danych. |
> | Akcja | Microsoft.DataFactory/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów fabryki danych. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Usuwanie zasad obliczeń. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Uzyskaj informacje o zasadach obliczeń. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Utwórz lub zaktualizuj zasady obliczeń. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Odłączanie konta DataLakeStore z konta usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Uzyskaj informacje o połączonych kontach DataLakeStore na koncie usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Utwórz lub zaktualizuj połączone konto DataLakeStore konta usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/delete | Usuwanie konta usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Usuwanie reguły zapory. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Uzyskaj informacje dotyczące reguły zapory. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Utwórz lub zaktualizuj regułę zapory. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Pobierz wynik operacji konto usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/read | Pobierz informacje o istniejącym kontem usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Lista tokenów sygnatur dostępu Współdzielonego dla magazynu kontenerów z konta usługi DataLakeAnalytics połączonego konta usługi Storage. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Pobierz kontenery z konta usługi DataLakeAnalytics połączonego konta usługi Storage. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Odłączanie konta magazynu z konta usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Uzyskać informacje na temat połączonego konta usługi Storage na koncie usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Tworzenie lub aktualizowanie konta usługi DataLakeAnalytics połączonego konta usługi Storage. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Przyznaj uprawnienia, aby anulować zadania przesłane przez innych użytkowników. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Przenieś SystemMaxAnalyticsUnits pomiędzy konta tej usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Usuń regułę sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Uzyskaj informacje dotyczące reguły sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/write | Utwórz lub zaktualizuj konto usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/capability/read | Uzyskaj informacje o możliwościach subskrypcji dotyczące przy użyciu usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/operationResults/read | Pobierz wynik operacji konto usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/usages/read | Uzyskiwanie informacji o użyciach limitu przydziału subskrypcji dotyczące przy użyciu usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/operations/read | Pobierz dostępne operacje usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/register/action | Zarejestruj subskrypcję usługi DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataLakeStore/accounts/delete | Usuwanie konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Włącz magazyn kluczy dla konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Usuń filtr EventGrid. |
> | Akcja | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Uzyskaj EventGrid filtru. |
> | Akcja | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Utwórz lub zaktualizuj filtr EventGrid. |
> | Akcja | Microsoft.DataLakeStore/accounts/firewallRules/delete | Usuwanie reguły zapory. |
> | Akcja | Microsoft.DataLakeStore/accounts/firewallRules/read | Uzyskaj informacje dotyczące reguły zapory. |
> | Akcja | Microsoft.DataLakeStore/accounts/firewallRules/write | Utwórz lub zaktualizuj regułę zapory. |
> | Akcja | Microsoft.DataLakeStore/accounts/operationResults/read | Pobierz wynik operacji DataLakeStore konta. |
> | Akcja | Microsoft.DataLakeStore/accounts/read | Uzyskaj informacje o istniejących kontach DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/accounts/Superuser/action | Przyznaj administratora na Data Lake Store w przypadku udzielenia z Microsoft.Authorization/roleAssignments/write. |
> | Akcja | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Usunięcie zaufanego dostawcy tożsamości. |
> | Akcja | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Uzyskać informacje na temat zaufanego dostawcy tożsamości. |
> | Akcja | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Utwórz lub zaktualizuj zaufanego dostawcy tożsamości. |
> | Akcja | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Usuń regułę sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Uzyskaj informacje dotyczące reguły sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeStore/accounts/write | Utwórz lub zaktualizuj konto DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/locations/capability/read | Uzyskaj informacje o możliwościach subskrypcji dotyczące przy użyciu DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/locations/operationResults/read | Pobierz wynik operacji DataLakeStore konta. |
> | Akcja | Microsoft.DataLakeStore/locations/usages/read | Uzyskiwanie informacji o użyciach limitu przydziału subskrypcji dotyczące przy użyciu DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/operations/read | Pobierz dostępne operacje DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/register/action | Zarejestruj subskrypcję DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataMigration/locations/operationResults/read | Pobierz stan długotrwałej operacji związanych z odpowiedzi 202 zaakceptowano |
> | Akcja | Microsoft.DataMigration/locations/operationStatuses/read | Pobierz stan długotrwałej operacji związanych z odpowiedzi 202 zaakceptowano |
> | Akcja | Microsoft.DataMigration/register/action | Rejestruje subskrypcję u dostawcy usługi Azure Database Migration Service |
> | Akcja | Microsoft.DataMigration/services/addWorker/action | Dodaje procesu roboczego usługi DMS pracownikom dostępny usługi |
> | Akcja | Microsoft.DataMigration/services/checkStatus/action | Sprawdź, czy usługa jest wdrożona i uruchomiona |
> | Akcja | Microsoft.DataMigration/services/configureWorker/action | Konfiguruje na pracowników dostępny usługi DMS procesu roboczego |
> | Akcja | Microsoft.DataMigration/services/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft.DataMigration/services/projects/accessArtifacts/action | Generowanie adresu URL, który może służyć do pobierania lub PUT obejmujących artefakty projektu |
> | Akcja | Microsoft.DataMigration/services/projects/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft.DataMigration/services/projects/files/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft.DataMigration/services/projects/files/read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft.DataMigration/services/projects/files/read/action | Uzyskiwanie adresu URL, który może służyć do odczytu zawartości pliku |
> | Akcja | Microsoft.DataMigration/services/projects/files/readWrite/action | Uzyskiwanie adresu URL, który może służyć do odczytywania i zapisywania zawartości pliku |
> | Akcja | Microsoft.DataMigration/services/projects/files/write | Utwórz lub zaktualizuj zasoby i ich właściwości |
> | Akcja | Microsoft.DataMigration/services/projects/read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft.DataMigration/services/projects/tasks/cancel/action | Anuluj zadanie, jeśli jest ono aktualnie uruchomione |
> | Akcja | Microsoft.DataMigration/services/projects/tasks/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft.DataMigration/services/projects/tasks/read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft.DataMigration/services/projects/tasks/write | Uruchamianie zadania usługi Azure Database Migration Service zadań |
> | Akcja | Microsoft.DataMigration/services/projects/write | Uruchamianie zadania usługi Azure Database Migration Service zadań |
> | Akcja | Microsoft.DataMigration/services/read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft.DataMigration/services/removeWorker/action | Usuwa na pracowników dostępny usługi DMS procesu roboczego |
> | Akcja | Microsoft.DataMigration/services/serviceTasks/cancel/action | Anuluj zadanie, jeśli jest ono aktualnie uruchomione |
> | Akcja | Microsoft.DataMigration/services/serviceTasks/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft.DataMigration/services/serviceTasks/read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft.DataMigration/services/serviceTasks/write | Uruchamianie zadania usługi Azure Database Migration Service zadań |
> | Akcja | Microsoft.DataMigration/services/slots/delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft.DataMigration/services/slots/read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft.DataMigration/services/slots/write | Utwórz lub zaktualizuj zasoby i ich właściwości |
> | Akcja | Microsoft.DataMigration/services/start/action | Uruchom usługę DMS, aby umożliwić jej ponowne przetworzenie migracji |
> | Akcja | Microsoft.DataMigration/services/stop/action | Zatrzymaj usługę DMS, aby zminimalizować jej koszt |
> | Akcja | Microsoft.DataMigration/services/updateAgentConfig/action | Aktualizuje konfigurację agenta usługi DMS przy użyciu podanych wartości. |
> | Akcja | Microsoft.DataMigration/services/write | Utwórz lub zaktualizuj zasoby i ich właściwości |
> | Akcja | Microsoft.DataMigration/skus/read | Pobierz listę jednostek SKU obsługiwanych przez zasoby usługi DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DBforMariaDB/checkNameAvailability/action | Sprawdź, czy nazwa danego serwera jest dostępne do alokowania na całym świecie, w ramach danej subskrypcji. |
> | Akcja | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Zwraca wyniki operacji serwera MariaDB |
> | Akcja | Microsoft.DBforMariaDB/locations/operationResults/read | ResourceGroup zwracane na podstawie wyników operacji serwera MariaDB |
> | Akcja | Microsoft.DBforMariaDB/locations/operationResults/read | Zwraca wyniki operacji serwera MariaDB |
> | Akcja | Microsoft.DBforMariaDB/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforMariaDB/operations/read | Zwraca listę operacji MariaDB. |
> | Akcja | Microsoft.DBforMariaDB/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft.DBforMariaDB/register/action | Zarejestruj dostawcę zasobów MariaDB |
> | Akcja | Microsoft.DBforMariaDB/servers/administrators/delete | Usuwa istniejącego administratora serwera MariaDB. |
> | Akcja | Microsoft.DBforMariaDB/servers/administrators/read | Pobiera listę administratorów serwera MariaDB. |
> | Akcja | Microsoft.DBforMariaDB/servers/administrators/write | Tworzy lub aktualizuje administrator serwera MariaDB z określonymi parametrami. |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Utworzenie nowej sesji zalecenie akcji |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/read | Zwraca listę doradców |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/read | Zwróć doradcy |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Zwraca listę zalecanych akcji |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Zwraca listę zalecanych akcji |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Zwróć Zalecana akcja |
> | Akcja | Microsoft.DBforMariaDB/servers/configurations/read | Zwraca listę konfiguracji serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft.DBforMariaDB/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft.DBforMariaDB/servers/databases/delete | Usuwa istniejącą bazę danych MariaDB. |
> | Akcja | Microsoft.DBforMariaDB/servers/databases/read | Zwraca listę baz danych MariaDB lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.DBforMariaDB/servers/databases/write | Tworzy bazę danych MariaDB z określonymi parametrami, lub zaktualizuj właściwości określonej bazy danych. |
> | Akcja | Microsoft.DBforMariaDB/servers/delete | Usuwa istniejącego serwera. |
> | Akcja | Microsoft.DBforMariaDB/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft.DBforMariaDB/servers/firewallRules/read | Zwraca listę zapory reguły dla serwera lub pobiera właściwości dla reguły zapory określony. |
> | Akcja | Microsoft.DBforMariaDB/servers/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforMariaDB/servers/logFiles/read | Zwraca listę MariaDB LogFiles. |
> | Akcja | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | Akcja | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów MariaDB |
> | Akcja | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.DBforMariaDB/servers/queryTexts/action | Zwróć tekstów, aby uzyskać listę zapytań |
> | Akcja | Microsoft.DBforMariaDB/servers/queryTexts/action | Zwróć tekst zapytania |
> | Akcja | Microsoft.DBforMariaDB/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforMariaDB/servers/recoverableServers/read | Zwraca informacje możliwe do odzyskania serwera MariaDB |
> | Akcja | Microsoft.DBforMariaDB/servers/replicas/read | Pobierz odczytu replik serwera MariaDB |
> | Akcja | Microsoft.DBforMariaDB/servers/restart/action | Powoduje ponowne uruchomienie określonego serwera. |
> | Akcja | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Pobierz szczegóły zasady wykrywania zagrożeń serwera skonfigurowane na danym serwerze |
> | Akcja | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Zwraca listę statystyki zapytań najpopularniejsze zapytania. |
> | Akcja | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Zwraca statystyki zapytań |
> | Akcja | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Aktualizowanie konfiguracji dla określonego serwera |
> | Akcja | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | Akcja | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguł lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforMariaDB/servers/waitStatistics/read | Zwraca statystyki oczekiwania dla wystąpienia |
> | Akcja | Microsoft.DBforMariaDB/servers/waitStatistics/read | Zwraca statystyki oczekiwania |
> | Akcja | Microsoft.DBforMariaDB/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DBforMySQL/checkNameAvailability/action | Sprawdź, czy nazwa danego serwera jest dostępne do alokowania na całym świecie, w ramach danej subskrypcji. |
> | Akcja | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Zwraca wyniki operacji serwera MySQL |
> | Akcja | Microsoft.DBforMySQL/locations/operationResults/read | ResourceGroup zwracane na podstawie wyników operacji serwera MySQL |
> | Akcja | Microsoft.DBforMySQL/locations/operationResults/read | Zwraca wyniki operacji serwera MySQL |
> | Akcja | Microsoft.DBforMySQL/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforMySQL/operations/read | Zwraca listę operacji MySQL. |
> | Akcja | Microsoft.DBforMySQL/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft.DBforMySQL/register/action | Rejestrowanie dostawcy zasobów MySQL |
> | Akcja | Microsoft.DBforMySQL/servers/administrators/delete | Usuwa istniejącego administratora serwera MySQL. |
> | Akcja | Microsoft.DBforMySQL/servers/administrators/read | Pobiera listę administratorów serwera MySQL. |
> | Akcja | Microsoft.DBforMySQL/servers/administrators/write | Tworzy lub aktualizuje administrator serwera MySQL z określonymi parametrami. |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Utworzenie nowej sesji zalecenie akcji |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/read | Zwraca listę doradców |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/read | Zwróć doradcy |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Zwraca listę zalecanych akcji |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Zwraca listę zalecanych akcji |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Zwróć Zalecana akcja |
> | Akcja | Microsoft.DBforMySQL/servers/configurations/read | Zwraca listę konfiguracji serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft.DBforMySQL/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft.DBforMySQL/servers/databases/delete | Usuwa istniejącą bazę danych MySQL. |
> | Akcja | Microsoft.DBforMySQL/servers/databases/read | Zwraca listę baz danych MySQL lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.DBforMySQL/servers/databases/write | Tworzy bazę danych MySQL przy użyciu określonych parametrów lub zaktualizuj właściwości określonej bazy danych. |
> | Akcja | Microsoft.DBforMySQL/servers/delete | Usuwa istniejącego serwera. |
> | Akcja | Microsoft.DBforMySQL/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft.DBforMySQL/servers/firewallRules/read | Zwraca listę zapory reguły dla serwera lub pobiera właściwości dla reguły zapory określony. |
> | Akcja | Microsoft.DBforMySQL/servers/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforMySQL/servers/logFiles/read | Zwraca listę PostgreSQL LogFiles. |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów MySQL |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.DBforMySQL/servers/queryTexts/action | Zwróć tekstów, aby uzyskać listę zapytań |
> | Akcja | Microsoft.DBforMySQL/servers/queryTexts/action | Zwróć tekst zapytania |
> | Akcja | Microsoft.DBforMySQL/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforMySQL/servers/recoverableServers/read | Zwraca informacje możliwe do odzyskania serwera MySQL |
> | Akcja | Microsoft.DBforMySQL/servers/replicas/read | Pobierz odczytu repliki serwera MySQL |
> | Akcja | Microsoft.DBforMySQL/servers/restart/action | Powoduje ponowne uruchomienie określonego serwera. |
> | Akcja | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Pobierz szczegóły zasady wykrywania zagrożeń serwera skonfigurowane na danym serwerze |
> | Akcja | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Zwraca listę statystyki zapytań najpopularniejsze zapytania. |
> | Akcja | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Zwraca statystyki zapytań |
> | Akcja | Microsoft.DBforMySQL/servers/updateConfigurations/action | Aktualizowanie konfiguracji dla określonego serwera |
> | Akcja | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | Akcja | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguł lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforMySQL/servers/waitStatistics/read | Zwraca statystyki oczekiwania dla wystąpienia |
> | Akcja | Microsoft.DBforMySQL/servers/waitStatistics/read | Zwraca statystyki oczekiwania |
> | Akcja | Microsoft.DBforMySQL/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Sprawdź, czy nazwa danego serwera jest dostępne do alokowania na całym świecie, w ramach danej subskrypcji. |
> | Akcja | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Zwraca wyniki operacji serwera PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/locations/operationResults/read | ResourceGroup zwracane na podstawie wyników operacji serwera PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/locations/operationResults/read | Zwraca wyniki operacji serwera PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Zwraca listę wynik operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/operations/read | Zwraca listę operacji PostgreSQL. |
> | Akcja | Microsoft.DBforPostgreSQL/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft.DBforPostgreSQL/register/action | Zarejestruj dostawcę zasobów PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/servers/administrators/delete | Usuwa istniejącego administratora serwera PostgreSQL. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/administrators/read | Pobiera listę administratorów serwera PostgreSQL. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/administrators/write | Tworzy lub aktualizuje administrator serwera PostgreSQL z określonymi parametrami. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/advisors/read | Zwraca listę doradców |
> | Akcja | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Zwraca listę zalecanych akcji |
> | Akcja | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Zalecenia |
> | Akcja | Microsoft.DBforPostgreSQL/servers/configurations/read | Zwraca listę konfiguracji serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft.DBforPostgreSQL/servers/databases/delete | Usuwa istniejącą bazę danych PostgreSQL. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/databases/read | Zwraca listę baz danych PostgreSQL lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/databases/write | Tworzy bazę danych PostgreSQL z określonymi parametrami lub aktualizuje właściwości określonej bazy danych. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/delete | Usuwa istniejącego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Zwraca listę zapory reguły dla serwera lub pobiera właściwości dla reguły zapory określony. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/logFiles/read | Zwraca listę PostgreSQL LogFiles. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Zwróć tekst zapytania |
> | Akcja | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Zwróć tekstów, aby uzyskać listę zapytań |
> | Akcja | Microsoft.DBforPostgreSQL/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Zwraca informacje możliwe do odzyskania serwera PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/servers/replicas/read | Pobierz odczytu repliki serwera PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/servers/restart/action | Powoduje ponowne uruchomienie określonego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Pobierz szczegóły zasady wykrywania zagrożeń serwera skonfigurowane na danym serwerze |
> | Akcja | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Zwraca listę statystyki zapytań najpopularniejsze zapytania. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Aktualizowanie konfiguracji dla określonego serwera |
> | Akcja | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | Akcja | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguł lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Zwraca statystyki oczekiwania dla wystąpienia |
> | Akcja | Microsoft.DBforPostgreSQL/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Zwraca listę konfiguracji serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/delete | Usuwa istniejącego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Zwraca listę zapory reguły dla serwera lub pobiera właściwości dla reguły zapory określony. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Aktualizowanie konfiguracji dla określonego serwera |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Devices/checkNameAvailability/Action | Sprawdź, czy nazwa usługi IotHub Jeśli jest dostępna |
> | Akcja | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Sprawdź, czy nazwa usługi w przypadku inicjowania obsługi jest dostępne |
> | Akcja | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Sprawdź certyfikat zasobu |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Tworzenie lub aktualizowanie certyfikatów |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Usuwanie zasobu dzierżawy usługi IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Usuń grupę konsumentów Centrum zdarzeń |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Pobieranie grupy konsumentów Centrum zdarzeń |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Tworzenie grupy użytkowników Centrum zdarzeń |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eksportowanie urządzeń |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Pobiera zasób statystyki dzierżawy usługi IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importuj urządzenia |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Pobiera klucz dzierżawy usługi IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Szczegółowe zadania przesłane na danej usługi IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Pobiera klucze dzierżawy usługi IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Pobiera definicje dzienników dostępne dla usługi IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Pobierz metryki limitu przydziału |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/Read | Pobiera zasób dzierżawy usługi IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Komunikat dla wszystkich tras istniejącego testu |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testowanie komunikat względem podanego testu trasy |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla usługi IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/Write | Utwórz lub zaktualizuj zasób dzierżawy usługi IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft.Devices/iotHubs/certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft.Devices/iotHubs/certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft.Devices/iotHubs/certificates/verify/Action | Sprawdź certyfikat zasobu |
> | Akcja | Microsoft.Devices/iotHubs/certificates/Write | Tworzenie lub aktualizowanie certyfikatów |
> | Akcja | Microsoft.Devices/iotHubs/Delete | Usuwanie zasobu usługi IotHub |
> | Akcja | Microsoft.Devices/IotHubs/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/IotHubs/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Usuwa filtr usługi Event Grid |
> | Akcja | Microsoft.Devices/iotHubs/eventGridFilters/Read | Pobiera filtr usługi Event Grid |
> | Akcja | Microsoft.Devices/iotHubs/eventGridFilters/Write | Utworzyć lub zaktualizować istniejący filtr usługi Event Grid |
> | Akcja | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Usuń grupę konsumentów Centrum zdarzeń |
> | Akcja | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Pobieranie grupy konsumentów Centrum zdarzeń |
> | Akcja | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Tworzenie grupy użytkowników Centrum zdarzeń |
> | Akcja | Microsoft.Devices/iotHubs/exportDevices/Action | Eksportowanie urządzeń |
> | Akcja | Microsoft.Devices/iotHubs/importDevices/Action | Importuj urządzenia |
> | Akcja | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Pobierz klucz IotHub dla podanej nazwie |
> | Akcja | Microsoft.Devices/iotHubs/iotHubStats/Read | Zebranie statystyk, IotHub |
> | Akcja | Microsoft.Devices/iotHubs/jobs/Read | Szczegółowe zadania przesłane na danej usługi IotHub |
> | Akcja | Microsoft.Devices/iotHubs/listkeys/Action | Pobierz wszystkie klucze usługi IotHub |
> | Akcja | Microsoft.Devices/IotHubs/logDefinitions/read | Pobiera definicje dzienników dostępne dla usługi IotHub |
> | Akcja | Microsoft.Devices/IotHubs/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft.Devices/iotHubs/operationresults/Read | Pobierz wynik operacji (przestarzałe API) |
> | Akcja | Microsoft.Devices/iotHubs/quotaMetrics/Read | Pobierz metryki limitu przydziału |
> | Akcja | Microsoft.Devices/iotHubs/Read | Pobiera zasoby usługi IotHub |
> | Akcja | Microsoft.Devices/iotHubs/routing/$testall/Action | Komunikat dla wszystkich tras istniejącego testu |
> | Akcja | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testowanie komunikat względem podanego testu trasy |
> | Akcja | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla usługi IotHub |
> | Akcja | Microsoft.Devices/iotHubs/skus/Read | Pobierz prawidłowe jednostki SKU usługi IotHub |
> | Akcja | Microsoft.Devices/iotHubs/Write | Utwórz lub zaktualizuj zasób usługi IotHub |
> | Akcja | Microsoft.Devices/locations/operationresults/Read | Pobierz wynik operacji usług Location based |
> | Akcja | Microsoft.Devices/operationresults/Read | Pobierz wynik operacji |
> | Akcja | Microsoft.Devices/operations/Read | Pobierz wszystkie operacje ResourceProvider |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/verify/Action | Sprawdź certyfikat zasobu |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/Write | Tworzenie lub aktualizowanie certyfikatów |
> | Akcja | Microsoft.Devices/provisioningServices/Delete | Usuń zasób IotDps |
> | Akcja | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Pobierz klucze IotDps dla nazwy klucza |
> | Akcja | Microsoft.Devices/provisioningServices/listkeys/Action | Pobierz wszystkie klucze IotDps |
> | Akcja | Microsoft.Devices/provisioningServices/logDefinitions/read | Pobiera definicje dzienników dostępne dla usługi aprowizacji |
> | Akcja | Microsoft.Devices/provisioningServices/metricDefinitions/read | Pobiera dostępne metryki dla usługi aprowizacji |
> | Akcja | Microsoft.Devices/provisioningServices/operationresults/Read | Pobierz wynik operacji usługi DPS |
> | Akcja | Microsoft.Devices/provisioningServices/Read | Pobierz zasób IotDps |
> | Akcja | Microsoft.Devices/provisioningServices/skus/Read | Pobierz prawidłowe IotDps jednostki SKU. |
> | Akcja | Microsoft.Devices/provisioningServices/Write | Utwórz zasób IotDps |
> | Akcja | Microsoft.Devices/register/action | Zarejestruj subskrypcję dostawcy zasobów usługi IotHub i włącza funkcję tworzenia zasobów usługi IotHub |
> | Akcja | Microsoft.Devices/register/action | Zarejestruj subskrypcję dostawcy zasobów usługi IotHub i włącza funkcję tworzenia zasobów usługi IotHub |
> | Akcja | Microsoft.Devices/usages/Read | Uzyskiwanie subskrypcji szczegóły użycia dla tego dostawcy. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DevSpaces/controllers/delete | Usuwanie usługi Azure Dev miejsca do magazynowania kontrolera i płaszczyzny danych |
> | Akcja | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Szczegóły połączenia listy dla infrastruktury kontrolera Azure Dev miejsca do magazynowania |
> | Akcja | Microsoft.DevSpaces/controllers/read | Właściwości odczytu Azure Dev miejsca do magazynowania kontrolera |
> | Akcja | Microsoft.DevSpaces/controllers/write | Tworzenie lub właściwości aktualizacji usługi Azure Dev miejsca do magazynowania kontrolera |
> | Akcja | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Sprawdź istniejące mapowanie kontrolera hosta kontenera |
> | Akcja | Microsoft.DevSpaces/locations/operationresults/read | Odczytaj stan dla operacji asynchronicznej |
> | Akcja | Microsoft.DevSpaces/register/action | Procedura Rejestruj dostawcę zasobów miejsca do magazynowania firmy Microsoft Dev z subskrypcją |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DevTestLab/labCenters/delete | Usuń centra laboratoryjne. |
> | Akcja | Microsoft.DevTestLab/labCenters/read | Przeczytaj centra laboratoryjne. |
> | Akcja | Microsoft.DevTestLab/labCenters/write | Dodaje lub modyfikuje centra laboratoryjne. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Do odczytu szablonów Menedżera zasobów platformy azure. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generuje szablonu usługi ARM dla danego artefaktu, przekazuje wymagane pliki na konto magazynu i sprawdza poprawność wygenerowanego artefaktu. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Przeczytaj artefaktów. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/delete | Usuń źródła artefaktu. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/read | Przeczytaj źródeł artefaktów. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/write | Dodaje lub modyfikuje źródeł artefaktów. |
> | Akcja | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Oświadczenie losowe przejęcia maszynę wirtualną w środowisku laboratoryjnym. |
> | Akcja | Microsoft.DevTestLab/labs/costs/read | Przeczytaj kosztów. |
> | Akcja | Microsoft.DevTestLab/labs/costs/write | Dodaje lub modyfikuje kosztów. |
> | Akcja | Microsoft.DevTestLab/labs/CreateEnvironment/action | Tworzenie maszyn wirtualnych w laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/customImages/delete | Usuwanie obrazów niestandardowych. |
> | Akcja | Microsoft.DevTestLab/labs/customImages/read | Przeczytaj obrazów niestandardowych. |
> | Akcja | Microsoft.DevTestLab/labs/customImages/write | Dodaje lub modyfikuje obrazów niestandardowych. |
> | Akcja | Microsoft.DevTestLab/labs/delete | Usuń labs. |
> | Akcja | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Upewnij się, że bieżący użytkownik nie ma prawidłowego profilu w środowisku laboratoryjnym. |
> | Akcja | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Eksportuje użycia zasobów laboratorium do konta magazynu |
> | Akcja | Microsoft.DevTestLab/labs/formulas/delete | Usunięcie formuły. |
> | Akcja | Microsoft.DevTestLab/labs/formulas/read | Przeczytaj formuły. |
> | Akcja | Microsoft.DevTestLab/labs/formulas/write | Dodaje lub modyfikuje formuły. |
> | Akcja | Microsoft.DevTestLab/labs/galleryImages/read | Przeczytaj galerii obrazów. |
> | Akcja | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Wygenerować identyfikator URI przekazywania obrazów niestandardowych dysku do laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/idleShutdowns/delete | Usuń zamykania w stanie bezczynności. |
> | Akcja | Microsoft.DevTestLab/labs/idleShutdowns/read | Przeczytaj zamykania w stanie bezczynności. |
> | Akcja | Microsoft.DevTestLab/labs/idleShutdowns/write | Dodaje lub modyfikuje zamykania w stanie bezczynności. |
> | Akcja | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Zaimportować maszynę wirtualną do innego laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/ListVhds/action | Wyświetl listę obrazów dysku dostępne na potrzeby tworzenia obrazu niestandardowego. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/delete | Usuń notificationchannels. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Wyślij powiadomienie na kanale podana. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/read | Przeczytaj notificationchannels. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/write | Dodaje lub modyfikuje notificationchannels. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Ocenia zasady laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/policies/delete | Usuń zasady. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/policies/read | Przeczytaj zasady. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/policies/write | Dodawanie lub modyfikowanie zasad. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/read | Przeczytaj zestawy zasad. |
> | Akcja | Microsoft.DevTestLab/labs/read | Przeczytaj labs. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/Execute/action | Wykonania harmonogramu. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Wyświetla listę wszystkich odpowiednich harmonogramów |
> | Akcja | Microsoft.DevTestLab/labs/schedules/read | Przeczytaj harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/serviceRunners/delete | Usuń usługę drugie miejsca. |
> | Akcja | Microsoft.DevTestLab/labs/serviceRunners/read | Drugie miejsca usługi odczytu. |
> | Akcja | Microsoft.DevTestLab/labs/serviceRunners/write | Dodaje lub modyfikuje używanych modułów uruchamiających usługi. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/delete | Usuń udostępniony galerii. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/read | Galerie udostępnionych do odczytu. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Usuń udostępnianych obrazów. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Przeczytaj udostępnianych obrazów. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Dodaje lub modyfikuje udostępnianych obrazów. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/write | Dodaje lub modyfikuje galerie udostępnionych. |
> | Akcja | Microsoft.DevTestLab/labs/users/delete | Usuń profile użytkowników. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/Attach/action | Dołączanie i utworzyć dzierżawę dysku do maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/delete | Usuń dyski. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/Detach/action | Odłącz i przerwać dzierżawy dysków dołączonych do maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/read | Przeczytaj dysków. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/write | Dodaje lub modyfikuje dysków. |
> | Akcja | Microsoft.DevTestLab/labs/users/environments/delete | Usuwanie środowisk. |
> | Akcja | Microsoft.DevTestLab/labs/users/environments/read | Odczyt środowisk. |
> | Akcja | Microsoft.DevTestLab/labs/users/environments/write | Dodaje lub modyfikuje środowisk. |
> | Akcja | Microsoft.DevTestLab/labs/users/read | Odczyt profilów użytkowników. |
> | Akcja | Microsoft.DevTestLab/labs/users/secrets/delete | Usuń wpisy tajne. |
> | Akcja | Microsoft.DevTestLab/labs/users/secrets/read | Odczyt kluczy tajnych. |
> | Akcja | Microsoft.DevTestLab/labs/users/secrets/write | Dodaje lub modyfikuje wpisy tajne. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Usuwanie usługi sieci szkieletowej. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Wyświetla listę harmonogramy dotyczy uruchomień/zatrzymań ewentualne. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Sieci szkieletowe odczytu usługi. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Wykonania harmonogramu. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Przeczytaj harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Uruchom usługi Service fabric. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zatrzymaj usługi Service fabric |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Dodaje lub modyfikuje Usługa sieci szkieletowej. |
> | Akcja | Microsoft.DevTestLab/labs/users/write | Dodaje lub modyfikuje profile użytkowników. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Dołączanie dysku danych nowej lub istniejącej maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Zastosowanie artefaktów do maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Przejęcie na własność istniejącej maszyny wirtualnej |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/delete | Usuń maszyny wirtualne. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Odłącz określony dysk od maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Pobiera ciąg, który reprezentuje zawartość pliku RDP dla maszyny wirtualnej |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Wyświetla listę harmonogramy dotyczy uruchomień/zatrzymań ewentualne. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/read | Przeczytaj maszyn wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Ponowne wdrażanie maszyny wirtualnej |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Zmień rozmiar maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Uruchom ponownie maszynę wirtualną. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Wykonania harmonogramu. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Przeczytaj harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Uruchom maszynę wirtualną. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Zatrzymaj maszynę wirtualną |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Przesyła wszystkie dyski z danymi dołączonych do maszyny wirtualnej należy do bieżącego użytkownika. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Zwolnij posiadania istniejącej maszyny wirtualnej |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/write | Dodaje lub modyfikuje maszyny wirtualne. |
> | Akcja | Microsoft.DevTestLab/labs/virtualNetworks/delete | Usuwanie sieci wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/virtualNetworks/read | Przeczytaj sieci wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/virtualNetworks/write | Dodaje lub modyfikuje sieci wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/vmPools/delete | Usuń pule maszyn wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/vmPools/read | Przeczytaj pule maszyn wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/vmPools/write | Dodaje lub modyfikuje pule maszyn wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/write | Dodawanie lub modyfikowanie laboratoriów. |
> | Akcja | Microsoft.DevTestLab/locations/operations/read | Operacje odczytu. |
> | Akcja | Microsoft.DevTestLab/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.DevTestLab/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/schedules/Execute/action | Wykonania harmonogramu. |
> | Akcja | Microsoft.DevTestLab/schedules/read | Przeczytaj harmonogramów. |
> | Akcja | Microsoft.DevTestLab/schedules/Retarget/action | Aktualizuje zasób docelowy zgodnie z harmonogramem identyfikatora. |
> | Akcja | Microsoft.DevTestLab/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DocumentDB/databaseAccountNames/read | Sprawdza dostępność nazwy. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Usuwanie kolekcji. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Przeczytaj kolekcję lub listę, wszystkie kolekcje. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Przeczytaj przepływność kolekcji. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Zaktualizuj przepływność kolekcji. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Utwórz lub zaktualizuj kolekcję. Dotyczy to tylko typy interfejsów API: "bazy danych mongodb". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Usuń kontener. Dotyczy to tylko typy interfejsów API: "sql". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "sql". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Przeczytaj kontener lub listę wszystkich kontenerów. Dotyczy to tylko typy interfejsów API: "sql". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "sql". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Przeczytaj przepływności kontenerów. Dotyczy to tylko typy interfejsów API: "sql". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Zaktualizuj przepływności kontenerów. Dotyczy to tylko typy interfejsów API: "sql". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Utwórz lub zaktualizuj kontenera. Dotyczy to tylko typy interfejsów API: "sql". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Usuwanie bazy danych. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Usuwanie wykresu. Dotyczy to tylko typy interfejsów API: "gremlin". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "gremlin". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Przeczytaj wykres lub wyświetlić listę wszystkich wykresów. Dotyczy to tylko typy interfejsów API: "gremlin". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "gremlin". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Przeczytaj przepływności wykresu. Dotyczy to tylko typy interfejsów API: "gremlin". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Zaktualizuj przepływności wykresu. Dotyczy to tylko typy interfejsów API: "gremlin". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Utwórz lub zaktualizuj wykresu. Dotyczy to tylko typy interfejsów API: "gremlin". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Przeczytaj bazę danych lub wyświetlić listę wszystkich baz danych. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Przeczytaj przepływności bazy danych. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Zaktualizuj przepływności bazy danych. Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Tworzenie bazy danych Dotyczy to tylko typy interfejsów API: "sql", "mongodb", "gremlin". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Usuń przestrzeń kluczy. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Przeczytaj przestrzeń kluczy lub wyświetlić listę wszystkich keyspaces. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "cassandra". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Przeczytaj przepływności przestrzeń kluczy. Dotyczy to tylko typy interfejsów API: "cassandra". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Zaktualizuj przepływności przestrzeń kluczy. Dotyczy to tylko typy interfejsów API: "cassandra". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Usuwanie tabeli. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Odczyt tabeli lub wyświetlić listę wszystkich tabel. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "cassandra". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Przeczytaj przepływności tabeli. Dotyczy to tylko typy interfejsów API: "cassandra". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Zaktualizuj przepływności tabeli. Dotyczy to tylko typy interfejsów API: "cassandra". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Utwórz lub zaktualizuj tabelę. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Utwórz przestrzeń kluczy. Dotyczy to tylko typy interfejsów API: "cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Usuwanie tabeli. Dotyczy to tylko typy interfejsów API: "table". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "table". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Odczyt tabeli lub wyświetlić listę wszystkich tabel. Dotyczy to tylko typy interfejsów API: "table". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Odczytaj stan operacji asynchronicznej. Dotyczy to tylko typy interfejsów API: "table". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Przeczytaj przepływności tabeli. Dotyczy to tylko typy interfejsów API: "table". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Zaktualizuj przepływności tabeli. Dotyczy to tylko typy interfejsów API: "table". Stosuje się tylko do ustawiania typów: "przepustowość". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Utwórz lub zaktualizuj tabelę. Dotyczy to tylko typy interfejsów API: "table". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/backup/action | Prześlij żądanie do skonfigurowania kopii zapasowej |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Zmień grupę zasobów konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Odczytuje definicje metryk w kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Odczytuje metryki kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Przeczytaj kluczowe metryki poziomu bazy danych konta partycji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Przeczytaj metryki poziomu partycji konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Przeczytaj partycje konta bazy danych w kolekcji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Odczytaj konto bazy danych partycji poziomu użycia |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Odczytuje użycia kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Odczytuje definicje metryk bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Odczytuje metryki bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Odczytuje użycia bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/delete | Usuwa konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Zmień priorytetów trybu failover w regionach konto bazy danych. Służy to do wykonania operacji ręcznej pracy awaryjnej |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Pobierz parametry połączenia dla konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Wyświetl klucze konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Odczytuje bazę danych, definicje metryk kont. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/metrics/read | Odczytuje metryki konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | W trybie offline region konta bazy danych.  |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online region konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Odczytaj stan operacji asynchronicznej |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Metryki opóźnienie odczytu |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/read | Przeczytaj percentyle opóźnienia w replikacji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Odczyt metryk opóźnienia dla określonego regionu źródłowego i docelowego |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Metryki opóźnienie odczytu dla konkretnego docelowego regionu |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Usuń serwer proxy połączeń prywatnych punktów końcowych konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Przeczytaj połączenia serwera proxy prywatnych punktów końcowych konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Sprawdź poprawność połączenia serwera proxy prywatnych punktów końcowych konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Utwórz lub zaktualizuj serwer proxy połączeń prywatnych punktów końcowych konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/read | Odczytuje konto bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Odczytuje bazę danych, klucze tylko do odczytu konta. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Odczytuje bazę danych, klucze tylko do odczytu konta. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Obróć klucze konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Odczytuje metryki regionalnych kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Przeczytaj kluczowe metryki poziomu regionalnego bazy danych konta partycji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Przeczytaj metryki poziomu regionalnego bazy danych konta partycji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Przeczytaj partycje konta regionalnego bazy danych w kolekcji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Odczytuje metryki konta region i bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/restore/action | Przesyłanie żądania przywracania |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/usages/read | Odczytuje bazę danych użycia konta. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/write | Aktualizowanie kont bazy danych. |
> | Akcja | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft.DocumentDB, że sieć wirtualna lub podsieć jest usuwana |
> | Akcja | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Odczyt stanu deleteVirtualNetworkOrSubnets operacji asynchronicznej |
> | Akcja | Microsoft.DocumentDB/locations/operationsStatus/read | Odczytuje stan operacji asynchronicznych |
> | Akcja | Microsoft.DocumentDB/operationResults/read | Odczytaj stan operacji asynchronicznej |
> | Akcja | Microsoft.DocumentDB/operations/read | Dostępne operacje odczytu dla Microsoft DocumentDB  |
> | Akcja | Microsoft.DocumentDB/register/action |  Zarejestruj dostawcę zasobów Microsoft DocumentDB dla subskrypcji |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DomainRegistration/checkDomainAvailability/Action | Sprawdź, czy domena jest dostępna do zakupu |
> | Akcja | Microsoft.DomainRegistration/domains/Delete | Usuwanie istniejącej domeny. |
> | Akcja | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Usuń identyfikator własności |
> | Akcja | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Identyfikatory własność listy |
> | Akcja | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Pobierz identyfikator własności |
> | Akcja | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Utwórz lub zaktualizuj identyfikator |
> | Akcja | Microsoft.DomainRegistration/domains/operationresults/Read | Operacja pobrania domeny |
> | Akcja | Microsoft.DomainRegistration/domains/Read | Pobieranie listy domen |
> | Akcja | Microsoft.DomainRegistration/domains/Read | Pobierz domeny |
> | Akcja | Microsoft.DomainRegistration/domains/renew/Action | Odnów istniejącej domeny. |
> | Akcja | Microsoft.DomainRegistration/domains/Write | Dodaj nową domenę, lub zaktualizuj istniejącą |
> | Akcja | Microsoft.DomainRegistration/generateSsoRequest/Action | Generuje żądanie logowania się do centrum kontroli domeny. |
> | Akcja | Microsoft.DomainRegistration/listDomainRecommendations/Action | Pobierz listę zaleceń dotyczących domeny, na podstawie słów kluczowych |
> | Akcja | Microsoft.DomainRegistration/operations/Read | Lista wszystkich operacji z rejestracji domeny usługi aplikacji |
> | Akcja | Microsoft.DomainRegistration/register/action | Zarejestruj dostawcę zasobów Domains firmy Microsoft dla subskrypcji |
> | Akcja | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Lista akcji umowy |
> | Akcja | Microsoft.DomainRegistration/topLevelDomains/Read | Pobrać toplevel domen |
> | Akcja | Microsoft.DomainRegistration/topLevelDomains/Read | Pobierz toplevel domeny |
> | Akcja | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Sprawdź poprawność obiektu zakupu domeny bez przesyłania |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.EventGrid/domains/delete | Usuwanie domeny |
> | Akcja | Microsoft.EventGrid/domains/listKeys/action | Wyświetl listę kluczy dla domeny |
> | Akcja | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla domen |
> | Akcja | Microsoft.EventGrid/domains/read | Przeczytaj domeny |
> | Akcja | Microsoft.EventGrid/domains/regenerateKey/action | Ponowne generowanie klucza dla domeny |
> | Akcja | Microsoft.EventGrid/domains/topics/read | Zapoznaj się z tematem domeny |
> | Akcja | Microsoft.EventGrid/domains/write | Utwórz lub zaktualizuj domenę |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/delete | Usuń eventSubscription |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Uzyskaj pełny adres url dla subskrypcji zdarzeń |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla eventSubscriptions |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/read | Przeczytaj eventSubscription |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/write | Utwórz lub zaktualizuj eventSubscription |
> | Akcja | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla tematów |
> | Akcja | Microsoft.EventGrid/extensionTopics/read | Przeczytaj extensionTopic. |
> | Akcja | Microsoft.EventGrid/locations/eventSubscriptions/read | Subskrypcje zdarzeń regionalnych listy |
> | Akcja | Microsoft.EventGrid/locations/operationResults/read | Przeczytaj wynik operacji regionalne |
> | Akcja | Microsoft.EventGrid/locations/operationsStatus/read | Odczytaj stan operacji regionalne |
> | Akcja | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Wyświetl subskrypcje zdarzeń regionalnych przez topictype |
> | Akcja | Microsoft.EventGrid/operationResults/read | Wynik operacji odczytu |
> | Akcja | Microsoft.EventGrid/operations/read | Wyświetl listę operacji EventGrid. |
> | Akcja | Microsoft.EventGrid/operationsStatus/read | Odczytaj stan operacji |
> | Akcja | Microsoft.EventGrid/register/action | Rejestruje subskrypcję dostawcy zasobów EventGrid. |
> | Akcja | Microsoft.EventGrid/topics/delete | Usuwanie tematu |
> | Akcja | Microsoft.EventGrid/topics/listKeys/action | Wyświetl listę kluczy dla tematu |
> | Akcja | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla tematów |
> | Akcja | Microsoft.EventGrid/topics/read | Zapoznaj się z tematem |
> | Akcja | Microsoft.EventGrid/topics/regenerateKey/action | Wygeneruj ponownie klucz dla tematu |
> | Akcja | Microsoft.EventGrid/topics/write | Utwórz lub zaktualizuj tematu |
> | Akcja | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Subskrypcje zdarzeń globalnej listy według typu tematu |
> | Akcja | Microsoft.EventGrid/topictypes/eventtypes/read | Przeczytaj eventtypes obsługiwane przez topictype |
> | Akcja | Microsoft.EventGrid/topictypes/read | Przeczytaj topictype |
> | Akcja | Microsoft.EventGrid/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.EventHub/availableClusterRegions/read | Przeczytaj operację, aby wyświetlić listę dostępnych klastrów wstępnie przygotowany przez region platformy Azure. |
> | Akcja | Microsoft.EventHub/checkNameAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.EventHub/checkNamespaceAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailability. |
> | Akcja | Microsoft.EventHub/clusters/delete | Usuwa istniejący zasób klastra. |
> | Akcja | Microsoft.EventHub/clusters/namespaces/read | Lista identyfikatorów ARM przestrzeni nazw dla przestrzeni nazw w ramach klastra. |
> | Akcja | Microsoft.EventHub/clusters/operationresults/read | Pobierz stan operacji asynchronicznej klastra. |
> | Akcja | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryki klastra opisów zasobów |
> | Akcja | Microsoft.EventHub/clusters/read | Pobiera opis zasobu klastra |
> | Akcja | Microsoft.EventHub/clusters/write | Tworzy lub modyfikuje istniejący zasób klastra. |
> | Akcja | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Usuwa reguły sieci wirtualnej dostawcy zasobów usługi EventHub dla określonej sieci wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/action | Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest przestarzały. Można zaktualizować reguły autoryzacji Namespace zamiast niego użyj wywołania PUT... Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć domyślnej reguły autoryzacji Namespace.  |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia Namespace |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Wygeneruj ponownie klucz podstawowy lub pomocniczy do zasobu |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie Namespace i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | Akcja | Microsoft.EventHub/namespaces/Delete | Usuwanie zasobu Namespace |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobiera autoryzacji kluczy reguły dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji awaryjnego odzyskiwania podstawowego Namespace firmy |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. Tę operację można wywołać tylko za pomocą podstawowej przestrzeni nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operacja aktualizowania usługi EventHub. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operacja usuwania reguł autoryzacji Centrum zdarzeń |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Pobierz parametry połączenia Centrum zdarzeń |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Pobierz listę reguł autoryzacji Centrum zdarzeń |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Wygeneruj ponownie klucz podstawowy lub pomocniczy do zasobu |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Utwórz reguły autoryzacji Centrum zdarzeń i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | Akcja | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operacja usuwania zasobu grupy konsumentów |
> | Akcja | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Pobierz listę opisów zasobów grupy konsumentów |
> | Akcja | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Tworzenie lub właściwości grupy konsumentów aktualizacji. |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/Delete | Operacja usuwania zasobów usługi EventHub |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/read | Pobierz listę opisów zasobów Centrum zdarzeń |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/write | Tworzenie lub właściwości aktualizacji usługi Event Hub. |
> | Akcja | Microsoft.EventHub/namespaces/ipFilterRules/delete | Usuń zasób filtru IP |
> | Akcja | Microsoft.EventHub/namespaces/ipFilterRules/read | Pobierz zasób filtru IP |
> | Akcja | Microsoft.EventHub/namespaces/ipFilterRules/write | Utwórz zasób filtru IP |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Odbieranie komunikatów |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Wysyłanie komunikatów |
> | Akcja | Microsoft.EventHub/namespaces/messagingPlan/read | Pobiera Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.EventHub/namespaces/messagingPlan/write | Aktualizuje Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.EventHub/namespaces/networkrulesets/delete | Usuwanie zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Akcja | Microsoft.EventHub/namespaces/networkrulesets/write | Tworzenie zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/operationresults/read | Pobierz stan operacji Namespace |
> | Akcja | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz listę opisów zasobów ustawień diagnostycznych Namespace |
> | Akcja | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Pobierz listę opisów zasobów ustawień diagnostycznych Namespace |
> | Akcja | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Pobierz listę dzienników Namespace opisów zasobów |
> | Akcja | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryk Namespace opisów zasobów |
> | Akcja | Microsoft.EventHub/namespaces/read | Pobierz listę opisów zasobów Namespace |
> | Akcja | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Usuwanie przestrzeni nazw usługi ACS |
> | Akcja | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Usuwanie zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Pobiera zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Tworzenie zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/write | Tworzenie zasobu Namespace i zaktualizuj jego właściwości. Tagi i pojemność Namespace są właściwości, które mogą być aktualizowane. |
> | Akcja | Microsoft.EventHub/operations/read | Pobierz operacje |
> | Akcja | Microsoft.EventHub/register/action | Rejestruje subskrypcję dostawcy zasobów usługi EventHub i włącza funkcję tworzenia zasobów usługi EventHub |
> | Akcja | Microsoft.EventHub/sku/read | Pobierz listę opisów zasobów jednostki Sku |
> | Akcja | Microsoft.EventHub/sku/regions/read | Pobierz listę opisów zasobów SkuRegions |
> | Akcja | Microsoft.EventHub/unregister/action | Rejestruje dostawcę zasobów usługi EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Features/features/read | Pobiera funkcje subskrypcji. |
> | Akcja | Microsoft.Features/operations/read | Pobiera listę operacji. |
> | Akcja | Microsoft.Features/providers/features/read | Pobiera funkcję subskrypcji danego dostawcy zasobów. |
> | Akcja | Microsoft.Features/providers/features/register/action | Rejestruje funkcję subskrypcji danego dostawcy zasobów. |
> | Akcja | Microsoft.Features/providers/features/unregister/action | Wyrejestrowuje funkcję subskrypcji danego dostawcy zasobów. |
> | Akcja | Microsoft.Features/register/action | Rejestruje funkcję subskrypcji. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Usuń przypisanie konfiguracji gościa. |
> | Akcja | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Przypisanie konfiguracji gościa GET. |
> | Akcja | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Raport o Get gościa konfiguracji przypisania. |
> | Akcja | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Utwórz nowe przypisanie konfiguracji gościa. |
> | Akcja | Microsoft.GuestConfiguration/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.HDInsight/clusters/applications/delete | Usuń aplikację dla klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/applications/read | Pobieranie aplikacji w przypadku klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/applications/write | Utwórz lub zaktualizuj aplikację dla klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/changerdpsetting/action | Zmień ustawienie połączenia RDP dla klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/configurations/action | Zaktualizuj konfigurację klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/configurations/action | Pobierz konfiguracje klastrów HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/configurations/read | Pobierz konfiguracje klastrów HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/delete | Usuń klaster HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/getGatewaySettings/action | Pobieranie ustawień bramy dla klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/read | Szczegółowe informacje o klastrze HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/roles/resize/action | Zmień rozmiar klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aktualizowanie ustawień bramy dla klastra HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/write | Utwórz lub zaktualizuj klaster HDInsight |
> | Akcja | Microsoft.HDInsight/locations/capabilities/read | Skorzystaj z możliwości subskrypcji |
> | Akcja | Microsoft.HDInsight/locations/checkNameAvailability/read | Sprawdź dostępność nazwy |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ImportExport/jobs/delete | Usuwa istniejące zadanie. |
> | Akcja | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Pobiera klucze funkcji BitLocker dla określonego zadania. |
> | Akcja | Microsoft.ImportExport/jobs/read | Pobiera właściwości dla określonego zadania lub zwraca listę wszystkich zadań. |
> | Akcja | Microsoft.ImportExport/jobs/write | Tworzy zadanie z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego zadania. |
> | Akcja | Microsoft.ImportExport/locations/read | Pobiera właściwości dla określonej lokalizacji lub zwraca listę lokalizacji. |
> | Akcja | Microsoft.ImportExport/register/action | Rejestruje subskrypcję dostawcy zasobów importu/eksportu i umożliwia tworzenie zadania importu/eksportu. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Insights/ActionGroups/Delete | Usuwanie grupy akcji |
> | Akcja | Microsoft.Insights/ActionGroups/Read | Odczyt grupy akcji |
> | Akcja | Microsoft.Insights/ActionGroups/Write | Utwórz lub zaktualizuj grupę akcji |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktywowany działania dziennika alertów |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Delete | Usuwanie alertu dziennika aktywności |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Read | Odczyt alertu dziennika aktywności |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Write | Utwórz lub zaktualizuj alertu dziennika aktywności |
> | Akcja | Microsoft.Insights/AlertRules/Activated/Action | Klasyczny alert metryki aktywowany |
> | Akcja | Microsoft.Insights/AlertRules/Delete | Usuwanie klasycznego alertu metryki |
> | Akcja | Microsoft.Insights/AlertRules/Incidents/Read | Przeczytaj incydentem klasycznego alertu metryki |
> | Akcja | Microsoft.Insights/AlertRules/Read | Przeczytaj klasycznego alertu metryki |
> | Akcja | Microsoft.Insights/AlertRules/Resolved/Action | Klasyczny alert metryki rozwiązane |
> | Akcja | Microsoft.Insights/AlertRules/Throttled/Action | Klasyczne metryki reguła alertu ograniczana |
> | Akcja | Microsoft.Insights/AlertRules/Write | Utwórz lub zaktualizuj klasycznego alertu metryki |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Delete | Usuń ustawienie skalowania automatycznego |
> | Akcja | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Utwórz lub zaktualizuj ustawienia diagnostyczne zasobu |
> | Akcja | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Przeczytaj definicje dzienników |
> | Akcja | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Read | Odczyt ustawienia autoskalowania |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Skalowanie automatyczne skalowanie w dół zainicjowane przez |
> | Akcja | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Skalowanie automatyczne skalowanie w dół ukończone |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Skalowanie automatyczne skalowanie w górę zainicjowane przez |
> | Akcja | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Skalowanie automatyczne skalowanie w górę ukończone |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Write | Utwórz lub zaktualizuj ustawienia automatycznego skalowania |
> | Akcja | Microsoft.Insights/Baseline/Read | Odczyt planu bazowego metryki (wersja zapoznawcza) |
> | Akcja | Microsoft.Insights/CalculateBaseline/Read | Oblicz punkt odniesienia dla wartości metryk (wersja zapoznawcza) |
> | Akcja | Microsoft.Insights/Components/AnalyticsItems/Delete | Usuwanie elementu analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsItems/Read | Odczytywanie elementu analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsItems/Write | Zapisywanie elementu analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsTables/Action | Akcja tabeli analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsTables/Delete | Trwa usuwanie usługi Application Insights schematu tabeli analizy |
> | Akcja | Microsoft.Insights/Components/AnalyticsTables/Read | Usługi Application Insights odczytywanie schematu tabeli analizy |
> | Akcja | Microsoft.Insights/Components/AnalyticsTables/Write | Zapisywanie usługi Application Insights schematu tabeli analizy |
> | Akcja | Microsoft.Insights/Components/Annotations/Delete | Usuwanie adnotacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Annotations/Read | Odczytywanie adnotacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Annotations/Write | Zapisywanie adnotacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Api/Read | Odczytywanie interfejsu API danych składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ApiKeys/Action | Generowanie klucza interfejsu API usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ApiKeys/Delete | Usuwanie klucza interfejsu API usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ApiKeys/Read | Odczytywanie klucza interfejsu API usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/BillingPlanForComponent/Read | Odczytywanie planu rozliczeniowego dla składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Odczytywanie bieżących funkcji rozliczeń składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Zapisywanie bieżących funkcji rozliczeń składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Odczytywanie konfiguracji integracji usługi Application Insights domyślne ALM |
> | Akcja | Microsoft.Insights/Components/Delete | Usuwanie konfiguracji składnika usługi application insights |
> | Akcja | Microsoft.Insights/Components/Events/Read | Pobieranie dzienników z usługi Application Insights, używając formatu zapytań OData |
> | Akcja | Microsoft.Insights/Components/ExportConfiguration/Action | Akcja ustawień eksportu usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ExportConfiguration/Delete | Usuwania eksportu usługi Application Insights ustawienia |
> | Akcja | Microsoft.Insights/Components/ExportConfiguration/Read | Odczytywanie Application Insights Eksportuj ustawienia |
> | Akcja | Microsoft.Insights/Components/ExportConfiguration/Write | Zapisywanie Application Insights Eksportuj ustawienia |
> | Akcja | Microsoft.Insights/Components/ExtendQueries/Read | Składnika usługi Application Insights odczytywanie rozszerzonych wyników zapytania |
> | Akcja | Microsoft.Insights/Components/Favorites/Delete | Usuwanie ulubionego elementu w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Favorites/Read | Odczytywanie ulubionego elementu w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Favorites/Write | Zapisywanie ulubionego elementu w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/FeatureCapabilities/Read | Możliwości funkcji składnika usługi Application Insights odczytu |
> | Akcja | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Dostępnych funkcji rozliczeń składnika usługi Application Insights odczytu |
> | Akcja | Microsoft.Insights/Components/GetToken/Read | Odczytywanie tokenu składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/MetricDefinitions/Read | Odczytywanie definicji metryk składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/Metrics/Read | Metryk składnika usługi Application Insights odczytu |
> | Akcja | Microsoft.Insights/Components/Move/Action | Przenieś składnik usługi Application Insights do innej grupy zasobów lub subskrypcji |
> | Akcja | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Trwa usuwanie usługi Application Insights osobistego elementu analizy usługi |
> | Akcja | Microsoft.Insights/Components/MyAnalyticsItems/Read | Odczytywanie usługi Application Insights osobistego elementu analizy usługi |
> | Akcja | Microsoft.Insights/Components/MyAnalyticsItems/Write | Zapisywanie usługi Application Insights osobistego elementu analizy usługi |
> | Akcja | Microsoft.Insights/Components/MyFavorites/Read | Odczytywanie osobistego ulubionego elementu usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/Operations/Read | Pobierz stan operacji długotrwałych w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/PricingPlans/Read | Odczytywanie planu cenowego składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/PricingPlans/Write | Zapisywanie planu cenowego składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Konfiguracji wykrywania proaktywnego usługi Application Insights odczytu |
> | Akcja | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Zapisywanie konfiguracji wykrywania proaktywnego usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/Components/Purge/Action | Czyszczenie danych z usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/Query/Read | Uruchom zapytania względem dzienników usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/QuotaStatus/Read | Odczytywanie stanu przydziału składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/Read | Odczytywanie konfiguracji składnika usługi application insights |
> | Akcja | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Usługi Application Insights odczytywanie lokalizacji testu internetowego |
> | Akcja | Microsoft.Insights/Components/Webtests/Read | Odczytywanie konfiguracji testu internetowego |
> | Akcja | Microsoft.Insights/Components/WorkItemConfigs/Delete | Usuwanie konfiguracji integracji z zarządzaniem cyklem życia aplikacji szczegółowych informacji |
> | Akcja | Microsoft.Insights/Components/WorkItemConfigs/Read | Odczytywanie konfiguracji integracji z zarządzaniem cyklem życia aplikacji szczegółowych informacji |
> | Akcja | Microsoft.Insights/Components/WorkItemConfigs/Write | Zapisywanie konfiguracji integracji z zarządzaniem cyklem życia aplikacji szczegółowych informacji |
> | Akcja | Microsoft.Insights/Components/Write | Zapisywanie w konfiguracji składnika usługi application insights |
> | Akcja | Microsoft.Insights/DiagnosticSettings/Delete | Usuwanie ustawień diagnostycznych zasobu |
> | Akcja | Microsoft.Insights/DiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft.Insights/DiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienia diagnostyczne zasobu |
> | Akcja | Microsoft.Insights/EventCategories/Read | Przeczytaj dostępne kategorie zdarzeń dziennika aktywności |
> | Akcja | Microsoft.Insights/eventtypes/digestevents/Read | Podsumowanie typ zdarzeń zarządzania odczytu |
> | Akcja | Microsoft.Insights/eventtypes/values/Read | Zdarzenia dziennika aktywności odczytu |
> | Akcja | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Usuń ustawienie diagnostyczne dzienników przepływu sieci |
> | Akcja | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Przeczytaj ustawienie diagnostyczne dzienników przepływu sieci |
> | Akcja | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne dzienników przepływu sieci |
> | Akcja | Microsoft.Insights/ListMigrationDate/Action | Odzyskaj datę migracji subskrypcji Get |
> | Akcja | Microsoft.Insights/ListMigrationDate/Read | Pobierz Odzyskaj datę migracji subskrypcji |
> | Akcja | Microsoft.Insights/LogDefinitions/Read | Przeczytaj definicje dzienników |
> | Akcja | Microsoft.Insights/LogProfiles/Delete | Usuwanie profilu dziennika dziennika aktywności |
> | Akcja | Microsoft.Insights/LogProfiles/Read | Odczytuj profil dziennika w dzienniku aktywności |
> | Akcja | Microsoft.Insights/LogProfiles/Write | Utwórz lub zaktualizuj profil dziennika dziennika aktywności |
> | Akcja | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Odczyt danych z tabeli ADAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/ADReplicationResult/Read | Odczyt danych z tabeli ADReplicationResult |
> | Akcja | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Odczyt danych z tabeli ADSecurityAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/Alert/Read | Odczytywanie danych z tabeli alertu |
> | Akcja | Microsoft.Insights/Logs/AlertHistory/Read | Odczyt danych z tabeli AlertHistory |
> | Akcja | Microsoft.Insights/Logs/ApplicationInsights/Read | Odczyt danych z tabeli dotycząca usługi Application Insights |
> | Akcja | Microsoft.Insights/Logs/AzureActivity/Read | Odczyt danych z tabeli AzureActivity |
> | Akcja | Microsoft.Insights/Logs/AzureMetrics/Read | Odczyt danych z tabeli AzureMetrics |
> | Akcja | Microsoft.Insights/Logs/BoundPort/Read | Odczyt danych z tabeli BoundPort |
> | Akcja | Microsoft.Insights/Logs/CommonSecurityLog/Read | Odczyt danych z tabeli CommonSecurityLog |
> | Akcja | Microsoft.Insights/Logs/ComputerGroup/Read | Odczyt danych z tabeli Grupa_komputerów |
> | Akcja | Microsoft.Insights/Logs/ConfigurationChange/Read | Odczyt danych z tabeli Zmianakonfiguracji |
> | Akcja | Microsoft.Insights/Logs/ConfigurationData/Read | Odczyt danych z tabeli danych konfiguracji |
> | Akcja | Microsoft.Insights/Logs/ContainerImageInventory/Read | Odczyt danych z tabeli ContainerImageInventory |
> | Akcja | Microsoft.Insights/Logs/ContainerInventory/Read | Odczyt danych z tabeli ContainerInventory |
> | Akcja | Microsoft.Insights/Logs/ContainerLog/Read | Odczyt danych z tabeli ContainerLog |
> | Akcja | Microsoft.Insights/Logs/ContainerServiceLog/Read | Odczyt danych z tabeli ContainerServiceLog |
> | Akcja | Microsoft.Insights/Logs/DeviceAppCrash/Read | Odczyt danych z tabeli DeviceAppCrash |
> | Akcja | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Odczyt danych z tabeli DeviceAppLaunch |
> | Akcja | Microsoft.Insights/Logs/DeviceCalendar/Read | Odczyt danych z tabeli DeviceCalendar |
> | Akcja | Microsoft.Insights/Logs/DeviceCleanup/Read | Odczyt danych z tabeli DeviceCleanup |
> | Akcja | Microsoft.Insights/Logs/DeviceConnectSession/Read | Odczyt danych z tabeli DeviceConnectSession |
> | Akcja | Microsoft.Insights/Logs/DeviceEtw/Read | Odczyt danych z tabeli DeviceEtw |
> | Akcja | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Odczyt danych z tabeli DeviceHardwareHealth |
> | Akcja | Microsoft.Insights/Logs/DeviceHealth/Read | Odczyt danych z tabeli kondycji urządzenia Zameldowaniem |
> | Akcja | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Odczyt danych z tabeli DeviceHeartbeat |
> | Akcja | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Odczyt danych z tabeli DeviceSkypeHeartbeat |
> | Akcja | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Odczyt danych z tabeli DeviceSkypeSignIn |
> | Akcja | Microsoft.Insights/Logs/DeviceSleepState/Read | Odczyt danych z tabeli DeviceSleepState |
> | Akcja | Microsoft.Insights/Logs/DHAppFailure/Read | Odczyt danych z tabeli DHAppFailure |
> | Akcja | Microsoft.Insights/Logs/DHAppReliability/Read | Odczyt danych z tabeli DHAppReliability |
> | Akcja | Microsoft.Insights/Logs/DHDriverReliability/Read | Odczyt danych z tabeli DHDriverReliability |
> | Akcja | Microsoft.Insights/Logs/DHLogonFailures/Read | Odczyt danych z tabeli DHLogonFailures |
> | Akcja | Microsoft.Insights/Logs/DHLogonMetrics/Read | Odczyt danych z tabeli DHLogonMetrics |
> | Akcja | Microsoft.Insights/Logs/DHOSCrashData/Read | Odczyt danych z tabeli DHOSCrashData |
> | Akcja | Microsoft.Insights/Logs/DHOSReliability/Read | Odczyt danych z tabeli DHOSReliability |
> | Akcja | Microsoft.Insights/Logs/DHWipAppLearning/Read | Odczyt danych z tabeli DHWipAppLearning |
> | Akcja | Microsoft.Insights/Logs/DnsEvents/Read | Odczyt danych z tabeli DnsEvents |
> | Akcja | Microsoft.Insights/Logs/DnsInventory/Read | Odczyt danych z tabeli DnsInventory |
> | Akcja | Microsoft.Insights/Logs/ETWEvent/Read | Odczyt danych z tabeli ETWEvent |
> | Akcja | Microsoft.Insights/Logs/Event/Read | Odczyt danych z tabeli zdarzeń |
> | Akcja | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Odczyt danych z tabeli ExchangeAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Odczyt danych z tabeli ExchangeOnlineAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/Heartbeat/Read | Odczyt danych z tabeli pulsu |
> | Akcja | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Odczyt danych z tabeli IISAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/InboundConnection/Read | Odczyt danych z tabeli InboundConnection |
> | Akcja | Microsoft.Insights/Logs/KubeNodeInventory/Read | Odczyt danych z tabeli KubeNodeInventory |
> | Akcja | Microsoft.Insights/Logs/KubePodInventory/Read | Odczyt danych z tabeli KubePodInventory |
> | Akcja | Microsoft.Insights/Logs/LinuxAuditLog/Read | Odczyt danych z tabeli LinuxAuditLog |
> | Akcja | Microsoft.Insights/Logs/MAApplication/Read | Odczyt danych z tabeli MAApplication |
> | Akcja | Microsoft.Insights/Logs/MAApplicationHealth/Read | Odczyt danych z tabeli MAApplicationHealth |
> | Akcja | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Odczyt danych z tabeli MAApplicationHealthAlternativeVersions |
> | Akcja | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Odczyt danych z tabeli MAApplicationHealthIssues |
> | Akcja | Microsoft.Insights/Logs/MAApplicationInstance/Read | Odczyt danych z tabeli MAApplicationInstance |
> | Akcja | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Odczyt danych z tabeli MAApplicationInstanceReadiness |
> | Akcja | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Odczyt danych z tabeli MAApplicationReadiness |
> | Akcja | Microsoft.Insights/Logs/MADeploymentPlan/Read | Odczyt danych z tabeli MADeploymentPlan |
> | Akcja | Microsoft.Insights/Logs/MADevice/Read | Odczyt danych z tabeli MADevice |
> | Akcja | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Odczyt danych z tabeli MADevicePnPHealth |
> | Akcja | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Odczyt danych z tabeli MADevicePnPHealthAlternativeVersions |
> | Akcja | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Odczyt danych z tabeli MADevicePnPHealthIssues |
> | Akcja | Microsoft.Insights/Logs/MADeviceReadiness/Read | Odczyt danych z tabeli MADeviceReadiness |
> | Akcja | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Odczyt danych z tabeli MADriverInstanceReadiness |
> | Akcja | Microsoft.Insights/Logs/MADriverReadiness/Read | Odczyt danych z tabeli MADriverReadiness |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAddin/Read | Odczyt danych z tabeli MAOfficeAddin |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Odczyt danych z tabeli MAOfficeAddinHealth |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Odczyt danych z tabeli MAOfficeAddinHealthIssues |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Odczyt danych z tabeli MAOfficeAddinInstance |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Odczyt danych z tabeli MAOfficeAddinInstanceReadiness |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Odczyt danych z tabeli MAOfficeAddinReadiness |
> | Akcja | Microsoft.Insights/Logs/MAOfficeApp/Read | Odczyt danych z tabeli MAOfficeApp |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Odczyt danych z tabeli MAOfficeAppHealth |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Odczyt danych z tabeli MAOfficeAppInstance |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Odczyt danych z tabeli MAOfficeAppReadiness |
> | Akcja | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Odczyt danych z tabeli MAOfficeBuildInfo |
> | Akcja | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Odczyt danych z tabeli MAOfficeCurrencyAssessment |
> | Akcja | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Odczyt danych z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Odczyt danych z tabeli MAOfficeDeploymentStatus |
> | Akcja | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Odczyt danych z tabeli MAOfficeMacroHealth |
> | Akcja | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Odczyt danych z tabeli MAOfficeMacroHealthIssues |
> | Akcja | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Odczyt danych z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Akcja | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Odczyt danych z tabeli MAOfficeMacroIssueReadiness |
> | Akcja | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Odczyt danych z tabeli MAOfficeMacroSummary |
> | Akcja | Microsoft.Insights/Logs/MAOfficeSuite/Read | Odczyt danych z tabeli MAOfficeSuite |
> | Akcja | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Odczyt danych z tabeli MAOfficeSuiteInstance |
> | Akcja | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Odczyt danych z tabeli MAProposedPilotDevices |
> | Akcja | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Odczyt danych z tabeli MAWindowsBuildInfo |
> | Akcja | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Odczyt danych z tabeli MAWindowsCurrencyAssessment |
> | Akcja | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Odczyt danych z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Odczyt danych z tabeli MAWindowsDeploymentStatus |
> | Akcja | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Odczyt danych z tabeli MAWindowsSysReqInstanceReadiness |
> | Akcja | Microsoft.Insights/Logs/NetworkMonitoring/Read | Odczyt danych z tabeli NetworkMonitoring |
> | Akcja | Microsoft.Insights/Logs/OfficeActivity/Read | Odczyt danych z tabeli OfficeActivity |
> | Akcja | Microsoft.Insights/Logs/Operation/Read | Odczyt danych z tabeli operacji |
> | Akcja | Microsoft.Insights/Logs/OutboundConnection/Read | Odczyt danych z tabeli OutboundConnection |
> | Akcja | Microsoft.Insights/Logs/Perf/Read | Odczyt danych z tabeli danych o wydajności |
> | Akcja | Microsoft.Insights/Logs/ProtectionStatus/Read | Odczyt danych z tabeli ProtectionStatus |
> | Akcja | Microsoft.Insights/Logs/Read | Odczytywanie danych z wszystkich dzienników |
> | Akcja | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Odczyt danych z tabeli ReservedAzureCommonFields |
> | Akcja | Microsoft.Insights/Logs/ReservedCommonFields/Read | Odczyt danych z tabeli ReservedCommonFields |
> | Akcja | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Odczyt danych z tabeli SCCMAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Odczyt danych z tabeli SCOMAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/SecurityAlert/Read | Odczyt danych z tabeli SecurityAlert |
> | Akcja | Microsoft.Insights/Logs/SecurityBaseline/Read | Odczyt danych z tabeli SecurityBaseline |
> | Akcja | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Odczyt danych z tabeli SecurityBaselineSummary |
> | Akcja | Microsoft.Insights/Logs/SecurityDetection/Read | Odczyt danych z tabeli SecurityDetection |
> | Akcja | Microsoft.Insights/Logs/SecurityEvent/Read | Odczyt danych z tabeli SecurityEvent |
> | Akcja | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Odczyt danych z tabeli ServiceFabricOperationalEvent |
> | Akcja | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Odczyt danych z tabeli ServiceFabricReliableActorEvent |
> | Akcja | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Odczyt danych z tabeli ServiceFabricReliableServiceEvent |
> | Akcja | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Odczyt danych z tabeli SfBAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Odczyt danych z tabeli SfBOnlineAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Odczyt danych z tabeli SharePointOnlineAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Odczyt danych z tabeli SPAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Odczyt danych z tabeli SQLAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Odczyt danych z tabeli SQLQueryPerformance |
> | Akcja | Microsoft.Insights/Logs/Syslog/Read | Odczyt danych z tabeli dziennika systemu |
> | Akcja | Microsoft.Insights/Logs/SysmonEvent/Read | Odczyt danych z tabeli SysmonEvent |
> | Akcja | Microsoft.Insights/Logs/UAApp/Read | Odczyt danych z tabeli UAApp |
> | Akcja | Microsoft.Insights/Logs/UAComputer/Read | Odczyt danych z tabeli UAComputer |
> | Akcja | Microsoft.Insights/Logs/UAComputerRank/Read | Odczyt danych z tabeli UAComputerRank |
> | Akcja | Microsoft.Insights/Logs/UADriver/Read | Odczyt danych z tabeli UADriver |
> | Akcja | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Odczyt danych z tabeli UADriverProblemCodes |
> | Akcja | Microsoft.Insights/Logs/UAFeedback/Read | Odczyt danych z tabeli UAFeedback |
> | Akcja | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Odczyt danych z tabeli UAHardwareSecurity |
> | Akcja | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Odczyt danych z tabeli UAIESiteDiscovery |
> | Akcja | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Odczyt danych z tabeli UAOfficeAddIn |
> | Akcja | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Odczyt danych z tabeli UAProposedActionPlan |
> | Akcja | Microsoft.Insights/Logs/UASysReqIssue/Read | Odczyt danych z tabeli UASysReqIssue |
> | Akcja | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Odczyt danych z tabeli UAUpgradedComputer |
> | Akcja | Microsoft.Insights/Logs/Update/Read | Odczytywanie danych z tabeli aktualizacji |
> | Akcja | Microsoft.Insights/Logs/UpdateRunProgress/Read | Odczyt danych z tabeli UpdateRunProgress |
> | Akcja | Microsoft.Insights/Logs/UpdateSummary/Read | Odczyt danych z tabeli UpdateSummary |
> | Akcja | Microsoft.Insights/Logs/Usage/Read | Odczytywanie danych z tabeli użycia |
> | Akcja | Microsoft.Insights/Logs/W3CIISLog/Read | Odczyt danych z tabeli W3CIISLog |
> | Akcja | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Odczyt danych z tabeli WaaSDeploymentStatus |
> | Akcja | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Odczyt danych z tabeli WaaSInsiderStatus |
> | Akcja | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Odczyt danych z tabeli WaaSUpdateStatus |
> | Akcja | Microsoft.Insights/Logs/WDAVStatus/Read | Odczyt danych z tabeli WDAVStatus |
> | Akcja | Microsoft.Insights/Logs/WDAVThreat/Read | Odczyt danych z tabeli WDAVThreat |
> | Akcja | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Odczyt danych z tabeli WindowsClientAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/WindowsFirewall/Read | Odczyt danych z tabeli WindowsFirewall |
> | Akcja | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Odczyt danych z tabeli WindowsServerAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/WireData/Read | Odczyt danych z tabeli danych o komunikacji sieciowej |
> | Akcja | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Odczyt danych z tabeli WUDOAggregatedStatus |
> | Akcja | Microsoft.Insights/Logs/WUDOStatus/Read | Odczyt danych z tabeli WUDOStatus |
> | Akcja | Microsoft.Insights/MetricAlerts/Delete | Usuwanie alertu metryki |
> | Akcja | Microsoft.Insights/MetricAlerts/Read | Odczyt alertu metryki |
> | Akcja | Microsoft.Insights/MetricAlerts/Status/Read | Odczyt stanu alertu metryki |
> | Akcja | Microsoft.Insights/MetricAlerts/Write | Utwórz lub zaktualizuj alertu metryki |
> | Akcja | Microsoft.Insights/MetricBaselines/Read | Odczytaj metryki linii bazowych |
> | Akcja | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/MetricDefinitions/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/Metricnamespaces/Read | Odczytaj metryki przestrzeni nazw |
> | Akcja | Microsoft.Insights/Metrics/Action | Metryki akcji |
> | Akcja | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Odczyt metryk |
> | Akcja | Microsoft.Insights/Metrics/providers/Metrics/Read | Odczyt metryk |
> | Akcja | Microsoft.Insights/Metrics/Read | Odczyt metryk |
> | DataAction | Microsoft.Insights/Metrics/Write | Metryki zapisu |
> | Akcja | Microsoft.Insights/MigrateToNewpricingModel/Action | Migruj subskrypcję do nowego modelu cen |
> | Akcja | Microsoft.Insights/Operations/Read | Operacje odczytu |
> | Akcja | Microsoft.Insights/Register/Action | Zarejestruj dostawcę Microsoft Insights |
> | Akcja | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Wycofaj subskrypcję do starszego modelu cen |
> | Akcja | Microsoft.Insights/ScheduledQueryRules/Delete | Usuwanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft.Insights/ScheduledQueryRules/Read | Odczytywanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft.Insights/ScheduledQueryRules/Write | Zapisywanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft.Insights/Tenants/Register/Action | Inicjuje dostawcę Microsoft Insights |
> | Akcja | Microsoft.Insights/Unregister/Action | Zarejestruj dostawcę Microsoft Insights |
> | Akcja | Microsoft.Insights/Webtests/Delete | Usuwanie konfiguracji testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/GetToken/Read | Odczytywanie tokenu testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/MetricDefinitions/Read | Odczytywanie definicji metryk testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/Metrics/Read | Odczytywanie metryk testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/Read | Odczytywanie konfiguracji testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/Write | Zapisywanie w konfiguracji testu internetowego |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Intune/diagnosticsettings/delete | Usuwanie ustawienia diagnostyczne |
> | Akcja | Microsoft.Intune/diagnosticsettings/read | Odczytywanie ustawienie diagnostyczne |
> | Akcja | Microsoft.Intune/diagnosticsettings/write | Zapisywanie ustawienia diagnostyczne |
> | Akcja | Microsoft.Intune/diagnosticsettingscategories/read | Odczytywanie kategorii ustawienie diagnostyczne |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.IoTCentral/appTemplates/action | Pobiera wszystkie szablony aplikacji dostępnych w usłudze Azure IoT Central |
> | Akcja | Microsoft.IoTCentral/checkNameAvailability/action | Sprawdza, czy nazwa IoT Central aplikacji jest dostępna |
> | Akcja | Microsoft.IoTCentral/checkSubdomainAvailability/action | Sprawdza, czy poddomeny IoT Central aplikacji jest dostępna |
> | Akcja | Microsoft.IoTCentral/IoTApps/delete | Usuwa aplikacje IoT Central |
> | Akcja | Microsoft.IoTCentral/IoTApps/read | Pobiera pojedynczą aplikacją centralną IoT |
> | Akcja | Microsoft.IoTCentral/IoTApps/write | Tworzy lub aktualizuje aplikacje IoT Central |
> | Akcja | Microsoft.IoTCentral/operations/read | Pobiera dostępne operacje dotyczące IoT Central aplikacji |
> | Akcja | Microsoft.IoTCentral/register/action | Zarejestruj subskrypcję dostawcy zasobów usługi Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.IoTSpaces/Graph/delete | Usuwa zasób grafu Microsoft.IoTSpaces |
> | Akcja | Microsoft.IoTSpaces/Graph/read | Pobiera zasoby Microsoft.IoTSpaces wykresu |
> | Akcja | Microsoft.IoTSpaces/Graph/write | Utwórz zasób Microsoft.IoTSpaces wykresu |
> | Akcja | Microsoft.IoTSpaces/register/action | Rejestruj subskrypcję dostawcy zasobów programu Microsoft.IoTSpaces Graph umożliwiające tworzenie zasobów |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.KeyVault/checkNameAvailability/read | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i nie jest w użyciu |
> | Akcja | Microsoft.KeyVault/deletedVaults/read | Wyświetl właściwości usuniętych nietrwale magazynów kluczy |
> | Akcja | Microsoft.KeyVault/hsmPools/delete | Usuń pulę modułów HSM |
> | Akcja | Microsoft.KeyVault/hsmPools/joinVault/action | Dołącz magazyn kluczy do puli modułów HSM |
> | Akcja | Microsoft.KeyVault/hsmPools/read | Wyświetl właściwości puli modułów HSM |
> | Akcja | Microsoft.KeyVault/hsmPools/write | Utwórz nową pulę modułów HSM aktualizacji właściwości istniejącej puli modułów HSM |
> | Akcja | Microsoft.KeyVault/locations/deletedVaults/purge/action | Przeczyść usunięty nietrwale magazyn kluczy |
> | Akcja | Microsoft.KeyVault/locations/deletedVaults/read | Wyświetlanie właściwości usunięty nietrwale magazyn kluczy |
> | Akcja | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia element Microsoft.KeyVault, że trwa usuwanie sieci wirtualnej lub podsieci |
> | Akcja | Microsoft.KeyVault/locations/operationResults/read | Sprawdź wynik operacji długiego uruchamiania |
> | Akcja | Microsoft.KeyVault/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.KeyVault |
> | Akcja | Microsoft.KeyVault/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.KeyVault/unregister/action | Wyrejestrowuje subskrypcję |
> | Akcja | Microsoft.KeyVault/vaults/accessPolicies/write | Aktualizowanie istniejących zasad dostępu przez scalanie lub zastępowanie lub Dodaj nowe zasady dostępu do magazynu. |
> | Akcja | Microsoft.KeyVault/vaults/delete | Usuń magazyn kluczy |
> | Akcja | Microsoft.KeyVault/vaults/deploy/action | Umożliwia dostęp do wpisów tajnych w magazynie kluczy podczas wdrażania zasobów platformy Azure |
> | Akcja | Microsoft.KeyVault/vaults/eventGridFilters/delete | Powiadamia element Microsoft.KeyVault o usuwaniu subskrypcji EventGrid usługi Key Vault |
> | Akcja | Microsoft.KeyVault/vaults/eventGridFilters/read | Powiadamia element Microsoft.KeyVault jest przeglądany subskrypcji EventGrid usługi Key Vault |
> | Akcja | Microsoft.KeyVault/vaults/eventGridFilters/write | Powiadamia element Microsoft.KeyVault, że trwa tworzenie nowej subskrypcji EventGrid dla usługi Key Vault |
> | Akcja | Microsoft.KeyVault/vaults/read | Wyświetlanie właściwości magazynu kluczy |
> | Akcja | Microsoft.KeyVault/vaults/secrets/read | Wyświetl właściwości wpisu tajnego, ale nie jego wartość. |
> | Akcja | Microsoft.KeyVault/vaults/secrets/write | Utwórz nowy wpis tajny lub zaktualizuj wartość istniejącego wpisu tajnego. |
> | Akcja | Microsoft.KeyVault/vaults/write | Utwórz nowy magazyn kluczy lub zaktualizuj właściwości istniejącego magazynu kluczy |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Kusto/Clusters/Activate/action | Uruchomienie klastra. |
> | Akcja | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Usuwa zasób dołączonej bazie danych konfiguracji. |
> | Akcja | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Odczytuje zasób dołączonej bazie danych konfiguracji. |
> | Akcja | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Zapisuje w dołączonej bazie danych konfiguracji zasobu. |
> | Akcja | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Służy do sprawdzania dostępności nazwy klastra. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Dodaje podmiotów zabezpieczeń bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Służy do sprawdzania dostępności nazwy dla danego typu. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Usuwa zasób połączenia danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Odczytuje zasób połączenia danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Zapisuje zasób połączenia usługi danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Sprawdza poprawność danych połączenia z bazą danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/delete | Usuwa zasób bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Usuwa zasób połączenia Centrum zdarzeń. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Odczytuje zasób połączenia Centrum zdarzeń. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Zapisuje zasób połączenia Centrum zdarzeń. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Sprawdzanie poprawności połączenia z bazą danych Centrum zdarzeń. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Wyświetla bazy danych jednostki. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/read | Odczytuje zasobów bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Usuwa bazy danych jednostki. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/write | Zapisuje zasobów bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Deactivate/action | Zatrzymuje klastra. |
> | Akcja | Microsoft.Kusto/Clusters/delete | Usuwa zasób klastra. |
> | Akcja | Microsoft.Kusto/Clusters/read | Odczytuje zasób klastra. |
> | Akcja | Microsoft.Kusto/Clusters/SKUs/read | Odczytuje jednostki SKU zasobu klastra. |
> | Akcja | Microsoft.Kusto/Clusters/Start/action | Uruchomienie klastra. |
> | Akcja | Microsoft.Kusto/Clusters/Stop/action | Zatrzymuje klastra. |
> | Akcja | Microsoft.Kusto/Clusters/write | Zapisuje zasobu klastra. |
> | Akcja | Microsoft.Kusto/Locations/CheckNameAvailability/action | Sprawdza dostępność nazwy zasobu. |
> | Akcja | Microsoft.Kusto/locations/operationresults/read | Odczyty operacji zasobów |
> | Akcja | Microsoft.Kusto/Operations/read | Odczyty operacji zasobów |
> | Akcja | Microsoft.Kusto/Register/action | Rejestruje subskrypcję dostawcy zasobów Kusto. |
> | Akcja | Microsoft.Kusto/SKUs/read | Odczytuje zasób jednostki SKU. |
> | Akcja | Microsoft.Kusto/Unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.LabServices/labAccounts/CreateLab/action | Tworzenie laboratorium na koncie laboratorium. |
> | Akcja | Microsoft.LabServices/labAccounts/delete | Usuwanie kont w laboratorium. |
> | Akcja | Microsoft.LabServices/labAccounts/galleryImages/delete | Usuwanie obrazów w galerii. |
> | Akcja | Microsoft.LabServices/labAccounts/galleryImages/read | Przeczytaj galerii obrazów. |
> | Akcja | Microsoft.LabServices/labAccounts/galleryImages/write | Dodawanie lub modyfikowanie galerii obrazów. |
> | Akcja | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Uzyskaj informacje o dostępności regionalnej dla każdej kategorii rozmiar skonfigurowany w ramach konta laboratorium |
> | Akcja | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Dodawanie użytkowników do laboratorium |
> | Akcja | Microsoft.LabServices/labAccounts/labs/delete | Usuń labs. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Oświadczenia losowe środowiska użytkownika w ustawieniach środowiska |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Usuń ustawienia środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Oświadczenia środowiska, a następnie przypisuje go do użytkownika |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Usuwanie środowisk. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Odczyt środowisk. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Resetuje hasło użytkownika w środowisku |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Uruchamia środowisko poprzez uruchomienie wszystkich zasobów w środowisku. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Dodaje lub modyfikuje środowisk. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Przepisy/deprovisions wymagane zasoby do ustawienia środowiska na podstawie bieżącego stanu ustawienia/w środowisku laboratoryjnym. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Odczytaj ustawienia środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Resetuje hasło na maszynie wirtualnej w szablonie. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Zapisuje bieżący obraz szablonu do galerii udostępnionych w ramach konta laboratorium |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Przeczytaj harmonogramów. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Uruchamia szablonu przez uruchomienie wszystkich zasobów w obrębie szablonu. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Zatrzymuje szablonu przez zatrzymanie wszystkich zasobów w obrębie szablonu. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Dodaje lub modyfikuje ustawienia środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/read | Przeczytaj labs. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/Register/action | Zarejestruj się w laboratorium zarządzanych. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Wyślij wiadomość e-mail z linkiem rejestracji do laboratorium |
> | Akcja | Microsoft.LabServices/labAccounts/labs/users/delete | Usuwanie użytkowników. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/users/read | Przeczytaj użytkowników. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/users/write | Dodawanie lub modyfikowanie użytkowników. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/write | Dodawanie lub modyfikowanie laboratoriów. |
> | Akcja | Microsoft.LabServices/labAccounts/read | Przeczytaj kontami laboratorium. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Usuń sharedgalleries. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedGalleries/read | Przeczytaj sharedgalleries. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedGalleries/write | Dodaje lub modyfikuje sharedgalleries. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedImages/delete | Usuń sharedimages. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedImages/read | Przeczytaj sharedimages. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedImages/write | Dodaje lub modyfikuje sharedimages. |
> | Akcja | Microsoft.LabServices/labAccounts/write | Dodaje lub modyfikuje kontami laboratorium. |
> | Akcja | Microsoft.LabServices/locations/operations/read | Operacje odczytu. |
> | Akcja | Microsoft.LabServices/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.LabServices/users/GetEnvironment/action | Pobiera szczegóły maszyny wirtualnej |
> | Akcja | Microsoft.LabServices/users/GetOperationBatchStatus/action | Pobierz stan operacji dla usługi batch |
> | Akcja | Microsoft.LabServices/users/GetOperationStatus/action | Pobiera stan operacji długotrwałej |
> | Akcja | Microsoft.LabServices/users/GetPersonalPreferences/action | Pobiera preferencje osobiste użytkownika |
> | Akcja | Microsoft.LabServices/users/ListAllEnvironments/action | Wyświetlić listę wszystkich środowisk dla użytkownika |
> | Akcja | Microsoft.LabServices/users/ListEnvironments/action | Wyświetlić listy środowisk dla użytkownika |
> | Akcja | Microsoft.LabServices/users/ListLabs/action | Wyświetl listę labs dla użytkownika. |
> | Akcja | Microsoft.LabServices/users/Register/action | Rejestrowanie użytkownika do zarządzanego laboratorium |
> | Akcja | Microsoft.LabServices/users/ResetPassword/action | Resetuje hasło użytkownika w środowisku |
> | Akcja | Microsoft.LabServices/users/StartEnvironment/action | Uruchamia środowisko poprzez uruchomienie wszystkich zasobów w środowisku. |
> | Akcja | Microsoft.LabServices/users/StopEnvironment/action | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Logic/integrationAccounts/agreements/delete | Usuwa umowę na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości umowy na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/agreements/read | Odczytuje umowę na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/agreements/write | Tworzy lub aktualizuje umowę na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/assemblies/delete | Usuwa zestaw na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości zestawu na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/assemblies/read | Odczytuje zestaw na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/assemblies/write | Tworzy lub aktualizuje zestaw na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Usuwa konfigurację partii na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Odczytuje konfigurację partii na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Tworzy lub aktualizuje konfigurację partii na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/certificates/delete | Usuwa certyfikat na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/certificates/read | Odczytuje certyfikat na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/certificates/write | Tworzy lub aktualizuje certyfikat na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/delete | Usuwa konto integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/join/action | Dołączanie konta integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla konta integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Pobiera klucze w magazynie kluczy. |
> | Akcja | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Rejestruje zdarzenia śledzenia na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/maps/delete | Usuwa mapowanie na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości mapowania na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/maps/read | Odczytuje mapowanie na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/maps/write | Tworzy lub aktualizuje mapowanie na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/partners/delete | Usuwa partnera na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości partnera na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/partners/read | Odczytuje partnera na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/partners/write | Tworzy lub aktualizuje partnera na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Odczytuje definicje dziennika konta integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/read | Odczytuje konto integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft.Logic/integrationAccounts/schemas/delete | Usuwa schemat na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości schematu na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/schemas/read | Odczytuje schemat na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/schemas/write | Tworzy lub aktualizuje schemat na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/sessions/delete | Usuwa sesję na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/sessions/read | Odczytuje konfigurację partii na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/sessions/write | Tworzy lub aktualizuje sesję na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/write | Tworzy lub aktualizuje konto integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/delete | Usuwa środowisko usługi integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/join/action | Dołącza do środowiska usługi integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Odczytuje środowisko usługi integracji zarządzane operacji interfejsu API. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Odczytuje środowisko usługi integracji zarządzanego interfejsu API. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Odczytuje integracji stany operacji środowiska usługi. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje integracji definicje metryk środowiska usługi. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/read | Odczytuje środowisko usługi integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/write | Tworzy lub aktualizuje środowisko usługi integracji. |
> | Akcja | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Pobiera przepływu pracy, zalecamy grupy operacji. |
> | Akcja | Microsoft.Logic/locations/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | Akcja | Microsoft.Logic/operations/read | Pobiera operację. |
> | Akcja | Microsoft.Logic/register/action | Rejestruje dostawcę zasobów Microsoft.Logic dla danej subskrypcji. |
> | Akcja | Microsoft.Logic/workflows/accessKeys/delete | Usuwa klucz dostępu. |
> | Akcja | Microsoft.Logic/workflows/accessKeys/list/action | Wyświetla wpisy tajne klucza dostępu. |
> | Akcja | Microsoft.Logic/workflows/accessKeys/read | Odczytuje klucz dostępu. |
> | Akcja | Microsoft.Logic/workflows/accessKeys/regenerate/action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft.Logic/workflows/accessKeys/write | Tworzy lub aktualizuje klucz dostępu. |
> | Akcja | Microsoft.Logic/workflows/delete | Usuwa przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/disable/action | Wyłącza przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/enable/action | Włącza przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/listSwagger/action | Pobiera przepływu pracy w definicji struktury swagger. |
> | Akcja | Microsoft.Logic/workflows/move/action | Przenosi przepływ pracy z istniejącego identyfikatora subskrypcji, grupy zasobów lub nazwy do innego identyfikatora subskrypcji, grupy zasobów i/lub nazwę. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Odczytuje przepływ pracy konfiguracji ustawień diagnostyki. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyki przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Odczytuje definicje dzienników przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje definicje metryk w przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/read | Odczytuje przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/regenerateAccessKey/action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft.Logic/workflows/run/action | Uruchamia przebieg przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Pobiera ślady wyrażenia akcji uruchamiania przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/read | Odczytuje akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Pobiera ślady wyrażenia powtórzenia akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/repetitions/read | Odczytuje powtórzenie akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Odczytuje powtórzenie akcji żądania historii przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Odczytuje historię żądań akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Odczytuje powtórzenie zakresu akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/cancel/action | Anuluje przebieg przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/delete | Usuwa przebieg przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/operations/read | Odczytuje stan operacji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/read | Odczytuje przebieg przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/suspend/action | Wstrzymuje przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/triggers/histories/read | Odczytuje historie wyzwalacza. |
> | Akcja | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Przesyła ponownie wyzwalacz przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Akcja | Microsoft.Logic/workflows/triggers/read | Odczytuje wyzwalacz. |
> | Akcja | Microsoft.Logic/workflows/triggers/reset/action | Resetuje wyzwalacz. |
> | Akcja | Microsoft.Logic/workflows/triggers/run/action | Uruchamia wyzwalacz. |
> | Akcja | Microsoft.Logic/workflows/triggers/setState/action | Ustawia stan wyzwalacza. |
> | Akcja | Microsoft.Logic/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/versions/read | Odczytuje wersję przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Akcja | Microsoft.Logic/workflows/write | Tworzy lub aktualizuje przepływ pracy. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Przenoszenie maszyn uczenia skojarzenia Plan zobowiązania |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Przeczytaj maszyn uczenia skojarzenia Plan zobowiązania |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/delete | Usuń maszynę, wszelkie zobowiązania Plan nauki |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/join/action | Dołącz do dowolnej maszyny zobowiązania Plan nauki |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/read | Przeczytaj maszyn zobowiązania Plan nauki |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/write | Utwórz lub zaktualizuj wszelkie planu zobowiązania nauki maszyny |
> | Akcja | Microsoft.MachineLearning/locations/operationresults/read | Pobierz wynik operacji Machine Learning |
> | Akcja | Microsoft.MachineLearning/locations/operationsstatus/read | Pobierz stan trwająca operacja Machine Learning |
> | Akcja | Microsoft.MachineLearning/operations/read | Pobierz operacje uczenia maszynowego |
> | Akcja | Microsoft.MachineLearning/register/action | Rejestruje subskrypcję dostawcy zasobów usługi sieci web usługi uczenia maszynowego i umożliwia tworzenie usług sieci web. |
> | Akcja | Microsoft.MachineLearning/skus/read | Pobierz jednostki SKU planu zobowiązania uczenia maszynowego |
> | Akcja | Microsoft.MachineLearning/webServices/action | Utwórz regionalnych właściwości obsługiwane regiony usługi sieci Web |
> | Akcja | Microsoft.MachineLearning/webServices/delete | Usuń wszystkie usługi internetowej Machine Learning |
> | Akcja | Microsoft.MachineLearning/webServices/listkeys/read | Pobierz klucze do usługi internetowej Machine Learning |
> | Akcja | Microsoft.MachineLearning/webServices/read | Przeczytaj dowolnej usługi internetowej Machine Learning |
> | Akcja | Microsoft.MachineLearning/webServices/write | Utwórz lub zaktualizuj wszelkie usługi internetowej Machine Learning |
> | Akcja | Microsoft.MachineLearning/Workspaces/delete | Usuń dowolną maszynę uczenia obszaru roboczego |
> | Akcja | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Klucze listy dla obszaru roboczego uczenia maszynowego |
> | Akcja | Microsoft.MachineLearning/Workspaces/read | Przeczytaj maszyn uczenia obszaru roboczego |
> | Akcja | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Ponowna synchronizacja kluczy skonfigurowano kont magazynu dla obszaru roboczego uczenia maszynowego |
> | Akcja | Microsoft.MachineLearning/Workspaces/write | Utwórz lub zaktualizuj dowolną maszynę uczenia obszaru roboczego |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Sprawdź, czy aktualizacje są dostępne dla systemu usług dla klastra operacjonalizacji |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Usuń wszelkie konta hostingu |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Utwórz listę kluczy skojarzone z klastra operacjonalizacji |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/read | Odczytaj wszelkie hostingu konto |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Aktualizowanie usługi systemowe w klastrze operacjonalizacji |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/write | Utwórz lub zaktualizuj dowolne konto hostingu |
> | Akcja | Microsoft.MachineLearningCompute/register/action | Rejestruje identyfikator subskrypcji do dostawcy zasobów i włącza funkcję tworzenia zasobów obliczeniowych uczenia maszynowego |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Pobiera stan operacji obliczeniowych określoną |
> | Akcja | Microsoft.MachineLearningServices/locations/usages/read | Raport użycia dla aml obliczenia zasobów w ramach subskrypcji |
> | Akcja | Microsoft.MachineLearningServices/locations/vmsizes/read | Pobierz rozmiarów maszyn wirtualnych obsługiwanych |
> | Akcja | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Pobiera stan operacji określonego obszaru roboczego |
> | Akcja | Microsoft.MachineLearningServices/register/action | Rejestruje subskrypcję dostawcy zasobów Machine Learning usług |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/delete | Usuwa zasoby obliczeniowe w obszarach roboczych programu Machine Learning Services |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Listy wpisów tajnych dla zasobów obliczeniowych w obszaru roboczego usługi Machine Learning Services |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Lista węzłów dla zasobów obliczeniowych w obszaru roboczego usługi Machine Learning Services |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/read | Pobiera zasoby obliczeniowe w obszarach roboczych programu Machine Learning Services |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/write | Tworzy lub aktualizuje zasobów obliczeniowych w obszarach roboczych programu Machine Learning Services |
> | Akcja | Microsoft.MachineLearningServices/workspaces/delete | Usuwa obszary robocze usługi uczenia maszynowego |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Tworzy lub aktualizuje eksperymentów w obszarach roboczych programu Machine Learning Services |
> | Akcja | Microsoft.MachineLearningServices/workspaces/listKeys/action | Utwórz listę kluczy tajnych dla obszaru roboczego usługi uczenia maszynowego |
> | Akcja | Microsoft.MachineLearningServices/workspaces/read | Pobiera obszarów roboczych usługi uczenia maszynowego |
> | Akcja | Microsoft.MachineLearningServices/workspaces/write | Tworzy lub aktualizuje obszarów roboczych serwera Machine Learning usług |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ManagedIdentity/register/action | Rejestruje subskrypcję dostawcy zasobów tożsamości zarządzanej |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Akcja RBAC do przypisywania istniejącego użytkownika tożsamości przypisanej do zasobu |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Usuwa istniejące tożsamości przypisanych przez użytkownika |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/read | Pobiera istniejące tożsamości przypisanych przez użytkownika |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/write | Tworzy nowy tożsamości przypisanych przez użytkownika lub aktualizuje do tagów skojarzonych z istniejących tożsamości przypisanych przez użytkownika |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Management/checkNameAvailability/action | Sprawdza, czy nazwa grupy zarządzania określony prawidłowy i unikatowy. |
> | Akcja | Microsoft.Management/getEntities/action | Wyświetl listę wszystkich jednostek (grup zarządzania, subskrypcji itp.) dla tego uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.Management/managementGroups/delete | Usuwanie grupy zarządzania. |
> | Akcja | Microsoft.Management/managementGroups/read | Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.Management/managementGroups/subscriptions/delete | Kojarzy anulować subskrypcję z grupy zarządzania. |
> | Akcja | Microsoft.Management/managementGroups/subscriptions/write | Kojarzy istniejącej subskrypcji z grupą zarządzania. |
> | Akcja | Microsoft.Management/managementGroups/write | Utwórz lub zaktualizuj grupę zarządzania. |
> | Akcja | Microsoft.Management/register/action | Zarejestrowanie określonej subskrypcji Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Daje dostęp do odczytu danych na konto usługi maps. |
> | Akcja | Microsoft.Maps/accounts/delete | Usuwanie konta usługi Maps. |
> | Akcja | Microsoft.Maps/accounts/eventGridFilters/delete | Usuń filtr usługi Event Grid |
> | Akcja | Microsoft.Maps/accounts/eventGridFilters/read | Pobierz filtr usługi Event Grid |
> | Akcja | Microsoft.Maps/accounts/eventGridFilters/write | Utwórz lub zaktualizuj filtr usługi Event Grid |
> | Akcja | Microsoft.Maps/accounts/listKeys/action | Wyświetl klucze konta usługi Maps |
> | Akcja | Microsoft.Maps/accounts/read | Uzyskaj konto usługi Maps. |
> | Akcja | Microsoft.Maps/accounts/regenerateKey/action | Generuj nowy klucz podstawowy lub pomocniczy konta usługi Maps |
> | Akcja | Microsoft.Maps/accounts/write | Utwórz lub zaktualizuj konto usługi Maps. |
> | Akcja | Microsoft.Maps/operations/read | Odczytaj operacje dostawcy |
> | Akcja | Microsoft.Maps/register/action | Zarejestruj dostawcę |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Zwraca umowę. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Akceptuje podpisanej umowy. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importuje obraz do usługi ACR użytkownika końcowego. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Zwraca wartość konfiguracji. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Zapisuje konfiguracji. |
> | Akcja | Microsoft.Marketplace/register/action | Rejestruje dostawcę zasobów Microsoft.Marketplace w ramach subskrypcji. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/delete | Wykonuje operację usuwania do zasobu usługi dev klasycznego. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Pobiera dev klasycznej usługi zasobu zarządzania kluczami. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Pobiera pojedynczy znak na adres URL usługi dev klasycznego. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/read | Wykonuje operację pobierania w usłudze classic deweloperów. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generuje klucze zarządzania zasobów klasycznych deweloperów usługi. |
> | Akcja | Microsoft.MarketplaceApps/Operations/read | Odczytaj operacje dla wszystkich typów zasobów. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Anuluj umowę do danego handlowa |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Zwróć umowę do danego handlowa |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Podpisać umowę o podanej handlowa |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/read | Zwróć wszystkie umowy w ramach danej subskrypcji |
> | Akcja | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Pobierz umowę do danej maszyny wirtualnej handlowa |
> | Akcja | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Zaloguj się lub Anuluj umowę do danej maszyny wirtualnej handlowa |
> | Akcja | Microsoft.MarketplaceOrdering/operations/read | Lista wszystkich możliwych operacji w interfejsie API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Media/checknameavailability/action | Sprawdza, czy nazwa konta usługi Media Services jest dostępna |
> | Akcja | Microsoft.Media/locations/checkNameAvailability/action | Sprawdza, czy nazwa konta usługi Media Services jest dostępna |
> | Akcja | Microsoft.Media/mediaservices/accountfilters/delete | Usuń filtr dowolnego konta |
> | Akcja | Microsoft.Media/mediaservices/accountfilters/read | Przeczytaj dowolny filtr konta |
> | Akcja | Microsoft.Media/mediaservices/accountfilters/write | Utwórz lub zaktualizuj dowolny filtr konta |
> | Akcja | Microsoft.Media/mediaservices/assets/assetfilters/delete | Usuń dowolny filtr zasobu |
> | Akcja | Microsoft.Media/mediaservices/assets/assetfilters/read | Przeczytaj dowolny filtr zasobu |
> | Akcja | Microsoft.Media/mediaservices/assets/assetfilters/write | Utwórz lub zaktualizuj dowolny filtr zasobu |
> | Akcja | Microsoft.Media/mediaservices/assets/delete | Usuń zasoby |
> | Akcja | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Pobierz klucz szyfrowania zawartości |
> | Akcja | Microsoft.Media/mediaservices/assets/listContainerSas/action | Zasób kontenerów sygnatury dostępu Współdzielonego adresy URL |
> | Akcja | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Lokalizatory przesyłania strumieniowego listy dla zasobu |
> | Akcja | Microsoft.Media/mediaservices/assets/read | Odczytaj wszelkie zasób |
> | Akcja | Microsoft.Media/mediaservices/assets/write | Utwórz lub zaktualizuj wszelkie zasób |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Usuń wszelkie zasady klucza zawartości |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Pobierz właściwości zasad przy użyciu kluczy tajnych |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/read | Przeczytaj żadnych zasad kluczy zawartości |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/write | Utwórz lub zaktualizuj wszelkie zasady klucza zawartości |
> | Akcja | Microsoft.Media/mediaservices/delete | Usuń wszystkie konta usługi Media Services |
> | Akcja | Microsoft.Media/mediaservices/eventGridFilters/delete | Usuń dowolny filtr siatki zdarzeń |
> | Akcja | Microsoft.Media/mediaservices/eventGridFilters/read | Przeczytaj dowolny filtr siatki zdarzeń |
> | Akcja | Microsoft.Media/mediaservices/eventGridFilters/write | Utwórz lub zaktualizuj dowolny filtr siatki zdarzeń |
> | Akcja | Microsoft.Media/mediaservices/liveEventOperations/read | Wszystkie zdarzenia na żywo operacja odczytu |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/delete | Usuń wszystkie wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Usuń wszelkie na żywo dane wyjściowe |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Przeczytaj żadnego wyniku na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Utwórz lub zaktualizuj wszelkie na żywo dane wyjściowe |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/read | Odczytywać wszystkie wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/reset/action | Resetuj każdej operacji, zdarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/start/action | Uruchom dowolną operację zdarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/stop/action | Zatrzymaj operację wszystkie wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/write | Utwórz lub zaktualizuj wszystkie wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveOutputOperations/read | Wszelkie dane wyjściowe na żywo operacja odczytu |
> | Akcja | Microsoft.Media/mediaservices/read | Przeczytaj dowolnego konta usługi Media Services |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Odczyt każdej operacji, punkt końcowy przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/delete | Usuwanie dowolnego punktu końcowego przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/read | Przeczytaj dowolnego punktu końcowego przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Skalowanie każdej operacji, punkt końcowy przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Rozpocznij każdej operacji, punkt końcowy przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Zatrzymaj każdej operacji, punkt końcowy przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/write | Utwórz lub zaktualizuj wszelkie punkt końcowy przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/delete | Usuń wszelkie Lokalizator przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Wyświetl listę kluczy zawartości |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Lista ścieżek |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/read | Przeczytaj wszelkie Lokalizator przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/write | Utwórz lub zaktualizuj wszelkie Lokalizator przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingPolicies/delete | Usuń wszelkie zasady przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingPolicies/read | Przeczytaj wszelkie zasady przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingPolicies/write | Utwórz lub zaktualizuj wszelkie zasady przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronizuj klucze magazynu dla konta usługi Azure Storage dołączone |
> | Akcja | Microsoft.Media/mediaservices/transforms/delete | Usuń wszystkie przekształcenia |
> | Akcja | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Anulowanie zadania |
> | Akcja | Microsoft.Media/mediaservices/transforms/jobs/delete | Usuń wszystkie zadania |
> | Akcja | Microsoft.Media/mediaservices/transforms/jobs/read | Odczytaj wszelkie zadanie |
> | Akcja | Microsoft.Media/mediaservices/transforms/jobs/write | Utwórz lub zaktualizuj wszystkie zadania |
> | Akcja | Microsoft.Media/mediaservices/transforms/read | Przeczytaj transformację |
> | Akcja | Microsoft.Media/mediaservices/transforms/write | Utwórz lub zaktualizuj wszelkie przekształcenia |
> | Akcja | Microsoft.Media/mediaservices/write | Utwórz lub zaktualizuj wszelkie konta usługi Media Services |
> | Akcja | Microsoft.Media/operations/read | Pobierz dostępne operacje |
> | Akcja | Microsoft.Media/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Media Services i włącza funkcję tworzenia konta usługi Media Services |
> | Akcja | Microsoft.Media/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Media Services |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Migrate/assessmentprojects/assessments/read | Wyświetla ocen w ramach projektu |
> | Akcja | Microsoft.Migrate/assessmentprojects/delete | Usuwa projekt oceny |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Pobierz właściwości ocenianą maszynę |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Usuwa ocenę |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Pobiera adres URL raportu oceny |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Pobiera właściwości oceny |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Tworzy nowego rozwiązania do oceny lub aktualizuje istniejące oceny |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/delete | Usuwa grupę |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/read | Pobierz właściwości grupy |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Grupy aktualizacji, dodając lub usuwając maszyn |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/write | Tworzy nową grupę lub aktualizuje istniejącą grupę |
> | Akcja | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Usuwa moduł funkcji Hyper-v |
> | Akcja | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Pobiera właściwości modułu zbierającego funkcji Hyper-v |
> | Akcja | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Tworzy nowy moduł zbierający funkcji Hyper-v lub aktualizuje istniejący moduł zbierający funkcji Hyper-v |
> | Akcja | Microsoft.Migrate/assessmentprojects/machines/read | Pobiera właściwości maszyny |
> | Akcja | Microsoft.Migrate/assessmentprojects/read | Pobiera właściwości projekt oceny |
> | Akcja | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Usuwa moduł zbierający VMware |
> | Akcja | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Pobiera właściwości modułu zbierającego programu VMware |
> | Akcja | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Tworzy nowy moduł zbierający VMware lub aktualizuje istniejący moduł zbierający VMware |
> | Akcja | Microsoft.Migrate/assessmentprojects/write | Tworzy nowy projekt oceny lub aktualizuje istniejący projekt oceny |
> | Akcja | Microsoft.Migrate/locations/assessmentOptions/read | Pobiera opcje oceny, które są dostępne w danej lokalizacji |
> | Akcja | Microsoft.Migrate/locations/checknameavailability/action | Służy do sprawdzania dostępności nazwy zasobów w danej subskrypcji w danej lokalizacji |
> | Akcja | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Pobiera właściwości wystąpienia bazy danych |
> | Akcja | Microsoft.Migrate/migrateprojects/Databases/read | Pobiera właściwości bazy danych |
> | Akcja | Microsoft.Migrate/migrateprojects/delete | Usuwa projekt migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/machines/read | Pobiera właściwości maszyny |
> | Akcja | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Usuwa zdarzenie migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Pobiera właściwości migracji zdarzenia. |
> | Akcja | Microsoft.Migrate/migrateprojects/read | Pobiera właściwości projektu migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/registerTool/action | Rejestruje narzędzie do projektu migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Wyczyść dane rozwiązanie projektu migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/solutions/Delete | Usuwa rozwiązania projektu migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Pobiera konfigurację rozwiązania projektu migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/solutions/read | Pobiera właściwości rozwiązania projektu migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/solutions/write | Tworzy nowy projekt migracji rozwiązanie lub aktualizuje istniejące rozwiązanie projektu migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/write | Tworzy nowy projekt migracji lub aktualizuje istniejący projekt migracji |
> | Akcja | Microsoft.Migrate/Operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.Migrate |
> | Akcja | Microsoft.Migrate/projects/assessments/read | Wyświetla ocen w ramach projektu |
> | Akcja | Microsoft.Migrate/projects/delete | Usuwa projekt |
> | Akcja | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Pobierz właściwości ocenianą maszynę |
> | Akcja | Microsoft.Migrate/projects/groups/assessments/delete | Usuwa ocenę |
> | Akcja | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Pobiera adres URL raportu oceny |
> | Akcja | Microsoft.Migrate/projects/groups/assessments/read | Pobiera właściwości oceny |
> | Akcja | Microsoft.Migrate/projects/groups/assessments/write | Tworzy nowego rozwiązania do oceny lub aktualizuje istniejące oceny |
> | Akcja | Microsoft.Migrate/projects/groups/delete | Usuwa grupę |
> | Akcja | Microsoft.Migrate/projects/groups/read | Pobierz właściwości grupy |
> | Akcja | Microsoft.Migrate/projects/groups/write | Tworzy nową grupę lub aktualizuje istniejącą grupę |
> | Akcja | Microsoft.Migrate/projects/keys/action | Pobiera klucze wspólne dla projektu |
> | Akcja | Microsoft.Migrate/projects/machines/read | Pobiera właściwości maszyny |
> | Akcja | Microsoft.Migrate/projects/read | Pobiera właściwości projektu |
> | Akcja | Microsoft.Migrate/projects/write | Tworzy nowy projekt lub aktualizuje istniejący projekt |
> | Akcja | Microsoft.Migrate/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.Migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MixedReality/register/action | Rejestruje subskrypcję dostawcy zasobów rzeczywistość mieszana. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Tworzyć kotwic przestrzenne |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Usuń kotwic przestrzenne |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Wykrywanie pobliskich kotwic przestrzenne |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Pobierz właściwości kotwic przestrzenne |
> | Akcja | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla Microsoft.MixedReality/SpatialAnchorsAccounts |
> | Akcja | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla Microsoft.MixedReality/SpatialAnchorsAccounts |
> | Akcja | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla Microsoft.MixedReality/SpatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Znajdź kotwic przestrzenne |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Przesyłanie danych diagnostycznych, aby poprawić jakość usług Azure przestrzenne kotwic |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aktualizacja właściwości kotwic przestrzenne |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.NetApp/locations/operationresults/read | Odczytuje zasób wynik operacji. |
> | Akcja | Microsoft.NetApp/locations/read | Odczyty na dostępność Sprawdź zasób. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Usuwa puli zasobów. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/read | Odczytuje puli zasobów. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Usuwa zasób woluminu. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Odczytuje zasób docelowy instalacji. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Odczytuje zasobu woluminu. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Usuwa zasób migawki. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Odczytuje zasób migawki. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Zapisuje zasobów migawki. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Zapisuje zasobu woluminu. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/write | Zapisuje puli zasobów. |
> | Akcja | Microsoft.NetApp/netAppAccounts/delete | Usuwa zasób konta. |
> | Akcja | Microsoft.NetApp/netAppAccounts/read | Odczytuje zasób konta. |
> | Akcja | Microsoft.NetApp/netAppAccounts/write | Zapisuje konta zasobu. |
> | Akcja | Microsoft.NetApp/Operations/read | Odczytuje operacją zasobów. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Pobierz dostępne nagłówków żądań bramy Application Gateway |
> | Akcja | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Pobierz dostępne nagłówek odpowiedzi usługa Application Gateway |
> | Akcja | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Pobierz dostępne zmiennych serwera bramy Application Gateway |
> | Akcja | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Ssl bramy aplikacji wstępnie zdefiniowane zasady |
> | Akcja | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Dostępne opcje protokołu Ssl bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Pobiera bramy aplikacji, która ustawia dostępne reguły zapory aplikacji sieci Web |
> | Akcja | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Dołącza do puli adresów zaplecza bramy aplikacji. Nie dla. |
> | Akcja | Microsoft.Network/applicationGateways/backendhealth/action | Pobiera stan zaplecza bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/delete | Usuwa bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/read | Pobiera bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/start/action | Uruchamia bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/stop/action | Zatrzymuje bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/write | Utworzenie bramy aplikacji lub aktualizacji bramy aplikacji |
> | Akcja | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Usuwa zasadę zapory aplikacji sieci Web dla bramy aplikacji |
> | Akcja | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Pobiera zasady brama aplikacji zapory aplikacji internetowych |
> | Akcja | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Tworzy zasady zapory aplikacji sieci Web dla bramy aplikacji lub aktualizacji zasad usługi Application Gateway zapory aplikacji internetowych |
> | Akcja | Microsoft.Network/applicationSecurityGroups/delete | Usuwa grupę zabezpieczeń aplikacji |
> | Akcja | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Dołącza do konfiguracji adresu IP do grup zabezpieczeń aplikacji. Nie dla. |
> | Akcja | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Dołącza reguły zabezpieczeń do grup zabezpieczeń aplikacji. Nie dla. |
> | Akcja | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Konfiguracje adresów IP listy w ApplicationSecurityGroup |
> | Akcja | Microsoft.Network/applicationSecurityGroups/read | Pobiera identyfikator grupy zabezpieczeń aplikacji. |
> | Akcja | Microsoft.Network/applicationSecurityGroups/write | Tworzy grupę zabezpieczeń aplikacji lub aktualizacji istniejącej grupy zabezpieczeń aplikacji. |
> | Akcja | Microsoft.Network/azureFirewallFqdnTags/read | Pobiera tagi FQDN zaporę platformy Azure |
> | Akcja | Microsoft.Network/azurefirewalls/delete | Usuwanie zapory platformy Azure |
> | Akcja | Microsoft.Network/azurefirewalls/read | Pobierz zaporę platformy Azure |
> | Akcja | Microsoft.Network/azurefirewalls/write | Tworzy lub aktualizuje zapory usługi Azure |
> | Akcja | Microsoft.Network/bastionHosts/delete | Usuwa hostem bastionu |
> | Akcja | Microsoft.Network/bastionHosts/read | Pobiera hostem bastionu |
> | Akcja | Microsoft.Network/bastionHosts/write | Utwórz lub zaktualizuj hostem bastionu |
> | Akcja | Microsoft.Network/bgpServiceCommunities/read | Pobierz usługę protokołu Bgp społeczności |
> | Akcja | Microsoft.Network/checkFrontDoorNameAvailability/action | Sprawdza, czy nazwa drzwiami frontowymi jest dostępna |
> | Akcja | Microsoft.Network/checkTrafficManagerNameAvailability/action | Służy do sprawdzania dostępności nazwy DNS względem usługi Traffic Manager. |
> | Akcja | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/connections/revoke/action | Oznacza stan połączenia usługi Expressroute jako odwołane |
> | Akcja | Microsoft.Network/connections/sharedkey/action | Get VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft.Network/connections/sharedKey/read | Gets VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft.Network/connections/sharedKey/write | Tworzy lub aktualizuje istniejące VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Pobiera konfigurację VirtualNetworkGatewayConnection urządzenia sieci Vpn |
> | Akcja | Microsoft.Network/connections/write | Tworzy lub aktualizuje istniejące VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/ddosCustomPolicies/delete | Usuwa przed atakami DDoS dostosowane zasady |
> | Akcja | Microsoft.Network/ddosCustomPolicies/read | Pobiera przed atakami DDoS dostosowanej definicji. Definicja zasad |
> | Akcja | Microsoft.Network/ddosCustomPolicies/write | Tworzy zasady przed atakami DDoS dostosowany lub aktualizuje istniejące zasady przed atakami DDoS dostosowane |
> | Akcja | Microsoft.Network/ddosProtectionPlans/delete | Usuwa planu ochrony przed atakami DDoS |
> | Akcja | Microsoft.Network/ddosProtectionPlans/join/action | Dołącza do planu ochrony przed atakami DDoS. Nie dla. |
> | Akcja | Microsoft.Network/ddosProtectionPlans/read | Pobiera Plan ochrony przed atakami DDoS |
> | Akcja | Microsoft.Network/ddosProtectionPlans/write | Tworzy Plan ochrony przed atakami DDoS lub aktualizuje Plan ochrony przed atakami DDoS  |
> | Akcja | Microsoft.Network/dnsoperationresults/read | Pobiera wyniki operacji DNS |
> | Akcja | Microsoft.Network/dnsoperationstatuses/read | Pobiera stan operacji DNS  |
> | Akcja | Microsoft.Network/dnszones/A/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "A", ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/A/read | Pobierz zestaw rekordów typu "A", w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/dnszones/A/write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/AAAA/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "AAAA" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/AAAA/read | Pobierz zestaw rekordów typu "AAAA" w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/dnszones/AAAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/all/read | Pobiera zestawy rekordów DNS w przypadku typów |
> | Akcja | Microsoft.Network/dnszones/CAA/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "CAA, ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/CAA/read | Pobierz zestaw rekordów typu "CAA" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element etag. |
> | Akcja | Microsoft.Network/dnszones/CAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "CAA" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/CNAME/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "CNAME, ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/CNAME/read | Pobierz zestaw rekordów typu "CNAME" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element etag. |
> | Akcja | Microsoft.Network/dnszones/CNAME/write | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/delete | Usunięcie strefy DNS, w formacie JSON. Właściwości strefy obejmują tagów, element etag, numberOfRecordSets i maxNumberOfRecordSets. |
> | Akcja | Microsoft.Network/dnszones/MX/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "MX", ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/MX/read | Pobierz zestaw rekordów typu "MX", w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/dnszones/MX/write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/NS/delete | Usuwa zestaw rekordów DNS typu NS |
> | Akcja | Microsoft.Network/dnszones/NS/read | Pobiera zestaw rekordów DNS typu NS |
> | Akcja | Microsoft.Network/dnszones/NS/write | Tworzy lub aktualizuje zestaw rekordów DNS typu NS |
> | Akcja | Microsoft.Network/dnszones/PTR/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "PTR" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/PTR/read | Pobierz zestaw rekordów typu "PTR", w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/dnszones/PTR/write | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/read | Uzyskiwanie strefy DNS, w formacie JSON. Właściwości strefy obejmują tagów, element etag, numberOfRecordSets i maxNumberOfRecordSets. Należy pamiętać, że to polecenie nie pobiera pożądanych zestawów rekordów znajdujących się w strefie. |
> | Akcja | Microsoft.Network/dnszones/recordsets/read | Pobiera zestawy rekordów DNS w przypadku typów |
> | Akcja | Microsoft.Network/dnszones/SOA/read | Pobiera zestaw rekordów DNS typu SOA |
> | Akcja | Microsoft.Network/dnszones/SOA/write | Tworzy lub aktualizuje zestaw rekordów DNS typu SOA |
> | Akcja | Microsoft.Network/dnszones/SRV/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "SRV" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/SRV/read | Pobierz zestaw rekordów typu "SRV" w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/dnszones/SRV/write | Utwórz lub zaktualizuj zestaw rekordów typu SRV |
> | Akcja | Microsoft.Network/dnszones/TXT/delete | Usuń zestaw rekordów o określonej nazwie, a następnie wpisz "TXT" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/TXT/read | Pobierz zestaw rekordów typu "TXT", w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/dnszones/TXT/write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/write | Utwórz lub zaktualizuj strefę DNS w grupie zasobów.  Używany do aktualizacji tagów w zasobie strefy DNS. Należy pamiętać, że to polecenie nie można utworzyć lub zaktualizować zestawów rekordów w strefie. |
> | Akcja | Microsoft.Network/expressRouteCircuits/authorizations/delete | Usuwa ExpressRouteCircuit autoryzacji |
> | Akcja | Microsoft.Network/expressRouteCircuits/authorizations/read | Pobiera ExpressRouteCircuit autoryzacji |
> | Akcja | Microsoft.Network/expressRouteCircuits/authorizations/write | Tworzy lub aktualizuje istniejące autoryzacji ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/delete | Usuwa obwodu usługi Expressroute |
> | Akcja | Microsoft.Network/expressRouteCircuits/join/action | Dołącza do obwodu usługi Express Route. Nie dla. |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Pobiera ExpressRouteCircuit ArpTable komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Usuwa połączenie ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Pobiera połączenie ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Tworzy lub aktualizuje istniejący zasób połączenia ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/delete | Usuwa ExpressRouteCircuit komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Pobiera elementu równorzędnego połączenia obwodu usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/read | Pobiera ExpressRouteCircuit komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Pobiera ExpressRouteCircuit RouteTable komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Pobiera ExpressRouteCircuit komunikacji równorzędnej RouteTable podsumowanie |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Pobiera ExpressRouteCircuit komunikacji równorzędnej Stat |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/write | Tworzy lub aktualizuje istniejącą ExpressRouteCircuit komunikację równorzędną |
> | Akcja | Microsoft.Network/expressRouteCircuits/read | Pobierz obwodu usługi Expressroute |
> | Akcja | Microsoft.Network/expressRouteCircuits/stats/read | Pobiera ExpressRouteCircuit Stat |
> | Akcja | Microsoft.Network/expressRouteCircuits/write | Tworzy lub aktualizuje istniejące obwodu usługi Expressroute |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/join/action | Sprzężenia moduł Express Route krzyżowe połączenia. Nie dla. |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Pobiera Expressroute między tabeli protokołu Arp komunikacji równorzędnej połączenie |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Usuwa Expressroute między połączenia komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/read | Pobiera Expressroute między połączenia komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Pobiera Expressroute między połączenia komunikacji równorzędnej Route Table |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Pobiera Expressroute między podsumowanie tabeli trasy połączenia komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/write | Tworzy wiele połączeń komunikacji równorzędnej usługi Expressroute lub aktualizuje istniejące Cross połączenia komunikacji równorzędnej usługi Expressroute |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/read | Pobierz Expressroute między połączenia |
> | Akcja | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Usuwa połączenia usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Pobiera połączenia usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Połączenia Expressroute tworzy lub aktualizuje istniejące połączenia usługi Expressroute |
> | Akcja | Microsoft.Network/expressRouteGateways/join/action | Dołącza do bramy usługi Expressroute. Nie dla. |
> | Akcja | Microsoft.Network/expressRouteGateways/read | Pobierz brama usługi Express Route |
> | Akcja | Microsoft.Network/expressRoutePorts/delete | Deletes ExpressRoutePorts |
> | Akcja | Microsoft.Network/expressRoutePorts/join/action | Dołącza do portów Express Route. Nie dla. |
> | Akcja | Microsoft.Network/expressRoutePorts/links/read | Gets ExpressRouteLink |
> | Akcja | Microsoft.Network/expressRoutePorts/read | Gets ExpressRoutePorts |
> | Akcja | Microsoft.Network/expressRoutePorts/write | Tworzy lub aktualizuje ExpressRoutePorts |
> | Akcja | Microsoft.Network/expressRoutePortsLocations/read | Lokalizacje Get Express Route portów |
> | Akcja | Microsoft.Network/expressRouteServiceProviders/read | Pobiera dostawców usługi Express Route |
> | Akcja | Microsoft.Network/frontDoors/backendPools/delete | Usuwa puli zaplecza |
> | Akcja | Microsoft.Network/frontDoors/backendPools/read | Pobiera puli zaplecza |
> | Akcja | Microsoft.Network/frontDoors/backendPools/write | Tworzy lub aktualizuje puli zaplecza |
> | Akcja | Microsoft.Network/frontDoors/delete | Usuwa drzwi |
> | Akcja | Microsoft.Network/frontDoors/frontendEndpoints/delete | Usuwa punkt końcowy serwera sieci Web |
> | Akcja | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Powoduje wyłączenie protokołu HTTPS w punkcie końcowym serwera sieci Web |
> | Akcja | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Włącza protokół HTTPS dla punktu końcowego serwera sieci Web |
> | Akcja | Microsoft.Network/frontDoors/frontendEndpoints/read | Pobiera punkt końcowy serwera sieci Web |
> | Akcja | Microsoft.Network/frontDoors/frontendEndpoints/write | Tworzy lub aktualizuje punktu końcowego serwera sieci Web |
> | Akcja | Microsoft.Network/frontDoors/healthProbeSettings/delete | Usuwa ustawienia sondy kondycji |
> | Akcja | Microsoft.Network/frontDoors/healthProbeSettings/read | Pobiera ustawienia sondy kondycji |
> | Akcja | Microsoft.Network/frontDoors/healthProbeSettings/write | Tworzy lub aktualizuje ustawienia sondy kondycji |
> | Akcja | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Akcja | Microsoft.Network/frontDoors/loadBalancingSettings/read | Pobiera obciążenia równoważenia ustawień |
> | Akcja | Microsoft.Network/frontDoors/loadBalancingSettings/write | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Akcja | Microsoft.Network/frontDoors/purge/action | Przeczyszczanie zawartości w pamięci podręcznej z drzwi |
> | Akcja | Microsoft.Network/frontDoors/read | Pobiera drzwi |
> | Akcja | Microsoft.Network/frontDoors/routingRules/delete | Usuwa regułę routingu |
> | Akcja | Microsoft.Network/frontDoors/routingRules/read | Pobiera regułę routingu |
> | Akcja | Microsoft.Network/frontDoors/routingRules/write | Tworzy lub aktualizuje regułę routingu |
> | Akcja | Microsoft.Network/frontDoors/validateCustomDomain/action | Sprawdza poprawność punktu końcowego serwera sieci Web dla drzwi |
> | Akcja | Microsoft.Network/frontDoors/write | Tworzy lub aktualizuje drzwi |
> | Akcja | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Zapora aplikacji sieci Web pobiera zarządzane zestawy reguł |
> | Akcja | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Usuwa zasady zapory aplikacji sieci Web |
> | Akcja | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Pobiera zasady zapory aplikacji sieci Web |
> | Akcja | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Tworzy lub aktualizuje zasady zapory aplikacji sieci Web |
> | Akcja | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Dołącza do puli adresów zaplecza modułu równoważenia obciążenia. Nie dla. |
> | Akcja | Microsoft.Network/loadBalancers/backendAddressPools/read | Pobiera definicji puli adresów wewnętrznej bazy danych modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/delete | Usuwa moduł równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Dołącza do konfiguracji adresu IP frontonu modułu równoważenia obciążenia. Nie dla. |
> | Akcja | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Pobiera definicję konfiguracji IP frontonu modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Dołącza modułu równoważenia obciążenia dla ruchu przychodzącego translatora adresów Sieciowych puli. Nie dla. |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatPools/read | Pobiera moduł równoważenia obciążenia dla ruchu przychodzącego translatora adresów sieciowych definicji puli |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/delete | Usuwa regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Dołącza do reguły nat dla ruchu przychodzącego modułu równoważenia obciążenia. Nie dla. |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/read | Pobiera moduł równoważenia obciążenia dla ruchu przychodzącego definicji reguły translatora adresów sieciowych |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/write | Tworzy regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia lub aktualizuje istniejącą regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/loadBalancingRules/read | Pobiera równoważenia definicji reguły obciążenia modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/networkInterfaces/read | Pobiera odwołania do wszystkich interfejsów sieciowych w ramach modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/outboundRules/read | Pobiera definicję reguły ruchu wychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/probes/join/action | Umożliwia użycie sond modułu równoważenia obciążenia. Na przykład z tą właściwością healthProbe uprawnień dla usługi VM scale set odwoływać się do sondy. Nie dla. |
> | Akcja | Microsoft.Network/loadBalancers/probes/read | Pobiera sondy modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/read | Pobiera definicji modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w ramach modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/write | Tworzy moduł równoważenia obciążenia lub aktualizuje istniejącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/localnetworkgateways/delete | Deletes LocalNetworkGateway |
> | Akcja | Microsoft.Network/localnetworkgateways/read | Gets LocalNetworkGateway |
> | Akcja | Microsoft.Network/localnetworkgateways/write | Tworzy lub aktualizuje istniejące LocalNetworkGateway |
> | Akcja | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Pobiera automatycznie zatwierdzona prywatnego łącza |
> | Akcja | Microsoft.Network/locations/availableDelegations/read | Pobiera dostępne delegacji |
> | Akcja | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Pobiera zasoby dostępne prywatne punktu końcowego |
> | Akcja | Microsoft.Network/locations/bareMetalTenants/action | Alokuje lub weryfikuje dzierżawy bez systemu operacyjnego |
> | Akcja | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Sprawdza, czy wsparcia Accelerated Networking |
> | Akcja | Microsoft.Network/locations/checkDnsNameAvailability/read | Sprawdza, czy etykieta dns jest dostępna w określonej lokalizacji |
> | Akcja | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Widoczność prywatne połączoną usługę kontroli |
> | Akcja | Microsoft.Network/locations/operationResults/read | Pobiera wynik operacji asynchronicznej POST lub operacja usuwania |
> | Akcja | Microsoft.Network/locations/operations/read | Pobiera zasób operacji, który reprezentuje stan operacji asynchronicznej |
> | Akcja | Microsoft.Network/locations/serviceTags/read | Pobierz tagi usługi |
> | Akcja | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Pobiera obsługiwane rozmiary maszyn wirtualnych |
> | Akcja | Microsoft.Network/locations/usages/read | Pobiera zasoby metryki użycia |
> | Akcja | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Pobiera listę dostępnych usług punktu końcowego sieci wirtualnej |
> | Akcja | Microsoft.Network/networkIntentPolicies/delete | Usuwa zasady intencji sieci |
> | Akcja | Microsoft.Network/networkIntentPolicies/read | Pobiera opis elementu Intent zasad sieciowych |
> | Akcja | Microsoft.Network/networkIntentPolicies/write | Tworzy zasady sieciowe przeznaczenie lub aktualizuje istniejące zasady sieciowe przeznaczenie |
> | Akcja | Microsoft.Network/networkInterfaces/delete | Usuwa interfejs sieciowy |
> | Akcja | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Pobieranie grup zabezpieczeń sieci skonfigurowane w sieci interfejsu maszyny wirtualnej |
> | Akcja | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Pobierz tabelę trasy skonfigurowane dla interfejsu sieciowego maszyny wirtualnej |
> | Akcja | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Dołącza do konfiguracji adresu IP interfejsu sieciowego. Nie dla. |
> | Akcja | Microsoft.Network/networkInterfaces/ipconfigurations/read | Pobiera definicję konfiguracji adresu ip interfejsu sieci.  |
> | Akcja | Microsoft.Network/networkInterfaces/join/action | Dołącza do maszyny wirtualnej do interfejsu sieciowego. Nie dla. |
> | Akcja | Microsoft.Network/networkInterfaces/loadBalancers/read | Pobiera wszystkie moduły równoważenia obciążenia, które interfejs sieciowy jest częścią |
> | Akcja | Microsoft.Network/networkInterfaces/read | Pobiera definicja interfejsu sieciowego.  |
> | Akcja | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Usuwa konfigurację wzorca Tap interfejsu sieciowego. |
> | Akcja | Microsoft.Network/networkInterfaces/tapConfigurations/read | Pobiera konfigurację wzorca Tap interfejsu sieciowego. |
> | Akcja | Microsoft.Network/networkInterfaces/tapConfigurations/write | Tworzy konfiguracji naciśnij interfejsu sieciowego lub aktualizacji istniejącej konfiguracji interfejsu sieciowego naciśnij. |
> | Akcja | Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Akcja | Microsoft.Network/networkProfiles/delete | Usuwa profil sieciowy |
> | Akcja | Microsoft.Network/networkProfiles/read | Pobiera profil sieciowy |
> | Akcja | Microsoft.Network/networkProfiles/removeContainers/action | Usuwa kontenery |
> | Akcja | Microsoft.Network/networkProfiles/setContainers/action | Ustawia kontenerów |
> | Akcja | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Ustawia kontener interfejsów sieciowych |
> | Akcja | Microsoft.Network/networkProfiles/write | Tworzy lub aktualizuje profil sieciowy |
> | Akcja | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Pobiera domyślną definicją reguły zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń. Nie dla. |
> | Akcja | Microsoft.Network/networkSecurityGroups/read | Pobiera definicję grupy zabezpieczeń sieci |
> | Akcja | Microsoft.Network/networkSecurityGroups/securityRules/delete | Usuwa regułę zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/securityRules/read | Pobiera definicji reguły zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/securityRules/write | Tworzy regułę zabezpieczeń lub aktualizuje istniejącą regułę zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/write | Tworzy sieciową grupę zabezpieczeń lub aktualizacji istniejącej sieciowej grupy zabezpieczeń |
> | Akcja | Microsoft.Network/networkWatchers/availableProvidersList/action | Zwraca wszystkie dostępne internet dostawcy usług dla określonego regionu platformy Azure. |
> | Akcja | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Zwraca oceny opóźnienia względnego dla Internetu dostawcy usług z określonej lokalizacji do regionów platformy Azure. |
> | Akcja | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfiguruje rejestrowanie przepływu dla zasobu docelowego. |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/delete | Usuwa monitora połączeń |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Monitorowanie łączności między punktami końcowymi określonego zapytania |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/read | Pobierz szczegóły monitora połączeń |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Rozpocznij monitorowanie łączności między punktami końcowymi określonego |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Monitorowanie łączności między punktami końcowymi określonego Stop/wstrzymywany |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/write | Tworzy monitora połączeń |
> | Akcja | Microsoft.Network/networkWatchers/connectivityCheck/action | Sprawdza możliwość nawiązywania bezpośredniego połączenia TCP z maszyny wirtualnej do danego punktu końcowego, łącznie z innej maszyny Wirtualnej lub dowolnego serwera zdalnego. |
> | Akcja | Microsoft.Network/networkWatchers/delete | Usuwa usługi network watcher |
> | Akcja | Microsoft.Network/networkWatchers/ipFlowVerify/action | Zwraca, czy pakiet może wchodzić lub wychodzić do / z określonej lokalizacji docelowej. |
> | Akcja | Microsoft.Network/networkWatchers/lenses/delete | Usuwa obiektywu |
> | Akcja | Microsoft.Network/networkWatchers/lenses/query/action | Zapytanie monitorowania ruchu sieciowego w określonym punkcie końcowym |
> | Akcja | Microsoft.Network/networkWatchers/lenses/read | Pobierz szczegóły obiektywu |
> | Akcja | Microsoft.Network/networkWatchers/lenses/start/action | Rozpocznij monitorowanie ruchu sieciowego w określonym punkcie końcowym |
> | Akcja | Microsoft.Network/networkWatchers/lenses/stop/action | Zatrzymaj/wstrzymywany monitorowania ruchu sieciowego w określonym punkcie końcowym |
> | Akcja | Microsoft.Network/networkWatchers/lenses/write | Tworzy obiektywu |
> | Akcja | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostyka konfiguracji sieci. |
> | Akcja | Microsoft.Network/networkWatchers/nextHop/action | Dla określonego obiektu docelowego i docelowy adres IP zwracać typ następnego przeskoku, a następnie nadzieję, że adres IP. |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/delete | Usuwa przechwytywania pakietów |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Pobiera informacje o właściwości i stan zasobów przechwytywania pakietów. |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/read | Pobierz definicję przechwytywania pakietów |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Zatrzymania uruchomionej sesji przechwytywania pakietów. |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/write | Tworzy przechwytywania pakietów |
> | Akcja | Microsoft.Network/networkWatchers/pingMeshes/delete | Usuwa PingMesh |
> | Akcja | Microsoft.Network/networkWatchers/pingMeshes/read | Pobierz szczegóły PingMesh |
> | Akcja | Microsoft.Network/networkWatchers/pingMeshes/start/action | Rozpocznij PingMesh między określonym maszyn wirtualnych |
> | Akcja | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Zatrzymaj PingMesh między określonym maszyn wirtualnych |
> | Akcja | Microsoft.Network/networkWatchers/pingMeshes/write | Tworzy PingMesh |
> | Akcja | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Zapytanie usługi Batch, monitorowanie łączności między punktami końcowymi określonego |
> | Akcja | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Pobiera stan przepływu logowania się do zasobu. |
> | Akcja | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Pobiera wynik rozwiązywania problemów z poprzednio uruchomionymi lub obecnie uruchomiona operacja rozwiązywania problemów. |
> | Akcja | Microsoft.Network/networkWatchers/read | Pobierz definicję obserwatora sieciowego |
> | Akcja | Microsoft.Network/networkWatchers/securityGroupView/action | Wyświetl skonfigurowane i obowiązujące reguły sieciowych grup zabezpieczeń stosowane na maszynie Wirtualnej. |
> | Akcja | Microsoft.Network/networkWatchers/topology/action | Pobiera widok poziomu sieci, zasobów i ich wzajemne relacje w grupie zasobów. |
> | Akcja | Microsoft.Network/networkWatchers/troubleshoot/action | Uruchamia Rozwiązywanie problemów w zasobie sieci na platformie Azure. |
> | Akcja | Microsoft.Network/networkWatchers/write | Usługi network watcher tworzy lub aktualizuje istniejące usługi network watcher |
> | Akcja | Microsoft.Network/operations/read | Pobierz dostępne operacje |
> | Akcja | Microsoft.Network/p2sVpnGateways/delete | Usuwa P2SVpnGateway. |
> | Akcja | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generowanie profil sieci Vpn dla P2SVpnGateway |
> | Akcja | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Pobiera P2SVpnGateway kondycji połączenia sieci VPN typu P2S |
> | Akcja | Microsoft.Network/p2sVpnGateways/read | Pobiera P2SVpnGateway. |
> | Akcja | Microsoft.Network/p2sVpnGateways/write | Umieszcza P2SVpnGateway. |
> | Akcja | Microsoft.Network/privateDnsOperationResults/read | Pobiera operację wyniki prywatnej strefy DNS |
> | Akcja | Microsoft.Network/privateDnsOperationStatuses/read | Pobiera stan prywatnej strefy DNS operacji |
> | Akcja | Microsoft.Network/privateDnsZones/A/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "A", ze strefy prywatnej strefy DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/A/read | Pobierz zestaw rekordów typu "A" w strefie prywatnej strefy DNS, w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/privateDnsZones/A/write | Utwórz lub zaktualizuj zestawu rekordów typu "A" w obrębie strefy prywatnej strefy DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/AAAA/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "AAAA" ze strefy prywatnej strefy DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/AAAA/read | Pobierz zestaw rekordów typu "AAAA" w strefie prywatnej strefy DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/privateDnsZones/AAAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w obrębie strefy prywatnej strefy DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/ALL/read | Pobiera zestawy rekordów prywatnej strefy DNS w przypadku typów |
> | Akcja | Microsoft.Network/privateDnsZones/CNAME/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "CNAME, ze strefy prywatnej strefy DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/CNAME/read | Pobierz zestaw rekordów typu "CNAME, w ramach strefy prywatnej strefy DNS, w formacie JSON. |
> | Akcja | Microsoft.Network/privateDnsZones/CNAME/write | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w obrębie strefy prywatnej strefy DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/delete | Usuwanie strefy prywatnej DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/MX/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "MX", ze strefy prywatnej strefy DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/MX/read | Pobierz zestaw rekordów typu "MX", w ramach strefy prywatnej strefy DNS, w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/privateDnsZones/MX/write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w obrębie strefy prywatnej strefy DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/PTR/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "PTR" ze strefy prywatnej strefy DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/PTR/read | Pobierz zestaw rekordów typu "PTR" w strefie prywatnej strefy DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/privateDnsZones/PTR/write | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w obrębie strefy prywatnej strefy DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/read | Pobierz właściwości strefy prywatnej strefy DNS, w formacie JSON. Należy pamiętać, że to polecenie nie pobrać sieci wirtualnych, z którymi jest połączony strefy prywatnej DNS lub zestawów rekordów znajdujących się w strefie. |
> | Akcja | Microsoft.Network/privateDnsZones/recordsets/read | Pobiera zestawy rekordów prywatnej strefy DNS w przypadku typów |
> | Akcja | Microsoft.Network/privateDnsZones/SOA/read | Pobierz zestaw rekordów typu "SOA, w ramach strefy prywatnej strefy DNS, w formacie JSON. |
> | Akcja | Microsoft.Network/privateDnsZones/SOA/write | Aktualizowanie zestawu rekordów typu "SOA" w obrębie strefy prywatnej strefy DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/SRV/delete | Usuń zestaw rekordów o określonej nazwie i wpisz "SRV" ze strefy prywatnej strefy DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/SRV/read | Pobierz zestaw rekordów typu "SRV" w strefie prywatnej strefy DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/privateDnsZones/SRV/write | Utwórz lub zaktualizuj zestaw rekordów typu "SRV" w obrębie strefy prywatnej strefy DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/TXT/delete | Usuń zestaw rekordów o określonej nazwie, a następnie wpisz "TXT" ze strefy prywatnej strefy DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/TXT/read | Pobierz zestaw rekordów typu "TXT", w ramach strefy prywatnej strefy DNS, w formacie JSON. Zestaw rekordów zawiera listę rekordów, a także czas wygaśnięcia, tagi i element etag. |
> | Akcja | Microsoft.Network/privateDnsZones/TXT/write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w obrębie strefy prywatnej strefy DNS. Rejestruje określone spowoduje zastąpienie bieżących rekordów w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Usuń łącze strefy prywatnej strefy DNS w sieci wirtualnej. |
> | Akcja | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Uzyskaj prywatnej strefy DNS strefy łącze do właściwości sieci wirtualnej w formacie JSON. |
> | Akcja | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Utwórz lub zaktualizuj łącze strefy prywatnej strefy DNS w sieci wirtualnej. |
> | Akcja | Microsoft.Network/privateDnsZones/write | Tworzenie lub aktualizowanie strefy prywatnej DNS w grupie zasobów. Należy pamiętać, że to polecenie nie można utworzyć lub zaktualizować linki sieci wirtualnej lub zestawów rekordów w strefie. |
> | Akcja | Microsoft.Network/privateEndpoints/delete | Usuwa zasób prywatnych punktów końcowych. |
> | Akcja | Microsoft.Network/privateEndpoints/read | Pobiera zasób prywatnych punktów końcowych. |
> | Akcja | Microsoft.Network/privateEndpoints/write | Tworzy nowy punkt końcowy prywatnej lub aktualizuje istniejące prywatnych punktów końcowych. |
> | Akcja | Microsoft.Network/privateLinkServices/delete | Usuwa zasób usługi prywatnego linku. |
> | Akcja | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Usuwa połączenie prywatnych punktów końcowych. |
> | Akcja | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Pobiera definicji połączenia prywatnych punktów końcowych. |
> | Akcja | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Tworzy nowe połączenie prywatnych punktów końcowych lub aktualizuje istniejące połączenie prywatnych punktów końcowych. |
> | Akcja | Microsoft.Network/privateLinkServices/read | Pobiera zasób usługi prywatnego linku. |
> | Akcja | Microsoft.Network/privateLinkServices/write | Tworzy nową usługę prywatnego linku lub aktualizuje istniejącą usługę prywatnego linku. |
> | Akcja | Microsoft.Network/publicIPAddresses/delete | Usuwa publicznego adresu Ip. |
> | Akcja | Microsoft.Network/publicIPAddresses/join/action | Dołącza do publicznego adresu ip. Nie dla. |
> | Akcja | Microsoft.Network/publicIPAddresses/read | Pobiera definicji adres publiczny adres ip. |
> | Akcja | Microsoft.Network/publicIPAddresses/write | Tworzy publiczny adres Ip lub aktualizuje istniejące publiczny adres Ip.  |
> | Akcja | Microsoft.Network/publicIPPrefixes/delete | Usuwa publiczny prefiks adresu Ip |
> | Akcja | Microsoft.Network/publicIPPrefixes/join/action | Tworzy sprzężenie PublicIPPrefix. Nie dla. |
> | Akcja | Microsoft.Network/publicIPPrefixes/read | Pobiera definicji publiczny prefiks adresu Ip |
> | Akcja | Microsoft.Network/publicIPPrefixes/write | Tworzy publiczny prefiks adresu Ip lub aktualizuje istniejące publiczny prefiks adresu Ip |
> | Akcja | Microsoft.Network/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.Network/routeFilters/delete | Usuwa definicję filtru tras |
> | Akcja | Microsoft.Network/routeFilters/join/action | Dołącza do filtru trasy. Nie dla. |
> | Akcja | Microsoft.Network/routeFilters/read | Pobiera definicji filtru tras |
> | Akcja | Microsoft.Network/routeFilters/routeFilterRules/delete | Usuwa definicję reguły filtru trasy |
> | Akcja | Microsoft.Network/routeFilters/routeFilterRules/read | Pobiera definicji reguły filtru trasy |
> | Akcja | Microsoft.Network/routeFilters/routeFilterRules/write | Tworzy reguły filtru trasy lub aktualizuje istniejącą regułę filtru trasy |
> | Akcja | Microsoft.Network/routeFilters/write | Tworzy filtru tras lub aktualizuje istniejący filtr tras |
> | Akcja | Microsoft.Network/routeTables/delete | Usuwa definicję tabeli tras |
> | Akcja | Microsoft.Network/routeTables/join/action | Tworzy sprzężenie tabeli tras. Nie dla. |
> | Akcja | Microsoft.Network/routeTables/read | Pobiera definicję tabeli tras |
> | Akcja | Microsoft.Network/routeTables/routes/delete | Usuwa definicję trasy |
> | Akcja | Microsoft.Network/routeTables/routes/read | Pobiera definicję trasy |
> | Akcja | Microsoft.Network/routeTables/routes/write | Tworzy trasę lub aktualizuje istniejącą trasę |
> | Akcja | Microsoft.Network/routeTables/write | Tworzy tabelę tras lub aktualizuje istniejącą tabelę tras |
> | Akcja | Microsoft.Network/securegateways/delete | Usuwanie bramy zabezpieczeń |
> | Akcja | Microsoft.Network/securegateways/read | Pobierz bramy zabezpieczeń |
> | Akcja | Microsoft.Network/securegateways/write | Tworzy lub aktualizuje bramę zabezpieczeń |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/delete | Usuwa zasad punktów końcowych usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/join/action | Dołącza do zasad punktów końcowych usługi. Nie dla. |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Dołącza podsieci do zasad punktów końcowych usługi. Nie dla. |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/read | Pobiera opis zasad punktu końcowego usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Usuwa definicją zasad punktów końcowych usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Pobiera opis definicji zasad punktu końcowego usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Tworzy definicją zasad punktów końcowych usługi lub aktualizuje istniejące definicją zasad punktów końcowych usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/write | Tworzy zasad punktów końcowych usługi lub aktualizuje istniejące zasady punktu końcowego usługi |
> | Akcja | Microsoft.Network/trafficManagerGeographicHierarchies/read | Pobiera hierarchia geograficzna usługi Traffic Manager zawierający regiony, do których mogą być używane z metody geograficznego routingu ruchu |
> | Akcja | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Usuwa punkt końcowy platformy Azure z istniejącego profilu usługi Traffic Manager. Usługa Traffic Manager przestanie routing ruchu na usunięto punkt końcowy platformy Azure. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Pobiera punkt końcowy platformy Azure, która należy do profilu usługi Traffic Manager, w tym wszystkich właściwości tego punktu końcowego usługi Azure. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Dodaj nowy punkt końcowy platformy Azure w istniejący profil usługi Traffic Manager, lub zaktualizuj właściwości istniejącego punktu końcowego platformy Azure, w tym profilu usługi Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/delete | Usuń profil usługi Traffic Manager. Wszystkie ustawienia skojarzone z profilem usługi Traffic Manager zostaną utracone, a profil, który nie jest już może służyć do kierowania ruchu. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Usuwa zewnętrzny punkt końcowy z istniejącego profilu usługi Traffic Manager. Usługa Traffic Manager przestanie routingu ruchu do usuniętego zewnętrzny punkt końcowy. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Pobiera zewnętrzny punkt końcowy, który należy do profilu usługi Traffic Manager, w tym wszystkie właściwości zewnętrzny punkt końcowy. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Dodawanie nowego zewnętrznego punktu końcowego w istniejący profil usługi Traffic Manager, lub zaktualizuj właściwości istniejącego zewnętrzny punkt końcowy w profilu usługi Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Pobiera Mapa cieplna usługi Traffic Manager dla danego profilu usługi Traffic Manager, który zawiera dane liczby i czas oczekiwania zapytania według lokalizacji i źródłowych adresów IP. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Usuwa punkt końcowy zagnieżdżonych z istniejącego profilu usługi Traffic Manager. Usługa Traffic Manager przestanie routing ruchu do usuniętego zagnieżdżone punktu końcowego. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Pobiera zagnieżdżone punkt końcowy, który należy do profilu usługi Traffic Manager, w tym wszystkich właściwości tego punktu końcowego zagnieżdżonych. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Dodaj nowy punkt końcowy zagnieżdżone w istniejący profil usługi Traffic Manager, lub zaktualizuj właściwości istniejącego zagnieżdżone punktu końcowego w tym profilu usługi Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/read | Pobieranie konfiguracji profilu usługi Traffic Manager. W tym ustawienia DNS, ustawienia routingu ruchu, ustawienia monitorowania punktu końcowego i listy punktów końcowych kierowany przez ten profil usługi Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/write | Tworzenie profilu usługi Traffic Manager, lub zmodyfikować konfigurację istniejącego profilu usługi Traffic Manager.<br>Obejmuje to włączenie lub wyłączenie profilu i modyfikowania ustawień DNS, ustawienia routingu ruchu lub ustawień monitorowania punktu końcowego.<br>Punkty końcowe kierowany przez profil usługi Traffic Manager może dodać, usunąć, włączona lub wyłączona. |
> | Akcja | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Usuwa klucz poziom subskrypcji użytych na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft.Network/trafficManagerUserMetricsKeys/read | Pobiera klucz poziom subskrypcji użytych na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft.Network/trafficManagerUserMetricsKeys/write | Tworzy nowy klucz poziom subskrypcji, który ma być używany dla kolekcji metryki użytkowników w czasie rzeczywistym. |
> | Akcja | Microsoft.Network/unregister/action | Wyrejestrowuje subskrypcję |
> | Akcja | Microsoft.Network/virtualHubs/delete | Usuwa koncentrator wirtualny |
> | Akcja | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Usuwa HubVirtualNetworkConnection |
> | Akcja | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | Akcja | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Utwórz lub zaktualizuj HubVirtualNetworkConnection |
> | Akcja | Microsoft.Network/virtualHubs/read | Pobierz koncentrator wirtualny |
> | Akcja | Microsoft.Network/virtualHubs/write | Utwórz lub zaktualizuj koncentrator wirtualny |
> | Akcja | microsoft.network/virtualnetworkgateways/connections/read | Get VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/virtualNetworkGateways/delete | Usuwa element virtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Generowanie pakietu klienta VPN dla bramy virtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Wygeneruj pakiet profilu VpnProfile dla bramy VirtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Pobiera virtualNetworkGateway anonsowane trasy |
> | Akcja | Microsoft.Network/virtualnetworkgateways/getbgppeerstatus/Action | Pobiera stan elementu równorzędnego protokołu bgp bramy virtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Pobiera virtualnetworkgateway rozpoznane trasy |
> | Akcja | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Get Per Vpn Client Connection Health for VirtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Uzyskać Ipsec klienta VPN parametrów VirtualNetworkGateway P2S klienta. |
> | Akcja | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Pobiera adres URL pakietu profilu vpn wstępnie wygenerowanego klienta |
> | Akcja | Microsoft.Network/virtualNetworkGateways/read | Gets a VirtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/reset/action | Resetuje element virtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Zresetuj klucz współużytkowany klienta VPN VirtualNetworkGateway P2S klienta. |
> | Akcja | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Konfigurowanie klienta VPN Ipsec parametrów VirtualNetworkGateway P2S klienta. |
> | Akcja | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Listy obsługiwanych urządzeń sieci Vpn |
> | Akcja | Microsoft.Network/virtualNetworkGateways/write | Tworzy lub aktualizuje element VirtualNetworkGateway |
> | Akcja | Microsoft.Network/virtualNetworks/BastionHosts/action | Pobiera hostem bastionu odwołań w sieci wirtualnej. |
> | Akcja | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Pobiera hostem bastionu odwołań w sieci wirtualnej. |
> | Akcja | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Sprawdź, czy adres Ip jest dostępne w określonej sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/delete | Usuwa sieć wirtualną |
> | Akcja | Microsoft.Network/virtualNetworks/peer/action | Łączy równorzędnie sieć wirtualną z inną siecią wirtualną |
> | Akcja | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/delete | Usuwa podsieć sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/join/action | Łączy sieci wirtualnej. Nie dla. |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza do zasobów, takich jak konto magazynu lub bazy danych SQL do podsieci. Nie dla. |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Przygotowuje podsieci, stosując niezbędne zasad sieciowych |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicji podsieci sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w podsieci sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/write | Tworzy podsieci sieci wirtualnej lub aktualizuje istniejącą podsieć sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/usages/read | Pobierz użycia adresów IP dla każdej podsieci sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Usuwa wirtualnej sieci równorzędnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Pobiera definicję komunikacji równorzędnej sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Tworzy komunikację równorzędną sieci wirtualnej lub aktualizuje istniejące komunikacji równorzędnej sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/write | Tworzy sieć wirtualną lub aktualizuje istniejącą sieć wirtualną |
> | Akcja | Microsoft.Network/virtualNetworkTaps/delete | Usuń naciśnij sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworkTaps/join/action | Łączy to w ramach sieci wirtualnej. Nie dla. |
> | Akcja | Microsoft.Network/virtualNetworkTaps/read | Get Virtual Network Tap |
> | Akcja | Microsoft.Network/virtualNetworkTaps/write | Utwórz lub zaktualizuj naciśnij sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualWans/delete | Usuwa wirtualnej sieci Wan |
> | Akcja | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Usuwa wirtualnej P2SVpnServerConfiguration sieci Wan |
> | Akcja | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Pobiera wirtualnego P2SVpnServerConfiguration sieci Wan |
> | Akcja | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Tworzy wirtualny P2SVpnServerConfiguration sieci Wan lub aktualizuje istniejący wirtualny P2SVpnServerConfiguration sieci Wan |
> | Akcja | Microsoft.Network/virtualWans/read | Uzyskaj wirtualne sieci Wan |
> | Akcja | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Pobiera obsługiwane VirtualWan dostawców zabezpieczeń. |
> | Akcja | Microsoft.Network/virtualWans/virtualHubs/read | Pobiera wszystkie wirtualne centra odwołujące się do wirtualnej sieci Wan. |
> | Akcja | Microsoft.Network/virtualwans/vpnconfiguration/action | Pobiera konfigurację sieci Vpn |
> | Akcja | Microsoft.Network/virtualWans/vpnSites/read | Pobiera wszystkie lokacje sieci VPN, odwołujące się do wirtualnej sieci Wan. |
> | Akcja | Microsoft.Network/virtualWans/write | Utwórz lub zaktualizuj wirtualne sieci Wan |
> | Akcja | Microsoft.Network/vpnGateways/delete | Usuwa bramy VpnGateway. |
> | Akcja | microsoft.network/vpngateways/listvpnconnectionshealth/action | Kondycja połączenia pobiera wszystkie lub podzbiór połączenia dla bramy VpnGateway |
> | Akcja | Microsoft.Network/vpnGateways/read | Pobiera bramy VpnGateway. |
> | Akcja | microsoft.network/vpngateways/reset/action | Resets a VpnGateway |
> | Akcja | microsoft.network/vpnGateways/vpnConnections/delete | Deletes a VpnConnection. |
> | Akcja | microsoft.network/vpnGateways/vpnConnections/read | Pobiera obiekt VpnConnection. |
> | Akcja | microsoft.network/vpnGateways/vpnConnections/write | Puts a VpnConnection. |
> | Akcja | Microsoft.Network/vpnGateways/write | Umieszcza bramy VpnGateway. |
> | Akcja | Microsoft.Network/vpnsites/delete | Usuwa zasób lokacji sieci Vpn. |
> | Akcja | Microsoft.Network/vpnsites/read | Pobiera zasób lokacji sieci Vpn. |
> | Akcja | Microsoft.Network/vpnsites/write | Tworzy lub aktualizuje zasób lokacji sieci Vpn. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Sprawdza, czy dana nazwa zasobu Namespace jest dostępna w usłudze notificationhub. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć domyślnej reguły autoryzacji Namespace.  |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia Namespace |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Klucz Namespace autoryzacji reguły ponownie wygenerować podstawowy/pomocniczy, należy określić klucz do ponownego wygenerowania |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie Namespace i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Sprawdza, czy dana nazwa usługi NotificationHub jest dostępna wewnątrz Namespace. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/Delete | Usuwanie zasobu Namespace |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Pobierz listę reguł autoryzacji Centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Usuń reguły autoryzacji Centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Pobierz parametry połączenia Centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Pobierz listę reguł autoryzacji Centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Powiadomienia Centrum autoryzacji reguły ponownie wygenerować podstawowy/klucz pomocniczy, należy określić klucz do ponownego wygenerowania |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Utwórz reguły autoryzacji Centrum powiadomień i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Wyślij testowe powiadomienie wypychane. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Usuń zasób Centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Pobierz listę metryk Namespace opisów zasobów |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Pobierz wszystkie poświadczenia systemu powiadomień platformy Centrum powiadomień. Obejmuje to, poświadczenia usługi WNS, MPNS, APNS, GCM i Baidu |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Pobierz listę opisów zasobów Centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Tworzenie Centrum powiadomień i zaktualizuj jego właściwości. Jego właściwości obejmują głównie poświadczenia systemu powiadomień platformy. Reguły autoryzacji i czas wygaśnięcia |
> | Akcja | Microsoft.NotificationHubs/Namespaces/read | Pobierz listę opisów zasobów Namespace |
> | Akcja | Microsoft.NotificationHubs/Namespaces/write | Tworzenie zasobu Namespace i zaktualizuj jego właściwości. Tagi i pojemność Namespace są właściwości, które mogą być aktualizowane. |
> | Akcja | Microsoft.NotificationHubs/operationResults/read | Zwraca wyniki operacji dla dostawcy usługi Notification Hubs |
> | Akcja | Microsoft.NotificationHubs/operations/read | Zwraca listę obsługiwanych operacji dla dostawcy usługi Notification Hubs |
> | Akcja | Microsoft.NotificationHubs/register/action | Rejestruje subskrypcję dostawcy zasobów NotificationHubs i włącza funkcję tworzenia przestrzeni nazw i NotificationHubs |
> | Akcja | Microsoft.NotificationHubs/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów NotificationHubs i włącza funkcję tworzenia przestrzeni nazw i NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.OffAzure/HyperVSites/clusters/read | Pobiera właściwości klastra funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/clusters/write | Tworzy lub aktualizuje klastra funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/delete | Usuwa lokacji funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/hosts/read | Pobiera właściwości hosta funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/hosts/write | Tworzy lub aktualizuje hosta funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/jobs/read | Pobiera właściwości zadania funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/machines/read | Pobiera właściwości maszyn Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/machines/start/action | Uruchom maszyny Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/machines/stop/action | Zatrzymuje maszyny Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Pobiera właściwości stan operacji dla funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/read | Pobiera właściwości lokacji funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/refresh/action | Odświeża obiektów w lokacji funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Pobiera właściwości konta Uruchom jako funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/usage/read | Pobiera użycia witryny funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/HyperVSites/write | Tworzy lub aktualizuje lokacji funkcji Hyper-V |
> | Akcja | Microsoft.OffAzure/Operations/read | Odczytuje ujawnione operacje |
> | Akcja | Microsoft.OffAzure/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.OffAzure |
> | Akcja | Microsoft.OffAzure/VMwareSites/delete | Usuwa lokacja programu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/jobs/read | Pobiera właściwości zadania programu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/machines/read | Pobiera właściwości maszyny VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/machines/start/action | Uruchomienie maszyn VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/machines/stop/action | Zatrzymuje maszyny VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Pobiera właściwości stan operacji dla oprogramowania VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/read | Pobiera właściwości lokacji programu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/refresh/action | Odświeża obiektów w lokacji programu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Pobiera właściwości konta Uruchom jako VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/usage/read | Pobiera użycia lokacja programu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/vcenters/read | Pobiera właściwości VMware vCenter |
> | Akcja | Microsoft.OffAzure/VMwareSites/vcenters/write | Tworzy lub aktualizuje VMware vCenter |
> | Akcja | Microsoft.OffAzure/VMwareSites/write | Tworzy lub aktualizuje lokacja programu VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.OperationalInsights/linkTargets/read | Wyświetla listę istniejących kont, które nie są skojarzone z subskrypcją platformy Azure. Aby połączyć tej subskrypcji platformy Azure do obszaru roboczego, użyj identyfikatora klienta zwróconego przez tę operację we właściwości identyfikatora klienta operacji Tworzenie obszaru roboczego. |
> | Akcja | Microsoft.operationalinsights/Operations/Read | Wyświetla listę wszystkich dostępnych operacji interfejsu API Rest OperationalInsights. |
> | Akcja | Microsoft.operationalinsights/Register/Action | Rergisters subskrypcji. |
> | Akcja | Microsoft.OperationalInsights/register/action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Akcja | Microsoft.operationalinsights/unregister/Action | Wyrejestrowuje subskrypcję. |
> | Akcja | Microsoft.OperationalInsights/workspaces/analytics/query/action | Wyszukaj przy użyciu nowego aparatu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Pobierz schemat wyszukiwania w wersji 2. |
> | Akcja | Microsoft.OperationalInsights/workspaces/api/query/action | Wyszukaj przy użyciu nowego aparatu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Pobierz schemat wyszukiwania w wersji 2. |
> | Akcja | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Usuń zakres konfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Pobierz zakres konfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Ustaw zakres konfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/datasources/delete | Usuń źródła danych w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/datasources/read | Pobierz źródła danych w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/datasources/write | Utwórz/zaktualizuj źródła danych w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/delete | Usuwa obszar roboczy. Jeśli obszar roboczy został połączony z istniejącego obszaru roboczego w czasie tworzenia obszaru roboczego, który został połączony z nie zostanie usunięty. |
> | Akcja | Microsoft.OperationalInsights/workspaces/gateways/delete | Usuwa bramę skonfigurowaną dla obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Generuje certyfikat rejestracji dla obszaru roboczego. Ten certyfikat służy do łączenia programu Microsoft System Center Operations Manager z obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Wyłącza pakiet intelligence pack dla danego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Włącza pakiet intelligence pack dla danego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Wyświetla listę wszystkich pakietów intelligence Pack, które są widoczne dla danego obszaru roboczego i zawiera również, czy pakiet jest włączony / wyłączony dla danego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Usuń połączone usługi w podanym obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/linkedServices/read | Pobierz połączone usługi w podanym obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/linkedServices/write | Utwórz/zaktualizuj połączone usługi w podanym obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/listKeys/action | Pobiera klucze listy dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/listKeys/read | Pobiera klucze listy dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/managementGroups/read | Pobiera nazwy i metadane dla grupy zarządzania programu System Center Operations Manager połączony z tym obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Pobierz definicje metryki w obszarze roboczym |
> | Akcja | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Usuń ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Pobierz ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Ustaw ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft.operationalinsights/workspaces/Operations/Read | Pobiera stan operacji obszaru roboczego OperationalInsights. |
> | Akcja | Microsoft.OperationalInsights/workspaces/purge/action | Usuń określone dane z obszaru roboczego |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Odczyt danych z tabeli AADDomainServicesAccountLogon |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Odczyt danych z tabeli AADDomainServicesAccountManagement |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Odczyt danych z tabeli AADDomainServicesDirectoryServiceAccess |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Odczyt danych z tabeli AADDomainServicesLogonLogoff |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Odczyt danych z tabeli AADDomainServicesPolicyChange |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Odczyt danych z tabeli AADDomainServicesPrivilegeUse |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Odczyt danych z tabeli AADDomainServicesSystemSecurity |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Odczyt danych z tabeli ADAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Odczyt danych z tabeli ADFActivityRun |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Odczyt danych z tabeli ADFPipelineRun |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Odczyt danych z tabeli ADFTriggerRun |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Odczyt danych z tabeli ADReplicationResult |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Odczyt danych z tabeli ADSecurityAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Alert/read | Odczytywanie danych z tabeli alertu |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Odczyt danych z tabeli AlertHistory |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Odczyt danych z tabeli AppCenterError |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Odczyt danych z tabeli dotycząca usługi Application Insights |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Odczyt danych z tabeli AuditLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Odczyt danych z tabeli AutoscaleEvaluationsLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Odczyt danych z tabeli AutoscaleScaleActionsLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Odczyt danych z tabeli AWSCloudTrail |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Odczyt danych z tabeli AzureActivity |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Odczyt danych z tabeli AzureAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Odczyt danych z tabeli AzureMetrics |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Odczyt danych z tabeli BlockchainApplicationLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Odczyt danych z tabeli BlockchainProxyLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Odczyt danych z tabeli BoundPort |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Odczyt danych z tabeli CommonSecurityLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Odczyt danych z tabeli Grupa_komputerów |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Odczyt danych z tabeli Zmianakonfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Odczyt danych z tabeli danych konfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Odczyt danych z tabeli ContainerImageInventory |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Odczyt danych z tabeli ContainerInventory |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Odczyt danych z tabeli ContainerLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Odczyt danych z tabeli ContainerNodeInventory |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Odczyt danych z tabeli ContainerServiceLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Odczyt danych z tabeli DatabricksAccounts |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Odczyt danych z tabeli DatabricksClusters |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Odczyt danych z tabeli DatabricksDBFS |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Odczyt danych z tabeli DatabricksJobs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Odczyt danych z tabeli DatabricksNotebook |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Odczyt danych z tabeli DatabricksSecrets |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Odczyt danych z tabeli DatabricksSQLPermissions |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Odczyt danych z tabeli DatabricksSSH |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Odczyt danych z tabeli DatabricksTables |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Odczyt danych z tabeli DatabricksWorkspace |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Odczyt danych z tabeli DeviceAppCrash |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Odczyt danych z tabeli DeviceAppLaunch |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Odczyt danych z tabeli DeviceCalendar |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Odczyt danych z tabeli DeviceCleanup |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Odczyt danych z tabeli DeviceConnectSession |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Odczyt danych z tabeli DeviceEtw |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Odczyt danych z tabeli DeviceHardwareHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Odczyt danych z tabeli kondycji urządzenia Zameldowaniem |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Odczyt danych z tabeli DeviceHeartbeat |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Odczyt danych z tabeli DeviceSkypeHeartbeat |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Odczyt danych z tabeli DeviceSkypeSignIn |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Odczyt danych z tabeli DeviceSleepState |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Odczyt danych z tabeli DHAppFailure |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Odczyt danych z tabeli DHAppReliability |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Odczyt danych z tabeli DHDriverReliability |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Odczyt danych z tabeli DHLogonFailures |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Odczyt danych z tabeli DHLogonMetrics |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Odczyt danych z tabeli DHOSCrashData |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Odczyt danych z tabeli DHOSReliability |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Odczyt danych z tabeli DHWipAppLearning |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Odczyt danych z tabeli DnsEvents |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Odczyt danych z tabeli DnsInventory |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Odczyt danych z tabeli ETWEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Event/read | Odczyt danych z tabeli zdarzeń |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Odczyt danych z tabeli ExchangeAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Odczyt danych z tabeli ExchangeOnlineAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Odczyt danych z tabeli pulsu |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Odczyt danych z tabeli HuntingBookmark |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Odczyt danych z tabeli IISAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Odczyt danych z tabeli InboundConnection |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Odczyt danych z tabeli InsightsMetrics |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Odczyt danych z tabeli IntuneAuditLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Odczyt danych z tabeli IntuneOperationalLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Odczyt danych z tabeli KubeEvents |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Odczyt danych z tabeli KubeNodeInventory |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Odczyt danych z tabeli KubePodInventory |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Odczyt danych z tabeli KubeServices |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Odczyt danych z tabeli LinuxAuditLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Odczyt danych z tabeli MAApplication |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Odczyt danych z tabeli MAApplicationHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Odczyt danych z tabeli MAApplicationHealthAlternativeVersions |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Odczyt danych z tabeli MAApplicationHealthIssues |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Odczyt danych z tabeli MAApplicationInstance |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Odczyt danych z tabeli MAApplicationInstanceReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Odczyt danych z tabeli MAApplicationReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Odczyt danych z tabeli MADeploymentPlan |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Odczyt danych z tabeli MADevice |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Odczyt danych z tabeli MADeviceNotEnrolled |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Odczyt danych z tabeli MADeviceNRT |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Odczyt danych z tabeli MADevicePnPHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Odczyt danych z tabeli MADevicePnPHealthAlternativeVersions |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Odczyt danych z tabeli MADevicePnPHealthIssues |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Odczyt danych z tabeli MADeviceReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Odczyt danych z tabeli MADriverInstanceReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Odczyt danych z tabeli MADriverReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Odczyt danych z tabeli MAOfficeAddin |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Odczyt danych z tabeli MAOfficeAddinEntityHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Odczyt danych z tabeli MAOfficeAddinHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Odczyt danych z tabeli MAOfficeAddinHealthEventNRT |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Odczyt danych z tabeli MAOfficeAddinHealthIssues |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Odczyt danych z tabeli MAOfficeAddinInstance |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Odczyt danych z tabeli MAOfficeAddinInstanceReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Odczyt danych z tabeli MAOfficeAddinReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Odczyt danych z tabeli MAOfficeApp |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Odczyt danych z tabeli MAOfficeAppCrashesNRT |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Odczyt danych z tabeli MAOfficeAppHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Odczyt danych z tabeli MAOfficeAppInstance |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Odczyt danych z tabeli MAOfficeAppInstanceHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Odczyt danych z tabeli MAOfficeAppReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Odczyt danych z tabeli MAOfficeAppSessionsNRT |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Odczyt danych z tabeli MAOfficeBuildInfo |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Odczyt danych z tabeli MAOfficeCurrencyAssessment |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Odczyt danych z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Odczyt danych z tabeli MAOfficeDeploymentStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Odczyt danych z tabeli MAOfficeDeploymentStatusNRT |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Odczyt danych z tabeli MAOfficeMacroErrorNRT |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Odczyt danych z tabeli MAOfficeMacroGlobalHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Odczyt danych z tabeli MAOfficeMacroHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Odczyt danych z tabeli MAOfficeMacroHealthIssues |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Odczyt danych z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Odczyt danych z tabeli MAOfficeMacroIssueReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Odczyt danych z tabeli MAOfficeMacroSummary |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Odczyt danych z tabeli MAOfficeSuite |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Odczyt danych z tabeli MAOfficeSuiteInstance |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Odczyt danych z tabeli MAProposedPilotDevices |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Odczyt danych z tabeli MAWindowsBuildInfo |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Odczyt danych z tabeli MAWindowsCurrencyAssessment |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Odczyt danych z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Odczyt danych z tabeli MAWindowsDeploymentStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Odczyt danych z tabeli MAWindowsDeploymentStatusNRT |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Odczyt danych z tabeli MAWindowsSysReqInstanceReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebApplicationLog/read | Odczyt danych z tabeli MicrosoftWebApplicationLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebFunctionExecutionLogs/read | Odczyt danych z tabeli MicrosoftWebFunctionExecutionLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Odczyt danych z tabeli MicrosoftWebStdOutStdErrLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebW3CLog/read | Odczyt danych z tabeli MicrosoftWebW3CLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Odczyt danych z tabeli NetworkMonitoring |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Odczyt danych z tabeli OfficeActivity |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Operation/read | Odczyt danych z tabeli operacji |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Odczyt danych z tabeli OutboundConnection |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Perf/read | Odczyt danych z tabeli danych o wydajności |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Odczyt danych z tabeli ProtectionStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/read | Uruchamianie zapytań względem danych w obszarze roboczym |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Odczyt danych z tabeli ReservedAzureCommonFields |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Odczyt danych z tabeli ReservedCommonFields |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Odczyt danych z tabeli SCCMAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Odczyt danych z tabeli SCOMAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Odczyt danych z tabeli SecurityAlert |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Odczyt danych z tabeli SecurityBaseline |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Odczyt danych z tabeli SecurityBaselineSummary |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Odczyt danych z tabeli SecurityDetection |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Odczyt danych z tabeli SecurityEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Odczyt danych z tabeli SecurityIoTRawEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Odczyt danych z tabeli SecurityRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Odczyt danych z tabeli ServiceFabricOperationalEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Odczyt danych z tabeli ServiceFabricReliableActorEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Odczyt danych z tabeli ServiceFabricReliableServiceEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Odczyt danych z tabeli SfBAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Odczyt danych z tabeli SfBOnlineAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Odczyt danych z tabeli SharePointOnlineAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Odczyt danych z tabeli SigninLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Odczyt danych z tabeli SPAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Odczyt danych z tabeli SQLAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Odczyt danych z tabeli SQLQueryPerformance |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Odczyt danych z tabeli SqlThreatProtectionLoginAudits |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Odczyt danych z tabeli SqlVulnerabilityAssessmentResult |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Odczyt danych z tabeli dziennika systemu |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Odczyt danych z tabeli SysmonEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Odczytywanie danych z żadnych dzienników niestandardowych |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Odczyt danych z tabeli ThreatIntelligenceIndicator |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Odczyt danych z tabeli UAApp |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Odczyt danych z tabeli UAComputer |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Odczyt danych z tabeli UAComputerRank |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Odczyt danych z tabeli UADriver |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Odczyt danych z tabeli UADriverProblemCodes |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Odczyt danych z tabeli UAFeedback |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Odczyt danych z tabeli UAHardwareSecurity |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Odczyt danych z tabeli UAIESiteDiscovery |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Odczyt danych z tabeli UAOfficeAddIn |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Odczyt danych z tabeli UAProposedActionPlan |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Odczyt danych z tabeli UASysReqIssue |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Odczyt danych z tabeli UAUpgradedComputer |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Update/read | Odczytywanie danych z tabeli aktualizacji |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Odczyt danych z tabeli UpdateRunProgress |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Odczyt danych z tabeli UpdateSummary |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Usage/read | Odczytywanie danych z tabeli użycia |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Odczyt danych z tabeli VMBoundPort |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Odczyt danych z tabeli VMConnection |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Odczyt danych z tabeli W3CIISLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Odczyt danych z tabeli WaaSDeploymentStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Odczyt danych z tabeli WaaSInsiderStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Odczyt danych z tabeli WaaSUpdateStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Odczyt danych z tabeli WDAVStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Odczyt danych z tabeli WDAVThreat |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Odczyt danych z tabeli WindowsClientAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Odczyt danych z tabeli WindowsEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Odczyt danych z tabeli WindowsFirewall |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Odczyt danych z tabeli WindowsServerAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WireData/read | Odczyt danych z tabeli danych o komunikacji sieciowej |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Odczyt danych z tabeli WorkloadMonitoringPerf |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Odczyt danych z tabeli WUDOAggregatedStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Odczyt danych z tabeli WUDOStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/read | Pobiera istniejący obszar roboczy |
> | Akcja | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Ponownie generuje klucz współużytkowany określony obszar roboczy |
> | Akcja | microsoft.operationalinsights/workspaces/rules/read | Pobieranie wszystkich reguł alertów. |
> | Akcja | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Usuwa zapisane zapytanie wyszukiwania |
> | Akcja | Microsoft.OperationalInsights/workspaces/savedSearches/read | Pobiera zapisane zapytanie wyszukiwania |
> | Akcja | microsoft.operationalinsights/workspaces/savedsearches/results/read | Pobierz zapisać wyniki wyszukiwania. Przestarzałe |
> | Akcja | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Usuwanie zaplanowanego wyszukiwania akcji. |
> | Akcja | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Pobierz akcje wyszukiwania według harmonogramu. |
> | Akcja | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Utwórz lub zaktualizuj operacje wyszukiwania według harmonogramu. |
> | Akcja | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Usuwanie zaplanowanych wyszukiwań. |
> | Akcja | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Uzyskaj zaplanowanych wyszukiwań. |
> | Akcja | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Utwórz lub zaktualizuj zaplanowanych wyszukiwań. |
> | Akcja | Microsoft.OperationalInsights/workspaces/savedSearches/write | Tworzy zapytanie zapisanego wyszukiwania |
> | Akcja | Microsoft.OperationalInsights/workspaces/schema/read | Pobiera schemat wyszukiwania dla obszaru roboczego.  Schemat wyszukiwania zawiera widoczne pola i ich typy. |
> | Akcja | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Akcja | microsoft.operationalinsights/workspaces/search/read | Uzyskiwanie wyników wyszukiwania. Przestarzałe. |
> | Akcja | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Pobiera klucze wspólne dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Pobiera klucze wspólne dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Usuwa konfigurację magazynu. Spowoduje to zatrzymanie usługi Microsoft Operational Insights na podstawie odczytu danych z konta magazynu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Pobiera konfigurację magazynu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Tworzy nową konfigurację magazynu. Te konfiguracje są używane do pobierania danych z lokalizacji na istniejące konto magazynu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Pobierz dziennik błędów tłumaczenia uaktualnienia wyszukiwania dla obszaru roboczego |
> | Akcja | Microsoft.OperationalInsights/workspaces/usages/read | Pobiera dane użycia dla obszaru roboczego, w tym ilość danych odczytanych przez obszar roboczy. |
> | Akcja | Microsoft.OperationalInsights/workspaces/write | Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącym obszarem roboczym. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.OperationsManagement/managementAssociations/delete | Usuń istniejące skojarzenie zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementAssociations/read | Pobieranie istniejącego skojarzenia zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementAssociations/write | Utwórz nowe skojarzenie zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementConfigurations/delete | Usuń istniejącą konfigurację zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementConfigurations/read | Pobieranie istniejącej konfiguracji zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementConfigurations/write | Utwórz nową konfigurację zarządzania |
> | Akcja | Microsoft.OperationsManagement/register/action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Akcja | Microsoft.OperationsManagement/solutions/delete | Usuń istniejące rozwiązanie OMS |
> | Akcja | Microsoft.OperationsManagement/solutions/read | Pobierz istniejące rozwiązanie OMS |
> | Akcja | Microsoft.OperationsManagement/solutions/write | Utwórz nowe rozwiązanie OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.PolicyInsights/asyncOperationResults/read | Pobiera wynik operacji asynchronicznej. |
> | Akcja | Microsoft.PolicyInsights/operations/read | Pobiera obsługiwane operacje na Microsoft.PolicyInsights w przestrzeni nazw |
> | Akcja | Microsoft.PolicyInsights/policyEvents/queryResults/action | Badanie informacji dotyczących zdarzenia dotyczące zasad. |
> | Akcja | Microsoft.PolicyInsights/policyEvents/queryResults/read | Badanie informacji dotyczących zdarzenia dotyczące zasad. |
> | Akcja | Microsoft.PolicyInsights/policyStates/queryResults/action | Kwerendy informacji na temat zasady stanów. |
> | Akcja | Microsoft.PolicyInsights/policyStates/queryResults/read | Kwerendy informacji na temat zasady stanów. |
> | Akcja | Microsoft.PolicyInsights/policyStates/summarize/action | Zapytania podsumowanie informacji na temat stanów najnowsze zasady. |
> | Akcja | Microsoft.PolicyInsights/policyStates/summarize/read | Zapytania podsumowanie informacji na temat stanów najnowsze zasady. |
> | Akcja | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Wyzwala nowy oceny zgodności dla wybranego zakresu. |
> | Akcja | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Badanie informacji dotyczących zasobów wymaganych przez zasady DeployIfNotExists. |
> | Akcja | Microsoft.PolicyInsights/register/action | Rejestruje dostawcę zasobów szczegółowych informacji o zasadach firmy Microsoft i umożliwia operacje na nim. |
> | Akcja | Microsoft.PolicyInsights/remediations/cancel/action | Anuluj korygowania funkcję Microsoft Policy w toku. |
> | Akcja | Microsoft.PolicyInsights/remediations/delete | Usuń zasady korygowania funkcję. |
> | Akcja | Microsoft.PolicyInsights/remediations/listDeployments/read | Wyświetla listę wdrożeń wymagane przez korygowanie zasad. |
> | Akcja | Microsoft.PolicyInsights/remediations/read | Uzyskaj korygowania funkcję zasad. |
> | Akcja | Microsoft.PolicyInsights/remediations/write | Utwórz lub zaktualizuj korygowania Microsoft Policy funkcję. |
> | Akcja | Microsoft.PolicyInsights/unregister/action | Wyrejestrowuje dostawcę zasobów szczegółowych informacji o zasadach firmy Microsoft. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Portal/consoles/delete | Usuwa wystąpienie usługi Cloud Shell. |
> | Akcja | Microsoft.Portal/consoles/read | Odczytuje wystąpienia usługi Cloud Shell. |
> | Akcja | Microsoft.Portal/consoles/write | Utwórz lub zaktualizuj wystąpienia usługi Cloud Shell. |
> | Akcja | Microsoft.Portal/dashboards/delete | Usuwa pulpit nawigacyjny z subskrypcji. |
> | Akcja | Microsoft.Portal/dashboards/read | Odczytuje pulpity nawigacyjne z subskrypcji. |
> | Akcja | Microsoft.Portal/dashboards/write | Dodaje lub modyfikuje pulpit nawigacyjny do subskrypcji. |
> | Akcja | Microsoft.Portal/register/action | Zarejestruj się w portalu |
> | Akcja | Microsoft.Portal/usersettings/delete | Usuwa ustawienia użytkownika usługi Cloud Shell. |
> | Akcja | Microsoft.Portal/usersettings/read | Odczytuje ustawienia użytkownika usługi Cloud Shell. |
> | Akcja | Microsoft.Portal/usersettings/write | Utwórz lub zaktualizuj ustawienia użytkownika w usłudze Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.PowerBIDedicated/capacities/delete | Usuwa usługi Power BI w wersji dedykowanej pojemności. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/read | Pobiera informacje o określonej pojemności usługi Power BI w wersji dedykowanej. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/resume/action | Wznawia pojemności. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/skus/read | Pobierz dostępne informacje o jednostce SKU pojemności |
> | Akcja | Microsoft.PowerBIDedicated/capacities/suspend/action | Wstrzymuje działanie pojemności. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/write | Tworzy lub aktualizuje określoną w wersji dedykowanej pojemności usługi Power BI. |
> | Akcja | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Sprawdza, czy nazwa podana w wersji dedykowanej pojemności usługi Power BI jest prawidłowa i nie jest używany. |
> | Akcja | Microsoft.PowerBIDedicated/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | Akcja | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | Akcja | Microsoft.PowerBIDedicated/operations/read | Pobiera informacje o operacjach |
> | Akcja | Microsoft.PowerBIDedicated/register/action | Rejestruje dostawcę zasobów usługi Power BI w wersji dedykowanej. |
> | Akcja | Microsoft.PowerBIDedicated/skus/read | Pobiera informacje o jednostkach SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | Akcja | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest wewnętrzną operacją używaną przez usługę |
> | Akcja | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | Akcja | microsoft.recoveryservices/Locations/backupProtectedItem/write | Utwórz chroniony element kopii zapasowej |
> | Akcja | microsoft.recoveryservices/Locations/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Akcja | microsoft.recoveryservices/Locations/backupStatus/action | Sprawdź stan kopii zapasowej dla magazynów usługi Recovery Services |
> | Akcja | microsoft.recoveryservices/Locations/backupValidateFeatures/action | Weryfikuj funkcje |
> | Akcja | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Sprawdź dostępność nazwy zasobu to interfejs API, aby sprawdzić, czy nazwa zasobu jest dostępna |
> | Akcja | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Akcja | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Akcja | Microsoft.RecoveryServices/register/action | Rejestry subskrypcji dla podanego dostawcy zasobów |
> | Akcja | microsoft.recoveryservices/Vaults/backupconfig/read | Zwraca konfigurację dla odzyskiwania usług magazynu. |
> | Akcja | microsoft.recoveryservices/Vaults/backupconfig/write | Aktualizuje konfigurację odzyskiwania usług magazynu. |
> | Akcja | microsoft.recoveryservices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/delete | Usuń opcję ochrony kopii zapasowej |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/read | Pobierz opcję ochrony kopii zapasowej |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | Utwórz opcję ochrony kopii zapasowej |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery z możliwością objęcia ochroną |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | Usuwa zarejestrowany kontener |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Wykonuje kopię zapasową elementu chronionego. |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Usuwa chroniony element |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Ustanów natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Przywróć punkty odzyskiwania dla elementów chronionych. |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Wycofaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Utwórz chroniony element kopii zapasowej |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | Tworzy zarejestrowany kontener |
> | Akcja | microsoft.recoveryservices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | Akcja | microsoft.recoveryservices/Vaults/backupJobs/cancel/action | Anuluj zadanie |
> | Akcja | microsoft.recoveryservices/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zadania. |
> | Akcja | microsoft.recoveryservices/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadań |
> | Akcja | microsoft.recoveryservices/Vaults/backupJobsExport/action | Eksportuj zadania |
> | Akcja | microsoft.recoveryservices/Vaults/backupOperationResults/read | Zwraca wynik operacji tworzenia kopii zapasowej magazynu usług Recovery Services. |
> | Akcja | microsoft.recoveryservices/Vaults/backupOperations/read | Zwraca operacji tworzenia kopii zapasowej odzyskiwania stanu usługi magazynu. |
> | Akcja | microsoft.recoveryservices/Vaults/backupPolicies/delete | Usuwa zasady ochrony |
> | Akcja | microsoft.recoveryservices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | Akcja | microsoft.recoveryservices/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | Akcja | microsoft.recoveryservices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Akcja | microsoft.recoveryservices/Vaults/backupPolicies/write | Tworzy zasady ochrony |
> | Akcja | microsoft.recoveryservices/Vaults/backupProtectableItems/read | Zwraca listę wszystkich elementów podlegających ochronie |
> | Akcja | microsoft.recoveryservices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Akcja | microsoft.recoveryservices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | Akcja | microsoft.recoveryservices/Vaults/backupProtectionIntents/read | Listę wszystkich kopii zapasowych opcjami ochrony |
> | Akcja | microsoft.recoveryservices/Vaults/backupSecurityPIN/action | Zabezpieczający numer PIN zwraca informacje dotyczące odzyskiwania usług magazynu. |
> | Akcja | microsoft.recoveryservices/Vaults/backupstorageconfig/read | Zwraca konfigurację magazynu dla odzyskiwania usług magazynu. |
> | Akcja | microsoft.recoveryservices/Vaults/backupstorageconfig/write | Aktualizuje konfigurację magazynu dla odzyskiwania usług magazynu. |
> | Akcja | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dotyczące chronionych elementów i serwerów chronionych usług Recovery Services. |
> | Akcja | microsoft.recoveryservices/Vaults/backupValidateOperation/action | Weryfikowanie operacji chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/delete | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "Magazyn" |
> | Akcja | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Pobiera konfigurację powiadomień magazynu usługi Recovery services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfiguruje powiadomienia pocztą e-mail do magazynu usługi Recovery services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Operacja Wyrejestruj kontener umożliwia wyrejestrowanie kontenera. |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyników operacji przesłanej asynchronicznie |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Pobierz kontenery operacji umożliwia pobranie kontenerów zarejestrowanych dla zasobu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operacja Rejestruj kontener usługi umożliwia rejestrowanie kontenera za pomocą usługi odzyskiwania. |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Odczytuje żadnych ustawień alertów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Utwórz lub zaktualizuj wszelkie ustawienia alertów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczyt zdarzeń |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność sieci szkieletowej |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Usuń wszystkie sieci szkieletowe |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Wdróż obraz serwera przetwarzania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrowanie sieci szkieletowej do usługi AAD |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Przeczytaj żadnych sieci szkieletowych |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skojarz ponownie bramę |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Usuń sieć szkieletową |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Odnów certyfikat sieci szkieletowej |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Przeczytaj sieci logicznych |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Odczyt wszystkich sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Usuń wszystkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytywać wszystkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Utwórz lub zaktualizuj wszelkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Odnajdywanie elementów podlegających ochronie |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytywać wszystkie kontenery ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Usuń kontener ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Usuń wszystkie elementy migracji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrowanie elementu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Odczytywać wszystkie punkty odzyskiwania migracji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Odczytuj wszystkie elementy migracji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Migrowanie testów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Test Migrowanie oczyszczania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Utwórz lub zaktualizuj wszystkie elementy migracji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczyt wszystkich elementów podlegających ochronie |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Dodawanie dysków |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Zastosuj punkt odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Usuń wszystkie chronione elementy |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover Commit |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowany tryb Failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczyt wszystkich chronionych elementów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytywać wszystkie punkty odzyskiwania replikacji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Usuń chroniony element |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Usuwanie dysków |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Napraw replikację |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Włącz ponownie ochronę chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Prześlij opinię |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Przeczytaj dowolnej docelowej rozmiarów wystąpień obliczeniowych |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testowanie trybu Failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Czyszczenie testowego przełączania do trybu Failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizacja usługi mobilności |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Utwórz lub zaktualizuj wszystkie chronione elementy |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Usuń wszystkie mapowania kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytywać wszystkie mapowania kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Usuń mapowanie kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Utwórz lub zaktualizuj wszelkie mapowania kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Przełącz kontener ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Utwórz lub zaktualizuj wszelkie kontenery ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Usuwanie wszystkich dostawców usług odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczyt wszystkich dostawców usług odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Odśwież dostawcę |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Usuń dostawcę usługi Recovery Services |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Utwórz lub zaktualizuj wszelkie dostawców usług odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytywać wszystkie klasyfikacje magazynów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Usuń wszystkie mapowania klasyfikacji magazynów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytywać wszystkie mapowania klasyfikacji magazynów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Utwórz lub zaktualizuj wszelkie mapowania klasyfikacji magazynów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Usuń wszelkie vCenters |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Przeczytaj wszelkie vCenters |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Utwórz lub zaktualizuj wszelkie vCenters |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Utwórz lub zaktualizuj wszystkie sieci szkieletowe |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Anulowanie zadania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/read | Odczytać wszystkie zadania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Uruchom ponownie zadanie |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Wznów zadanie |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Odczytuj wszystkie elementy migracji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Odczytywać wszystkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Odczyt wszystkich sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Usuń wszystkie zasady |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Odczytywać wszystkie zasady |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Utwórz lub zaktualizuj wszystkie zasady |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Odczyt wszystkich chronionych elementów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Odczytywać wszystkie mapowania kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Odczytywać wszystkie kontenery ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Usuń wszelkie plany odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan odzyskiwania zatwierdzania trybu failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan planowanego trybu Failover odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Odczyt wszystkich planów odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Ponownie włączyć ochronę planu odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan odzyskiwania do trybu Failover testu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan odzyskiwania systemu testowego przełączania w tryb Failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania do trybu failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Utwórz lub zaktualizuj wszelkie plany odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Odczyt wszystkich dostawców usług odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Odczytywać wszystkie mapowania klasyfikacji magazynów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Odczytywać wszystkie klasyfikacje magazynów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Odczytać dowolny  |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationUsages/read | Przeczytaj wszelkie użycia replikacji magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Przeczytaj wszelkie kondycję replikacji magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Odśwież kondycję magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Odczytać dowolny  |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Utwórz lub zaktualizuj dowolny  |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Przeczytaj wszelkie vCenters |
> | Akcja | microsoft.recoveryservices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/vaults/usages/read | Przeczytaj wszelkie użycia magazynu |
> | Akcja | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja Token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Relay/checkNameAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.Relay/checkNamespaceAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailability. |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/action | Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest przestarzały. Można zaktualizować reguły autoryzacji Namespace zamiast niego użyj wywołania PUT... Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć domyślnej reguły autoryzacji Namespace.  |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia Namespace |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Wygeneruj ponownie klucz podstawowy lub pomocniczy do zasobu |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie Namespace i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | Akcja | Microsoft.Relay/namespaces/Delete | Usuwanie zasobu Namespace |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobiera autoryzacji kluczy reguły dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji awaryjnego odzyskiwania podstawowego Namespace firmy |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. Tę operację można wywołać tylko za pomocą podstawowej przestrzeni nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operacja aktualizowania HybridConnection. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operacja usuwania reguł autoryzacji HybridConnection |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Pobierz parametry połączenia HybridConnection |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Pobierz listę reguł autoryzacji HybridConnection |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Wygeneruj ponownie klucz podstawowy lub pomocniczy do zasobu |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Utwórz reguły autoryzacji HybridConnection i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/Delete | Operacja usuwania zasobu HybridConnection |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/read | Pobierz listę opisów zasobów HybridConnection |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/write | Tworzenie lub aktualizacja HybridConnection właściwości. |
> | Akcja | Microsoft.Relay/namespaces/messagingPlan/read | Pobiera Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.Relay/namespaces/messagingPlan/write | Aktualizuje Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.Relay/namespaces/operationresults/read | Pobierz stan operacji Namespace |
> | Akcja | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryk Namespace opisów zasobów |
> | Akcja | Microsoft.Relay/namespaces/read | Pobierz listę opisów zasobów Namespace |
> | Akcja | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Usuwanie przestrzeni nazw usługi ACS |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operacja aktualizowania WcfRelay. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operacja usuwania reguł autoryzacji WcfRelay |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Pobierz parametry połączenia WcfRelay |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Pobierz listę reguł autoryzacji WcfRelay |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Wygeneruj ponownie klucz podstawowy lub pomocniczy do zasobu |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Utwórz reguły autoryzacji WcfRelay i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/Delete | Operacja usuwania zasobu WcfRelay |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/read | Pobierz listę opisów zasobów WcfRelay |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/write | Tworzenie lub aktualizacja WcfRelay właściwości. |
> | Akcja | Microsoft.Relay/namespaces/write | Tworzenie zasobu Namespace i zaktualizuj jego właściwości. Tagi i pojemność Namespace są właściwości, które mogą być aktualizowane. |
> | Akcja | Microsoft.Relay/operations/read | Pobierz operacje |
> | Akcja | Microsoft.Relay/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i włącza funkcję tworzenia zasobów usługi Relay |
> | Akcja | Microsoft.Relay/unregister/action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i włącza funkcję tworzenia zasobów usługi Relay |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Pobiera stan dostępności określonego zasobu |
> | Akcja | Microsoft.ResourceHealth/AvailabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Akcja | Microsoft.ResourceHealth/events/read | Pobierz zdarzenia usługi Service Health dla danej subskrypcji |
> | Akcja | Microsoft.Resourcehealth/healthevent/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft.Resourcehealth/healthevent/Activated/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft.Resourcehealth/healthevent/InProgress/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft.Resourcehealth/healthevent/Pending/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft.Resourcehealth/healthevent/Resolved/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft.Resourcehealth/healthevent/Updated/action | Oznacza zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft.ResourceHealth/impactedResources/read | Pobierz zasoby objęte wpływem dla danej subskrypcji |
> | Akcja | Microsoft.ResourceHealth/metadata/read | Pobiera metadane |
> | Akcja | Microsoft.ResourceHealth/Operations/read | Pobierz operacje dostępne dla ResourceHealth firmy Microsoft |
> | Akcja | Microsoft.ResourceHealth/register/action | Rejestruje subskrypcję ResourceHealth firmy Microsoft |
> | Akcja | Microsoft.ResourceHealth/unregister/action | Wyrejestrowuje subskrypcję ResourceHealth firmy Microsoft |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Resources/checkPolicyCompliance/action | Sprawdź stan zgodności danego zasobu wobec zasad zasobów. |
> | Akcja | Microsoft.Resources/checkResourceName/action | Sprawdź poprawność nazwy zasobu. |
> | Akcja | Microsoft.Resources/deployments/cancel/action | Anuluje wdrożenie. |
> | Akcja | Microsoft.Resources/deployments/delete | Usuwa wdrożenie. |
> | Akcja | Microsoft.Resources/deployments/operations/read | Pobiera lub wyświetla ich listę operacji wdrażania. |
> | Akcja | Microsoft.Resources/deployments/read | Pobiera lub wyświetla ich listę wdrożeń. |
> | Akcja | Microsoft.Resources/deployments/validate/action | Weryfikuje wdrożenie. |
> | Akcja | Microsoft.Resources/deployments/whatIf/action | Prognozuje zmiany do wdrożenia szablonu. |
> | Akcja | Microsoft.Resources/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft.Resources/links/delete | Usuwa link do zasobu. |
> | Akcja | Microsoft.Resources/links/read | Pobiera lub zawiera linki do zasobów. |
> | Akcja | Microsoft.Resources/links/write | Tworzy lub aktualizuje link do zasobu. |
> | Akcja | Microsoft.Resources/marketplace/purchase/action | Pozwala kupić zasób w portalu Marketplace. |
> | Akcja | Microsoft.Resources/providers/read | Pobierz listę dostawców. |
> | Akcja | Microsoft.Resources/resources/read | Pobierz listę zasobów w oparciu o filtry. |
> | Akcja | Microsoft.Resources/subscriptions/locations/read | Pobiera listę obsługiwanych lokalizacji. |
> | Akcja | Microsoft.Resources/subscriptions/operationresults/read | Uzyskaj subskrypcję wyniki operacji. |
> | Akcja | Microsoft.Resources/subscriptions/providers/read | Pobiera lub wyświetla ich listę dostawców zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/read | Pobiera listę subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/delete | Usuwa grupę zasobów i wszystkie jej zasoby. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Pobiera lub wyświetla ich listę operacji wdrażania. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Pobiera lub wyświetla ich listę stany operacji wdrażania. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Pobiera lub wyświetla ich listę wdrożeń. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Przenosi zasoby z jednej grupy zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Pobiera zasoby grupy zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Waliduj przeniesienie zasobów między grupami zasobów do innej. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/write | Tworzy lub aktualizuje grupę zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/resources/read | Pobiera zasoby subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/delete | Usuwa tag subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/read | Pobiera lub wyświetla ich listę tagów subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Usuwa wartość tagu subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Pobiera lub wyświetla ich listę wartości tagów. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Dodaje wartość tagu subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/write | Dodaje tag subskrypcji. |
> | Akcja | Microsoft.Resources/tags/delete | Usuwa wszystkie tagi do zasobu. |
> | Akcja | Microsoft.Resources/tags/read | Pobiera wszystkie tagi do zasobu. |
> | Akcja | Microsoft.Resources/tags/write | Aktualizuje tagi do zasobu, zastępowanie lub scalanie istniejące tagi z nowym zestawem tagów lub usuwając istniejące tagi. |
> | Akcja | Microsoft.Resources/tenants/read | Pobiera listę dzierżawców. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Scheduler/jobcollections/delete | Usuwa kolekcję zadań. |
> | Akcja | Microsoft.Scheduler/jobcollections/disable/action | Wyłącza kolekcję zadań. |
> | Akcja | Microsoft.Scheduler/jobcollections/enable/action | Włącza kolekcję zadań. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/delete | Usuwa zadanie. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Generuje definicję aplikacji logiki na podstawie zadania usługi Scheduler. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Pobiera historię zadania. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/read | Pobiera zadanie. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/run/action | Uruchamia zadanie. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/write | Tworzy lub aktualizuje zadanie. |
> | Akcja | Microsoft.Scheduler/jobcollections/read | Pobierz kolekcję zadań |
> | Akcja | Microsoft.Scheduler/jobcollections/write | Tworzy lub aktualizuje kolekcję zadań. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Search/checkNameAvailability/action | Służy do sprawdzania dostępności nazwy usługi. |
> | Akcja | Microsoft.Search/operations/read | Wyświetla listę wszystkich dostępnych operacji dostawcy Microsoft.Search. |
> | Akcja | Microsoft.Search/register/action | Rejestruje subskrypcję dostawcy zasobów wyszukiwania i umożliwia tworzenie usługi wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/createQueryKey/action | Tworzy klucz zapytania. |
> | Akcja | Microsoft.Search/searchServices/delete | Usuwa usługę wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/deleteQueryKey/delete | Usuwa klucz zapytania. |
> | Akcja | Microsoft.Search/searchServices/listAdminKeys/action | Odczytuje klucze administratora. |
> | Akcja | Microsoft.Search/searchServices/listQueryKeys/read | Zwraca listę kluczy interfejsu API zapytań dla danej usługi Azure Search. |
> | Akcja | Microsoft.Search/searchServices/read | Odczytuje usługi wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/regenerateAdminKey/action | Ponownie generuje klucz administratora. |
> | Akcja | Microsoft.Search/searchServices/start/action | Uruchamia usługę wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/stop/action | Zatrzymuje usługę wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/write | Tworzy lub aktualizuje usługę wyszukiwania. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Wymusza ruch danego wzmacniania ochrony reguły przez utworzenie reguł zabezpieczeń dopasowania na danym sieciowe grupy zabezpieczeń |
> | Akcja | Microsoft.Security/adaptiveNetworkHardenings/read | Pobiera zalecenia adaptacyjne ograniczania funkcjonalności sieci platformy Azure chronionych zasobów |
> | Akcja | Microsoft.Security/advancedThreatProtectionSettings/read | Pobiera Zaawansowane ustawienia ochrony przed zagrożeniami dla zasobu |
> | Akcja | Microsoft.Security/advancedThreatProtectionSettings/write | Aktualizuje Zaawansowane ustawienia ochrony przed zagrożeniami dla zasobu |
> | Akcja | Microsoft.Security/alerts/read | Pobiera wszystkie alerty zabezpieczeń dostępne |
> | Akcja | Microsoft.Security/applicationWhitelistings/read | Pobiera whitelistings aplikacji |
> | Akcja | Microsoft.Security/applicationWhitelistings/write | Tworzy nowe listy dozwolonych aplikacji lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Security/complianceResults/read | Pobiera wyniki sprawdzania zgodności dla zasobu |
> | Akcja | Microsoft.Security/informationProtectionPolicies/read | Pobiera informacje o zasadach ochrony dla zasobu |
> | Akcja | Microsoft.Security/informationProtectionPolicies/write | Aktualizacje z zasadami ochrony informacji dla zasobu |
> | Akcja | Microsoft.Security/locations/alerts/activate/action | Uaktywnij alert zabezpieczeń |
> | Akcja | Microsoft.Security/locations/alerts/dismiss/action | Odrzuć alert zabezpieczeń |
> | Akcja | Microsoft.Security/locations/alerts/read | Pobiera wszystkie alerty zabezpieczeń dostępne |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Usuwa zasady dostępu do sieci just-in-time |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicjuje żądanie zasad dostępu just in time sieci |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Pobiera zasady dostępu just in time sieci |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Tworzy nowe zasady dostępu do sieci just in time lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Security/locations/read | Pobiera lokalizację danych zabezpieczeń |
> | Akcja | Microsoft.Security/locations/tasks/activate/action | Aktywuj zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft.Security/locations/tasks/dismiss/action | Odrzuć zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft.Security/locations/tasks/read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Akcja | Microsoft.Security/locations/tasks/resolve/action | Rozwiąż zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft.Security/locations/tasks/start/action | Rozpocznij zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft.Security/policies/read | Pobiera zasady zabezpieczeń |
> | Akcja | Microsoft.Security/policies/write | Aktualizuje zasady zabezpieczeń |
> | Akcja | Microsoft.Security/pricings/delete | Usuwa ustawienia cen dla zakresu |
> | Akcja | Microsoft.Security/pricings/read | Pobiera ustawienia cen dla zakresu |
> | Akcja | Microsoft.Security/pricings/write | Aktualizacje cen ustawienia dla zakresu |
> | Akcja | Microsoft.Security/register/action | Rejestruje subskrypcję dla usługi Azure Security Center |
> | Akcja | Microsoft.Security/securityContacts/delete | Usuwa kontakt zabezpieczeń |
> | Akcja | Microsoft.Security/securityContacts/read | Pobiera kontaktu zabezpieczeń |
> | Akcja | Microsoft.Security/securityContacts/write | Aktualizacje zabezpieczeń skontaktuj się z |
> | Akcja | Microsoft.Security/securitySolutions/delete | Usuwa rozwiązanie w zakresie zabezpieczeń |
> | Akcja | Microsoft.Security/securitySolutions/read | Pobiera rozwiązania zabezpieczeń |
> | Akcja | Microsoft.Security/securitySolutions/write | Tworzy nowe rozwiązanie zabezpieczeń lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Security/securitySolutionsReferenceData/read | Pobiera dane referencyjne rozwiązania zabezpieczeń |
> | Akcja | Microsoft.Security/securityStatuses/read | Pobiera stany kondycji zabezpieczeń zasobów platformy Azure |
> | Akcja | Microsoft.Security/securityStatusesSummaries/read | Pobiera podsumowania stanów w zakresie zabezpieczeń |
> | Akcja | Microsoft.Security/settings/read | Pobiera ustawienia dla zakresu |
> | Akcja | Microsoft.Security/settings/write | Aktualizuje ustawienia dla zakresu |
> | Akcja | Microsoft.Security/tasks/read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Akcja | Microsoft.Security/unregister/action | Wyrejestrowuje subskrypcję w usłudze Azure Security Center |
> | Akcja | Microsoft.Security/webApplicationFirewalls/delete | Usuwa zapory aplikacji sieci web |
> | Akcja | Microsoft.Security/webApplicationFirewalls/read | Pobiera zapory aplikacji sieci web |
> | Akcja | Microsoft.Security/webApplicationFirewalls/write | Nowa Zapora aplikacji sieci web tworzy lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Security/workspaceSettings/connect/action | Zmienianie obszaru roboczego ustawienia ponownego łączenia |
> | Akcja | Microsoft.Security/workspaceSettings/delete | Usuwa ustawienia obszaru roboczego |
> | Akcja | Microsoft.Security/workspaceSettings/read | Pobiera ustawienia obszaru roboczego |
> | Akcja | Microsoft.Security/workspaceSettings/write | Aktualizuje ustawienia obszaru roboczego |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettings/delete | Usuwanie ustawienia diagnostyczne |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettings/read | Odczytywanie ustawienie diagnostyczne |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettings/write | Zapisywanie ustawienia diagnostyczne |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Odczytywanie kategorii ustawienie diagnostyczne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ServiceBus/checkNameAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.ServiceBus/checkNamespaceAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailability. |
> | Akcja | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Usuwa reguły sieci wirtualnej dostawcy zasobów usługi ServiceBus dla określonej sieci wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/action | Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest przestarzały. Można zaktualizować reguły autoryzacji Namespace zamiast niego użyj wywołania PUT... Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć domyślnej reguły autoryzacji Namespace.  |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia Namespace |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Wygeneruj ponownie klucz podstawowy lub pomocniczy do zasobu |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie Namespace i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji, podstawowe i pomocnicze klucze. |
> | Akcja | Microsoft.ServiceBus/namespaces/Delete | Usuwanie zasobu Namespace |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobiera autoryzacji kluczy reguły dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji awaryjnego odzyskiwania podstawowego Namespace firmy |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. Tę operację można wywołać tylko za pomocą podstawowej przestrzeni nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii, skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Usuwa filtr usługi Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Pobiera filtr usługi Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Tworzy lub aktualizuje filtr usługi Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventhubs/read | Pobierz listę opisów zasobów Centrum zdarzeń |
> | Akcja | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Usuń zasób filtru IP |
> | Akcja | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Pobierz zasób filtru IP |
> | Akcja | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Utwórz zasób filtru IP |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/browse/action | Przeglądaj wiadmości |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/defer/action | Odroczenie wiadomości |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Odbieranie komunikatów |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/schedule/action | Shedule wiadomości |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Wysyłanie komunikatów |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/setstate/action | Ustaw stan sesji |
> | Akcja | Microsoft.ServiceBus/namespaces/messagingPlan/read | Pobiera Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.ServiceBus/namespaces/messagingPlan/write | Aktualizuje Plan obsługi wiadomości dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane przez zasób MessagingPlan są przenoszone do (nadrzędnego) zasobu Namespace w nowszych wersjach interfejsu API...<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.ServiceBus/namespaces/migrate/action | Operacja migracji przestrzeni nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Usuwa konfigurację migracji. |
> | Akcja | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Pobiera konfigurację migracji, które wskazuje stan migracji i oczekujących operacji replikacji |
> | Akcja | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Przywraca standard do migracji przestrzeni nazw w warstwie premium |
> | Akcja | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Przypisuje DNS skojarzone z standardową przestrzeń nazw do przestrzeni nazw premium, która ukończenia migracji i zatrzymuje synchronizacji zasobów z warstwy standardowa do przestrzeni nazw w warstwie premium |
> | Akcja | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Tworzy lub migracji aktualizacje konfiguracji. Spowoduje to uruchomienie synchronizacji zasobów od planu standard do przestrzeni nazw premium |
> | Akcja | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Usuwanie zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Akcja | Microsoft.ServiceBus/namespaces/networkrulesets/write | Tworzenie zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/operationresults/read | Pobierz stan operacji Namespace |
> | Akcja | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz listę opisów zasobów ustawień diagnostycznych Namespace |
> | Akcja | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Pobierz listę opisów zasobów ustawień diagnostycznych Namespace |
> | Akcja | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Pobierz listę dzienników Namespace opisów zasobów |
> | Akcja | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryk Namespace opisów zasobów |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operacja zaktualizowania kolejki. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operacja usuwania reguł autoryzacji kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Pobieranie parametrów połączenia do kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Pobierz listę reguł autoryzacji kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Wygeneruj ponownie klucz podstawowy lub pomocniczy do zasobu |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Utwórz reguły autoryzacji kolejki i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/Delete | Operacja usuwania zasobu kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/read | Pobierz listę opisów zasobów kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/write | Tworzenie lub właściwości kolejki aktualizacji. |
> | Akcja | Microsoft.ServiceBus/namespaces/read | Pobierz listę opisów zasobów Namespace |
> | Akcja | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Usuwanie przestrzeni nazw usługi ACS |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operacja aktualizowania tematu. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operacja usuwania reguł autoryzacji tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Pobieranie parametrów połączenia do tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Pobierz listę reguł autoryzacji tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Wygeneruj ponownie klucz podstawowy lub pomocniczy do zasobu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Utwórz reguły autoryzacji z tematu i zaktualizuj jego właściwości. Można zaktualizować prawa dostępu reguł autoryzacji. |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/Delete | Operacja usuwania zasobu tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/read | Pobierz listę opisów zasobów tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operacja usuwania zasobu TopicSubscription |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Pobierz listę opisów zasobów TopicSubscription |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operacja usuwania zasobu reguły |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Pobierz listę opisów zasobów reguły |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Tworzenie lub właściwości reguły aktualizacji. |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Tworzenie lub aktualizacja TopicSubscription właściwości. |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/write | Tworzenie lub właściwości aktualizacji tematu. |
> | Akcja | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Usuwanie zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Pobiera zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Tworzenie zasobu reguły sieci Wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/write | Tworzenie zasobu Namespace i zaktualizuj jego właściwości. Tagi i pojemność Namespace są właściwości, które mogą być aktualizowane. |
> | Akcja | Microsoft.ServiceBus/operations/read | Pobierz operacje |
> | Akcja | Microsoft.ServiceBus/register/action | Rejestruje subskrypcję dostawcy zasobów usługi ServiceBus i włącza funkcję tworzenia zasobów usługi ServiceBus |
> | Akcja | Microsoft.ServiceBus/sku/read | Pobierz listę opisów zasobów jednostki Sku |
> | Akcja | Microsoft.ServiceBus/sku/regions/read | Pobierz listę opisów zasobów SkuRegions |
> | Akcja | Microsoft.ServiceBus/unregister/action | Rejestruje subskrypcję dostawcy zasobów usługi ServiceBus i włącza funkcję tworzenia zasobów usługi ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/delete | Usuń dowolną aplikację |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/read | Odczytaj dowolną aplikację |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/delete | Usuń dowolną usługę |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Odczytaj dowolną partycję |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Odczytaj dowolną replikę |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/read | Odczytaj dowolną usługę |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Odczytaj stan dowolnej usługi |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/write | Utwórz lub zaktualizuj dowolną usługę |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/write | Utwórz lub zaktualizuj dowolną aplikację |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Usuń dowolny typ aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/read | Odczytaj dowolny typ aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Usuń dowolną wersję typu aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Odczytaj dowolną wersję typu aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Utwórz lub zaktualizuj dowolną wersję typu aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/write | Utwórz lub zaktualizuj dowolny typ aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/delete | Usuń dowolny klaster |
> | Akcja | Microsoft.ServiceFabric/clusters/nodes/read | Odczytaj dowolny węzeł |
> | Akcja | Microsoft.ServiceFabric/clusters/read | Odczytaj dowolny klaster |
> | Akcja | Microsoft.ServiceFabric/clusters/statuses/read | Odczytaj stan dowolnego klastra |
> | Akcja | Microsoft.ServiceFabric/clusters/write | Utwórz lub zaktualizuj dowolny klaster |
> | Akcja | Microsoft.ServiceFabric/locations/clusterVersions/read | Odczytaj wersję dowolnego klastra |
> | Akcja | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Odczytaj dowolną wersję klastra dla określonego środowiska |
> | Akcja | Microsoft.ServiceFabric/locations/operationresults/read | Odczytaj dowolne wyniki operacji |
> | Akcja | Microsoft.ServiceFabric/locations/operations/read | Odczytaj dowolne operacje według lokalizacji |
> | Akcja | Microsoft.ServiceFabric/operations/read | Odczytaj dowolne dostępne operacje |
> | Akcja | Microsoft.ServiceFabric/register/action | Zarejestruj dowolną akcję |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.SignalRService/locations/checknameavailability/action | Sprawdza, czy nazwa jest dostępna do użycia z nową usługą SignalR |
> | Akcja | Microsoft.SignalRService/locations/operationresults/signalr/read | Kwerenda o stan operacji asynchronicznej |
> | Akcja | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Kwerenda o stan operacji asynchronicznej |
> | Akcja | Microsoft.SignalRService/locations/usages/read | Pobierz użycie limitu przydziału dla usługi Azure SignalR service |
> | Akcja | Microsoft.SignalRService/operationresults/read | Kwerenda o stan operacji asynchronicznej |
> | Akcja | Microsoft.SignalRService/operationstatus/read | Kwerenda o stan operacji asynchronicznej |
> | Akcja | Microsoft.SignalRService/register/action | Rejestruje dostawcę zasobów "Microsoft.SignalRService" z subskrypcji |
> | Akcja | Microsoft.SignalRService/SignalR/delete | Usuń całą usługę SignalR |
> | Akcja | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Usuń filtr siatki zdarzeń z SignalR. |
> | Akcja | Microsoft.SignalRService/SignalR/eventGridFilters/read | Pobierz właściwości filtru siatki określonego zdarzenia lub list, wszystkie usługi event grid filtry dla określonego elementu SignalR. |
> | Akcja | Microsoft.SignalRService/SignalR/eventGridFilters/write | Utwórz lub zaktualizuj filtr siatki zdarzeń dla SignalR z określonymi parametrami. |
> | Akcja | Microsoft.SignalRService/SignalR/listkeys/action | Wyświetl wartości kluczy dostępu do biblioteki SignalR, w portalu zarządzania lub za pośrednictwem interfejsu API |
> | Akcja | Microsoft.SignalRService/SignalR/read | Wyświetl ustawienia i konfiguracje SignalR, w portalu zarządzania lub za pośrednictwem interfejsu API |
> | Akcja | Microsoft.SignalRService/SignalR/regeneratekey/action | Zmień wartość kluczy dostępu do biblioteki SignalR w portalu zarządzania lub za pośrednictwem interfejsu API |
> | Akcja | Microsoft.SignalRService/SignalR/restart/action | Ponowne uruchomienie usługi Azure SignalR service w portalu zarządzania lub za pośrednictwem interfejsu API. Będzie pewnych przestojów. |
> | Akcja | Microsoft.SignalRService/SignalR/write | Zmodyfikuj ustawienia i konfiguracje w portalu zarządzania lub za pośrednictwem interfejsu API SignalR |
> | Akcja | Microsoft.SignalRService/unregister/action | Wyrejestrowuje dostawcę zasobów "Microsoft.SignalRService" z subskrypcji |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Wyświetla listę artefaktów aplikacji z definicji aplikacji. |
> | Akcja | Microsoft.Solutions/applicationDefinitions/delete | Usuwa definicję aplikacji. |
> | Akcja | Microsoft.Solutions/applicationDefinitions/read | Pobiera listę definicji aplikacji. |
> | Akcja | Microsoft.Solutions/applicationDefinitions/write | Dodaj lub zmodyfikuj definicję aplikacji. |
> | Akcja | Microsoft.Solutions/applications/applicationArtifacts/read | Wyświetla listę artefaktów aplikacji. |
> | Akcja | Microsoft.Solutions/applications/delete | Usuwa aplikację. |
> | Akcja | Microsoft.Solutions/applications/read | Pobiera listę aplikacji. |
> | Akcja | Microsoft.Solutions/applications/refreshPermissions/action | Odświeża uprawnień aplikacji. |
> | Akcja | Microsoft.Solutions/applications/updateAccess/action | Aktualizuje dostęp do aplikacji. |
> | Akcja | Microsoft.Solutions/applications/write | Tworzy aplikację. |
> | Akcja | Microsoft.Solutions/jitRequests/delete | Usuń JitRequest |
> | Akcja | Microsoft.Solutions/jitRequests/read | Pobiera listę JitRequests |
> | Akcja | Microsoft.Solutions/jitRequests/write | Tworzy JitRequest |
> | Akcja | Microsoft.Solutions/locations/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.Solutions/register/action | Zarezerwuj w rozwiązaniach. |
> | Akcja | Microsoft.Solutions/unregister/action | Wyrejestrowuje z rozwiązania. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Sql/checkNameAvailability/action | Sprawdź, czy nazwa danego serwera jest dostępne do alokowania na całym świecie, w ramach danej subskrypcji. |
> | Akcja | Microsoft.Sql/instancePools/delete | Usuwa z puli wystąpienia |
> | Akcja | Microsoft.Sql/instancePools/read | Pobiera wystąpienie puli |
> | Akcja | Microsoft.Sql/instancePools/usages/read | Pobiera informacje o użycie puli wystąpienia |
> | Akcja | Microsoft.Sql/instancePools/write | Tworzy lub aktualizuje pulę wystąpień |
> | Akcja | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Pobieranie wyników blob serwera rozszerzonej inspekcji operacji zestawu zasad |
> | Akcja | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Pobieranie wyników blob server inspekcji operacji zestawu zasad |
> | Akcja | Microsoft.Sql/locations/capabilities/read | Pobiera możliwości dla tej subskrypcji w danej lokalizacji |
> | Akcja | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/locations/databaseOperationResults/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Pobiera w trakcie wykonywania operacji na usunięto serwer |
> | Akcja | Microsoft.Sql/locations/deletedServerOperationResults/read | Pobiera w trakcie wykonywania operacji na usunięto serwer |
> | Akcja | Microsoft.Sql/locations/deletedServers/read | Zwraca listę usuniętych serwerów lub pobiera właściwości dla określonego serwera usunięte. |
> | Akcja | Microsoft.Sql/locations/deletedServers/recover/action | Odzyskaj usunięto serwer |
> | Akcja | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Pobiera operacji asynchronicznej platformy azure dla operacji asynchronicznej puli elastycznej |
> | Akcja | Microsoft.Sql/locations/elasticPoolOperationResults/read | Pobiera wynik operacji puli elastycznej. |
> | Akcja | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption — funkcja ochrony szyfrowania |
> | Akcja | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption — funkcja ochrony szyfrowania |
> | Akcja | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Pobieranie wyników blob serwera rozszerzonej inspekcji operacji zestawu zasad |
> | Akcja | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Pobieranie wyników blob serwera rozszerzonej inspekcji operacji zestawu zasad |
> | Akcja | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Pobiera stan operacji reguły zapory. |
> | Akcja | Microsoft.Sql/locations/firewallRulesOperationResults/read | Pobiera stan operacji reguły zapory. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/delete | Usuwa istniejącą grupę trybu failover wystąpienia. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Wykonuje planowanego trybu failover w istniejącej grupie trybu failover wystąpienia. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszonego trybu failover w istniejącej grupie trybu failover wystąpienia. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/read | Zwraca listę wystąpienia w tryb failover grupy lub pobiera właściwości dla grupy trybu failover określonym wystąpieniu. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/write | Tworzy wystąpienie grupy trybu failover z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego wystąpienia grupy trybu failover. |
> | Akcja | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Pobiera stan operacji puli wystąpienia |
> | Akcja | Microsoft.Sql/locations/instancePoolOperationResults/read | Pobiera wynik operacji puli wystąpienia |
> | Akcja | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Zwraca szczegóły punktu końcowego określonego interfejsu operacji asynchronicznej platformy Azure |
> | Akcja | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Zwraca szczegóły operacji profilu punktu końcowego określonego interfejsu |
> | Akcja | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Pobiera stan operacji agenta zadania. |
> | Akcja | Microsoft.Sql/locations/jobAgentOperationResults/read | Pobiera wynik operacji agenta zadania. |
> | Akcja | Microsoft.Sql/locations/longTermRetentionBackups/read | Wyświetla listę długoterminowego przechowywania kopii zapasowych dla każdej bazy danych na każdym serwerze w lokalizacji |
> | Akcja | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Wyświetla listę długoterminowego przechowywania kopii zapasowych dla każdej bazy danych na serwerze |
> | Akcja | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Usuwa kopię zapasową przechowywania długoterminowego |
> | Akcja | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Wyświetla listę długoterminowego przechowywania kopii zapasowych bazy danych |
> | Akcja | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Kończy operację przywracania zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption wystąpienia zarządzanego — funkcja ochrony szyfrowania |
> | Akcja | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption wystąpienia zarządzanego — funkcja ochrony szyfrowania |
> | Akcja | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Klucze wystąpienia zarządzane przez pobiera w trakcie wykonywania operacji na technologii transparent data encryption |
> | Akcja | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Klucze wystąpienia zarządzane przez pobiera w trakcie wykonywania operacji na technologii transparent data encryption |
> | Akcja | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Pobiera w trakcie wykonywania operacji na zarządzanej bazy danych przezroczyste szyfrowanie danych |
> | Akcja | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Pobiera w trakcie wykonywania operacji na zarządzanej bazy danych przezroczyste szyfrowanie danych |
> | Akcja | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Pobiera wynik operacji połączenia prywatnych punktów końcowych |
> | Akcja | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Pobiera wynik operacji połączenia prywatnych punktów końcowych |
> | Akcja | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Pobiera wynik operacji prywatnych punktów końcowych połączenia serwera proxy |
> | Akcja | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Pobiera wynik operacji prywatnych punktów końcowych połączenia serwera proxy |
> | Akcja | Microsoft.Sql/locations/read | Pobiera dostępne lokalizacje, w ramach danej subskrypcji |
> | Akcja | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption serwera klucze |
> | Akcja | Microsoft.Sql/locations/serverKeyOperationResults/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption serwera klucze |
> | Akcja | Microsoft.Sql/locations/syncAgentOperationResults/read | Pobierz wynik operacji zasobów agenta synchronizacji |
> | Akcja | Microsoft.Sql/locations/syncDatabaseIds/read | Pobieranie identyfikatorów bazy danych synchronizacji dla konkretnego regionu i subskrypcji |
> | Akcja | Microsoft.Sql/locations/syncGroupOperationResults/read | Pobierz wynik operacji synchronizacji grupy zasobów |
> | Akcja | Microsoft.Sql/locations/syncMemberOperationResults/read | Pobierz wynik operacji zasobów elementu członkowskiego synchronizacji |
> | Akcja | Microsoft.Sql/locations/usages/read | Pobiera kolekcję metryki użycia dla tej subskrypcji w lokalizacji |
> | Akcja | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Zwraca szczegóły reguły sieci wirtualnej określonej operacji asynchronicznej platformy azure  |
> | Akcja | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Zwraca szczegóły operacji reguły określonej sieci wirtualnej  |
> | Akcja | Microsoft.Sql/managedInstances/administrators/delete | Usuwa istniejącego administratora wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/managedInstances/administrators/read | Pobiera listę administratorów wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/managedInstances/administrators/write | Tworzy lub aktualizuje administratora wystąpienia zarządzanego z określonymi parametrami. |
> | Akcja | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Pobiera zasady przechowywania krótkoterminowych dla zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Aktualizuje zasady przechowywania krótkoterminowych dla zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Etykiety ważności aktualizacji usługi Batch |
> | Akcja | Microsoft.Sql/managedInstances/databases/delete | Usuwa istniejące zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla bazy danych wystąpień zarządzanych |
> | Akcja | Microsoft.Sql/managedInstances/databases/read | Pobiera istniejący zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/read | Pobierz schemat zarządzanej bazy danych. |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Pobierz kolumny zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Usuń etykieta poufności danej kolumny |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Wyłącz zalecenia czułości w danej kolumnie |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Włącz zalecenia czułości w danej kolumnie |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Pobierz etykieta poufności danej kolumny |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Utwórz lub zaktualizuj etykieta poufności danej kolumny |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Pobierz tabelę zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń zarządzanej bazy danych skonfigurowane dla danego serwera |
> | Akcja | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń bazy danych dla danego zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/securityEvents/read | Pobiera zdarzenia zabezpieczeń zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Pobieranie szczegółów bazy danych przezroczyste szyfrowanie danych na danym zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Zmień bazę danych przezroczyste szyfrowanie danych dla danego zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Usuń oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na givendatabase |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Pobierz reguły oceny luk w zabezpieczeniach według planu bazowego dla danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Zmień reguły oceny luk w zabezpieczeniach, według planu bazowego, określonej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Przekonwertować istniejące wyników skanowania ludzi czytelnym formacie. Jeśli istnieje już nic się dzieje |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Wykonywanie skanowania bazy danych oceny luk w zabezpieczeniach. |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Zwraca listę luk w zabezpieczeniach bazy danych rekordów skanowania oceny lub pobrać rekordu skanowania dla identyfikatora określonego skanowania. |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/write | Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych. |
> | Akcja | Microsoft.Sql/managedInstances/delete | Usuwa istniejącego wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/managedInstances/encryptionProtector/read | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonego serwera funkcja ochrony szyfrowania. |
> | Akcja | Microsoft.Sql/managedInstances/encryptionProtector/write | Aktualizowanie właściwości dla określonego funkcja ochrony szyfrowania serwera. |
> | Akcja | Microsoft.Sql/managedInstances/keys/delete | Usuwa istniejący klucz wystąpienia zarządzanego Azure SQL. |
> | Akcja | Microsoft.Sql/managedInstances/keys/read | Zwraca listę wystąpienia zarządzanego kluczy lub pobiera właściwości dla klucza określonego wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/managedInstances/keys/write | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości bądź tagi dla klucza określonego wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/managedInstances/metricDefinitions/read | Pobierz definicje metryk wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/metrics/read | Pobierz metryki dla wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/read | Zwraca listę wystąpień zarządzanych lub pobiera właściwości dla określonego wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/managedInstances/recoverableDatabases/read | Zwraca listę możliwych do odzyskania, zarządzane bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Pobiera zasady przechowywania krótkoterminowych porzuconych zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Aktualizuje zasady przechowywania krótkoterminowych dla porzuconych zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Zwraca listę z możliwością przywrócenia porzucony, zarządzane bazy danych. |
> | Akcja | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń serwera zarządzanego skonfigurowany dla danego serwera |
> | Akcja | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera zarządzanego dla danego serwera zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/tdeCertificates/action | Utwórz/zaktualizuj TDE certyfikatu |
> | Akcja | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Usuń oceny luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na danego wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/write | Tworzy wystąpienia zarządzanego z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/operations/read | Pobiera dostępne operacje REST |
> | Akcja | Microsoft.Sql/register/action | Rejestruje subskrypcję dostawcy zasobów bazy danych Microsoft SQL, a następnie włącza funkcję tworzenia bazy danych programu Microsoft SQL. |
> | Akcja | Microsoft.Sql/servers/administratorOperationResults/read | Pobiera w trakcie wykonywania operacji na administratorów serwera |
> | Akcja | Microsoft.Sql/servers/administrators/delete | Usuń administratora serwera |
> | Akcja | Microsoft.Sql/servers/administrators/read | Pobieranie szczegółów administratora serwera |
> | Akcja | Microsoft.Sql/servers/administrators/write | Utwórz lub zaktualizuj administratora serwera |
> | Akcja | Microsoft.Sql/servers/advisors/read | Zwraca listę doradców dostępny dla serwera |
> | Akcja | Microsoft.Sql/servers/advisors/recommendedActions/read | Zwraca listę zalecane akcje advisor określonego serwera |
> | Akcja | Microsoft.Sql/servers/advisors/recommendedActions/write | Stosowanie zalecaną akcję na serwerze programu |
> | Akcja | Microsoft.Sql/servers/advisors/write | Aktualizacje automatyczne execute stan doradcy na poziomie serwera. |
> | Akcja | Microsoft.Sql/servers/auditingPolicies/read | Pobieranie szczegółów tabeli server domyślne zasady skonfigurowane na danym serwerze inspekcji |
> | Akcja | Microsoft.Sql/servers/auditingPolicies/write | Zmień domyślny serwer inspekcji tabel dla danego serwera |
> | Akcja | Microsoft.Sql/servers/auditingSettings/operationResults/read | Pobieranie wyników blob server inspekcji operacji zestawu zasad |
> | Akcja | Microsoft.Sql/servers/auditingSettings/read | Pobieranie szczegółów zasad inspekcji obiektów blob serwera skonfigurowane na danym serwerze |
> | Akcja | Microsoft.Sql/servers/auditingSettings/write | Zmień Inspekcja obiektów blob serwera dla danego serwera |
> | Akcja | Microsoft.Sql/servers/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania serwera |
> | Akcja | Microsoft.Sql/servers/automaticTuning/write | Aktualizuje ustawienia automatycznego dostrajania serwera i zwraca zaktualizowanych ustawień |
> | Akcja | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Usuwa istniejący magazyn archiwizacji kopii zapasowej. |
> | Akcja | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Ta operacja służy do Pobierz magazyn przechowywania kopii zapasowych, długoterminowe. Zwraca informacje o magazynie zarejestrowane na tym serwerze |
> | Akcja | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ta operacja służy do rejestrowania przechowywania kopii zapasowych długoterminowym magazynie do serwera |
> | Akcja | Microsoft.Sql/servers/communicationLinks/delete | Usuwa istniejące łącze komunikacji serwera. |
> | Akcja | Microsoft.Sql/servers/communicationLinks/read | Zwraca listę linki komunikacyjne z określonego serwera. |
> | Akcja | Microsoft.Sql/servers/communicationLinks/write | Utwórz lub zaktualizuj łącza komunikacyjnego serwera. |
> | Akcja | Microsoft.Sql/servers/connectionPolicies/read | Zwraca listę zasad połączenia serwera z określonego serwera. |
> | Akcja | Microsoft.Sql/servers/connectionPolicies/write | Utwórz lub zaktualizuj zasady połączeń serwera. |
> | Akcja | Microsoft.Sql/servers/databases/advisors/read | Zwraca listę doradcy jest dostępna dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Zwraca listę zalecane działania określonego advisor dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Zastosuj Zalecana akcja w bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/advisors/write | Update-konaj stan doradcy na poziomie bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/auditingPolicies/read | Pobieranie szczegółów zasad inspekcji tabeli skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/auditingPolicies/write | Zmień zasady inspekcji tabel dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/auditingSettings/read | Pobieranie szczegółów zasad inspekcji obiektów blob, skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/auditingSettings/write | Zmień zasady inspekcji obiektów blob dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów blob bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualizuje ustawienia automatycznego dostrajania dla bazy danych i zwraca zaktualizowanych ustawień |
> | Akcja | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Zwraca listę archiwizacji zasady tworzenia kopii zapasowych określonej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Utwórz lub zaktualizuj zasady archiwizacji kopii zapasowych bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Pobiera zasady przechowywania krótkoterminowych dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Aktualizuje krótkoterminowa zasady przechowywania dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/connectionPolicies/read | Pobieranie szczegółów zasad połączenia skonfigurowane w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/connectionPolicies/write | Zmiana zasad połączenia dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Etykiety ważności aktualizacji usługi Batch |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Zwraca listę maskowania zasad danych bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Usuwanie reguły dla danej bazy danych maskowania danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Pobierz szczegóły reguły zasady skonfigurowane w danej bazie danych maskowania danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Zmień reguły dla danej bazy danych maskowania danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Zmiany zasad dla danej bazy danych maskowania danych |
> | Akcja | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Zwraca informacje o kroku zapytania rozproszonego zapytania magazynu danych dla Identyfikatora wybrany krok |
> | Akcja | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Zwraca informacje zapytania dystrybucji magazynu danych dla Identyfikatora wybranego zapytania |
> | Akcja | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Pobiera wystąpienia SQL Data Warehouse, co obejmuje zapytania uruchomionego i wstrzymania działań użytkownika |
> | Akcja | Microsoft.Sql/servers/databases/delete | Usuwa istniejącą bazę danych. |
> | Akcja | Microsoft.Sql/servers/databases/export/action | Eksportowanie bazy danych Azure SQL |
> | Akcja | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Pobieranie szczegółów zasad inspekcji rozszerzonego obiektu blob skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Zmiana zasad inspekcji rozszerzonego obiektu blob dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/extensions/read | Pobiera kolekcję rozszerzeń dla bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/extensions/write | Zmień rozszerzenie dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/failover/action | Tryb failover bazy danych zainicjowanej przez klienta. |
> | Akcja | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Pobieranie geograficznie zasady tworzenia kopii zapasowych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Utwórz lub zaktualizuj zasady geobackup bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/importExportOperationResults/read | Pobiera w trakcie operacji importu/eksportu |
> | Akcja | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Pobiera listę okna obsługi dostępne dla wybranej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/maintenanceWindows/read | Pobiera konserwacji ustawienia systemu windows dla wybranej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/maintenanceWindows/write | Ustawia konserwacji ustawienia systemu windows dla wybranej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/metrics/read | Zwróć metryki dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/move/action | Zmień nazwę istniejącej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/operationResults/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/operations/cancel/action | Anuluje usługi Azure SQL Database oczekująca operacja asynchroniczna, która nie została jeszcze zakończona. |
> | Akcja | Microsoft.Sql/servers/databases/operations/read | Zwraca listę operacji wykonywanych w bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/pause/action | Wstrzymywanie bazy danych z magazynu danych Azure SQL |
> | Akcja | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Zwraca kolekcję tekstów zapytania, które odnoszą się do określonych parametrów. |
> | Akcja | Microsoft.Sql/servers/databases/queryStore/read | Zwraca bieżące wartości ustawień Store kwerendy dla bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/queryStore/write | Aktualizuje ustawienia Store kwerendy dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/read | Zwraca listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/delete | Zakończenie relacji replikacji wymuszone i z potencjalną utratą danych |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Trybu failover po zsynchronizowaniu wszystkich zmienia z serwera podstawowego, dzięki czemu tę bazę danych do relationship\u0027s replikacji podstawowej i wprowadzania zdalnego podstawowego do pomocniczego |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Tryb failover bezpośrednio z potencjalną utratą danych, dzięki czemu tę bazę danych do relationship\u0027s replikacji podstawowej i wprowadzania zdalnego podstawowego do pomocniczego |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/read | Zwraca listę replikacji łącza lub pobiera właściwości dla wybranych łączach replikacji określony. |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Zakończenie relacji replikacji wymuszone lub po synchronizacji z partnerem |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Aktualizowanie trybu replikacji dla łącza do trybu synchroniczna lub asynchroniczna |
> | Akcja | Microsoft.Sql/servers/databases/restorePoints/action | Tworzy nowy punkt przywracania |
> | Akcja | Microsoft.Sql/servers/databases/restorePoints/delete | Usuwa punkt przywracania bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/restorePoints/read | Zwraca przywrócić punktów dla bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/resume/action | Wznawianie bazy danych z magazynu danych Azure SQL |
> | Akcja | Microsoft.Sql/servers/databases/schemas/read | Pobierz schemat bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Pobierz kolumny bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Usuń etykieta poufności danej kolumny |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Wyłącz zalecenia czułości w danej kolumnie |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Włącz zalecenia czułości w danej kolumnie |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Pobierz etykieta poufności danej kolumny |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Utwórz lub zaktualizuj etykieta poufności danej kolumny |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/read | Pobierz tabelę bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Pobieranie listy zalecenia dotyczące indeksu w bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Zastosuj zalecenie dotyczące indeksu |
> | Akcja | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń bazy danych skonfigurowane dla danego serwera |
> | Akcja | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń bazy danych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/securityMetrics/read | Pobiera kolekcję metryki zabezpieczeń bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/sensitivityLabels/read | Utwórz listę etykiet poufności danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Zwraca sugestii dotyczących skalowania bazy danych w górę lub w dół w oparciu statystyk wykonywania zapytań, aby zwiększyć wydajność i obniżyć koszty |
> | Akcja | Microsoft.Sql/servers/databases/skus/read | Pobiera kolekcję dostępnych jednostek SKU dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Anuluj synchronizację grupy synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/delete | Usuwa istniejącą grupą synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Zwraca listę synchronizacji schematy bazy danych Centrum |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/logs/read | Zwraca listę dzienników grupy synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/read | Zwraca listę synchronizacji grupy lub pobiera właściwości dla grupy synchronizacji określonej. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Odśwież schemat bazy danych Centrum synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu Centrum synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Usuwa istniejącego elementu członkowskiego synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Zwraca listę elementów członkowskich synchronizacji lub pobiera właściwości dla elementu członkowskiego synchronizacji określonej funkcji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Odśwież schemat elementu członkowskiego synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu elementu członkowskiego synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Zwraca listę synchronizacji schematy bazy danych elementu członkowskiego |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Tworzy element członkowski synchronizacji z określonymi parametrami lub aktualizuje właściwości dla elementu członkowskiego synchronizacji określonej. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Wyzwalacz Synchronizacja grupy synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/write | Tworzy grupę synchronizacji z określonymi parametrami lub zaktualizować grupy synchronizacji określonej właściwości. |
> | Akcja | Microsoft.Sql/servers/databases/topQueries/queryText/action | Zwraca tekst języka Transact-SQL dla Identyfikatora wybranego zapytania |
> | Akcja | Microsoft.Sql/servers/databases/topQueries/read | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Akcja | Microsoft.Sql/servers/databases/topQueries/statistics/read | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Akcja | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Pobiera w trakcie wykonywania operacji na technologii transparent data encryption |
> | Akcja | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Pobieranie stanu i szczegółowe informacje o technologii transparent data encryption zabezpieczeń funkcji określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Zmień stan szyfrowania danych |
> | Akcja | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Uaktualnianie bazy danych magazynu danych Azure SQL |
> | Akcja | Microsoft.Sql/servers/databases/usages/read | Pobiera informacje o użycia usługi Azure SQL Database |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Usuń oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na givendatabase |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Pobierz reguły oceny luk w zabezpieczeniach według planu bazowego dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Zmień reguły oceny luk w zabezpieczeniach, według planu bazowego, określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Przekonwertować istniejące wyników skanowania ludzi czytelnym formacie. Jeśli istnieje już nic się dzieje |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Wykonywanie skanowania bazy danych oceny luk w zabezpieczeniach. |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Zwraca listę luk w zabezpieczeniach bazy danych rekordów skanowania oceny lub pobrać rekordu skanowania dla identyfikatora określonego skanowania. |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Wykonywanie skanowania bazy danych oceny luk w zabezpieczeniach. |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Pobierz wynik skanowania oceny luk w zabezpieczeniach bazy danych operacji wykonywania |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Pobierz szczegóły oceny luk w zabezpieczeniach, skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Zmiana oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/write | Tworzy bazę danych z określonymi parametrami lub aktualizuje właściwości bądź tagi określonej bazy danych. |
> | Akcja | Microsoft.Sql/servers/delete | Usuwa istniejącego serwera. |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Usuwa istniejące konfiguracjami odzyskiwania po awarii dla danego serwera |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Tryb failover DisasterRecoveryConfiguration |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Wymuszenia trybu Failover DisasterRecoveryConfiguration |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Pobiera kolekcję po awarii z konfiguracjami odzyskiwania, które zawierają ten serwer |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Konfiguracja odzyskiwania po awarii serwera zmiany |
> | Akcja | Microsoft.Sql/servers/elasticPoolEstimates/read | Zwraca listę już utworzone dla tego serwera oszacowania puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPoolEstimates/write | Tworzy nowy szacunek puli elastycznej dla listy baz danych, pod warunkiem |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/read | Zwraca listę doradcy jest dostępna dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Zwraca listę zalecane akcje advisor określonego dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Zastosuj Zalecana akcja w puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/write | Update-konaj stan doradcy na poziomie puli elastycznej. |
> | Akcja | Microsoft.Sql/servers/elasticPools/databases/read | Pobiera listę baz danych dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/delete | Usuwanie istniejącej puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Pobieranie działania i szczegółowe informacje na temat danego elastycznej puli baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Pobieranie działania i szczegółowe informacje na temat danego bazy danych, który jest częścią elastycznej puli baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/failover/action | Klientów zainicjować trybu failover puli elastycznej. |
> | Akcja | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/metrics/read | Zwróć metryki dla elastycznych pul baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Anuluje elastyczną pulę Azure SQL oczekująca operacja asynchroniczna, która nie została jeszcze zakończona. |
> | Akcja | Microsoft.Sql/servers/elasticPools/operations/read | Zwraca listę operacji wykonywanych na elastycznej puli |
> | Akcja | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/read | Pobierz szczegóły puli elastycznej na danym serwerze |
> | Akcja | Microsoft.Sql/servers/elasticPools/skus/read | Pobiera kolekcję dostępnych jednostek SKU dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/write | Utwórz nową lub zmienić właściwości istniejącej puli elastycznej |
> | Akcja | Microsoft.Sql/servers/encryptionProtector/read | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonego serwera funkcja ochrony szyfrowania. |
> | Akcja | Microsoft.Sql/servers/encryptionProtector/write | Aktualizowanie właściwości dla określonego funkcja ochrony szyfrowania serwera. |
> | Akcja | Microsoft.Sql/servers/extendedAuditingSettings/read | Pobierz szczegóły obiektu blob serwera rozszerzonej skonfigurowane na danym serwerze zasady inspekcji |
> | Akcja | Microsoft.Sql/servers/extendedAuditingSettings/write | Zmień serwer rozszerzonej inspekcji obiektów blob dla danego serwera |
> | Akcja | Microsoft.Sql/servers/failoverGroups/delete | Usuwa istniejącą grupę trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/failover/action | Wykonuje planowanego trybu failover w istniejącej grupie trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszonego trybu failover w istniejącej grupie trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/read | Zwraca listę trybu failover grupy lub pobiera właściwości dla grupy trybu failover określonym. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/write | Tworzy grupę trybu failover z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego trybu failover grupy. |
> | Akcja | Microsoft.Sql/servers/firewallRules/delete | Usuwa istniejącą regułę zapory na serwerze. |
> | Akcja | Microsoft.Sql/servers/firewallRules/read | Zwraca listę zapory serwera reguł lub pobiera właściwości dla określonego serwera, reguły zapory. |
> | Akcja | Microsoft.Sql/servers/firewallRules/write | Tworzy regułę zapory na serwerze z określonymi parametrami, zaktualizować właściwości określonej reguły lub zastąpienie wszystkich istniejących reguł nowych reguł zapory serwera. |
> | Akcja | Microsoft.Sql/servers/import/action | Tworzenie nowej bazy danych na serwerze i wdrożyć schemat i dane z pakietu DacPac |
> | Akcja | Microsoft.Sql/servers/importExportOperationResults/read | Pobiera w trakcie operacji importu/eksportu |
> | Akcja | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Usuwa profil punktu końcowego określonego interfejsu |
> | Akcja | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Zwraca właściwości dla określonego interfejsu profilu punktu końcowego |
> | Akcja | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Tworzy profil punktu końcowego interfejsu z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego interfejsu punktu końcowego |
> | Akcja | Microsoft.Sql/servers/jobAgents/delete | Usuwa agenta zadań w usłudze Azure SQL DB |
> | Akcja | Microsoft.Sql/servers/jobAgents/read | Pobiera agenta zadań w usłudze Azure SQL DB |
> | Akcja | Microsoft.Sql/servers/jobAgents/write | Tworzy lub aktualizuje agenta zadań w usłudze Azure SQL DB |
> | Akcja | Microsoft.Sql/servers/keys/delete | Usuwa istniejący klucz serwera. |
> | Akcja | Microsoft.Sql/servers/keys/read | Zwraca listę server kluczy lub pobiera właściwości dla klucza określonego serwera. |
> | Akcja | Microsoft.Sql/servers/keys/write | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości bądź tagi dla klucza określonego serwera. |
> | Akcja | Microsoft.Sql/servers/operationResults/read | Pobiera w trakcie operacji serwera |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Usuwa istniejący serwer proxy połączeń prywatnych punktów końcowych |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Zwraca listę wszystkich prywatnych punktów końcowych połączenia serwerów proxy lub pobiera właściwości dla serwera proxy połączenia prywatnego określonym w punkcie końcowym. |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Sprawdza poprawność prywatne połączenie z punktem końcowym tworzenie wywołania po stronie NRP |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Tworzy połączenie serwera proxy prywatnych punktów końcowych z określonymi parametrami lub aktualizuje właściwości bądź tagi proxy połączenia określony prywatnych punktów końcowych usługi. |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnections/delete | Usuwa istniejące połączenie prywatnych punktów końcowych |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnections/read | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla połączenia prywatnego określonym w punkcie końcowym. |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnections/write | Zatwierdza lub odrzuca istniejące połączenie prywatnych punktów końcowych |
> | Akcja | Microsoft.Sql/servers/privateLinkResources/read | Pobierz zasoby prywatnego linku dla odpowiedniego programu sql server |
> | Akcja | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla serwerów |
> | Akcja | Microsoft.Sql/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Pobierz metryki dla zalecane elastyczne pule baz danych dla danego serwera |
> | Akcja | Microsoft.Sql/servers/recommendedElasticPools/read | Pobierz zalecenia dla elastycznych pul baz danych do zmniejszania kosztów lub zwiększenia wydajności na podstawie wykorzystania historycznych zasobów |
> | Akcja | Microsoft.Sql/servers/recoverableDatabases/read | Ta operacja służy do odzyskiwania po awarii na żywo bazy danych do przywracania bazy danych Ostatnia znana dobra punktu kopii zapasowej. Zwraca informacje o ostatnim dobrej kopii zapasowej, ale doesn\u0027t faktycznie Przywracanie bazy danych. |
> | Akcja | Microsoft.Sql/servers/replicationLinks/read | Zwraca listę replikacji łącza lub pobiera właściwości dla wybranych łączach replikacji określony. |
> | Akcja | Microsoft.Sql/servers/restorableDroppedDatabases/read | Zostanie wyświetlona lista baz danych, które zostały usunięte na danym serwerze, które są nadal w ramach zasad przechowywania. |
> | Akcja | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Pobierz wyniki operacji zapisu zasady wykrywania zagrożeń serwera |
> | Akcja | Microsoft.Sql/servers/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń serwera skonfigurowany dla danego serwera |
> | Akcja | Microsoft.Sql/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.Sql/servers/serviceObjectives/read | Pobieranie listy celów poziomu usług (znany także jako warstwy wydajności) dostępne na danym serwerze |
> | Akcja | Microsoft.Sql/servers/syncAgents/delete | Usuwa istniejącego agenta synchronizacji. |
> | Akcja | Microsoft.Sql/servers/syncAgents/generateKey/action | Wygeneruj klucz rejestracji agenta synchronizacji |
> | Akcja | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Zwraca listę bazy danych połączone agenta synchronizacji |
> | Akcja | Microsoft.Sql/servers/syncAgents/read | Zwraca listę agentów synchronizacji lub pobiera właściwości dla agenta synchronizacji określonej. |
> | Akcja | Microsoft.Sql/servers/syncAgents/write | Tworzy agenta synchronizacji z określonymi parametrami lub aktualizuje właściwości dla agenta synchronizacji określonej. |
> | Akcja | Microsoft.Sql/servers/tdeCertificates/action | Utwórz/zaktualizuj TDE certyfikatu |
> | Akcja | Microsoft.Sql/servers/usages/read | Zwraca limit przydziału jednostek DTU serwera i bieżącej consuption jednostek DTU przez wszystkie bazy danych na serwerze |
> | Akcja | Microsoft.Sql/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | Akcja | Microsoft.Sql/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguł lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.Sql/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Usuń oceny luk w zabezpieczeniach dla danego serwera |
> | Akcja | Microsoft.Sql/servers/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na danym serwerze |
> | Akcja | Microsoft.Sql/servers/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danego serwera |
> | Akcja | Microsoft.Sql/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |
> | Akcja | Microsoft.Sql/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów bazy danych Microsoft SQL, a następnie włącza funkcję tworzenia bazy danych programu Microsoft SQL. |
> | Akcja | Microsoft.Sql/virtualClusters/delete | Usuwa istniejący klaster wirtualny. |
> | Akcja | Microsoft.Sql/virtualClusters/read | Zwraca listę klastrów wirtualnych lub pobiera właściwości dla określonego klastra wirtualnego. |
> | Akcja | Microsoft.Sql/virtualClusters/write | Aktualizuje tagi klaster wirtualny. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Storage/checknameavailability/read | Sprawdza się, że nazwa konta jest prawidłowa i nie jest używany. |
> | Akcja | Microsoft.Storage/locations/checknameavailability/read | Sprawdza się, że nazwa konta jest prawidłowa i nie jest używany. |
> | Akcja | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia element Microsoft.Storage, że trwa usuwanie sieci wirtualnej lub podsieci |
> | Akcja | Microsoft.Storage/locations/usages/read | Zwraca limit i bieżącą liczbę użyć dla zasobów w określonej subskrypcji |
> | Akcja | Microsoft.Storage/operations/read | Sonduje stan operacji asynchronicznej. |
> | Akcja | Microsoft.Storage/register/action | Rejestruje subskrypcję dostawcy zasobów magazynu i umożliwia tworzenie kont magazynu. |
> | Akcja | Microsoft.Storage/skus/read | Wyświetla listę jednostek SKU obsługiwanych przez zasób Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Zwraca wynik dodania zawartości obiektu blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Zwraca wynik usunięcia obiektu blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Zwraca wynik usunięcia automatycznych migawek |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Zwraca listę obiektów blob na koncie przy użyciu tagów zgodne z filtrem |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Zwraca obiekt blob lub listę obiektów blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Zwraca wynik odczytywanie tagów obiektów blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Zwraca wynik zapisania obiektu blob tagów |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Zwraca wynik zapisania obiektu blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Wyczyść blobcontainer prawne przechowywania |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Zwraca wynik usunięcia kontenera |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Usuń zasady niezmienności kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Rozszerz zasady niezmienności kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Zasady niezmienności kontenera obiektów blob blokady |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Pobierz zasady niezmienności kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Umieść zasady niezmienności kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Zwraca wynik dzierżawy kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/read | Zwraca kontener |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/read | Zwraca listę kontenerów |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ustaw prawnych dla kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/write | Zwraca wyniki poprawki kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/write | Zwraca wynik umieszczania obiektu blob kontenera |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla usługi blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/read | Zwraca obiekt blob service statystyki lub wartości |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/write | Zwraca wynik umieszczania obiektu blob właściwości usługi |
> | Akcja | Microsoft.Storage/storageAccounts/delete | Usuwa istniejące konto magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/failover/action | Klient jest w stanie kontrolować trybu failover w przypadku problemów dotyczących dostępności |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write |  |
> | Akcja | Microsoft.Storage/storageAccounts/fileServices/read | Pobierz właściwości usługi plików |
> | Akcja | Microsoft.Storage/storageAccounts/listAccountSas/action | Zwraca token sygnatury dostępu Współdzielonego konta dla podanego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/listServiceSas/action | Zwraca token sygnatury dostępu Współdzielonego usługi dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/managementPolicies/delete | Usuń zasady zarządzania kontami magazynu |
> | Akcja | Microsoft.Storage/storageAccounts/managementPolicies/read | Pobierz zasady konta zarządzania magazynem |
> | Akcja | Microsoft.Storage/storageAccounts/managementPolicies/write | Umieść zasady zarządzania kontami magazynu |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Usuwanie punktu końcowego prywatne połączenie proxy |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/validate/action | Sprawdź poprawność proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Umieść serwery proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Usuń połączenie z punktem końcowym prywatne |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Uzyskaj połączenie z punktem końcowym prywatne |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Umieść połączenie z punktem końcowym prywatne |
> | Akcja | Microsoft.Storage/storageAccounts/privateLinkResources/read | Pobierz identyfikatory GroupID konto magazynu |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Zwraca wynik usunięcia kolejki |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Zwraca wynik dodania komunikatu |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Zwraca wynik usunięcia komunikatu |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Zwraca wynik przetworzenia komunikatu |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Zwraca komunikat |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Zwraca wynik zapisania komunikatu |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/queues/write | Zwraca wynik zapisania kolejki |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/read | Pobierz właściwości usługi kolejki |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/read | Zwraca statystyki lub wartości właściwości usługi w kolejce. |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/write | Zwraca wynik ustawienia właściwości usługi kolejki |
> | Akcja | Microsoft.Storage/storageAccounts/read | Zwraca listę magazynu kont lub pobiera właściwości dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/regeneratekey/action | Generuje ponownie klucze dostępu dla podanego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Przywracanie zakresów obiektów blob do stanu określonego czasu |
> | Akcja | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Odwołuje wszystkie klucze delegowania użytkownika dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Utwórz/zaktualizuj ustawienia diagnostyczne konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/tableServices/read | Pobierz właściwości usługi tabeli |
> | Akcja | Microsoft.Storage/storageAccounts/write | Tworzy konto magazynu z podanymi parametrami lub aktualizuje właściwości bądź tagi albo dodaje niestandardową domenę dla podanego konta magazynu. |
> | Akcja | Microsoft.Storage/usages/read | Zwraca limit i bieżącą liczbę użyć dla zasobów w określonej subskrypcji |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | microsoft.storagesync/locations/checkNameAvailability/action | Sprawdza się, że nazwa usługi synchronizacji magazynu jest prawidłowa i nie jest używany. |
> | Akcja | microsoft.storagesync/locations/workflows/operations/read | Pobiera stan operacji asynchronicznej |
> | Akcja | microsoft.storagesync/storageSyncServices/delete | Usuń wszystkie usługi synchronizacji magazynu |
> | Akcja | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla usługi synchronizacji magazynu |
> | Akcja | microsoft.storagesync/storageSyncServices/read | Przeczytaj dowolnej usługi synchronizacji magazynu |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/delete | Usuń wszelkie zarejestrowanego serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla zarejestrowanego serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/read | Przeczytaj dowolnego zarejestrowanego serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/write | Utwórz lub zaktualizuj wszelkie zarejestrowanego serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Usuń wszystkie punkty końcowe w chmurze |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Pobiera stan operacji asynchronicznej/przywracania kopii zapasowej |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Wywołaj tę akcję po utworzeniu kopii zapasowej |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Wywołaj tę akcję po przywracaniu |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Wywołaj tę akcję przed kopią zapasową |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Wywołaj tę akcję przed przywróceniem |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Odczytywać wszystkie punkty końcowe w chmurze |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Przywróć pulsu |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Utwórz lub zaktualizuj wszystkie punkty końcowe w chmurze |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/delete | Usuń wszystkie grupy synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla grupy synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/read | Przeczytaj żadnych grup synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Usuń wszystkie punkty końcowe serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla punkty końcowe serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Odczytywać wszystkie punkty końcowe serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Wywołaj tę akcję, aby odwołać pliki do serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Utwórz lub zaktualizuj wszystkie punkty końcowe serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/write | Utwórz lub zaktualizuj żadnych grup synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Pobiera stan operacji asynchronicznej |
> | Akcja | microsoft.storagesync/storageSyncServices/workflows/operations/read | Pobiera stan operacji asynchronicznej |
> | Akcja | microsoft.storagesync/storageSyncServices/workflows/read | Przeczytaj przepływów pracy |
> | Akcja | microsoft.storagesync/storageSyncServices/write | Utwórz lub zaktualizuj wszelkie usługi synchronizacji magazynu |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/delete | Usuwa rekordy kontroli dostępu |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/read | Wyświetla lub pobiera rekordy kontroli dostępu |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/write | Utwórz lub zaktualizuj rekordy kontroli dostępu |
> | Akcja | Microsoft.StorSimple/managers/alerts/read | Wyświetla lub pobiera alerty |
> | Akcja | Microsoft.StorSimple/managers/backups/read | Wyświetla lub pobiera zestaw kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/bandwidthSettings/delete | Usuwa istniejące ustawienia przepustowości (8000 Series tylko) |
> | Akcja | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/bandwidthSettings/read | Lista ustawień przepustowości (tylko w przypadku serii 8000) |
> | Akcja | Microsoft.StorSimple/managers/bandwidthSettings/write | Tworzy nową lub aktualizuje ustawienia przepustowości (8000 Series tylko) |
> | Akcja | Microsoft.StorSimple/managers/certificates/write | Tworzenie lub aktualizowanie certyfikatów |
> | Akcja | Microsoft.StorSimple/Managers/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Akcja | Microsoft.StorSimple/managers/clearAlerts/action | Usuń zaznaczenie wszystkich alertów skojarzonych z Menedżerem urządzeń. |
> | Akcja | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Wyświetl urządzenie w chmurze obsługiwane konfiguracje |
> | Akcja | Microsoft.StorSimple/managers/configureDevice/action | Konfiguruje urządzenia |
> | Akcja | Microsoft.StorSimple/managers/delete | Usuwa menedżerów urządzeń |
> | Akcja | Microsoft.StorSimple/Managers/delete | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "Magazyn" |
> | Akcja | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/alertSettings/read | Wyświetla lub pobiera ustawienia alertu |
> | Akcja | Microsoft.StorSimple/managers/devices/alertSettings/write | Utwórz lub zaktualizuj ustawienia alertu |
> | Akcja | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autoryzacja dla usługi Przerzucanie klucza szyfrowania urządzeń |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Ręczna kopia zapasowa, aby utworzyć na żądanie należy wykonać kopię zapasową wszystkich woluminów, które są chronione przez zasady. |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Usuwa istniejące zasady kopii zapasowych (8000 Series tylko) |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/read | Listy kopii zapasowej zasady (tylko w przypadku serii 8000) |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Usuwa istniejące harmonogramy |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Lista harmonogramy |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Tworzy nową lub aktualizuje harmonogramów |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/write | Tworzy nową lub aktualizacji zasad kopii zapasowych (8000 Series tylko) |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/delete | Usuwa zestaw kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klonuj udział lub wolumin za pomocą elementu kopii zapasowej. |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/read | Wyświetla lub pobiera zestaw kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/restore/action | Przywróć wszystkie woluminy z zestawu kopii zapasowych. |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Usuwa grupy harmonogram tworzenia kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Wyświetla lub pobiera grupy harmonogramu kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Utwórz lub zaktualizuj grupy harmonogramu kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/delete | Usuwa ustawienia protokołu Chap |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/read | Wyświetla lub pobiera ustawienia protokołu Chap |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/write | Utwórz lub zaktualizuj ustawienia protokołu Chap |
> | Akcja | Microsoft.StorSimple/managers/devices/deactivate/action | Dezaktywuje urządzenia. |
> | Akcja | Microsoft.StorSimple/managers/devices/delete | Usuwa urządzenia |
> | Akcja | Microsoft.StorSimple/managers/devices/disks/read | Wyświetla lub pobiera dysków |
> | Akcja | Microsoft.StorSimple/managers/devices/download/action | Pobierz aktualizacje dla urządzenia. |
> | Akcja | Microsoft.StorSimple/managers/devices/failover/action | Tryb failover urządzenia. |
> | Akcja | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/failoverTargets/read | Wyświetla lub pobiera obiekty docelowe trybu Failover urządzeń |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Wykonaj kopię zapasową, serwera plików. |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/delete | Usuwa na serwerach plików |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/read | Wyświetla lub pobiera na serwerach plików |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Usuwa akcji |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Wyświetla lub pobiera akcje |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Utwórz lub zaktualizuj akcji |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/write | Utwórz lub zaktualizuj serwery plików |
> | Akcja | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Zmiana stanu zasilania kontrolera grup składników sprzętowych |
> | Akcja | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Lista grup składników sprzętowych |
> | Akcja | Microsoft.StorSimple/managers/devices/install/action | Zainstaluj aktualizacje na urządzeniu. |
> | Akcja | Microsoft.StorSimple/managers/devices/installUpdates/action | Instaluje aktualizacje na urządzeniach (8000 Series tylko). |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Wykonaj kopię zapasową serwera iSCSI. |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Usuwa iSCSI serwerów |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Usuwa dysków |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Wyświetla lub pobiera dysków |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Utwórz lub zaktualizuj dyski |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/read | Wyświetla lub pobiera iSCSI serwerów |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/write | Utwórz lub zaktualizuj serwery iSCSI |
> | Akcja | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Anuluj uruchomione zadanie |
> | Akcja | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/jobs/read | Wyświetla lub pobiera zadania |
> | Akcja | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Lista zestawów trybu failover dla istniejącego urządzenia (8000 Series tylko). |
> | Akcja | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Wyświetl listę celów trybu failover urządzeń (8000 Series tylko). |
> | Akcja | Microsoft.StorSimple/managers/devices/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Potwierdza migracja zakończyła się powodzeniem, a następnie zatwierdzić. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Lista upewnij się, stan migracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Pobierz stan potwierdzenia migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Pobierz stan zadania oceny migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Pobierz stan migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Zaimportować konfiguracje źródeł dla migracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Lista oszacowanie migracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Wyświetl stan migracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Rozpoczynanie migracji za pomocą źródło konfiguracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Uruchamianie zadania można oszacować, na czas trwania procesu migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/networkSettings/read | Wyświetla lub pobiera ustawienia sieci |
> | Akcja | Microsoft.StorSimple/managers/devices/networkSettings/write | Tworzy nową lub aktualizuje ustawienia sieci |
> | Akcja | Microsoft.StorSimple/managers/devices/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Lista publicznego klucza szyfrowania dla Menedżera urządzeń |
> | Akcja | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publikowanie pakietu dla pomocy technicznej dla istniejącego urządzenia. Pakiet dla pomocy technicznej usługi StorSimple jest mechanizm łatwy w użyciu, który gromadzi informacje o wszystkich dzienników istotnych uzyskanymi Microsoft Support rozwiązać wszelkie problemy z urządzeniem StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/devices/read | Wyświetla lub pobiera urządzenia |
> | Akcja | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Skanowanie w poszukiwaniu aktualizacji w urządzeniu. |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/read | Ustawienia zabezpieczeń listy |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synchronizowanie certyfikatu zarządzania zdalnego dla urządzenia. |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Aktualizowanie ustawień zabezpieczeń. |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/write | Tworzy nową lub aktualizuje ustawienia zabezpieczeń |
> | Akcja | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Wyślij wiadomość e-mail z alertem testu do adresatów wiadomości e-mail skonfigurowany. |
> | Akcja | Microsoft.StorSimple/managers/devices/shares/read | Wyświetla lub pobiera akcje |
> | Akcja | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/timeSettings/read | Wyświetla lub pobiera ustawienia czasu |
> | Akcja | Microsoft.StorSimple/managers/devices/timeSettings/write | Tworzy nową lub aktualizuje ustawienia czasu |
> | Akcja | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/updateSummary/read | Wyświetla lub pobiera Podsumowanie aktualizacji |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Usuwa istniejące kontenery woluminów (8000 Series tylko) |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Listy metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Listę definicji metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/read | Lista kontenerów woluminów (tylko w przypadku serii 8000) |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Usuwa istniejące woluminy |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Listy metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Listę definicji metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Wyświetl wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Listę woluminów |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Tworzy nową lub aktualizuje woluminów |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/write | Tworzy nową lub aktualizuje kontenery woluminów (8000 Series tylko) |
> | Akcja | Microsoft.StorSimple/managers/devices/volumes/read | Listę woluminów |
> | Akcja | Microsoft.StorSimple/managers/devices/write | Utwórz lub zaktualizuj urządzenia |
> | Akcja | Microsoft.StorSimple/managers/encryptionSettings/read | Wyświetla lub pobiera ustawienia szyfrowania |
> | Akcja | Microsoft.StorSimple/managers/extendedInformation/delete | Usuwa informacje o rozszerzonych magazynu |
> | Akcja | Microsoft.StorSimple/Managers/extendedInformation/delete | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.StorSimple/managers/extendedInformation/read | Wyświetla lub pobiera rozszerzone informacje o magazynie |
> | Akcja | Microsoft.StorSimple/Managers/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.StorSimple/managers/extendedInformation/write | Utwórz lub zaktualizuj rozszerzonych informacji o magazynie |
> | Akcja | Microsoft.StorSimple/Managers/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.StorSimple/managers/features/read | Lista funkcji |
> | Akcja | Microsoft.StorSimple/managers/fileservers/read | Wyświetla lub pobiera na serwerach plików |
> | Akcja | Microsoft.StorSimple/managers/getEncryptionKey/action | Pobierz klucz szyfrowania dla Menedżera urządzeń. |
> | Akcja | Microsoft.StorSimple/managers/iscsiservers/read | Wyświetla lub pobiera iSCSI serwerów |
> | Akcja | Microsoft.StorSimple/managers/jobs/read | Wyświetla lub pobiera zadania |
> | Akcja | Microsoft.StorSimple/managers/listActivationKey/action | Pobiera klucz aktywacji Menedżer urządzeń StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Utwórz listę kluczy publicznych szyfrowania dla Menedżera urządzeń StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrowanie do usługi Resource Manager z Mangers klasyczny |
> | Akcja | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Zwraca listę konfiguracji źródłowej migracji (tylko w przypadku serii 8000) |
> | Akcja | Microsoft.StorSimple/managers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Tworzenie nowego urządzenia w chmurze. |
> | Akcja | Microsoft.StorSimple/managers/read | Wyświetla lub pobiera menedżerów urządzeń |
> | Akcja | Microsoft.StorSimple/Managers/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | Akcja | Microsoft.StorSimple/managers/regenerateActivationKey/action | Wygeneruj ponownie klucz aktywacji dla istniejących Menedżer urządzeń StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Usuwa poświadczenia konta magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/read | Wyświetla lub pobiera poświadczenia konta magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/write | Utwórz lub zaktualizuj poświadczenia konta magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageDomains/delete | Usuwa domeny magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/storageDomains/read | Wyświetla lub pobiera domeny magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageDomains/write | Utwórz lub zaktualizuj domeny magazynu |
> | Akcja | Microsoft.StorSimple/managers/write | Utwórz lub zaktualizuj menedżerów urządzeń |
> | Akcja | Microsoft.StorSimple/Managers/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.StorSimple/operations/read | Wyświetla lub pobiera operacje |
> | Akcja | Microsoft.StorSimple/register/action | Register Provider Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.StreamAnalytics/locations/quotas/Read | Odczyt Stream Analytics — limit przydziału subskrypcji |
> | Akcja | Microsoft.StreamAnalytics/operations/Read | Operacje odczytu w Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/Register/action | Zarejestruj subskrypcję za pomocą dostawcy zasobów usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Delete | Usuń zadanie usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Usuń funkcję zadania usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Odczytaj wyniki operacji na funkcję zadania usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Stanowisko do odczytu Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Pobierz definicję domyślną funkcję zadania Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Test Stream Analytics stanowiska |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Zapis funkcję zadania usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Usuń Stream Analytics — dane wejściowe zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Odczytaj wyniki operacji na dane wejściowe zadania analizy Stream |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Odczyt Stream Analytics — dane wejściowe zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Przykładowe Stream Analytics — dane wejściowe zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Dane wejściowe testu Stream Analytics zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Zapis Stream Analytics — dane wejściowe zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Odczytaj wyniki operacji na zadanie usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Usuń Stream Analytics — dane wyjściowe zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Odczytaj wyniki operacji dla Stream Analytics — dane wyjściowe zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Odczyt Stream Analytics — dane wyjściowe zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Dane wyjściowe testu Stream Analytics zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Zapis Stream Analytics — dane wyjściowe zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Odczytaj ustawienie diagnostyczne. |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Zapisz ustawienia diagnostyczne. |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla streamingjobs |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla streamingjobs |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Read | Odczyt zadanie usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Start/action | Uruchom zadanie usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Zatrzymaj zadanie usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Usuń transformacja zadania usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Transformacja zadania usługi Stream Analytics odczytu |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Zapis transformacja zadania usługi Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Write | Zapis zadanie usługi Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Subscription/CreateSubscription/action | Tworzenie subskrypcji platformy Azure |
> | Akcja | Microsoft.Subscription/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.Subscription |
> | Akcja | Microsoft.Subscription/SubscriptionDefinitions/read | Pobierz definicję subskrypcji platformy Azure w grupie zarządzania. |
> | Akcja | Microsoft.Subscription/SubscriptionDefinitions/write | Tworzenie definicji subskrypcji platformy Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Support/register/action | Przeprowadza rejestrację u dostawcy zasobów pomocy technicznej |
> | Akcja | Microsoft.Support/supportTickets/read | Pobiera szczegóły biletu pomocy technicznej (w tym stan, ważność, szczegóły dotyczące kontaktu i komunikacji) lub pobiera listę biletów pomocy technicznej dla różnych subskrypcji. |
> | Akcja | Microsoft.Support/supportTickets/write | Tworzy lub aktualizuje bilet pomocy technicznej. Można utworzyć bilet pomocy technicznej dotyczący problemów technicznych, rozliczeń, limitów przydziału lub problemów związanych z zarządzaniem subskrypcją. Można także zaktualizować ważność, szczegóły dotyczące kontaktu i komunikacji dla istniejących biletów pomocy technicznej. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Usuwa zasady dostępu. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Pobierz właściwości zasad dostępu. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Tworzy nową zasadę dostępu dla środowiska, lub aktualizuje istniejące zasady dostępu. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/delete | Usuwa środowisko. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Usuwa źródła zdarzeń. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/eventsources/read | Pobierz właściwości źródła zdarzenia. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/eventsources/write | Tworzy nowe źródło zdarzeń dla środowiska, lub aktualizuje istniejące źródło zdarzeń. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/read | Pobierz właściwości środowiska. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Usuwa odwołanie do zestawu danych. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Pobierz właściwości zestawu danych referencyjnych. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Tworzy nowego zestawu danych referencyjnych dla środowiska, lub aktualizuje istniejący zestaw danych referencyjnych. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/status/read | Pobierz stan środowiska w stanie jego skojarzony operacje, takie jak ruch przychodzący. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/write | Tworzy nowe środowisko, lub aktualizuje istniejące środowisko. |
> | Akcja | Microsoft.TimeSeriesInsights/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Time Series Insights oraz umożliwia tworzenie środowiska usługi Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.VisualStudio/Account/Delete | Usuń konto |
> | Akcja | Microsoft.VisualStudio/Account/Extension/Read | Rozszerzenie Account odczytu |
> | Akcja | Microsoft.VisualStudio/Account/Project/Read | Odczytaj konto/projektu |
> | Akcja | Microsoft.VisualStudio/Account/Project/Write | Ustaw konto/projekt |
> | Akcja | Microsoft.VisualStudio/Account/Read | Odczytaj konto |
> | Akcja | Microsoft.VisualStudio/Account/Write | Ustaw konto |
> | Akcja | Microsoft.VisualStudio/Extension/Delete | Usuń rozszerzenie |
> | Akcja | Microsoft.VisualStudio/Extension/Read | Rozszerzenie odczytu |
> | Akcja | Microsoft.VisualStudio/Extension/Write | Rozszerzenia zestawu |
> | Akcja | Microsoft.VisualStudio/Project/Delete | Usuń projekt |
> | Akcja | Microsoft.VisualStudio/Project/Read | Odczyt projektu |
> | Akcja | Microsoft.VisualStudio/Project/Write | Ustaw projekt |
> | Akcja | Microsoft.VisualStudio/Register/Action | Zarejestruj subskrypcję platformy Azure przy użyciu dostawcy Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | microsoft.web/apimanagementaccounts/apiacls/read | Pobierz interfejs Api zarządzania kontami Apiacls. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Usuwanie interfejsu Api zarządzania konta interfejsów API Apiacls. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/apiacls/read | Pobierz interfejs Api zarządzania konta interfejsów API Apiacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Write | Aktualizowanie interfejsu Api zarządzania konta interfejsów API Apiacls. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Pobierz interfejs Api zarządzania konta interfejsów API Connectionacls. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Upewnij się, zgody kodu interfejsu Api zarządzania konta interfejsów API połączeń. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Usuwanie interfejsu Api zarządzania konta interfejsów API połączeń Connectionacls. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Pobierz interfejs Api zarządzania konta interfejsów API połączeń Connectionacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Write | Aktualizowanie interfejsu Api zarządzania konta interfejsów API połączeń Connectionacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/DELETE | Usuwanie interfejsu Api zarządzania konta interfejsów API połączeń. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Pobieranie linków wyrażania zgody dla interfejsu Api zarządzania konta interfejsów API połączeń. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Lista połączeń klucze połączenia interfejsu Api zarządzania konta interfejsów API. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/listsecrets/Action | Lista kluczy tajnych połączenia interfejsu Api zarządzania konta interfejsów API. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/connections/read | Pobierz interfejs Api zarządzania konta interfejsów API połączeń. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/Write | Aktualizowanie interfejsu Api zarządzania konta interfejsów API połączeń. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/DELETE | Usuń interfejsów API usługi Api Management kont. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Usuwanie usługi Api Management interfejsy API kont zlokalizowane definicje. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Uzyskaj usługi Api Management interfejsy API kont zlokalizowane definicje. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Aktualizacja interfejsów Api zarządzania kontami usługi API zlokalizowany definicje. |
> | Akcja | microsoft.web/apimanagementaccounts/apis/read | Pobierz interfejsy API usługi Api Management kont. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Write | Zaktualizuj interfejsów API usługi Api Management kont. |
> | Akcja | microsoft.web/apimanagementaccounts/connectionacls/read | Pobierz interfejs Api zarządzania kontami Connectionacls. |
> | Akcja | microsoft.web/availablestacks/read | Pobierz dostępne stosów. |
> | Akcja | Microsoft.Web/certificates/Delete | Usunięcie istniejącego certyfikatu. |
> | Akcja | Microsoft.Web/certificates/Read | Pobierz listę certyfikatów. |
> | Akcja | Microsoft.Web/certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejącą. |
> | Akcja | microsoft.web/checknameavailability/read | Sprawdź, czy nazwa zasobu jest dostępny. |
> | Akcja | microsoft.web/classicmobileservices/read | Uzyskiwanie klasycznego usług Mobile Services. |
> | Akcja | Microsoft.Web/connectionGateways/Delete | Usuwa bramy połączenia. |
> | Akcja | Microsoft.Web/connectionGateways/Join/Action | Dołącza do bramy połączenia. |
> | Akcja | Microsoft.Web/connectionGateways/ListStatus/Action | Wyświetla stan bramy połączenia. |
> | Akcja | Microsoft.Web/connectionGateways/Move/Action | Przenosi bramy połączenia. |
> | Akcja | Microsoft.Web/connectionGateways/Read | Pobierz listę bram połączenia. |
> | Akcja | Microsoft.Web/connectionGateways/Write | Tworzy lub aktualizuje bramy połączenia. |
> | Akcja | Microsoft.Web/Connections/confirmconsentcode/Action | Upewnij się, kod wyrażania zgody połączenia. |
> | Akcja | Microsoft.Web/connections/Delete | Usuwa połączenie. |
> | Akcja | Microsoft.Web/connections/Join/Action | Dołącza do połączenia. |
> | Akcja | microsoft.web/connections/listconsentlinks/action | Listy linków wyrażania zgody dla połączeń. |
> | Akcja | Microsoft.Web/connections/Move/Action | Przenosi połączenia. |
> | Akcja | Microsoft.Web/connections/Read | Pobierz listę połączeń. |
> | Akcja | Microsoft.Web/connections/Write | Tworzy lub aktualizuje połączenia. |
> | Akcja | Microsoft.Web/customApis/Delete | Usuwa niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Wyodrębnia definicji interfejsu API z WSDL. |
> | Akcja | Microsoft.Web/customApis/Join/Action | Dołącza do niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/customApis/listWsdlInterfaces/Action | Wyświetla listę interfejsów WSDL dla niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/customApis/Move/Action | Przenosi niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/customApis/Read | Pobierz listę niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/customApis/Write | Tworzy lub aktualizuje niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/deletedSites/Read | Pobierz właściwości aplikacji sieci Web usunięto |
> | Akcja | microsoft.web/deploymentlocations/read | Uzyskanie lokalizacji wdrożenia. |
> | Akcja | Microsoft.Web/geoRegions/Read | Pobierz listę regionów geograficznych. |
> | Akcja | microsoft.web/hostingenvironments/capacities/read | Pobierz hostingu możliwości środowiska. |
> | Akcja | Microsoft.Web/hostingEnvironments/Delete | Usuń środowisko App Service Environment |
> | Akcja | microsoft.web/hostingenvironments/detectors/read | Pobierz hostingu detektory środowisk. |
> | Akcja | microsoft.web/hostingenvironments/diagnostics/read | Pobierz hostingu środowiska diagnostyki. |
> | Akcja | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Pobierz punkty końcowe sieci wszystkie zależności dla ruchu przychodzącego. |
> | Akcja | Microsoft.Web/hostingEnvironments/Join/Action | Dołącza środowisko App Service Environment |
> | Akcja | microsoft.web/hostingenvironments/metricdefinitions/read | Pobierz hostingu definicje metryk środowiska. |
> | Akcja | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Pobierz hostingu definicje metryk pule pełniących wiele środowisk. |
> | Akcja | microsoft.web/hostingenvironments/multirolepools/metrics/read | Pobierz hostingu środowisk pełniących wiele pul metryki. |
> | Akcja | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Pobierz właściwości puli frontonu w środowisku usługi App Service |
> | Akcja | microsoft.web/hostingenvironments/multirolepools/skus/read | Pobierz hostingu środowisk pełniących wiele pul w jednostki SKU. |
> | Akcja | microsoft.web/hostingenvironments/multirolepools/usages/read | Pobierz hostingu środowisk pełniących wiele pul użycia. |
> | Akcja | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Tworzenie nowej puli frontonu w środowisku usługi App Service lub zaktualizuj istniejącą |
> | Akcja | microsoft.web/hostingenvironments/operations/read | Pobierz hostingu operacji środowiska. |
> | Akcja | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Pobierz punkty końcowe sieci wszystkie zależności ruchu wychodzącego. |
> | Akcja | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Połączenia prywatne punktu końcowego zatwierdzenia |
> | Akcja | Microsoft.Web/hostingEnvironments/Read | Pobierz właściwości środowiska usługi App Service |
> | Akcja | Microsoft.Web/hostingEnvironments/reboot/Action | Ponowne uruchomienie wszystkich maszyn w środowisku usługi App Service |
> | Akcja | Microsoft.Web/hostingenvironments/resume/Action | Wznów, środowiskach hostingu. |
> | Akcja | microsoft.web/hostingenvironments/serverfarms/read | Uzyskaj plany usługi App Service środowiska hostingu. |
> | Akcja | microsoft.web/hostingenvironments/sites/read | Uzyskaj hostowanie aplikacji sieci Web środowiska. |
> | Akcja | Microsoft.Web/hostingenvironments/suspend/Action | Wstrzymaj, środowiskach hostingu. |
> | Akcja | microsoft.web/hostingenvironments/usages/read | Pobierz hostingu użycia środowiska. |
> | Akcja | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Pobierz hostingu definicje metryki Workerpools środowisk. |
> | Akcja | microsoft.web/hostingenvironments/workerpools/metrics/read | Pobierz hostingu środowiska Workerpools metryki. |
> | Akcja | Microsoft.Web/hostingEnvironments/workerPools/Read | Pobierz właściwości puli procesów roboczych w środowisku usługi App Service |
> | Akcja | microsoft.web/hostingenvironments/workerpools/skus/read | Pobierz hostingu jednostki SKU Workerpools środowisk. |
> | Akcja | microsoft.web/hostingenvironments/workerpools/usages/read | Pobierz hostingu środowiska Workerpools użycia. |
> | Akcja | Microsoft.Web/hostingEnvironments/workerPools/Write | Tworzenie nowej puli procesów roboczych w środowisku usługi App Service lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Web/hostingEnvironments/Write | Utwórz nowe środowisko App Service Environment lub zaktualizuj istniejącą grupę |
> | Akcja | microsoft.web/ishostingenvironmentnameavailable/read | GET, jeśli nazwa środowiska hostingu jest dostępna. |
> | Akcja | microsoft.web/ishostnameavailable/read | Sprawdź, czy nazwa hosta jest dostępny. |
> | Akcja | microsoft.web/isusernameavailable/read | Sprawdź, czy nazwa użytkownika jest dostępna. |
> | Akcja | Microsoft.Web/listSitesAssignedToHostName/Read | Pobierz nazwy lokacji przypisany do nazwy hosta. |
> | Akcja | microsoft.web/locations/apioperations/read | Pobierz operacje lokalizacji interfejsu API. |
> | Akcja | microsoft.web/locations/connectiongatewayinstallations/read | Rozpoczynanie instalacji bramy połączenia lokalizacji. |
> | Akcja | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Sieć wirtualna lub podsieć powiadomienie usunięcia dla lokalizacji. |
> | Akcja | microsoft.web/locations/extractapidefinitionfromwsdl/action | Prowadzenie definicji interfejsu Api WSDL dla lokalizacji. |
> | Akcja | microsoft.web/locations/listwsdlinterfaces/action | Interfejsy WSDL listy dla lokalizacji. |
> | Akcja | microsoft.web/locations/managedapis/apioperations/read | Pobierz operacje zarządzany interfejs API lokalizacji. |
> | Akcja | Microsoft.Web/locations/managedapis/Join/Action | Tworzy sprzężenie zarządzany interfejs API. |
> | Akcja | microsoft.web/locations/managedapis/read | Pobierz lokalizacje zarządzanych interfejsów API. |
> | Akcja | microsoft.web/operations/read | Pobierz operacje. |
> | Akcja | microsoft.web/publishingusers/read | Rozpoczynanie publikowania użytkowników. |
> | Akcja | microsoft.web/publishingusers/write | Aktualizacja publikowanie użytkowników. |
> | Akcja | Microsoft.Web/recommendations/Read | Pobierz listę zaleceń dla subskrypcji. |
> | Akcja | Microsoft.Web/Register/Action | Rejestrowanie dostawcy zasobów Microsoft.Web dla subskrypcji. |
> | Akcja | microsoft.web/resourcehealthmetadata/read | Pobierz metadane kondycji zasobu. |
> | Akcja | microsoft.web/serverfarms/capabilities/read | Skorzystaj z możliwości planów usługi App Service. |
> | Akcja | Microsoft.Web/serverfarms/Delete | Usuń istniejący Plan usługi App Service |
> | Akcja | microsoft.web/serverfarms/firstpartyapps/settings/delete | Usuń ustawienia aplikacji innych firm pierwszy planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/firstpartyapps/settings/read | Pobierz ustawienia aplikacji innych firm pierwszy planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/firstpartyapps/settings/write | Aktualizowanie ustawień aplikacji innych firm pierwszy planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Usuń usługi App Service plany hybrydowe połączenie przestrzeni nazw przekaźników. |
> | Akcja | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Uzyskaj usługi App Service plany hybrydowe połączenie przestrzeni nazw przekaźników. |
> | Akcja | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Uzyskaj hybrydowe planów usługi App Service połączenia aplikacje sieci Web przekaźniki przestrzeni nazw. |
> | Akcja | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Uzyskaj limity planu połączeń hybrydowych planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/hybridconnectionrelays/read | Uzyskaj przekaźniki połączenia hybrydowego planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/metricdefinitions/read | Pobierz definicje metryk planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/metrics/read | Pobierz metryki planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/operationresults/read | Pobierz wyniki operacji planów usługi App Service. |
> | Akcja | Microsoft.Web/serverfarms/Read | Pobiera właściwości planu usługi App Service |
> | Akcja | Microsoft.Web/serverfarms/restartSites/Action | Uruchom ponownie wszystkie aplikacje sieci Web w planie usługi App Service |
> | Akcja | microsoft.web/serverfarms/sites/read | Pobierz aplikacje sieci Web planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/skus/read | Pobierz jednostki SKU planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/usages/read | Pobierz użycia planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Zaktualizuj bramy połączenia sieci wirtualnej planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/read | Uzyskaj połączenia sieć wirtualna planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Usuń trasy połączenia sieć wirtualna planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Uzyskaj tras połączenia sieci wirtualnych planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Aktualizowanie tras połączenia sieci wirtualnych planów usługi App Service. |
> | Akcja | microsoft.web/serverfarms/workers/reboot/action | Uruchom ponownie pracowników planów usługi App Service. |
> | Akcja | Microsoft.Web/serverfarms/Write | Utwórz nowy Plan usługi App Service lub zaktualizuj istniejącą |
> | Akcja | microsoft.web/sites/analyzecustomhostname/read | Analizuj niestandardową nazwę hosta. |
> | Akcja | Microsoft.Web/sites/applySlotConfig/Action | Zastosuj konfigurację sieci web app miejsca w docelowym gnieździe w bieżącej aplikacji sieci web |
> | Akcja | Microsoft.Web/sites/backup/Action | Utwórz nową kopię zapasową aplikacji sieci web |
> | Akcja | Microsoft.Web/Sites/Backup/Read | Uzyskaj kopii zapasowej aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/backup/write | Aktualizacja kopii zapasowej aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/backups/action | Odnajduje istniejącą kopię zapasową aplikacji, którą można przywrócić z obiektu blob w usłudze Azure storage. |
> | Akcja | microsoft.web/sites/backups/delete | Usuwanie kopii zapasowych aplikacji internetowych. |
> | Akcja | microsoft.web/sites/backups/list/action | Wykaz kopii zapasowych aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/backups/Read | Pobierz właściwości elementu kopii zapasowej aplikacji sieci web |
> | Akcja | Microsoft.Web/Sites/Backups/Restore/Action | Przywracanie kopii zapasowych aplikacji internetowych. |
> | Akcja | microsoft.web/sites/backups/write | Aktualizowanie kopii zapasowych aplikacji internetowych. |
> | Akcja | Microsoft.Web/Sites/config/DELETE | Usuwanie konfiguracji aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/config/list/Action | Lista aplikacji sieci Web poufne ustawienia zabezpieczeń, takich jak publikowanie poświadczeń, ustawienia aplikacji i parametrów połączenia |
> | Akcja | Microsoft.Web/sites/config/Read | Pobieranie ustawień konfiguracji aplikacji sieci Web |
> | Akcja | microsoft.web/sites/config/snapshots/read | Pobierz migawki konfiguracji aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/config/Write | Aktualizowanie ustawień konfiguracji aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/containerlogs/Action | Pobierz pliki z rozszerzeniem zip dzienniki kontenerów dla aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/containerlogs/Download/Action | Pobierz dzienniki kontenerów aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/continuouswebjobs/DELETE | Usuwanie zadania Webjob ciągłej aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/continuouswebjobs/Read | Pobierz zadania ciągłego sieci Web aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/continuouswebjobs/Start/Action | Uruchom zadania ciągłego sieci Web aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/continuouswebjobs/Stop/Action | Zatrzymywanie zadania Webjob ciągłej aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/Delete | Usuń istniejącą aplikację sieci Web |
> | Akcja | microsoft.web/sites/deployments/delete | Usunięcie wdrożenia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/deployments/log/read | Pobierz dziennik wdrożenia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/deployments/read | Uzyskaj wdrożenia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/deployments/write | Aktualizowanie wdrożenia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/detectors/read | Uzyskaj detektory aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/analyses/execute/Action | Przeprowadź analizę Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/analyses/read | Uzyskaj analizy Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/aspnetcore/read | Uzyskiwanie diagnostyki aplikacji sieci Web dla aplikacji platformy ASP.NET Core. |
> | Akcja | microsoft.web/sites/diagnostics/autoheal/read | Uzyskaj funkcji Autoheal diagnostyki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/deployment/read | Pobierz wdrożenie diagnostyki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/deployments/read | Uzyskaj wdrożeń Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/detectors/execute/Action | Uruchom wykrywanie diagnostyki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/detectors/read | Uzyskaj wykrywanie diagnostyki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Uzyskaj żądania sieci Web diagnostyki aplikacji nie powiodło się dla identyfikatora Uri. |
> | Akcja | microsoft.web/sites/diagnostics/frebanalysis/read | Uzyskaj analizy FREB diagnostyki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/loganalyzer/read | Uzyskaj analizator dzienników diagnostyki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/read | Uzyskaj kategorie Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/runtimeavailability/read | Uzyskaj dostępność środowiska uruchomieniowego Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/servicehealth/read | Pobierz stan usługi Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Uzyskaj analizy użycia Procesora witryny Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/sitecrashes/read | Uzyskaj awarii lokacji Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/sitelatency/read | Uzyskaj czas oczekiwania witryny Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Uzyskaj analizy pamięci witryny Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Uzyskaj Aktualizacja ustawień ponowne uruchomienie witryny Diagnostyka aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/siterestartuserinitiated/Read | Uzyskaj sieci Web diagnostyki aplikacji witryny inicjowane przez ponowne uruchomienie użytkownika. |
> | Akcja | microsoft.web/sites/diagnostics/siteswap/read | Uzyskaj wymiany lokacji Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/threadcount/read | Pobieranie liczby wątków Diagnostyka aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/workeravailability/read | Uzyskaj Workeravailability diagnostyki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Uzyskaj odtworzenia procesu roboczego Diagnostyka aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/domainownershipidentifiers/Read | Pobierz identyfikatory własności domeny aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/domainownershipidentifiers/Write | Aktualizuj identyfikatory własności domeny aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Extensions/DELETE | Usuwanie rozszerzenia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/extensions/read | Można pobierać rozszerzenia witryny aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Extensions/Write | Aktualizowanie rozszerzenia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/Action | Funkcje Web Apps. |
> | Akcja | Microsoft.Web/Sites/Functions/DELETE | Usuwanie funkcji aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/listsecrets/Action | Listy kluczy tajnych funkcji aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/masterkey/Read | Uzyskaj Masterkey funkcje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/Read | Uzyskaj funkcje aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/functions/token/read | Token funkcje aplikacji sieci Web GET. |
> | Akcja | Microsoft.Web/Sites/Functions/Write | Aktualizuj funkcje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hostnamebindings/DELETE | Usuwanie powiązania nazwy hosta aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/hostnamebindings/read | Uzyskaj powiązania nazwy hosta aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/hostnamebindings/write | Zaktualizuj powiązania nazwy hosta aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/hostruntime/functions/keys/read | Pobierz klucze funkcji Hostruntime aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/hostruntime/host/_master/read | Pobierz klucz główny w aplikacji funkcji dla operacji administratora |
> | Akcja | Microsoft.Web/sites/hostruntime/host/action | Wykonywanie aplikacji funkcji środowiska uruchomieniowego akcji, takich jak synchronizowanie wyzwalaczy, Dodaj funkcje, wywoływać funkcje i usuwanie funkcji itp. |
> | Akcja | Microsoft.Web/Sites/hostruntime/host/Read | Uzyskiwanie hosta Hostruntime aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnection/DELETE | Usuwanie połączenia hybrydowego aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnection/Read | Uzyskaj połączenie hybrydowe aplikacje sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnection/Write | Zaktualizuj połączenie hybrydowe aplikacje sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/DELETE | Usuń przekaźniki przestrzenie nazw połączenia hybrydowe aplikacje sieci Web. |
> | Akcja | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Lista kluczy sieci Web aplikacji hybrydowych połączenia przestrzenie nazw przekaźników. |
> | Akcja | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Uzyskaj przekaźniki przestrzenie nazw połączenia hybrydowe aplikacje sieci Web. |
> | Akcja | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Zaktualizuj przekaźniki przestrzenie nazw połączenia hybrydowe aplikacje sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnectionrelays/Read | Uzyskaj przekaźniki połączenia hybrydowe aplikacje sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/Deployments/DELETE | Usuwanie wdrożenia wystąpienia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/instances/deployments/read | Uzyskaj wdrożenia wystąpienia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/instances/extensions/log/read | Pobierz dziennik rozszerzenia wystąpienia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/instances/extensions/processes/read | Uzyskaj procesów rozszerzenia wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/Extensions/Read | Można pobierać rozszerzenia wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/processes/DELETE | Usuń procesy wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/processes/Modules/Read | Pobierz moduły procesy wystąpienia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/instances/processes/read | Uzyskaj procesy wystąpienia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/instances/processes/threads/read | Rozpoczynanie wątków procesów wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/Read | Pobierz wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/listsyncfunctiontriggerstatus/Action | Lista synchronizacji funkcji wyzwalacza stan aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/metricdefinitions/read | Pobierz definicje metryki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/metrics/read | Pobierz metryki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/metricsdefinitions/read | Pobierz definicje metryk aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/migratemysql/Action | Przeprowadź migrację aplikacji sieci Web MySql. |
> | Akcja | microsoft.web/sites/migratemysql/read | Get Web Apps Migrate MySql. |
> | Akcja | microsoft.web/sites/networktrace/action | Network Trace Web Apps. |
> | Akcja | microsoft.web/sites/networktraces/operationresults/read | Pobierz wyniki operacji śledzenia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/newpassword/Action | Newpassword Web Apps. |
> | Akcja | Microsoft.Web/Sites/operationresults/Read | Pobierz wyniki operacji aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Operations/Read | Pobierz operacje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/perfcounters/Read | Pobierz liczniki wydajności aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/premieraddons/delete | Usuń dodatków Premier aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/premieraddons/read | Uzyskaj dodatków Premier aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/premieraddons/write | Zaktualizuj dodatków Premier aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/privateaccess/read | Pobierz dane dotyczące włączania dostępu do prywatnej witryny i autoryzowanych sieci wirtualnych, które mogą uzyskać dostęp do witryny. |
> | Akcja | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Połączenia prywatne punktu końcowego zatwierdzenia |
> | Akcja | microsoft.web/sites/processes/modules/read | Pobierz moduły procesów aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/processes/read | Uzyskaj procesy aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/processes/threads/read | Rozpoczynanie wątków procesów aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/publiccertificates/delete | Usuń certyfikat publiczny aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/publiccertificates/read | Pobierz certyfikat publiczny aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/publiccertificates/write | Aktualizuj certyfikaty publiczne aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/publish/Action | Publikowanie aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/publishxml/Action | Rozpoczynanie publikowania pliku xml profilu dla aplikacji sieci Web |
> | Akcja | microsoft.web/sites/publishxml/read | Pobierać aplikacje sieci Web, publikowanie XML. |
> | Akcja | Microsoft.Web/sites/Read | Pobierz właściwości aplikacji sieci Web |
> | Akcja | microsoft.web/sites/recommendationhistory/read | Pobieranie historii zalecenie dotyczące aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/recommendations/disable/Action | Wyłącz zalecenia dotyczące aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/recommendations/Read | Pobierz listę zaleceń dotyczących aplikacji sieci web. |
> | Akcja | Microsoft.Web/Sites/Recover/Action | Odzyskiwanie aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/resetSlotConfig/Action | Zresetuj konfigurację aplikacji sieci web |
> | Akcja | Microsoft.Web/Sites/resourcehealthmetadata/Read | Pobierz metadane kondycji zasobów aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/restart/Action | Uruchom ponownie aplikację internetową |
> | Akcja | microsoft.web/sites/restore/read | Rozpoczynanie przywracania aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/restore/write | Restore Web Apps. |
> | Akcja | microsoft.web/sites/restorefrombackupblob/action | Przywracanie aplikacji internetowej z kopii zapasowej magazynu obiektów Blob. |
> | Akcja | microsoft.web/sites/restorefromdeletedwebapp/action | Przywracanie aplikacji sieci Web z usunięto aplikację. |
> | Akcja | Microsoft.Web/Sites/restoresnapshot/Action | Przywracanie migawki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/siteextensions/DELETE | Usuwanie rozszerzenia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/siteextensions/Read | Można pobierać rozszerzenia witryny aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/siteextensions/Write | Aktualizowanie rozszerzenia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/analyzecustomhostname/read | Pobierz Web miejsc aplikacji analizowanie niestandardową nazwę hosta. |
> | Akcja | Microsoft.Web/sites/slots/applySlotConfig/Action | Zastosuj konfigurację sieci web app miejsca w docelowym gnieździe do bieżącego miejsca. |
> | Akcja | Microsoft.Web/sites/slots/backup/Action | Utwórz nową kopię zapasową miejsca aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/backup/read | Pobierz kopię zapasową miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/backup/write | Aktualizacja kopii zapasowej miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/backups/action | Wykryj kopie zapasowe miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/backups/delete | Usuwanie kopii zapasowych miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/backups/list/action | Lista miejsca kopii zapasowych aplikacji internetowych. |
> | Akcja | Microsoft.Web/sites/slots/backups/Read | Pobierz właściwości kopia zapasowa miejsc aplikacji sieci web |
> | Akcja | microsoft.web/sites/slots/backups/restore/action | Przywracanie kopii zapasowych miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/config/delete | Usuwanie konfiguracji miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/config/list/Action | Lista miejsca aplikacji sieci Web poufne ustawienia zabezpieczeń, takich jak publikowanie poświadczeń, ustawienia aplikacji i parametrów połączenia |
> | Akcja | Microsoft.Web/sites/slots/config/Read | Pobieranie ustawień konfiguracji miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/config/Write | Aktualizowanie ustawień konfiguracji miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/containerlogs/Action | Pobierz pliki z rozszerzeniem zip dzienniki kontenera dla miejsca aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/containerlogs/download/action | Pobierz dzienniki kontenerów miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/continuouswebjobs/DELETE | Usuwanie sieci Web Apps miejsc ciągłe zadania Web Job. |
> | Akcja | Microsoft.Web/Sites/Slots/continuouswebjobs/Read | Uzyskaj Web Apps miejsc ciągłe zadania Web Job. |
> | Akcja | Microsoft.Web/Sites/Slots/continuouswebjobs/Start/Action | Uruchom Web Apps miejsc ciągłe zadania Web Job. |
> | Akcja | Microsoft.Web/Sites/Slots/continuouswebjobs/Stop/Action | Zatrzymaj Web Apps miejsc ciągłe zadania Web Job. |
> | Akcja | Microsoft.Web/sites/slots/Delete | Usuwanie istniejącego miejsca aplikacji sieci Web |
> | Akcja | microsoft.web/sites/slots/deployments/delete | Usuwanie wdrożenia miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/deployments/log/read | Pobierz dziennik wdrożeń miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/deployments/read | Uzyskaj wdrożeń miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/deployments/write | Aktualizowanie wdrożenia miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/detectors/read | Uzyskaj detektory miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Uruchom analizę diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/analyses/read | Uzyskaj analizy diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Uzyskiwanie diagnostyki miejsc aplikacji sieci Web dla aplikacji platformy ASP.NET Core. |
> | Akcja | microsoft.web/sites/slots/diagnostics/autoheal/read | Uzyskaj Web Apps miejsc diagnostyki w funkcji Autoheal. |
> | Akcja | microsoft.web/sites/slots/diagnostics/deployment/read | Rozpoczynanie wdrażanie diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/deployments/read | Uzyskaj wdrożeń diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Uruchom wykrywanie diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/detectors/read | Uzyskaj wykrywanie diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Uzyskaj Web Apps miejsc diagnostyki FREB analizy. |
> | Akcja | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Uzyskaj sieci Web Apps miejsc diagnostyki dziennika analizatora. |
> | Akcja | microsoft.web/sites/slots/diagnostics/read | Pobierz diagnostykę miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Uzyskaj dostępność diagnostyki gniazda sieci Web aplikacji w czasie wykonywania. |
> | Akcja | microsoft.web/sites/slots/diagnostics/servicehealth/read | Pobierz stan usługi Diagnostyka miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Uzyskaj analizy Procesora witryny diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Uzyskaj sieci Web Apps miejsc Diagnostyka witryny awarie. |
> | Akcja | microsoft.web/sites/slots/diagnostics/sitelatency/read | Uzyskaj czas oczekiwania witryny diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Uzyskaj analizy pamięci witryny diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Uzyskaj miejsc aplikacji sieci Web diagnostyki Aktualizacja ustawień ponowne uruchomienie witryny. |
> | Akcja | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Uzyskaj sieci Web Apps miejsc diagnostyki lokacji ponowne uruchomienie inicjowane przez użytkownika. |
> | Akcja | microsoft.web/sites/slots/diagnostics/siteswap/read | Uzyskaj Web Apps miejsc diagnostyki lokacji wymiany. |
> | Akcja | microsoft.web/sites/slots/diagnostics/threadcount/read | Pobieranie liczby wątków diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/workeravailability/read | Uzyskaj Workeravailability diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Uzyskaj odtworzenia procesu procesu roboczego diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/domainownershipidentifiers/Read | Uzyskaj sieci Web Apps miejsc domeny własność identyfikatorów. |
> | Akcja | Microsoft.Web/Sites/Slots/Functions/listsecrets/Action | Funkcji miejsc listy wpisów tajnych w sieci Web aplikacji. |
> | Akcja | microsoft.web/sites/slots/functions/read | Uzyskaj funkcji miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/hostnamebindings/delete | Usuwanie powiązania nazwy hosta miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/hostnamebindings/read | Uzyskaj powiązania nazwy hosta miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/hostnamebindings/write | Zaktualizuj powiązania nazwy hosta miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/hybridconnection/DELETE | Usuwanie połączenia hybrydowego miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/hybridconnection/Read | Uzyskaj połączenie hybrydowe miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/hybridconnection/Write | Aktualizowanie połączenia hybrydowego miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Usuwanie sieci Web aplikacji miejsc hybrydowe połączenie przestrzeni nazw przekaźników. |
> | Akcja | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Aktualizowanie sieci Web aplikacji miejsc hybrydowe połączenie przestrzeni nazw przekaźników. |
> | Akcja | microsoft.web/sites/slots/hybridconnectionrelays/read | Uzyskaj sieci Web Apps miejsc hybrydowego połączenia przekaźników. |
> | Akcja | microsoft.web/sites/slots/instances/deployments/read | Uzyskaj wdrożenia wystąpienia miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/instances/processes/delete | Usuń procesy wystąpień miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/instances/processes/read | Uzyskaj procesy wystąpień miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/instances/read | Pobierz wystąpienia miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/metricdefinitions/read | Pobierz definicje metryk miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/metrics/read | Pobierz metryki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/migratemysql/read | Pobierz Web miejsc aplikacji migracji MySql. |
> | Akcja | microsoft.web/sites/slots/networktrace/action | Miejsc aplikacji sieci Web śledzenia sieci. |
> | Akcja | microsoft.web/sites/slots/networktraces/operationresults/read | Pobierz wyniki operacji śledzenia sieci na miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/newpassword/Action | Miejsc aplikacji sieci Web Newpassword. |
> | Akcja | microsoft.web/sites/slots/operationresults/read | Pobierz wyniki operacji miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/operations/read | Pobierz operacje miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/perfcounters/Read | Pobierz liczniki wydajności miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/phplogging/read | Uzyskaj Phplogging miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/premieraddons/delete | Usuń dodatków Premier miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/premieraddons/read | Uzyskaj dodatków Premier miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/premieraddons/write | Zaktualizuj dodatków Premier miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/processes/read | Uzyskaj procesy miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/publiccertificates/delete | Usuń certyfikaty publiczne miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/publiccertificates/read | Pobierz certyfikaty publiczne miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/publiccertificates/write | Tworzenie lub aktualizowanie certyfikatów publicznych miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/publish/Action | Publikowanie miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/publishxml/Action | Rozpoczynanie publikowania pliku xml profilu dla miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/Read | Pobierz właściwości miejsce wdrożenia aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/Recover/Action | Odzyskaj miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/resetSlotConfig/Action | Resetowanie konfiguracji miejsca aplikacji sieci web |
> | Akcja | Microsoft.Web/Sites/Slots/resourcehealthmetadata/Read | Pobierz metadane kondycji zasobu miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/restart/Action | Uruchom ponownie miejsca aplikacji sieci Web |
> | Akcja | microsoft.web/sites/slots/restore/read | Rozpoczynanie przywracania miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/restore/write | Przywróć miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/restorefrombackupblob/action | Przywróć miejsca aplikacji sieci Web z obiektów Blob kopii zapasowej. |
> | Akcja | microsoft.web/sites/slots/restorefromdeletedwebapp/action | Przywróć miejsc aplikacji sieci Web z usunięto aplikację. |
> | Akcja | Microsoft.Web/Sites/Slots/restoresnapshot/Action | Przywracanie migawki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/siteextensions/DELETE | Usuń rozszerzenia witryny miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/siteextensions/read | Pobierz rozszerzenia witryny miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/siteextensions/Write | Aktualizowanie rozszerzeń witryny miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/slotsdiffs/Action | Pobierz różnic w konfiguracji aplikacji sieci web i gniazd |
> | Akcja | Microsoft.Web/sites/slots/slotsswap/Action | Zamiana miejsc wdrożenia aplikacji sieci Web |
> | Akcja | microsoft.web/sites/slots/snapshots/read | Pobierz migawki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/sourcecontrols/Read | Pobierz ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/start/Action | Rozpocznij miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/stop/Action | Zatrzymaj miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/Sync/Action | Synchronizacja miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/triggeredwebjobs/delete | Usuwanie zadania Webjob wyzwalane miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/triggeredwebjobs/read | Rozpoczynanie zadania Webjob wyzwalane miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/triggeredwebjobs/Run/Action | Uruchamianie zadania Webjob wyzwalane miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/usages/read | Pobierz użycia miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/virtualnetworkconnections/delete | Usuwanie połączeń sieci wirtualnej miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Zaktualizuj bramy połączenia sieci wirtualnej miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/virtualnetworkconnections/read | Uzyskaj połączeń sieci wirtualnej miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/virtualnetworkconnections/write | Aktualizowanie połączenia sieć wirtualna miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/webjobs/Read | Rozpoczynanie zadania Webjob miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/Write | Utwórz nowe miejsce aplikacji sieci Web lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Web/sites/slotsdiffs/Action | Pobierz różnic w konfiguracji aplikacji sieci web i gniazd |
> | Akcja | Microsoft.Web/sites/slotsswap/Action | Zamiana miejsc wdrożenia aplikacji sieci Web |
> | Akcja | microsoft.web/sites/snapshots/read | Pobierz migawki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła dla aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/sourcecontrols/Read | Pobieranie ustawień konfiguracji aplikacji sieci Web kontroli źródła |
> | Akcja | Microsoft.Web/sites/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła dla aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/start/Action | Uruchamiają aplikację sieci Web |
> | Akcja | Microsoft.Web/sites/stop/Action | Zatrzymaj aplikację sieci Web |
> | Akcja | Microsoft.Web/Sites/Sync/Action | Synchronizuj aplikacje sieci Web. |
> | Akcja | Microsoft.Web/Sites/syncfunctiontriggers/Action | Wyzwalacze funkcji synchronizacji dla aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/triggeredwebjobs/DELETE | Usuń wyzwolone zadania Webjob w aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/triggeredwebjobs/history/read | Pobieranie historii wyzwolone zadania Webjob aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/triggeredwebjobs/read | Uzyskaj wyzwolone zadania Webjob w aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/triggeredwebjobs/Run/Action | Aplikacje sieci Web wykonywania wyzwolone zadania Webjob. |
> | Akcja | microsoft.web/sites/usages/read | Pobierz użycia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/delete | Usuwanie połączenia sieć wirtualna aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/gateways/read | Pobierz bramy połączenia sieci wirtualnej aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/gateways/write | Zaktualizuj bramy połączenia sieci wirtualnej aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/read | Uzyskaj połączenia sieć wirtualna aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/write | Aktualizowanie połączenia sieć wirtualna aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/webjobs/Read | Rozpoczynanie zadania Webjob aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/Write | Utwórz nową aplikację sieci Web lub zaktualizuj istniejącą |
> | Akcja | microsoft.web/skus/read | Pobierz jednostki SKU. |
> | Akcja | microsoft.web/sourcecontrols/read | Uzyskaj kontroli źródła. |
> | Akcja | microsoft.web/sourcecontrols/write | Zaktualizuj kontrolki źródła. |
> | Akcja | Microsoft.Web/unregister/Action | Wyrejestruj dostawcę zasobów Microsoft.Web dla subskrypcji. |
> | Akcja | microsoft.web/validate/action | Sprawdzanie poprawności. |
> | Akcja | microsoft.web/verifyhostingenvironmentvnet/action | Upewnij się, hostingu środowiska sieci wirtualnej. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.WorkloadMonitor/components/read | Pobiera składników dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/componentsSummary/read | Pobiera podsumowanie składników |
> | Akcja | Microsoft.WorkloadMonitor/monitorInstances/read | Pobiera wystąpienia monitorów dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Pobiera podsumowanie wystąpień |
> | Akcja | Microsoft.WorkloadMonitor/monitors/read | Pobiera monitorów dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/monitors/write | Konfigurowanie monitorowania dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/notificationSettings/read | Pobiera ustawienia powiadomień dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/notificationSettings/write | Skonfiguruj ustawienia powiadomień dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/operations/read | Pobiera obsługiwane operacje |

## <a name="next-steps"></a>Kolejne kroki

- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
