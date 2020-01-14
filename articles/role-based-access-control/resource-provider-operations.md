---
title: Operacje dostawcy zasobów Azure Resource Manager | Microsoft Docs
description: Wyświetla listę operacji dostępnych dla dostawców zasobów Menedżer zasobów Microsoft Azure
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9d4b4134fa26fd2cb904a862ac16544873bf8bcb
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934459"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operacje dostawcy zasobów Azure Resource Manager

W tym artykule wymieniono operacje dostępne dla każdego dostawcy zasobów Azure Resource Manager. Te operacje mogą być używane w [rolach niestandardowych](custom-roles.md) w celu zapewnienia szczegółowej [kontroli dostępu opartej na rolach (RBAC)](overview.md) na potrzeby zasobów na platformie Azure. Ciągi operacji mają następujący format: `{Company}.{ProviderName}/{resourceType}/{action}`. Aby uzyskać listę sposobu mapowania przestrzeni nazw dostawcy zasobów na usługi platformy Azure, zobacz [dopasowanie dostawcy zasobów do usługi](../azure-resource-manager/azure-services-resource-providers.md).

Operacje dostawcy zasobów są zawsze rozwijane. Aby uzyskać najnowsze operacje, Użyj listy operacji [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) lub [AZ Provider](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. AAD/domainServices/Delete | Usuwanie usługi domeny |
> | Działanie | Microsoft. AAD/domainServices/oucontainer/usuwanie | Usuń kontener jednostki organizacyjnej |
> | Działanie | Microsoft. AAD/domainServices/oucontainer/odczyt | Odczytaj kontenery jednostek organizacyjnych |
> | Działanie | Microsoft. AAD/domainServices/oucontainer/zapis | Zapisz kontener jednostki organizacyjnej |
> | Działanie | Microsoft.AAD/domainServices/read | Odczytaj usługi domenowe |
> | Działanie | Microsoft. AAD/domainServices/Write | Zapisz usługę domenową |
> | Działanie | Microsoft.AAD/locations/operationresults/read |  |
> | Działanie | Microsoft. AAD/Operations/Read |  |
> | Działanie | Microsoft. AAD/rejestrowanie/akcja | Zarejestruj usługę domeny |
> | Działanie | Microsoft. AAD/Wyrejestruj/akcja | Wyrejestruj usługę domenową |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. aadiam/diagnosticsettings/Delete | Usuwanie ustawień diagnostycznych |
> | Działanie | Microsoft. aadiam/diagnosticsettings/odczyt | Odczytywanie ustawień diagnostycznych |
> | Działanie | Microsoft. aadiam/diagnosticsettings/Write | Zapisywanie ustawień diagnostycznych |
> | Działanie | Microsoft. aadiam/diagnosticsettingscategories/odczyt | Odczytywanie kategorii ustawień diagnostycznych |
> | Działanie | Microsoft. aadiam/metricDefinitions/odczyt | Odczytywanie definicji metryk na poziomie dzierżawy |
> | Działanie | Microsoft. aadiam/Metrics/Read | Odczytywanie metryk na poziomie dzierżawy |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Dodatki/operacje/odczyt | Pobiera obsługiwane operacje RP. |
> | Działanie | Microsoft. Dodatki/rejestrowanie/akcja | Zarejestruj określoną subskrypcję w programie Microsoft. Dodatki |
> | Działanie | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Wyświetla bieżące informacje o planach pomocy technicznej dla określonej subskrypcji. |
> | Działanie | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Usuwa określony plan obsługi kanonicznej |
> | Działanie | Microsoft.Addons/supportProviders/supportPlanTypes/read | Pobierz określony stan kanonicznego planu pomocy technicznej. |
> | Działanie | Microsoft.Addons/supportProviders/supportPlanTypes/write | Dodaje określony typ planu obsługi kanonicznej. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Action | Utwórz nowy las dla dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/addomainservicemembers/Read | Pobiera wszystkie serwery dla określonej nazwy usługi. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Alerts/Read | Pobiera szczegóły alertów dla lasu, takie jak alertid, Data wygenerowania alertu, ostatnie wykrycie alertu, opis alertu, Ostatnia aktualizacja, poziom alertu, stan alertu, linki do rozwiązywania problemów z alertami itp. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/konfiguracja/odczyt | Pobiera konfigurację usługi dla lasu. Przykład — nazwa lasu, poziom funkcjonalności, główna rola FSMO nazw domen, główna rola FSMO schematu itp. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Delete | Usuwa usługę i jej serwery wraz z danymi dotyczącymi kondycji. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Dimensions/Read | Pobiera szczegóły domen i lokacji dla lasu. Przykład — stan kondycji, aktywne alerty, rozwiązane alerty, właściwości, takie jak poziom funkcjonalności domeny, Las, wzorzec infrastruktury, PDC, główny serwer RID itd.  |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Features/UserPreference/Read | Pobiera ustawienie preferencji użytkownika dla lasu.<br>Przykład — MetricCounterName, takie jak ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Ustawienia dla wykresów interfejsu użytkownika itd. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/forestsummary/Read | Pobiera podsumowanie lasu dla danego lasu, takie jak nazwa lasu, liczba domen w tym lesie, liczba lokacji i szczegółowe informacje o lokacjach itp. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/metricmetadata/Read | Pobiera listę obsługiwanych metryk dla danej usługi.<br>Na przykład blokada konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (/s) dla usługi AD FS.<br>Uwierzytelnianie NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnienia Kerberos/s, wątki ATQ i łącznie z ADDomainService.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD dla ADSync. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Metrics/Groups/Read | Dana usługa ten interfejs API pobiera informacje o metrykach.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/premiumcheck/Read | Ten interfejs API pobiera listę wszystkich dołączanych ADDomainServices dla dzierżawy w warstwie Premium. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/odczyt | Pobiera szczegóły usługi dla określonej nazwy usługi. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/replicationdetails/Read | Pobiera szczegóły replikacji dla wszystkich serwerów dla określonej nazwy usługi. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/ReplicationStatus/Read | Pobiera liczbę kontrolerów domeny i ich błędy replikacji, jeśli istnieją. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/replicationsummary/Read | Pobiera pełną listę kontrolerów domeny wraz ze szczegółami replikacji dla danego lasu. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Servicemembers/akcja | Dodaj wystąpienie serwera do usługi. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Servicemembers/Credentials/Read | Podczas rejestracji serwera ADDomainService ten interfejs API jest wywoływany w celu uzyskania poświadczeń do dołączania nowych serwerów. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Servicemembers/Delete | Usuwa serwer dla danej usługi i dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/addsservices/Write | Tworzy lub aktualizuje wystąpienie ADDomainService dla dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/konfiguracja/akcja | Aktualizuje konfigurację dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/konfiguracja/odczyt | Odczytuje konfigurację dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/konfiguracja/zapis | Tworzy konfigurację dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/Logs/Contents/Read | Pobiera zawartość instalacji agenta i dzienników rejestracji przechowywanych w obiekcie blob. |
> | Działanie | Microsoft. ADHybridHealthService/Logs/odczyt | Pobiera dzienniki instalacji i rejestracji agenta dla dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/Operations/Read | Pobiera listę operacji obsługiwanych przez system. |
> | Działanie | Microsoft. ADHybridHealthService/Register/Action | Rejestruje dostawcę zasobów ADHybrid Usługa kondycji i umożliwia utworzenie zasobu Usługa kondycji ADHybrid. |
> | Działanie | Microsoft. ADHybridHealthService/Reports/availabledeployments/Read | Pobiera listę dostępnych regionów, które są używane przez DevOps do obsługi zdarzeń klientów. |
> | Działanie | Microsoft. ADHybridHealthService/Reports/badpassword/Read | Pobiera listę nieprawidłowych prób wprowadzenia hasła dla wszystkich użytkowników w Active Directory usługa federacyjna. |
> | Działanie | Microsoft. ADHybridHealthService/Reports/badpassworduseridipfrequency/Read | Pobiera identyfikator URI sygnatury dostępu współdzielonego obiektu BLOB zawierający stan i ewentualny wynik nowo umieszczonego zadania raportu dla częstotliwości nieprawidłowej nazwy użytkownika/hasła na wartość UserId na adres IP dziennie dla danej dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/Reports/consentedtodevopstenants/Read | Pobiera listę DevOps, które wyraziły zgodę. Zwykle używany do obsługi klienta. |
> | Działanie | Microsoft. ADHybridHealthService/Reports/isdevops/Read | Pobiera wartość wskazującą, czy dzierżawca jest DevOps, czy nie. |
> | Działanie | Microsoft. ADHybridHealthService/Reports/selectdevopstenant/Read | Aktualizuje identyfikator użytkownika (objectid) dla wybranej dzierżawy Ops usługi dev. |
> | Działanie | Microsoft. ADHybridHealthService/Reports/selecteddeployment/Read | Pobiera wybrane wdrożenie dla danej dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/Reports/tenantassigneddeployment/Read | Podaną identyfikator dzierżawy Pobiera lokalizację magazynu dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/Reports/updateselecteddeployment/Read | Pobiera lokalizację geograficzną, z której będą uzyskiwane dostęp do danych. |
> | Działanie | Microsoft. ADHybridHealthService/usługi/akcja | Aktualizuje wystąpienie usługi w dzierżawie. |
> | Działanie | Microsoft. ADHybridHealthService/usługi/alerty/odczyt | Odczytuje alerty dla usługi. |
> | Działanie | Microsoft. ADHybridHealthService/usługi/alerty/odczyt | Odczytuje alerty dla usługi. |
> | Działanie | Microsoft. ADHybridHealthService/Services/checkservicefeatureavailibility/Read | Dana nazwa funkcji weryfikuje, czy usługa ma wszystkie elementy wymagane do korzystania z tej funkcji. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Delete | Usuwa wystąpienie usługi w dzierżawie. |
> | Działanie | Microsoft. ADHybridHealthService/Services/exporterrors/Read | Pobiera błędy eksportu dla danej usługi synchronizacji. |
> | Działanie | Microsoft. ADHybridHealthService/Services/exportstatus/Read | Pobiera stan eksportu dla danej usługi. |
> | Działanie | Microsoft. ADHybridHealthService/Services/opinia/opinia/odczyt | Pobiera informacje zwrotne o alertach dla danej usługi i serwera. |
> | Działanie | Microsoft. ADHybridHealthService/Services/metricmetadata/Read | Pobiera listę obsługiwanych metryk dla danej usługi.<br>Na przykład blokada konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (/s) dla usługi AD FS.<br>Uwierzytelnianie NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnienia Kerberos/s, wątki ATQ i łącznie z ADDomainService.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD dla ADSync. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Metrics/Groups/Average/Read | Przy użyciu usługi ten interfejs API pobiera średnią dla metryk dla danej usługi.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Metrics/Groups/Read | Dana usługa ten interfejs API pobiera informacje o metrykach.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Metrics/Groups/sum/Read | Ponieważ usługa ten interfejs API pobiera Zagregowany widok dla metryk dla danej usługi.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Działanie | Microsoft. ADHybridHealthService/Services/monitoringconfiguration/Write | Dodawanie lub aktualizowanie konfiguracji monitorowania dla usługi. |
> | Działanie | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/Read | Pobiera konfiguracje monitorowania dla danej usługi. |
> | Działanie | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/Write | Dodawanie lub aktualizowanie konfiguracji monitorowania dla usługi. |
> | Działanie | Microsoft. ADHybridHealthService/Services/premiumcheck/Read | Ten interfejs API pobiera listę wszystkich dołączanych usług dla dzierżawy w warstwie Premium. |
> | Działanie | Microsoft. ADHybridHealthService/usługi/odczyt | Odczytuje wystąpienia usługi w dzierżawie. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Reports/blobUris/Read | Pobiera wszystkie ryzykowne identyfikatory URI raportów IP z ostatnich 7 dni. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Reports/Details/Read | Pobiera raport dotyczący pierwszych 50 użytkowników z błędami nieprawidłowych haseł w ciągu ostatnich 7 dni |
> | Działanie | Microsoft. ADHybridHealthService/Services/Reports/generateBlobUri/Action | Generuje raport o ryzykownych adresach IP i zwraca do niego skierowany identyfikator URI. |
> | Działanie | Microsoft. ADHybridHealthService/usługi/Servicemembers/akcja | Tworzy wystąpienie serwera w usłudze. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Servicemembers/Alerts/Read | Odczytuje alerty dla serwera. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Servicemembers/Credentials/Read | Podczas rejestracji serwera ten interfejs API jest wywoływany w celu uzyskania poświadczeń do dołączania nowych serwerów. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Servicemembers/dataaktualności/odczyt | Dla danego serwera ten interfejs API pobiera listę typów danych, które są przekazywane przez serwery, oraz najnowsze czasu dla każdego przekazywania. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Servicemembers/Delete | Usuwa wystąpienie serwera w usłudze. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Servicemembers/exportstatus/Read | Pobiera szczegóły błędu eksportu synchronizacji dla danej usługi synchronizacji. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Servicemembers/Metrics/Groups/Read | Dana usługa ten interfejs API pobiera informacje o metrykach.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Servicemembers/Metrics/Read | Pobiera listę łączników i uruchamia nazwy profilów dla danej usługi i członka usługi. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Servicemembers/Read | Odczytuje wystąpienie serwera w usłudze. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Servicemembers/ServiceConfiguration/Read | Pobiera konfigurację usługi dla danej dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/Services/tenantwhitelisting/Read | Pobiera stan listy dozwolonych funkcji dla danej dzierżawy. |
> | Działanie | Microsoft. ADHybridHealthService/Services/Write | Tworzy wystąpienie usługi w dzierżawie. |
> | Działanie | Microsoft. ADHybridHealthService/Unregister/Action | Wyrejestrowuje subskrypcję dla dostawcy zasobów usługi ADHybrid Usługa kondycji. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Advisor/configurations/read | Pobierz konfiguracje |
> | Działanie | Microsoft.Advisor/configurations/write | Tworzy/aktualizuje konfigurację |
> | Działanie | Microsoft. Advisor/generateRecommendations/akcja | Pobiera stan zaleceń generowania |
> | Działanie | Microsoft.Advisor/generateRecommendations/read | Pobiera stan zaleceń generowania |
> | Działanie | Microsoft.Advisor/metadata/read | Pobierz metadane |
> | Działanie | Microsoft. Advisor/Operations/Read | Pobiera operacje dla programu Microsoft Advisor |
> | Działanie | Microsoft. Advisor/zalecenia/dostępne/akcja | Nowe zalecenie jest dostępne w programie Microsoft Advisor |
> | Działanie | Microsoft. Advisor/zalecenia/odczyt | Odczytuje zalecenia |
> | Działanie | Microsoft. Advisor/zalecenia/tłumienie/usuwanie | Usuwa pominięcie |
> | Działanie | Microsoft. Advisor/zalecenia/tłumienie/odczyt | Pobiera pominięcia |
> | Działanie | Microsoft. Advisor/zalecenia/tłumienie/zapis | Tworzy/aktualizuje pominięcia |
> | Działanie | Microsoft. Advisor/rejestr/akcja | Rejestruje subskrypcję usługi Microsoft Advisor |
> | Działanie | Microsoft. Advisor/tłumienie/usuwanie | Usuwa pominięcie |
> | Działanie | Microsoft. Advisor/tłumienie/odczyt | Pobiera pominięcia |
> | Działanie | Microsoft. Advisor/tłumienie/zapis | Tworzy/aktualizuje pominięcia |
> | Działanie | Microsoft. Advisor/Unregister/Action | Wyrejestrowuje subskrypcję usługi Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.AlertsManagement/actionRules/delete | Usuń regułę akcji w danej subskrypcji. |
> | Działanie | Microsoft.AlertsManagement/actionRules/read | Pobierz wszystkie reguły akcji dla filtrów wejściowych. |
> | Działanie | Microsoft. AlertsManagement/actionRules/Write | Utwórz lub zaktualizuj regułę akcji w danej subskrypcji |
> | Działanie | Microsoft. AlertsManagement/Alerts/changestate/Action | Zmień stan alertu. |
> | Działanie | Microsoft.AlertsManagement/alerts/diagnostics/read | Pobierz całą diagnostykę dla alertu |
> | Działanie | Microsoft.AlertsManagement/alerts/history/read | Pobierz historię alertu |
> | Działanie | Microsoft.AlertsManagement/alerts/read | Pobierz wszystkie alerty dla filtrów wejściowych. |
> | Działanie | Microsoft.AlertsManagement/alertsList/read | Pobierz wszystkie alerty dla filtrów wejściowych między subskrypcjami |
> | Działanie | Microsoft. AlertsManagement/alertsMetaData/odczyt | Pobierz metadane alertów dla parametru wejściowego. |
> | Działanie | Microsoft. AlertsManagement/alertsSummary/odczyt | Pobierz podsumowanie alertów |
> | Działanie | Microsoft. AlertsManagement/alertsSummaryList/odczyt | Pobierz podsumowanie alertów w ramach subskrypcji |
> | Działanie | Microsoft.AlertsManagement/Operations/read | Odczytuje podane operacje |
> | Działanie | Microsoft. AlertsManagement/Register/Action | Rejestruje subskrypcję zarządzania alertami firmy Microsoft |
> | Działanie | Microsoft. AlertsManagement/smartDetectorAlertRules/Delete | Usuń regułę alertu inteligentnego wykrywania w danej subskrypcji |
> | Działanie | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Pobieranie wszystkich reguł alertów dotyczących detektora inteligentnego dla filtrów wejściowych |
> | Działanie | Microsoft. AlertsManagement/smartDetectorAlertRules/Write | Utwórz lub zaktualizuj regułę alertu inteligentnego wykrywania w danej subskrypcji |
> | Działanie | Microsoft.AlertsManagement/smartGroups/changestate/action | Zmiana stanu grupy inteligentnej |
> | Działanie | Microsoft.AlertsManagement/smartGroups/history/read | Pobierz historię grupy inteligentnej |
> | Działanie | Microsoft.AlertsManagement/smartGroups/read | Pobierz wszystkie grupy inteligentne dla filtrów wejściowych |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. AnalysisServices/Locations/checkNameAvailability/Action | Sprawdza, czy dana nazwa Analysis Server jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft.AnalysisServices/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | Działanie | Microsoft.AnalysisServices/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | Działanie | Microsoft. AnalysisServices/Operations/Read | Pobiera informacje o operacjach |
> | Działanie | Microsoft. AnalysisServices/Register/Action | Rejestruje dostawcę zasobów Analysis Services. |
> | Działanie | Microsoft. AnalysisServices/serwery/usuwanie | Usuwa Analysis Server. |
> | Działanie | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Wyświetlenie listy stan bramy skojarzonej z serwerem. |
> | Działanie | Microsoft. AnalysisServices/serwery/odczyt | Pobiera informacje o określonym Analysis Server. |
> | Działanie | Microsoft. AnalysisServices/serwery/wznowienie/akcja | Wznawia Analysis Server. |
> | Działanie | Microsoft. AnalysisServices/serwery/jednostki SKU/odczyt | Pobierz informacje o dostępnych jednostkach SKU dla serwera |
> | Działanie | Microsoft.AnalysisServices/servers/suspend/action | Wstrzymuje Analysis Server. |
> | Działanie | Microsoft. AnalysisServices/serwery/zapis | Tworzy lub aktualizuje określony Analysis Server. |
> | Działanie | Microsoft. AnalysisServices/jednostki SKU/odczyt | Pobiera informacje o jednostkach SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.ApiManagement/checkNameAvailability/read | Sprawdza, czy podana nazwa usługi jest dostępna |
> | Działanie | Microsoft.ApiManagement/operations/read | Odczytuj wszystkie operacje interfejsu API dostępne dla zasobu Microsoft. ApiManagement |
> | Działanie | Microsoft.ApiManagement/register/action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. ApiManagement |
> | Działanie | Microsoft.ApiManagement/reports/read | Uzyskuj raporty zagregowane według okresów, regionu geograficznego, deweloperów, produktów, interfejsów API, operacji, subskrypcji i byRequest. |
> | Działanie | Microsoft.ApiManagement/service/apis/delete | Usuwa określony interfejs API wystąpienia usługi API Management. |
> | Działanie | Microsoft.ApiManagement/service/apis/diagnostics/delete | Usuwa określoną diagnostykę z interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/diagnostics/read | Wyświetla listę wszystkich diagnostyki interfejsu API. lub pobiera szczegóły diagnostyki dla interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/Diagnostyka/zapis | Tworzy nową diagnostykę dla interfejsu API lub aktualizuje istniejący. lub aktualizuje szczegóły diagnostyki dla interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Usuwa określony komentarz z problemu. |
> | Działanie | Microsoft.ApiManagement/service/apis/issues/attachments/read | Wyświetla listę wszystkich załączników dla problemu skojarzonego z określonym interfejsem API. lub pobiera szczegóły załącznika problemu dla interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/apis/issues/attachments/write | Tworzy nowy załącznik dla problemu w interfejsie API lub aktualizuje istniejący. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/Komentarze/usuwanie | Usuwa określony komentarz z problemu. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/Komentarze/odczyt | Wyświetla listę wszystkich komentarzy dotyczących problemu skojarzonego z określonym interfejsem API. lub pobiera szczegóły komentarza dotyczącego problemu dla interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/Komentarze/zapis | Tworzy nowy komentarz dotyczący problemu w interfejsie API lub aktualizuje istniejący. |
> | Działanie | Microsoft.ApiManagement/service/apis/issues/delete | Usuwa określony problem z interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/issues/read | Wyświetla listę wszystkich problemów skojarzonych z określonym interfejsem API. lub pobiera szczegóły problemu dotyczącego interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/apis/issues/write | Tworzy nowy problem dla interfejsu API lub aktualizuje istniejący. lub aktualizuje istniejący problem dla interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/delete | Usuwa określoną operację w interfejsie API. |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/policies/delete | Usuwa konfigurację zasad w operacji interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/policies/read | Pobierz listę konfiguracji zasad na poziomie operacji interfejsu API. lub Pobierz konfigurację zasad na poziomie operacji interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/policies/write | Tworzy lub aktualizuje konfigurację zasad dla poziomu operacji interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/policy/delete | Usuń konfigurację zasad na poziomie operacji |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/policy/read | Pobierz konfigurację zasad na poziomie operacji |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/policy/write | Utwórz konfigurację zasad na poziomie operacji |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/read | Wyświetla kolekcję operacji dla określonego interfejsu API. lub pobiera szczegóły operacji interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/tags/delete | Odłącz tag od operacji. |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/tags/read | Wyświetla listę wszystkich tagów skojarzonych z operacją. lub Pobierz tag skojarzony z operacją. |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/tags/write | Przypisz tag do operacji. |
> | Działanie | Microsoft.ApiManagement/service/apis/operations/write | Tworzy nową operację w interfejsie API lub aktualizuje istniejącą. lub aktualizuje szczegóły operacji w interfejsie API określonym przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/apis/operationsByTags/read | Wyświetla kolekcję operacji skojarzonych ze znacznikami. |
> | Działanie | Microsoft.ApiManagement/service/apis/policies/delete | Usuwa konfigurację zasad w interfejsie API. |
> | Działanie | Microsoft.ApiManagement/service/apis/policies/read | Pobierz konfigurację zasad na poziomie interfejsu API. lub Pobierz konfigurację zasad na poziomie interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/policies/write | Tworzy lub aktualizuje konfigurację zasad dla interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/policy/delete | Usuń konfigurację zasad na poziomie interfejsu API |
> | Działanie | Microsoft.ApiManagement/service/apis/policy/read | Pobierz konfigurację zasad na poziomie interfejsu API |
> | Działanie | Microsoft.ApiManagement/service/apis/policy/write | Utwórz konfigurację zasad na poziomie interfejsu API |
> | Działanie | Microsoft.ApiManagement/service/apis/products/read | Wyświetla listę wszystkich produktów, które są częścią interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/read | Wyświetla wszystkie interfejsy API wystąpienia usługi API Management. lub pobiera szczegóły interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/apis/releases/delete | Usuwa wszystkie wersje interfejsu API lub usuwa określoną wersję w interfejsie API. |
> | Działanie | Microsoft.ApiManagement/service/apis/releases/read | Wyświetla wszystkie wersje interfejsu API.<br>Wersja interfejsu API jest tworzona podczas aktualnej wersji interfejsu API.<br>Wersje są również używane do wycofywania do poprzednich wersji.<br>Wyniki będą stronicowane i mogą być ograniczone przez $top i $skip parametrów.<br>lub zwraca szczegółowe informacje o wersji interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/releases/write | Tworzy nową wersję interfejsu API. lub aktualizuje szczegóły wydania interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/apis/revisions/delete | Usuwa wszystkie poprawki interfejsu API |
> | Działanie | Microsoft.ApiManagement/service/apis/revisions/read | Wyświetla wszystkie wersje interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/schemas/delete | Usuwa konfigurację schematu w interfejsie API. |
> | Działanie | Microsoft.ApiManagement/service/apis/schemas/read | Pobierz konfigurację schematu na poziomie interfejsu API. lub zapoznaj się z konfiguracją schematu na poziomie interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/schematy/zapis | Tworzy lub aktualizuje konfigurację schematu dla interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Usuń opis tagu dla interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Wyświetla listę wszystkich opisów tagów w zakresie interfejsu API. Model podobny do struktury Swagger-tagDescription jest zdefiniowany na poziomie interfejsu API, ale tag może być przypisany do operacji lub uzyskać opis tagu w zakresie interfejsu API |
> | Działanie | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Utwórz/zaktualizuj opis tagu w zakresie interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/tags/delete | Odłącz tag od interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/tags/read | Wyświetla listę wszystkich tagów skojarzonych z interfejsem API. lub uzyskaj tag skojarzony z interfejsem API. |
> | Działanie | Microsoft.ApiManagement/service/apis/tags/write | Przypisz tag do interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apis/write | Tworzy nowy lub aktualizuje istniejący interfejs API wystąpienia usługi API Management. lub aktualizuje określony interfejs API wystąpienia usługi API Management. |
> | Działanie | Microsoft.ApiManagement/service/apisByTags/read | Wyświetla kolekcję interfejsów API skojarzonych ze znacznikami. |
> | Działanie | Microsoft.ApiManagement/service/apiVersionSets/delete | Usuwa określony zestaw wersji interfejsu API. |
> | Działanie | Microsoft.ApiManagement/service/apiVersionSets/read | Wyświetla kolekcję zestawów wersji interfejsu API w określonym wystąpieniu usługi. lub pobiera szczegóły zestawu wersji interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Pobierz listę jednostek wersji |
> | Działanie | Microsoft.ApiManagement/service/apiVersionSets/write | Tworzy lub aktualizuje zestaw wersji interfejsu API. lub aktualizuje szczegóły VersionSet interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Aktualizuje zasoby Microsoft. ApiManagement uruchomione w Virtual Network w celu pobrania zaktualizowanych ustawień sieci. |
> | Działanie | Microsoft.ApiManagement/service/authorizationServers/delete | Usuwa określone wystąpienie serwera autoryzacji. |
> | Działanie | Microsoft. ApiManagement/Service/authorizationServers/listSecrets/Action | Pobiera wpisy tajne dla serwera autoryzacji. |
> | Działanie | Microsoft.ApiManagement/service/authorizationServers/read | Wyświetla kolekcję serwerów autoryzacji zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły serwera autoryzacji bez wpisów tajnych. |
> | Działanie | Microsoft.ApiManagement/service/authorizationServers/write | Tworzy nowy serwer autoryzacji lub aktualizuje istniejący serwer autoryzacji. lub aktualizuje szczegóły serwera autoryzacji określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/backends/delete | Usuwa określone zaplecze. |
> | Działanie | Microsoft.ApiManagement/service/backends/read | Wyświetla kolekcję punktów końcowych w określonym wystąpieniu usługi. lub pobiera szczegóły zaplecza określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/backends/reconnect/action | Powiadamia serwer proxy APIM o utworzeniu nowego połączenia z zapleczem po upływie określonego limitu czasu. Jeśli nie określono limitu czasu, zostanie użyty limit czasu wynoszący 2 minuty. |
> | Działanie | Microsoft.ApiManagement/service/backends/write | Tworzy lub aktualizuje zaplecze. lub aktualizuje istniejące zaplecze. |
> | Działanie | Microsoft.ApiManagement/service/backup/action | Usługa tworzenia kopii zapasowych API Management do określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Działanie | Microsoft.ApiManagement/service/caches/delete | Usuwa określoną pamięć podręczną. |
> | Działanie | Microsoft.ApiManagement/service/caches/read | Wyświetla kolekcję wszystkich zewnętrznych pamięci podręcznych w określonym wystąpieniu usługi. lub pobiera szczegóły pamięci podręcznej określonej przez jej identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/caches/write | Tworzy lub aktualizuje zewnętrzną pamięć podręczną, która ma być używana w wystąpieniu usługi API Management. lub aktualizuje szczegóły pamięci podręcznej określonej przez jej identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/certificates/delete | Usuwa określony certyfikat. |
> | Działanie | Microsoft.ApiManagement/service/certificates/read | Wyświetla kolekcję wszystkich certyfikatów w określonym wystąpieniu usługi. lub pobiera szczegóły certyfikatu określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/certificates/write | Tworzy lub aktualizuje certyfikat używany do uwierzytelniania w zapleczu. |
> | Działanie | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Usuwa określony element zawartości. |
> | Działanie | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Zwraca listę elementów zawartości lub zwraca szczegóły elementu zawartości |
> | Działanie | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Tworzy nowy element zawartości lub aktualizuje określony element zawartości |
> | Działanie | Microsoft.ApiManagement/service/contentTypes/read | Zwraca listę typów zawartości |
> | Działanie | Microsoft.ApiManagement/service/delete | Usuń wystąpienie usługi API Management |
> | Działanie | Microsoft.ApiManagement/service/diagnostics/delete | Usuwa określoną diagnostykę. |
> | Działanie | Microsoft.ApiManagement/service/diagnostics/read | Wyświetla listę wszystkich diagnostyki wystąpienia usługi API Management. lub pobiera szczegóły diagnostyki określone przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Diagnostics/Write | Tworzy nową diagnostykę lub aktualizuje istniejącą. lub aktualizuje szczegóły diagnostyki określonej przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/Action | Pobiera konfigurację bramy. lub aktualizuje puls bramy. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/API/Delete | Usuwa określony interfejs API z określonej bramy. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/API/Read | Wyświetla kolekcję interfejsów API skojarzonych z bramą. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/API/Write | Dodaje interfejs API do określonej bramy. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/Delete | Usuwa określoną bramę. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/hostnameConfigurations/Read | Wyświetla listę konfiguracji nazwy hosta dla określonej bramy. |
> | Działanie | Microsoft. ApiManagement/Service/Gateway//klucze/akcja | Pobiera klucze bramy. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/Read | Wyświetla kolekcję bram zarejestrowanych w wystąpieniu usługi. lub pobiera szczegóły bramy określone przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/regeneratePrimaryKey/Action | Ponownie generuje podstawowy klucz bramy invalidationg wszystkie utworzone przez niego tokeny. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/regenerateSecondaryKey/Action | Ponownie generuje pomocniczy klucz bramy invalidationg wszystkie utworzone przez niego tokeny. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/token/Action | Pobiera token autoryzacji dostępu współdzielonego dla bramy. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/Write | Tworzy lub aktualizuje bramę do użycia w wystąpieniu usługi API Management. lub aktualizuje szczegóły bramy określone przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/getssotoken/action | Pobiera token rejestracji jednokrotnej, którego można użyć do zalogowania się do portalu usługi API Management starszej wersji jako administrator |
> | Działanie | Microsoft.ApiManagement/service/groups/delete | Usuwa konkretną grupę wystąpienia usługi API Management. |
> | Działanie | Microsoft.ApiManagement/service/groups/read | Wyświetla kolekcję grup zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły grupy określonej przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/groups/users/delete | Usuń istniejącego użytkownika z istniejącej grupy. |
> | Działanie | Microsoft.ApiManagement/service/groups/users/read | Wyświetla kolekcję jednostek użytkownika skojarzonych z grupą. |
> | Działanie | Microsoft.ApiManagement/service/groups/users/write | Dodaj istniejącego użytkownika do istniejącej grupy |
> | Działanie | Microsoft. ApiManagement/Service/Groups/Write | Tworzy lub aktualizuje grupę. lub aktualizuje Szczegóły grupy określonej przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/identityProviders/delete | Usuwa określoną konfigurację dostawcy tożsamości. |
> | Działanie | Microsoft. ApiManagement/Service/skojarzeni/listSecrets/Action | Pobiera wpisy tajne dostawcy tożsamości. |
> | Działanie | Microsoft.ApiManagement/service/identityProviders/read | Wyświetla kolekcję dostawców tożsamości skonfigurowanych w określonym wystąpieniu usługi. lub pobiera szczegóły konfiguracji dostawcy tożsamości bez wpisów tajnych. |
> | Działanie | Microsoft.ApiManagement/service/identityProviders/write | Tworzy lub aktualizuje konfigurację IdentityProvider. lub aktualizuje istniejącą konfigurację IdentityProvider. |
> | Działanie | Microsoft.ApiManagement/service/issues/read | Wyświetla kolekcję problemów w określonym wystąpieniu usługi. lub pobiera API Management Szczegóły problemu |
> | Działanie | Microsoft.ApiManagement/service/locations/networkstatus/read | Pobiera stan dostępu do sieci dla zasobów, od których zależy usługa w danej lokalizacji. |
> | Działanie | Microsoft.ApiManagement/service/loggers/delete | Usuwa określony rejestrator. |
> | Działanie | Microsoft.ApiManagement/service/loggers/read | Wyświetla kolekcję rejestratorów w określonym wystąpieniu usługi. lub pobiera szczegóły rejestratora określony przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/loggers/write | Tworzy lub aktualizuje rejestrator. lub aktualizuje istniejący rejestrator. |
> | Działanie | Microsoft.ApiManagement/service/managedeployments/action | Zmień jednostkę SKU/jednostki, Dodaj/Usuń wdrożenia regionalne usługi API Management |
> | Działanie | Microsoft. ApiManagement/Service/namedValues/Delete | Usuwa określoną nazwaną wartość z wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/namedValues/listSecrets/Action | Pobiera klucze tajne nazwanej wartości określone przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/namedValues/Read | Wyświetla kolekcję nazwanych wartości zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły nazwanej wartości określone przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/namedValues/Write | Tworzy lub aktualizuje nazwaną wartość. lub aktualizuje określoną nazwaną wartość. |
> | Działanie | Microsoft.ApiManagement/service/networkstatus/read | Pobiera stan dostępu do sieci dla zasobów, od których zależy usługa. |
> | Działanie | Microsoft.ApiManagement/service/notifications/action | Wysyła powiadomienie do określonego użytkownika |
> | Działanie | Microsoft.ApiManagement/service/notifications/read | Wyświetla kolekcję właściwości zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły powiadomienia określone przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Usuwa wiadomość e-mail z listy powiadomień. |
> | Działanie | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Pobiera listę wiadomości E-mail dotyczących adresatów powiadomień subskrybowanych w ramach powiadomienia. |
> | Działanie | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Dodaje adres E-mail do listy adresatów powiadomienia. |
> | Działanie | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Usuwa użytkownika API Management z listy powiadomień. |
> | Działanie | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Pobiera listę użytkowników adresatów powiadomień, którzy zasubskrybują powiadomienie. |
> | Działanie | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Dodaje API Management użytkownika do listy adresatów powiadomienia. |
> | Działanie | Microsoft.ApiManagement/service/notifications/write | Utwórz lub zaktualizuj powiadomienia wydawcy API Management. |
> | Działanie | Microsoft.ApiManagement/service/openidConnectProviders/delete | Usuwa określonego dostawcę połączenia OpenID Connect dla wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/openidConnectProviders/listSecrets/Action | Pobiera określone wpisy tajne dostawcy połączenia OpenID Connect. |
> | Działanie | Microsoft.ApiManagement/service/openidConnectProviders/read | Listy wszystkich dostawców połączeń OpenID Connect. lub pobiera określonego dostawcę połączenia OpenID Connect bez wpisów tajnych. |
> | Działanie | Microsoft.ApiManagement/service/openidConnectProviders/write | Tworzy lub aktualizuje dostawcę połączenia OpenID Connect. lub aktualizuje określonego dostawcę połączenia OpenID Connect. |
> | Działanie | Microsoft.ApiManagement/service/operationresults/read | Pobiera bieżący stan długotrwałej operacji |
> | Działanie | Microsoft.ApiManagement/service/policies/delete | Usuwa globalną konfigurację zasad usługi API Management. |
> | Działanie | Microsoft.ApiManagement/service/policies/read | Wyświetla wszystkie globalne definicje zasad usługi API Management. lub uzyskać definicję zasad globalnych usługi API Management. |
> | Działanie | Microsoft.ApiManagement/service/policies/write | Tworzy lub aktualizuje globalną konfigurację zasad usługi API Management. |
> | Działanie | Microsoft.ApiManagement/service/policy/delete | Usuń konfigurację zasad na poziomie dzierżawy |
> | Działanie | Microsoft.ApiManagement/service/policy/read | Pobierz konfigurację zasad na poziomie dzierżawy |
> | Działanie | Microsoft.ApiManagement/service/policy/write | Utwórz konfigurację zasad na poziomie dzierżawy |
> | Działanie | Microsoft. ApiManagement/Service/policyDescriptions/Read | Wyświetla listę wszystkich opisów zasad. |
> | Działanie | Microsoft.ApiManagement/service/policySnippets/read | Wyświetla wszystkie fragmenty kodu zasad. |
> | Działanie | Microsoft.ApiManagement/service/portalsettings/read | Uzyskaj ustawienia logowania dla portalu lub uzyskaj ustawienia rejestracji w portalu lub uzyskaj ustawienia delegowania dla portalu. |
> | Działanie | Microsoft.ApiManagement/service/portalsettings/write | Zaktualizuj ustawienia logowania. lub Utwórz lub zaktualizuj ustawienia logowania. lub zaktualizuj ustawienia rejestracji lub zaktualizuj ustawienia rejestracji lub zaktualizuj ustawienia delegowania. lub Utwórz lub zaktualizuj ustawienia delegowania. |
> | Działanie | Microsoft.ApiManagement/service/products/apis/delete | Usuwa określony interfejs API z określonego produktu. |
> | Działanie | Microsoft.ApiManagement/service/products/apis/read | Wyświetla kolekcję interfejsów API skojarzonych z produktem. |
> | Działanie | Microsoft.ApiManagement/service/products/apis/write | Dodaje interfejs API do określonego produktu. |
> | Działanie | Microsoft.ApiManagement/service/products/delete | Usuń produkt. |
> | Działanie | Microsoft.ApiManagement/service/products/groups/delete | Usuwa skojarzenie między określoną grupą i produktem. |
> | Działanie | Microsoft.ApiManagement/service/products/groups/read | Wyświetla listę grup deweloperów skojarzonych z określonym produktem. |
> | Działanie | Microsoft.ApiManagement/service/products/groups/write | Dodaje skojarzenie między określoną grupą deweloperów i określonym produktem. |
> | Działanie | Microsoft.ApiManagement/service/products/policies/delete | Usuwa konfigurację zasad w produkcie. |
> | Działanie | Microsoft.ApiManagement/service/products/policies/read | Pobierz konfigurację zasad na poziomie produktu. lub Pobierz konfigurację zasad na poziomie produktu. |
> | Działanie | Microsoft.ApiManagement/service/products/policies/write | Tworzy lub aktualizuje konfigurację zasad dla produktu. |
> | Działanie | Microsoft.ApiManagement/service/products/policy/delete | Usuń konfigurację zasad na poziomie produktu |
> | Działanie | Microsoft.ApiManagement/service/products/policy/read | Pobierz konfigurację zasad na poziomie produktu |
> | Działanie | Microsoft.ApiManagement/service/products/policy/write | Utwórz konfigurację zasad na poziomie produktu |
> | Działanie | Microsoft.ApiManagement/service/products/read | Wyświetla kolekcję produktów w określonym wystąpieniu usługi. lub pobiera szczegóły produktu określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/products/subscriptions/read | Zawiera listę kolekcji subskrypcji dla określonego produktu. |
> | Działanie | Microsoft.ApiManagement/service/products/tags/delete | Odłącz tag od produktu. |
> | Działanie | Microsoft.ApiManagement/service/products/tags/read | Wyświetla listę wszystkich tagów skojarzonych z produktem. lub Pobierz tag skojarzony z produktem. |
> | Działanie | Microsoft.ApiManagement/service/products/tags/write | Przypisz tag do produktu. |
> | Działanie | Microsoft.ApiManagement/service/products/write | Tworzy lub aktualizuje produkt. lub zaktualizuj istniejące informacje o produkcie. |
> | Działanie | Microsoft.ApiManagement/service/productsByTags/read | Wyświetla kolekcję produktów skojarzonych ze znacznikami. |
> | Działanie | Microsoft.ApiManagement/service/properties/delete | Usuwa określoną właściwość z wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/Properties/listSecrets/Action | Pobiera wpisy tajne właściwości określonej przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/properties/read | Wyświetla kolekcję właściwości zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły właściwości określonej przez jej identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/properties/write | Tworzy lub aktualizuje właściwość. lub aktualizuje konkretną właściwość. |
> | Działanie | Microsoft.ApiManagement/service/quotas/periods/read | Pobierz wartość licznika przydziału dla okresu |
> | Działanie | Microsoft. ApiManagement/Service/limit przydziału/okresy/zapis | Ustaw bieżącą wartość licznika przydziału |
> | Działanie | Microsoft.ApiManagement/service/quotas/read | Pobierz wartości przydziału |
> | Działanie | Microsoft.ApiManagement/service/quotas/write | Ustaw bieżącą wartość licznika przydziału |
> | Działanie | Microsoft.ApiManagement/service/read | Odczytaj metadane dla wystąpienia usługi API Management |
> | Działanie | Microsoft.ApiManagement/service/regions/read | Wyświetla listę wszystkich regionów świadczenia usługi Azure, w których istnieje usługa. |
> | Działanie | Microsoft.ApiManagement/service/reports/read | Pobierz raport zagregowany według okresów lub Pobierz raport zagregowany według regionu geograficznego lub Pobierz raport zagregowany przez deweloperów.<br>lub uzyskać raport zagregowany według produktów.<br>lub uzyskać raport zagregowany przez interfejsy API lub uzyskać raport zagregowany według operacji lub uzyskać raport zagregowany według subskrypcji.<br>lub Pobierz żądania danych raportowania |
> | Działanie | Microsoft.ApiManagement/service/restore/action | Przywracanie usługi API Management z określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Działanie | Microsoft.ApiManagement/service/subscriptions/delete | Usuwa określoną subskrypcję. |
> | Działanie | Microsoft. ApiManagement/Service/subscriptions/listSecrets/Action | Pobiera określone klucze subskrypcji. |
> | Działanie | Microsoft.ApiManagement/service/subscriptions/read | Wyświetla wszystkie subskrypcje wystąpienia usługi API Management. lub pobiera określoną jednostkę subskrypcji (bez kluczy). |
> | Działanie | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Ponownie generuje klucz podstawowy istniejącej subskrypcji wystąpienia usługi API Management. |
> | Działanie | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Generuje ponownie klucz pomocniczy istniejącej subskrypcji wystąpienia usługi API Management. |
> | Działanie | Microsoft.ApiManagement/service/subscriptions/write | Tworzy lub aktualizuje subskrypcję określonego użytkownika dla określonego produktu. lub aktualizuje szczegóły subskrypcji określonej przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/tagResources/read | Wyświetla kolekcję zasobów skojarzonych ze znacznikami. |
> | Działanie | Microsoft.ApiManagement/service/tags/delete | Usuwa konkretny tag wystąpienia usługi API Management. |
> | Działanie | Microsoft.ApiManagement/service/tags/read | Wyświetla kolekcję tagów zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły tagu określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/tags/write | Tworzy tag. lub aktualizuje szczegóły tagu określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/templates/delete | Resetuj szablon wiadomości e-mail domyślnego API Management |
> | Działanie | Microsoft.ApiManagement/service/templates/read | Pobiera wszystkie szablony wiadomości e-mail lub pobiera API Management szczegóły szablonu wiadomości e-mail |
> | Działanie | Microsoft.ApiManagement/service/templates/write | Utwórz lub zaktualizuj szablon wiadomości e-mail API Management lub API Management szablon wiadomości e-mail |
> | Działanie | Microsoft.ApiManagement/service/tenant/delete | Usuń konfigurację zasad dla dzierżawy |
> | Działanie | Microsoft.ApiManagement/service/tenant/deploy/action | Uruchamia zadanie wdrażania, aby zastosować zmiany z określonej gałęzi git do konfiguracji w bazie danych. |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/listSecrets/akcja | Pobierz szczegóły informacji o dostępie dzierżawy |
> | Działanie | Microsoft.ApiManagement/service/tenant/operationResults/read | Pobierz listę wyników operacji lub Pobierz wynik określonej operacji |
> | Działanie | Microsoft.ApiManagement/service/tenant/read | Pobierz definicję zasad globalnych usługi API Management. lub Uzyskaj szczegółowe informacje o dostępie dzierżawy |
> | Działanie | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Wygeneruj ponownie podstawowy klucz dostępu |
> | Działanie | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Ponowne generowanie pomocniczego klucza dostępu |
> | Działanie | Microsoft.ApiManagement/service/tenant/save/action | Tworzy potwierdzeń z migawką konfiguracji do określonej gałęzi w repozytorium |
> | Działanie | Microsoft.ApiManagement/service/tenant/syncState/read | Pobierz stan ostatniej synchronizacji git |
> | Działanie | Microsoft.ApiManagement/service/tenant/validate/action | Sprawdza poprawność zmian z określonej gałęzi git |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/zapis | Ustaw konfigurację zasad dla dzierżawy lub Zaktualizuj szczegóły informacji o dostępie dzierżawy |
> | Działanie | Microsoft.ApiManagement/service/updatecertificate/action | Przekaż certyfikat SSL dla usługi API Management |
> | Działanie | Microsoft.ApiManagement/service/updatehostname/action | Instalowanie, aktualizowanie lub usuwanie niestandardowych nazw domen dla usługi API Management |
> | Działanie | Microsoft.ApiManagement/service/users/action | Rejestrowanie nowego użytkownika |
> | Działanie | Microsoft.ApiManagement/service/users/confirmations/send/action | Wysyła potwierdzenie |
> | Działanie | Microsoft.ApiManagement/service/users/delete | Usuwa określonego użytkownika. |
> | Działanie | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Pobiera adres URL przekierowania zawierający token uwierzytelniania do podpisywania danego użytkownika w portalu dla deweloperów. |
> | Działanie | Microsoft.ApiManagement/service/users/groups/read | Wyświetla listę wszystkich grup użytkowników. |
> | Działanie | Microsoft.ApiManagement/service/users/identities/read | Lista wszystkich tożsamości użytkowników. |
> | Działanie | Microsoft.ApiManagement/service/users/keys/read | Pobierz klucze skojarzone z użytkownikiem |
> | Działanie | Microsoft.ApiManagement/service/users/read | Wyświetla kolekcję zarejestrowanych użytkowników w określonym wystąpieniu usługi. lub pobiera szczegóły użytkownika określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/users/subscriptions/read | Zawiera listę kolekcji subskrypcji określonego użytkownika. |
> | Działanie | Microsoft.ApiManagement/service/users/token/action | Pobiera token autoryzacji dostępu współdzielonego dla użytkownika. |
> | Działanie | Microsoft.ApiManagement/service/users/write | Tworzy lub aktualizuje użytkownika. lub aktualizuje szczegóły użytkownika określonego przez jego identyfikator. |
> | Działanie | Microsoft.ApiManagement/service/write | Utwórz nowe wystąpienie usługi API Management Service |
> | Działanie | Microsoft.ApiManagement/unregister/action | Wyrejestrowywanie subskrypcji dla dostawcy zasobów Microsoft. ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Authorization/classicAdministrators/Delete | Usuwa administratora z subskrypcji. |
> | Działanie | Microsoft. Authorization/classicAdministrators/operationstatuses/odczyt | Pobiera Stany operacji administratora subskrypcji. |
> | Działanie | Microsoft. Authorization/classicAdministrators/Read | Odczytuje administratorów dla subskrypcji. |
> | Działanie | Microsoft. Authorization/classicAdministrators/Write | Dodaj lub zmodyfikuj administratora do subskrypcji. |
> | Działanie | Microsoft. Authorization/denyAssignments/Delete | Usuń przypisanie odmowy w określonym zakresie. |
> | Działanie | Microsoft. Authorization/denyAssignments/Read | Pobierz informacje o przypisaniu Odmów. |
> | Działanie | Microsoft. Authorization/denyAssignments/Write | Utwórz przypisanie odmowy w określonym zakresie. |
> | Działanie | Microsoft. Authorization/elevateAccess/akcja | Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy |
> | Działanie | Microsoft. Authorization/Locks/Delete | Usuń blokady w określonym zakresie. |
> | Działanie | Microsoft. Authorization/Locks/Read | Pobiera blokady w określonym zakresie. |
> | Działanie | Microsoft. Authorization/Locks/Write | Dodaj blokady w podanym zakresie. |
> | Działanie | Microsoft. Authorization/Operations/Read | Pobiera listę operacji |
> | Działanie | Microsoft. Autoryzacja/uprawnienia/Odczyt | Wyświetla wszystkie uprawnienia, które obiekt wywołujący ma w danym zakresie. |
> | Działanie | Microsoft. Autoryzacja/zasady/Inspekcja/akcja | Działanie podejmowane w wyniku oceny Azure Policy z efektem "inspekcja" |
> | Działanie | Microsoft. authorization/policies/auditIfNotExists/Action | Działanie podejmowane w wyniku oceny Azure Policy z efektem "auditIfNotExists" |
> | Działanie | Microsoft. Autoryzacja/zasady/odmowa/akcja | Działanie podejmowane w wyniku oceny Azure Policy z efektem "odmowa" |
> | Działanie | Microsoft. authorization/policies/deployIfNotExists/Action | Działanie podejmowane w wyniku oceny Azure Policy z efektem "deployIfNotExists" |
> | Działanie | Microsoft. Authorization/policyAssignments/Delete | Usuń przypisanie zasad w określonym zakresie. |
> | Działanie | Microsoft. Authorization/policyAssignments/Read | Pobierz informacje o przypisaniu zasad. |
> | Działanie | Microsoft. Authorization/policyAssignments/Write | Utwórz przypisanie zasad w określonym zakresie. |
> | Działanie | Microsoft. Authorization/policyDefinitions/Delete | Usuń definicję zasad. |
> | Działanie | Microsoft. Authorization/policyDefinitions/Read | Pobierz informacje o definicji zasad. |
> | Działanie | Microsoft. Authorization/policyDefinitions/Write | Utwórz niestandardową definicję zasad. |
> | Działanie | Microsoft. Authorization/policySetDefinitions/Delete | Usuń definicję zestawu zasad. |
> | Działanie | Microsoft. Authorization/policySetDefinitions/Read | Pobierz informacje o definicji zestawu zasad. |
> | Działanie | Microsoft. Authorization/policySetDefinitions/Write | Utwórz niestandardową definicję zestawu zasad. |
> | Działanie | Microsoft. Authorization/providerOperations/Read | Pobierz operacje dla wszystkich dostawców zasobów, których można użyć w definicjach ról. |
> | Działanie | Microsoft. Authorization/roleAssignments/Delete | Usuń przypisanie roli w określonym zakresie. |
> | Działanie | Microsoft. Authorization/roleAssignments/Read | Pobierz informacje o przypisaniu roli. |
> | Działanie | Microsoft.Authorization/roleAssignments/write | Utwórz przypisanie roli w określonym zakresie. |
> | Działanie | Microsoft. Authorization/roleDefinitions/Delete | Usuń określoną definicję roli niestandardowej. |
> | Działanie | Microsoft.Authorization/roleDefinitions/read | Pobierz informacje o definicji roli. |
> | Działanie | Microsoft. Authorization/roleDefinitions/Write | Utwórz lub zaktualizuj niestandardową definicję roli z określonymi uprawnieniami i możliwymi do przypisania zakresami. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Automation/automationAccounts/agentRegistrationInformation/odczyt | Odczytaj Informacje rejestracyjne Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/agentRegistrationInformation/regenerateKey/Action | Zapisuje żądanie ponownego wygenerowania kluczy Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/certyfikaty/usuwanie | Usuwa zasób certyfikatu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Certificates/GetCount/Action | Odczytuje liczbę certyfikatów |
> | Działanie | Microsoft. Automation/automationAccounts/Certificates/Read | Pobiera zasób certyfikatu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Certificates/Write | Tworzy lub aktualizuje zasób certyfikatu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/compilationjobs/odczyt | Odczytuje kompilację Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/compilationjobs/odczyt | Odczytuje kompilację Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/compilationjobs/Write | Zapisuje kompilację Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/compilationjobs/Write | Zapisuje kompilację Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/konfiguracje/Content/Read | Odczytuje zawartość nośnika konfiguracji |
> | Działanie | Microsoft. Automation/automationAccounts/konfiguracje/usuwanie | Usuwa zawartość Azure Automation DSC |
> | Działanie | Microsoft.Automation/automationAccounts/configurations/getCount/action | Odczytuje liczbę zawartości Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/konfiguracje/odczyt | Pobiera zawartość Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/konfiguracje/zapis | Zapisuje zawartość Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/Connections/Delete | Usuwa zasób połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Connections/akcja | Odczytuje liczbę połączeń |
> | Działanie | Microsoft. Automation/automationAccounts/Connections/Read | Pobiera zasób połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Connections/Write | Tworzy lub aktualizuje zasób połączenia Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/connectionTypes/delete | Usuwa zasób typu połączenia Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/connectionTypes/read | Pobiera zasób typu połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/connectionTypes/Write | Tworzy zasób typu połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/poświadczenia/usuwanie | Usuwa zasób poświadczenia Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/credentials/getCount/action | Odczytuje liczbę poświadczeń |
> | Działanie | Microsoft.Automation/automationAccounts/credentials/read | Pobiera zasób poświadczenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/poświadczenia/zapis | Tworzy lub aktualizuje zasób poświadczenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Delete | Usuwa konto Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Usuwa zasoby hybrydowego procesu roboczego elementu Runbook |
> | Działanie | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | Działanie | Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Pobiera zawartość elementu Runbook Azure Automation w czasie wykonywania zadania |
> | Działanie | Microsoft.Automation/automationAccounts/jobs/stop/action | Kończy zadanie Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadania Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadania Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/jobSchedules/Delete | Usuwa harmonogram zadań Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/jobSchedules/read | Pobiera harmonogram zadań Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/jobSchedules/write | Tworzy harmonogram zadań Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem usługi Automation |
> | Działanie | Microsoft. Automation/automationAccounts/listKeys/akcja | Odczytuje klucze dla konta usługi Automation |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/działania/odczyt | Pobiera działania Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/usuwanie | Usuwa moduł Azure Automation PowerShell |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/GetCount/Action | Pobiera liczbę modułów programu PowerShell w ramach konta usługi Automation |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/odczyt | Pobiera moduł Azure Automation PowerShell |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/zapis | Tworzy lub aktualizuje moduł Azure Automation PowerShell |
> | Działanie | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Usuwa konfigurację węzła Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodeConfigurations/rawContent/Action | Odczytuje zawartość konfiguracji węzła Azure Automation DSC |
> | Działanie | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Odczytuje konfigurację węzła Azure Automation DSC |
> | Działanie | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Zapisuje konfigurację węzła Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodecounts/odczyt | Odczytuje podsumowanie liczby węzłów dla określonego typu |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Delete | Usuwa Azure Automation węzły DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Read | Odczytuje Azure Automation węzłów DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Reports/Content/Read | Odczytuje zawartość raportu Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Reports/Read | Odczytuje Azure Automation raporty DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Write | Tworzy lub aktualizuje Azure Automation węzłów DSC |
> | Działanie | Microsoft. Automation/automationAccounts/objectDataTypes/Fields/Read | Pobiera Azure Automation TypeFields |
> | Działanie | Microsoft. Automation/automationAccounts/python2Packages/Delete | Usuwa pakiet Azure Automation Python 2 |
> | Działanie | Microsoft.Automation/automationAccounts/python2Packages/read | Pobiera pakiet Azure Automation Python 2 |
> | Działanie | Microsoft.Automation/automationAccounts/python2Packages/write | Tworzy lub aktualizuje pakiet Azure Automation Python 2 |
> | Działanie | Microsoft. Automation/automationAccounts/python3Packages/Delete | Usuwa pakiet Azure Automation Python 3 |
> | Działanie | Microsoft.Automation/automationAccounts/python3Packages/read | Pobiera pakiet Azure Automation Python 3 |
> | Działanie | Microsoft. Automation/automationAccounts/python3Packages/Write | Tworzy lub aktualizuje pakiet Azure Automation Python 3 |
> | Działanie | Microsoft.Automation/automationAccounts/read | Pobiera konto Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Content/Read | Pobiera zawartość elementu Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Delete | Usuwa element Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Draft/Content/Write | Tworzy zawartość wersji roboczej elementu Runbook Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Pobiera wyniki operacji wersji roboczej elementu Runbook Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/draft/read | Pobiera wersję roboczą elementu Runbook Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Pobiera zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Wznawia zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Kończy zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Wstrzymuje zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Tworzy zadanie testowe wersji roboczej elementu Runbook Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Cofnij edycję wersji roboczej elementu Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/GetCount/Action | Pobiera liczbę elementów Runbook Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publikuje wersję roboczą elementu Runbook Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element Runbook Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/runbooks/write | Tworzy lub aktualizuje element Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/harmonogramy/usuwanie | Usuwa zasób harmonogramu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Schedules/GetCount/Action | Pobiera liczbę harmonogramów Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/schedules/read | Pobiera zasób harmonogramu Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/schedules/write | Tworzy lub aktualizuje zasób harmonogramu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/odczyt | Pobiera Azure Automation uruchomienie komputera z konfiguracją aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/odczyt | Pobiera Azure Automation uruchomienie konfiguracji aktualizacji oprogramowania |
> | Działanie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Usuwa Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Usuwa Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Pobiera Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Pobiera Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Tworzy lub aktualizuje Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Tworzy lub aktualizuje Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/Statystyka/odczyt | Pobiera statystyki Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Pobierz maszynę wdrożenia aktualizacji Azure Automation |
> | Działanie | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Pobiera zadanie aktualizacji Azure Automation Update Management |
> | Działanie | Microsoft. Automation/automationAccounts/Usages/Read | Pobiera Azure Automation użycie |
> | Działanie | Microsoft. Automation/automationAccounts/zmienne/usuwanie | Usuwa zasób zmiennej Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/zmienne/odczyt | Odczytuje Azure Automation zasób zmiennej |
> | Działanie | Microsoft. Automation/automationAccounts/zmienne/zapis | Tworzy lub aktualizuje zasób zmiennej Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/obserwatorzy/usuwanie | Usuwanie zadania obserwatora Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/obserwatorzy/odczyt | Pobiera Azure Automation zadanie obserwatora |
> | Działanie | Microsoft. Automation/automationAccounts/obserwatorzy/uruchamianie/akcja | Uruchamianie zadania obserwatora Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/obserwatorzy/akcja | Zatrzymywanie zadania obserwatora Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/zegarki/strumienie/odczyt | Pobiera strumień zadania obserwatora Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/WATCHS/watcherActions/Delete | Usuwanie akcji zadania obserwatora Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/WATCHS/watcherActions/Read | Pobiera Azure Automation akcje zadania obserwatora |
> | Działanie | Microsoft. Automation/automationAccounts/WATCHS/watcherActions/Write | Tworzenie akcji zadania obserwatora Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/obserwatorzy/zapis | Tworzy zadanie obserwatora Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/webhooks/Action | Generuje identyfikator URI dla Azure Automation elementu webhook |
> | Działanie | Microsoft. Automation/automationAccounts/webhooks/Delete | Usuwa element webhook Azure Automation  |
> | Działanie | Microsoft. Automation/automationAccounts/webhooks/Read | Odczytuje Azure Automation elementu webhook |
> | Działanie | Microsoft. Automation/automationAccounts/webhooks/Write | Tworzy lub aktualizuje Azure Automation elementu webhook |
> | Działanie | Microsoft.Automation/automationAccounts/write | Tworzy lub aktualizuje konto Azure Automation |
> | Działanie | Microsoft. Automation/Operations/Read | Pobiera dostępne operacje dla Azure Automation zasobów |
> | Działanie | Microsoft. Automation/rejestrowanie/akcja | Rejestruje subskrypcję w usłudze Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Usuń zasób katalogu B2C |
> | Działanie | Microsoft.AzureActiveDirectory/b2cDirectories/read | Wyświetl zasób katalogu B2C |
> | Działanie | Microsoft.AzureActiveDirectory/b2cDirectories/write | Utwórz lub zaktualizuj zasób katalogu B2C |
> | Działanie | Microsoft.AzureActiveDirectory/b2ctenants/read | Wyświetla wszystkie dzierżawy B2C, w których użytkownik jest członkiem |
> | Działanie | Microsoft.AzureActiveDirectory/operations/read | Przeczytaj wszystkie operacje interfejsu API dostępne dla dostawcy zasobów Microsoft. usługi azureactivedirectory |
> | Działanie | Microsoft.AzureActiveDirectory/register/action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. usługi azureactivedirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. AzureStack/Operations/Read | Pobiera właściwości operacji dostawcy zasobów |
> | Działanie | Microsoft. AzureStack/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. AzureStack |
> | Działanie | Microsoft. AzureStack/registrations/customerSubscriptions/Delete | Usuwa subskrypcję klienta Azure Stack |
> | Działanie | Microsoft.AzureStack/registrations/customerSubscriptions/read | Pobiera właściwości subskrypcji klienta Azure Stack |
> | Działanie | Microsoft.AzureStack/registrations/customerSubscriptions/write | Tworzy lub aktualizuje subskrypcję klienta Azure Stack |
> | Działanie | Microsoft. AzureStack/rejestracje/usuwanie | Usuwa rejestrację Azure Stack |
> | Działanie | Microsoft.AzureStack/registrations/getActivationKey/action | Pobiera najnowszy klucz aktywacji Azure Stack |
> | Działanie | Microsoft. AzureStack/rejestracje/produkty/getproduct/Action | Pobiera Azure Stack produkt Marketplace |
> | Działanie | Microsoft. AzureStack/registrations/Products/getproductss/Action | Pobiera listę produktów Azure Stack Marketplace |
> | Działanie | Microsoft.AzureStack/registrations/products/listDetails/action | Pobiera rozszerzone szczegóły dotyczące produktu Azure Stack Marketplace |
> | Działanie | Microsoft.AzureStack/registrations/products/read | Pobiera właściwości produktu Azure Stack Marketplace |
> | Działanie | Microsoft. AzureStack/rejestracje/produkty/uploadProductLog/akcja | Rejestrowanie stanu i sygnatury czasowej operacji produktu Azure Stack Marketplace |
> | Działanie | Microsoft.AzureStack/registrations/read | Pobiera właściwości rejestracji Azure Stack |
> | Działanie | Microsoft. AzureStack/rejestracje/zapis | Tworzy lub aktualizuje rejestrację Azure Stack |
> | Działanie | Microsoft. AzureStack/verificationKeys/getCurrentKey/akcja | Pobiera bieżącą wersję Azure Stack podpisywania klucza publicznego |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Batch/batchAccounts/applications/delete | Usuwa aplikację |
> | Działanie | Microsoft.Batch/batchAccounts/applications/read | Wyświetla listę aplikacji lub pobiera właściwości aplikacji |
> | Działanie | Microsoft. Batch/batchAccounts/Applications/Versions/Activate/Action | Aktywuje pakiet aplikacji |
> | Działanie | Microsoft.Batch/batchAccounts/applications/versions/delete | Usuwa pakiet aplikacji |
> | Działanie | Microsoft.Batch/batchAccounts/applications/versions/read | Pobiera właściwości pakietu aplikacji |
> | Działanie | Microsoft.Batch/batchAccounts/applications/versions/write | Tworzy nowy pakiet aplikacji lub aktualizuje istniejący pakiet aplikacji |
> | Działanie | Microsoft. Batch/batchAccounts/Applications/Write | Tworzy nową aplikację lub aktualizuje istniejącą aplikację |
> | Działanie | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Pobiera wyniki długotrwałej operacji certyfikatu na koncie wsadowym |
> | Działanie | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Anuluje nieudane usunięcie certyfikatu na koncie wsadowym |
> | Działanie | Microsoft. Batch/batchAccounts/Certificates/Delete | Usuwa certyfikat z konta usługi Batch |
> | Działanie | Microsoft.Batch/batchAccounts/certificates/read | Wyświetla listę certyfikatów na koncie wsadowym lub pobiera właściwości certyfikatu |
> | Działanie | Microsoft. Batch/batchAccounts/Certificates/Write | Tworzy nowy certyfikat na koncie wsadowym lub aktualizuje istniejący certyfikat |
> | Działanie | Microsoft.Batch/batchAccounts/delete | Usuwa konto wsadowe |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Delete | Usuwa zadanie z konta usługi Batch |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Read | Wyświetla listę zadań na koncie wsadowym lub pobiera właściwości zadania |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Write | Tworzy nowe zadanie na koncie wsadowym lub aktualizuje istniejące zadanie |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Delete | Usuwa harmonogram zadań z konta usługi Batch |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/odczyt | Wyświetla listę harmonogramów zadań na koncie wsadowym lub pobiera właściwości harmonogramu zadań |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Write | Tworzy nowy harmonogram zadań na koncie wsadowym lub aktualizuje istniejący harmonogram zadań |
> | Działanie | Microsoft.Batch/batchAccounts/listkeys/action | Wyświetla klucze dostępu dla konta usługi Batch |
> | Działanie | Microsoft.Batch/batchAccounts/operationResults/read | Pobiera wyniki długotrwałej operacji konta partii |
> | Działanie | Microsoft.Batch/batchAccounts/poolOperationResults/read | Pobiera wyniki długotrwałej operacji puli na koncie wsadowym |
> | Działanie | Microsoft.Batch/batchAccounts/pools/delete | Usuwa pulę z konta usługi Batch |
> | Działanie | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Wyłącza automatyczne skalowanie puli kont usługi Batch |
> | Działanie | Microsoft.Batch/batchAccounts/pools/read | Wyświetla listę pul na koncie wsadowym lub pobiera właściwości puli |
> | Działanie | Microsoft.Batch/batchAccounts/pools/stopResize/action | Kończy trwającą operację zmiany rozmiaru w puli kont wsadowych |
> | Działanie | Microsoft.Batch/batchAccounts/pools/write | Tworzy nową pulę na koncie wsadowym lub aktualizuje istniejącą pulę |
> | Działanie | Microsoft.Batch/batchAccounts/read | Wyświetla listę kont wsadowych lub pobiera właściwości konta w usłudze Batch |
> | Działanie | Microsoft.Batch/batchAccounts/regeneratekeys/action | Generuje ponownie klucze dostępu dla konta usługi Batch |
> | Działanie | Microsoft. Batch/batchAccounts/syncAutoStorageKeys/Action | Synchronizuje klucze dostępu dla konta usługi autostorage skonfigurowanego dla konta usługi Batch |
> | Działanie | Microsoft. Batch/batchAccounts/Write | Tworzy nowe konto wsadowe lub aktualizuje istniejące konto w usłudze Batch |
> | Działanie | Microsoft.Batch/locations/accountOperationResults/read | Pobiera wyniki długotrwałej operacji konta partii |
> | Działanie | Microsoft.Batch/locations/checkNameAvailability/action | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft.Batch/locations/quotas/read | Pobiera przydziały usługi Batch dla określonej subskrypcji w określonym regionie platformy Azure |
> | Działanie | Microsoft.Batch/operations/read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. Batch |
> | Działanie | Microsoft.Batch/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Batch i umożliwia tworzenie kont usługi Batch |
> | Działanie | Microsoft.Batch/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Batch uniemożliwiającą tworzenie kont usługi Batch |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. rozliczenia/billingAccounts/umowy/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingPermissions/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/billingPermissions/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/klienci/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/faktury/arkusza cen/Pobierz/akcja |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/billingPermissions/Read |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/Przenieś/akcja |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/transfer/akcja |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/akcja |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/produkty/Przenieś/akcja |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/produkty/transfer/akcja |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/produkty/validateMoveEligibility/akcja |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/zapis |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/arkusza cen/pobieranie/akcja |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/zapis |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingProfiles/zapis |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/billingSubscriptions/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/klienci/billingPermissions/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/klienci/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/działy/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/enrollmentAccounts/billingPermissions/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/enrollmentAccounts/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/enrollmentDepartments/billingPermissions/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/akcja |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/produkty/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/odczyt |  |
> | Działanie | Microsoft. rozliczenia/billingAccounts/zapis |  |
> | Działanie | Microsoft. rozliczenia/działy/odczyt |  |
> | Działanie | Microsoft. rozliczenia/faktury/pobieranie/akcja | Pobierz fakturę przy użyciu linku pobierania z listy |
> | Działanie | Microsoft. rozliczenia/faktury/odczyt |  |
> | Działanie | Microsoft. rozliczenia/rejestr/akcja |  |
> | Działanie | Microsoft. rozliczenia/validateAddress/akcja |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.BingMaps/mapApis/Delete | Operacja usuwania |
> | Działanie | Microsoft.BingMaps/mapApis/listSecrets/action | Wystaw wpisy tajne |
> | Działanie | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Odczytaj Token autoryzacji logowania jednokrotnego dla zasobu |
> | Działanie | Microsoft.BingMaps/mapApis/Read | Operacja odczytu |
> | Działanie | Microsoft.BingMaps/mapApis/regenerateKey/action | Generuje ponownie klucz |
> | Działanie | Microsoft.BingMaps/mapApis/Write | Operacja zapisu |
> | Działanie | Microsoft.BingMaps/Operations/read | Opis operacji. |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/Delete | Usuwa istniejącego członka łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/listApiKeys/akcja | Pobiera lub Wyświetla istniejące klucze interfejsu API elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/odczyt | Pobiera lub Wyświetla istniejące elementy członkowskie łańcucha bloków. |
> | Akcja dataaction | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Connect/Action | Nawiązuje połączenie z węzłem transakcji elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Usuwa istniejący węzeł transakcji elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Pobiera lub Wyświetla istniejące klucze interfejsu API węzła transakcji elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Pobiera lub Wyświetla istniejące węzły transakcji składowych łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Write | Tworzy lub aktualizuje węzeł transakcji elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/Write | Tworzy lub aktualizuje element członkowski łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/cordaMembers/Delete | Usuwa istniejący łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/cordaMembers/odczyt | Pobiera lub Wyświetla istniejące elementy członkowskie łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/cordaMembers/Write | Tworzy lub aktualizuje członka łańcucha bloków. |
> | Działanie | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Pobiera wyniki operacji elementów członkowskich łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa zasobu jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft. łańcucha bloków/Operations/Read | Wyświetl listę wszystkich operacji w dostawcy zasobów programu Microsoft łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/Register/Action | Rejestruje subskrypcję dostawcy zasobów łańcucha bloków. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. plan/blueprintAssignments/assignmentOperations/odczyt | Odczytaj wszelkie artefakty strategii |
> | Działanie | Microsoft. plan/blueprintAssignments/usuwanie | Usuń wszelkie artefakty strategii |
> | Działanie | Microsoft. plan/blueprintAssignments/odczyt | Odczytaj wszelkie artefakty strategii |
> | Działanie | Microsoft. plan/blueprintAssignments/whoisblueprint/akcja | Pobierz identyfikator obiektu podmiotu zabezpieczeń usługi Azure Plans. |
> | Działanie | Microsoft. plan/blueprintAssignments/zapis | Utwórz lub zaktualizuj wszelkie artefakty strategii |
> | Działanie | Microsoft. plan/plany/artefakty/usuwanie | Usuń wszelkie artefakty strategii |
> | Działanie | Microsoft. plan/plany/artefakty/odczyt | Odczytaj wszelkie artefakty strategii |
> | Działanie | Microsoft. plan/plany/artefakty/zapis | Utwórz lub zaktualizuj wszelkie artefakty strategii |
> | Działanie | Microsoft. plan/plany/usuwanie | Usuń wszystkie plany |
> | Działanie | Microsoft. plan/plany/odczyt | Przeczytaj wszelkie plany |
> | Działanie | Microsoft. plan/plany/wersje/artefakty/odczyt | Odczytaj wszelkie artefakty strategii |
> | Działanie | Microsoft. plan/plany/wersje/usuwanie | Usuń wszystkie plany |
> | Działanie | Microsoft. plan/plany/wersje/odczyt | Przeczytaj wszelkie plany |
> | Działanie | Microsoft. plan/plany/wersje/zapis | Utwórz lub zaktualizuj wszelkie plany |
> | Działanie | Microsoft. plan/plany/zapis | Utwórz lub zaktualizuj wszelkie plany |
> | Działanie | Microsoft. plan/rejestr/akcja | Rejestruje dostawcę zasobów usługi Azure Plans |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. BotService/botServices/Channels/Delete | Usuwanie usługi bot |
> | Działanie | Microsoft. BotService/botServices/Channels/Read | Odczytaj usługę bot |
> | Działanie | Microsoft. BotService/botServices/Channels/Write | Napisz usługę bot |
> | Działanie | Microsoft. BotService/botServices/Connections/Delete | Usuwanie usługi bot |
> | Działanie | Microsoft. BotService/botServices/Connections/Read | Odczytaj usługę bot |
> | Działanie | Microsoft. BotService/botServices/Connections/Write | Napisz usługę bot |
> | Działanie | Microsoft. BotService/botServices/Delete | Usuwanie usługi bot |
> | Działanie | Microsoft. BotService/botServices/odczyt | Odczytaj usługę bot |
> | Działanie | Microsoft. BotService/botServices/Write | Napisz usługę bot |
> | Działanie | Microsoft.BotService/locations/operationresults/read | Odczytywanie stanu operacji asynchronicznej |
> | Działanie | Microsoft. BotService/Operations/Read | Odczytaj operacje dla wszystkich typów zasobów |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. cache/checknameavailability/akcja | Sprawdza, czy nazwa jest dostępna do użycia z nowym Redis Cache |
> | Działanie | Microsoft.Cache/locations/operationresults/read | Pobiera wynik długotrwałej operacji, dla której nagłówek "Location" został wcześniej zwrócony do klienta |
> | Działanie | Microsoft.Cache/operations/read | Wyświetla listę operacji obsługiwanych przez dostawcę "Microsoft. cache". |
> | Działanie | Microsoft. cache/Redis/Delete | Usuń cały Redis Cache |
> | Działanie | Microsoft. cache/Redis/eksport/akcja | Eksportuj dane Redis do przedprawionych obiektów blob magazynu w określonym formacie |
> | Działanie | Microsoft. cache/Redis/firewallRules/Delete | Usuwanie reguł zapory adresów IP Redis Cache |
> | Działanie | Microsoft. cache/Redis/firewallRules/odczyt | Pobieranie reguł zapory adresów IP Redis Cache |
> | Działanie | Microsoft. cache/Redis/firewallRules/zapis | Edytowanie reguł zapory adresów IP Redis Cache |
> | Działanie | Microsoft. cache/Redis/forceReboot/akcja | Wymuś ponowne uruchomienie wystąpienia pamięci podręcznej, potencjalnie z utratą danych. |
> | Działanie | Microsoft. cache/Redis/import/akcja | Importuj dane określonego formatu z wielu obiektów BLOB do Redis |
> | Działanie | Microsoft.Cache/redis/linkedservers/delete | Usuwanie połączonego serwera z Redis Cache |
> | Działanie | Microsoft.Cache/redis/linkedservers/read | Pobierz połączone serwery skojarzone z pamięcią podręczną Redis. |
> | Działanie | Microsoft.Cache/redis/linkedservers/write | Dodawanie połączonego serwera do Redis Cache |
> | Działanie | Microsoft. cache/Redis/listKeys/akcja | Wyświetlanie wartości Redis Cache kluczy dostępu w portalu zarządzania |
> | Działanie | Microsoft.Cache/redis/listUpgradeNotifications/read | Wyświetl listę najnowszych powiadomień dotyczących uaktualnienia dla dzierżawy pamięci podręcznej. |
> | Działanie | Microsoft. cache/Redis/metricDefinitions/odczyt | Pobiera dostępne metryki dla Redis Cache |
> | Działanie | Microsoft. cache/Redis/patchSchedules/Delete | Usuwanie harmonogramu poprawek Redis Cache |
> | Działanie | Microsoft.Cache/redis/patchSchedules/read | Pobiera harmonogram stosowania poprawek Redis Cache |
> | Działanie | Microsoft. cache/Redis/patchSchedules/zapis | Modyfikowanie harmonogramu poprawek Redis Cache |
> | Działanie | Microsoft.Cache/redis/read | Wyświetlanie ustawień i konfiguracji Redis Cache w portalu zarządzania |
> | Działanie | Microsoft. cache/Redis/regenerateKey/akcja | Zmiana wartości Redis Cache kluczy dostępu w portalu zarządzania |
> | Działanie | Microsoft. cache/Redis/Start/akcja | Uruchom wystąpienie pamięci podręcznej. |
> | Działanie | Microsoft. cache/Redis/Stop/akcja | Zatrzymaj wystąpienie pamięci podręcznej. |
> | Działanie | Microsoft. cache/Redis/Write | Modyfikowanie ustawień i konfiguracji Redis Cache w portalu zarządzania |
> | Działanie | Microsoft. cache/Register/Action | Rejestruje dostawcę zasobów "Microsoft. cache" z subskrypcją |
> | Działanie | Microsoft. cache/Unregister/akcja | Wyrejestrowuje dostawcę zasobów "Microsoft. cache" z subskrypcją |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Capacity/appliedreservations/read | Odczytuj wszystkie rezerwacje |
> | Działanie | Microsoft. Pojemność/calculateexchange/akcja | Oblicza kwotę i cenę wymiany nowych zakupów i zwraca błędy zasad. |
> | Działanie | Microsoft. Pojemność/calculateprice/akcja | Oblicz dowolną cenę rezerwacji |
> | Działanie | Microsoft. Pojemność/wykazy/odczyt | Odczytaj wykaz rezerwacji |
> | Działanie | Microsoft. Pojemność/checkoffers/akcja | Sprawdź wszystkie oferty subskrypcji |
> | Działanie | Microsoft. Pojemność/checkscopes/akcja | Sprawdź dowolną subskrypcję |
> | Działanie | Microsoft. Pojemność/commercialreservationorders/odczyt | Pobieranie zamówień rezerwacji utworzonych w dowolnej dzierżawie |
> | Działanie | Microsoft. Pojemność/wymiana/akcja | Wymiana dowolnej rezerwacji |
> | Działanie | Microsoft.Capacity/operations/read | Odczytaj dowolną operację |
> | Działanie | Microsoft. Pojemność/rejestr/akcja | Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie zasobów pojemności. |
> | Działanie | Microsoft. Pojemność/reservationorders/akcja | Aktualizowanie dowolnej rezerwacji |
> | Działanie | Microsoft. Pojemność/reservationorders/availablescopes/akcja | Znajdowanie dowolnego dostępnego zakresu |
> | Działanie | Microsoft. Pojemność/reservationorders/calculaterefund/akcja | Oblicza kwotę zwrotu i cenę nowego zakupu oraz zwraca błędy zasad. |
> | Działanie | Microsoft. Pojemność/reservationorders/usuwanie | Usuń dowolne zastrzeżenie |
> | Działanie | Microsoft. Pojemność/reservationorders/scalanie/akcja | Scalanie wszelkich rezerwacji |
> | Działanie | Microsoft.Capacity/reservationorders/read | Odczytuj wszystkie rezerwacje |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/akcja | Aktualizowanie dowolnej rezerwacji |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/availablescopes/akcja | Znajdowanie dowolnego dostępnego zakresu |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/usuwanie | Usuń dowolne zastrzeżenie |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/odczyt | Odczytuj wszystkie rezerwacje |
> | Działanie | Microsoft.Capacity/reservationorders/reservations/revisions/read | Odczytuj wszystkie rezerwacje |
> | Działanie | Microsoft.Capacity/reservationorders/reservations/write | Utwórz dowolną rezerwację |
> | Działanie | Microsoft. Pojemność/reservationorders/powrót/akcja | Zwróć wszelkie rezerwacje |
> | Działanie | Microsoft. Pojemność/reservationorders/Split/akcja | Podziel wszystkie rezerwacje |
> | Działanie | Microsoft. Pojemność/reservationorders/swap/akcja | Zamień wszystkie rezerwacje |
> | Działanie | Microsoft.Capacity/reservationorders/write | Utwórz dowolną rezerwację |
> | Działanie | Microsoft. Pojemność/dzierżawcy/rejestr/akcja | Rejestracja dowolnej dzierżawy |
> | Działanie | Microsoft. Pojemność/Wyrejestrowanie/akcja | Wyrejestrowywanie dzierżawy |
> | Działanie | Microsoft.Capacity/validatereservationorder/action | Weryfikowanie wszelkich rezerwacji |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Delete |  |
> | Działanie | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/odczyt |  |
> | Działanie | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Write |  |
> | Działanie | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Delete |  |
> | Działanie | Microsoft. CDN/cdnwebapplicationfirewallpolicies/odczyt |  |
> | Działanie | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Write |  |
> | Działanie | Microsoft. CDN/CheckNameAvailability/akcja |  |
> | Działanie | Microsoft. CDN/CheckResourceUsage/akcja |  |
> | Działanie | Microsoft.Cdn/edgenodes/delete |  |
> | Działanie | Microsoft.Cdn/edgenodes/read |  |
> | Działanie | Microsoft.Cdn/edgenodes/write |  |
> | Działanie | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Delete |  |
> | Działanie | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/odczyt |  |
> | Działanie | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/zapis |  |
> | Działanie | Microsoft. CDN/operationresults/Delete |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/przeczyszczanie/akcja |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/Start/akcja |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Działanie | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Działanie | Microsoft.Cdn/operationresults/read |  |
> | Działanie | Microsoft. CDN/operationresults/Write |  |
> | Działanie | Microsoft.Cdn/operations/read |  |
> | Działanie | Microsoft. CDN/profile/CheckResourceUsage/akcja |  |
> | Działanie | Microsoft. CDN/profile/usuwanie |  |
> | Działanie | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Działanie | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Działanie | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/customdomains/EnableCustomHttps/Action |  |
> | Działanie | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/customdomains/Write |  |
> | Działanie | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/obciążenie/akcja |  |
> | Działanie | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Działanie | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/źródła/zapis |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/Przeczyść/akcja |  |
> | Działanie | Microsoft.Cdn/profiles/endpoints/read |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/uruchomienie/akcja |  |
> | Działanie | Microsoft. CDN/profile/Endpoints/akcja |  |
> | Działanie | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/zapis |  |
> | Działanie | Microsoft. CDN/profile/GenerateSsoUri/akcja |  |
> | Działanie | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Działanie | Microsoft.Cdn/profiles/read |  |
> | Działanie | Microsoft. CDN/profile/zapis |  |
> | Działanie | Microsoft. CDN/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi CDN i umożliwia tworzenie profilów sieci CDN. |
> | Działanie | Microsoft. CDN/ValidateProbe/akcja |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Usuń istniejący certyfikat |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Pobieranie listy certyfikatów |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejący |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/Delete | Usuń istniejący AppServiceCertificate |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/Read | Pobierz listę CertificateOrders |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Wydaj ponownie istniejące zamówienia certyfikatu |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/Renew/akcja | Odnów istniejący zamówienia certyfikatu |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Wyślij ponownie wiadomość e-mail z certyfikatem |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Wyślij ponownie wiadomości e-mail z żądaniem na inny adres e-mail |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Pobierz pieczęć witryny dla wystawionego Certyfikat usługi App Service |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/retrieveCertificateActions/akcja | Pobieranie listy akcji certyfikatów |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/retrieveEmailHistory/akcja | Pobierz historię poczty e-mail certyfikatu |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/verifyDomainOwnership/akcja | Weryfikuj własność domeny |
> | Działanie | Microsoft.CertificateRegistration/certificateOrders/Write | Dodaj nowy zamówienia certyfikatu lub zaktualizuj istniejący |
> | Działanie | Microsoft.CertificateRegistration/operations/Read | Wyświetl listę wszystkich operacji z rejestracji certyfikatu usługi App Service |
> | Działanie | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Zainicjuj obsługę główną usługi dla jednostki aplikacji usługi |
> | Działanie | Microsoft. CertificateRegistration/Register/Action | Zarejestruj dostawcę zasobów Microsoft Certificates dla subskrypcji |
> | Działanie | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Weryfikowanie obiektu zakupu certyfikatu bez jego przesyłania |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ClassicCompute/możliwości/odczyt | Pokazuje możliwości |
> | Działanie | Microsoft. ClassicCompute/checkDomainNameAvailability/Action | Sprawdza dostępność danej nazwy domeny. |
> | Działanie | Microsoft. ClassicCompute/checkDomainNameAvailability/odczyt | Pobiera dostępność danej nazwy domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Active/Write | Ustawia nazwę aktywnej domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/availabilitySets/Read | Pokaż zestaw dostępności dla zasobu. |
> | Działanie | Microsoft. ClassicCompute/domainNames/możliwości/odczyt | Pokazuje możliwości nazw domen |
> | Działanie | Microsoft.ClassicCompute/domainNames/delete | Usuń nazwy domen dla zasobów. |
> | Działanie | Microsoft. ClassicCompute/domainNames/deploymentslots/Read | Pokazuje miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/deploymentslots/role/odczyt | Pobierz rolę w miejscu wdrożenia nazwy domeny |
> | Działanie | Microsoft. ClassicCompute/domainNames/deploymentslots/role/roleinstances/Read | Pobierz wystąpienie roli dla roli w miejscu wdrożenia nazwy domeny |
> | Działanie | Microsoft. ClassicCompute/domainNames/deploymentslots/State/Read | Pobierz stan miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/deploymentslots/State/Write | Dodaj stan miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/deploymentslots/upgradedomain/Read | Pobierz domenę uaktualnienia dla miejsca wdrożenia w nazwie domeny |
> | Działanie | Microsoft. ClassicCompute/domainNames/deploymentslots/upgradedomain/Write | Aktualizuj domenę uaktualnienia dla miejsca wdrożenia w nazwie domeny |
> | Działanie | Microsoft. ClassicCompute/domainNames/deploymentslots/Write | Tworzy lub aktualizuje wdrożenie. |
> | Działanie | Microsoft. ClassicCompute/domainNames/rozszerzenia/usuwanie | Usuń rozszerzenia nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Extensions/operationStatuses/Read | Odczytuje stan operacji dla rozszerzeń nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Extensions/odczyt | Zwraca rozszerzenia nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Extensions/Write | Dodaj rozszerzenia nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Delete | Usuń nowe wewnętrzne równoważenie obciążenia. |
> | Działanie | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Odczytuje stan operacji dla wewnętrznych modułów równoważenia obciążenia nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Read | Pobiera wewnętrzne moduły równoważenia obciążenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Write | Tworzy nowe wewnętrzne równoważenie obciążenia. |
> | Działanie | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Odczytuje stan operacji dla zestawów punktów końcowych z równoważeniem obciążenia nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Read | Pobierz zestawy punktów końcowych ze zrównoważonym obciążeniem. |
> | Działanie | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Write | Dodaj zestaw punktów końcowych ze zrównoważonym obciążeniem. |
> | Działanie | Microsoft. ClassicCompute/domainNames/operationstatuses/Read | Pobierz stan operacji dla nazwy domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/operationStatuses/Read | Odczytuje stan operacji dla rozszerzeń nazw domen. |
> | Działanie | Microsoft.ClassicCompute/domainNames/read | Zwróć nazwy domen dla zasobów. |
> | Działanie | Microsoft. ClassicCompute/domainNames/servicecertificates/Delete | Usuń używane certyfikaty usługi. |
> | Działanie | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Odczytuje stan operacji dla certyfikatów usługi nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/servicecertificates/Read | Zwraca używane certyfikaty usługi. |
> | Działanie | Microsoft. ClassicCompute/domainNames/servicecertificates/Write | Dodaj lub zmodyfikuj używane certyfikaty usługi. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/abortMigration/Action | Przerywa migrację miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/commitMigration/Action | Zatwierdza migrację miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/Delete | Usuwa określone miejsce wdrożenia. |
> | Działanie | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Odczytuje stan operacji dla gniazd nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/prepareMigration/Action | Przygotowuje migrację miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/Read | Pokazuje miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/extensionReferences/Delete | Usuń odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/extensionReferences/operationStatuses/Read | Odczytuje stan operacji dla odwołań rozszerzenia ról gniazd nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/extensionReferences/Read | Zwraca odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/extensionReferences/Write | Dodaj lub zmodyfikuj odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/metricdefinitions/Read | Pobierz definicję metryki roli dla nazwy domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/Metrics/Read | Pobierz metrykę roli dla nazwy domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/operationstatuses/Read | Pobierz stan operacji dla roli gniazda nazwy domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/Read | Uzyskaj rolę dla miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/downloadremotedesktopconnectionfile/Action | Pobiera plik połączenia pulpitu zdalnego dla wystąpienia roli w roli gniazda nazwy domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/operationStatuses/Read | Pobiera stan operacji dla wystąpienia roli w ramach roli gniazda nazwy domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/Read | Pobierz wystąpienie roli. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/Rebuild/Action | Odbudowuje wystąpienie roli. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/Reimage/Action | Odtwarza z obrazu wystąpienie roli. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/restart/Action | Uruchamia ponownie wystąpienia roli. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/SKU/odczyt | Pobierz jednostkę SKU roli dla miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/role/Write | Dodaj rolę dla miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/Start/akcja | Uruchamia miejsce wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/State/Start/Write | Zmienia stan miejsca wdrożenia na zatrzymane. |
> | Działanie | Microsoft. ClassicCompute/domainNames/szczeliny/stan/Zatrzymaj/Zapisz | Zmienia stan miejsca wdrożenia na rozpoczęte. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/akcja/Stop | Wstrzymuje miejsce wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/upgradeDomain/Write | Przeprowadź uaktualnienie domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/validateMigration/Action | Sprawdza poprawność migracji miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/Write | Tworzy lub aktualizuje wdrożenie. |
> | Działanie | Microsoft. ClassicCompute/domainNames/swap/Action | Zamienia miejsce przejściowe na miejsce produkcyjne. |
> | Działanie | Microsoft.ClassicCompute/domainNames/write | Dodaj lub zmodyfikuj nazwy domen dla zasobów. |
> | Działanie | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Przenieś wszystkie zasoby klasyczne do innej subskrypcji. |
> | Działanie | Microsoft.ClassicCompute/operatingSystemFamilies/read | Wyświetla listę rodzin systemów operacyjnych gościa dostępnych w Microsoft Azure, a także listę wersji systemu operacyjnego dostępnych dla każdej rodziny. |
> | Działanie | Microsoft.ClassicCompute/operatingSystems/read | Wyświetla listę wersji systemu operacyjnego gościa, które są obecnie dostępne w Microsoft Azure. |
> | Działanie | Microsoft. ClassicCompute/Operations/Read | Pobiera listę operacji. |
> | Działanie | Microsoft.ClassicCompute/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft.ClassicCompute/quotas/read | Pobieranie przydziału dla subskrypcji. |
> | Działanie | Microsoft. ClassicCompute/Register/Action | Zarejestruj się w ramach obliczeń klasycznych |
> | Działanie | Microsoft.ClassicCompute/resourceTypes/skus/read | Pobiera listę jednostek SKU dla obsługiwanych typów zasobów. |
> | Działanie | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Sprawdź dostępność subskrypcji dla klasycznej operacji przenoszenia. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszynami wirtualnymi. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Pobiera możliwe operacje asynchroniczne |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/attachDisk/akcja | Dołącza dysk danych do maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/przechwytywanie/akcja | Przechwyć maszynę wirtualną. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/delete | Usuwa maszyny wirtualne. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/detachDisk/akcja | Odłącza dysk danych z maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/diagnosticsettings/Read | Pobierz ustawienia diagnostyki maszyny wirtualnej. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/disks/read | Pobiera listę dysków z danymi |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Pobiera plik RDP dla maszyny wirtualnej. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Odczytuje stan operacji dla rozszerzeń maszyn wirtualnych. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/extensions/read | Pobiera rozszerzenie maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Extensions/Write | Umieszcza rozszerzenie maszyny wirtualnej. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Pobierz definicję metryki maszyny wirtualnej. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/metrics/read | Pobiera metryki. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszynami wirtualnymi. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Odczytuje stan operacji dla maszyn wirtualnych. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/performMaintenance/akcja | Wykonuje konserwację na maszynie wirtualnej. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/read | Pobiera listę maszyn wirtualnych. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Ponownie wdraża maszynę wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/restart/akcja | Uruchamia ponownie maszyny wirtualne. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Shutdown/akcja | Zamknij maszynę wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Start/akcja | Uruchom maszynę wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Stop/akcja | Wyłącza maszynę wirtualną. |
> | Działanie | Microsoft.ClassicCompute/virtualMachines/write | Dodaj lub zmodyfikuj maszyny wirtualne. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Pobierz stan operacji połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/Komunikacja równorzędna/usuwanie | Usuń komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Pobierz stan operacji komunikacji równorzędnej połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Pobierz komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/Peers/Write | Dodaj komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Pobierz połączenia krzyżowe w usłudze Express Route. |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Dodaj połączenia krzyżowe dla trasy Express Route. |
> | Działanie | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Pobiera listę obsługiwanych urządzeń. |
> | Działanie | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń. |
> | Działanie | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla sieciowej grupy zabezpieczeń. |
> | Działanie | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne sieciowych grup zabezpieczeń |
> | Działanie | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne grup zabezpieczeń sieci. Ta operacja jest uzupełniana przez dostawcę zasobów usługi Insights. |
> | Działanie | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Pobiera zdarzenia dla sieciowej grupy zabezpieczeń |
> | Działanie | Microsoft.ClassicNetwork/networkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń. |
> | Działanie | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Usuwa regułę zabezpieczeń. |
> | Działanie | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Odczytuje stan operacji dla reguł zabezpieczeń sieciowych grup zabezpieczeń. |
> | Działanie | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Pobiera regułę zabezpieczeń. |
> | Działanie | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Dodaje lub aktualizuje regułę zabezpieczeń. |
> | Działanie | Microsoft.ClassicNetwork/networkSecurityGroups/write | Dodaje nową sieciową grupę zabezpieczeń. |
> | Działanie | Microsoft. ClassicNetwork/Operations/Read | Pobierz operacje dotyczące sieci klasycznej. |
> | Działanie | Microsoft.ClassicNetwork/quotas/read | Pobieranie przydziału dla subskrypcji. |
> | Działanie | Microsoft. ClassicNetwork/Register/Action | Zarejestruj w sieci klasycznej |
> | Działanie | Microsoft.ClassicNetwork/reservedIps/delete | Usuń zastrzeżony adres IP. |
> | Działanie | Microsoft.ClassicNetwork/reservedIps/join/action | Dołącz zastrzeżony adres IP |
> | Działanie | Microsoft.ClassicNetwork/reservedIps/link/action | Połącz zastrzeżony adres IP |
> | Działanie | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Odczytuje stan operacji dla zarezerwowanych adresów IP. |
> | Działanie | Microsoft.ClassicNetwork/reservedIps/read | Pobiera zastrzeżone adresy IP |
> | Działanie | Microsoft.ClassicNetwork/reservedIps/write | Dodaj nowy zastrzeżony adres IP |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/akcja | Przerywa migrację Virtual Network |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/możliwości/odczyt | Pokazuje możliwości |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/akcja | Sprawdza dostępność danego adresu IP w sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/akcja | Zatwierdza migrację Virtual Network |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Delete | Usuwa sieć wirtualną. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Cofa odwołanie certyfikatu klienta. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Odczytaj odwołane certyfikaty klienta. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Odwołuje certyfikat klienta. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Usuwa certyfikat klienta bramy sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Pobiera certyfikat według odcisku palca. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Wyświetla pakiet certyfikatu bramy sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Znajdź certyfikaty główne klienta. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Przekazuje nowy certyfikat główny klienta. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Connect/Action | Nawiązuje połączenie z lokacją usługi Site Gateway. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Disconnect/Action | Rozłącza połączenie między lokacją a bramą lokacji. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Read | Pobiera listę połączeń. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/test/akcja | Testuje połączenie bramy lokacja-lokacja. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Delete | Usuwa bramę sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Pobiera skrypt konfiguracji urządzenia. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/downloadDiagnostics/Action | Pobiera diagnostykę bramy. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Pobiera klucz usługi obwodu. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/listPackage/Action | Wyświetla pakiet bramy sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Odczytuje stan operacji dla bram sieci wirtualnych. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Packages/Read | Pobiera pakiet bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Read | Pobiera bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/startDiagnostics/Action | Uruchamia diagnostykę dla bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/stopDiagnostics/Action | Wyłącza diagnostykę bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Brama/zapis | Dodaje bramę sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/join/action | Przyłącza do sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Odczytuje stan operacji dla sieci wirtualnych. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/peer/Action | Tworzy równorzędną sieć wirtualną z inną siecią wirtualną. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/akcja | Przygotowuje migrację Virtual Network |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/read | Pobierz sieć wirtualną. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Usuwa zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Pobiera zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Dodaje lub aktualizuje zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grupy zabezpieczeń sieci skojarzonej z podsiecią sieci wirtualnej. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/akcja | Sprawdza poprawność migracji Virtual Network |
> | Działanie | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Pobiera komunikację równorzędną sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Write | Dodaj nową sieć wirtualną. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ClassicStorage/możliwości/odczyt | Pokazuje możliwości |
> | Działanie | Microsoft. ClassicStorage/checkStorageAccountAvailability/Action | Sprawdza dostępność konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/checkStorageAccountAvailability/odczyt | Pobierz dostępność konta magazynu. |
> | Działanie | Microsoft.ClassicStorage/disks/read | Zwraca dysk konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/images/operationstatuses/Read | Pobiera stan operacji obrazu. |
> | Działanie | Microsoft.ClassicStorage/images/read | Zwraca obraz. |
> | Działanie | Microsoft. ClassicStorage/Operations/Read | Pobiera operacje magazynu klasycznego |
> | Działanie | Microsoft.ClassicStorage/osImages/read | Zwraca obraz systemu operacyjnego. |
> | Działanie | Microsoft.ClassicStorage/osPlatformImages/read | Pobiera obraz platformy systemu operacyjnego. |
> | Działanie | Microsoft.ClassicStorage/publicImages/read | Pobiera publiczny obraz maszyny wirtualnej. |
> | Działanie | Microsoft.ClassicStorage/quotas/read | Pobieranie przydziału dla subskrypcji. |
> | Działanie | Microsoft. ClassicStorage/Register/Action | Zarejestruj do magazynu klasycznego |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/abortMigration/akcja | Przerywa migrację konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/commitMigration/akcja | Zatwierdza migrację konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Delete | Usuń konto magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/disks/Delete | Usuwa dany dysk konta magazynu. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/disks/read | Zwraca dysk konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/disks/Write | Dodaje dysk konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/images/Delete | Usuwa dany obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/images/operationstatuses/Read | Zwraca stan operacji obrazu konta magazynu. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/images/read | Zwraca obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/operationStatuses/Read | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Usuwa dany obraz systemu operacyjnego konta magazynu. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/osImages/read | Zwraca obraz systemu operacyjnego konta magazynu. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/osImages/write | Dodaje dany obraz systemu operacyjnego konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/prepareMigration/akcja | Przygotowuje migrację konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/read | Zwróć konto magazynu z danym kontem. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/regenerateKey/akcja | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Services/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Services/Metrics/Read | Pobiera metryki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Services/odczyt | Pobierz dostępne usługi. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/validateMigration/akcja | Sprawdza poprawność migracji konta magazynu. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Usuwa dany obraz maszyny wirtualnej. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Pobiera stan operacji obrazu maszyny wirtualnej. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Zwraca obraz maszyny wirtualnej. |
> | Działanie | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Dodaje dany obraz maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Write | Dodaje nowe konto magazynu. |
> | Działanie | Microsoft.ClassicStorage/vmImages/read | Wyświetla listę obrazów maszyn wirtualnych. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/AnomalyDetector/szeregów czasowych/cała/Wykryj/akcja | Ta operacja generuje model przy użyciu całej serii, każdy punkt jest wykrywany z tym samym modelem.<br>W przypadku tej metody punkty przed i po określonym punkcie są używane do określenia, czy jest to anomalia.<br>Całe wykrywanie może dać użytkownikowi ogólny stan szeregów czasowych. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/AnomalyDetector/szeregów czasowych/Last/Detect/Action | Ta operacja generuje model przy użyciu punktów przed najnowszym. W przypadku tej metody tylko punkty historyczne są używane do określenia, czy punkt docelowy jest anomalią. Najnowsze punkty wykrywania są zgodne z scenariuszem monitorowania metryk biznesowych w czasie rzeczywistym. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/automatyczne sugerowanie/wyszukiwanie/akcja | Ta operacja zawiera sugestie dotyczące danego zapytania lub zapytania częściowego. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/analiza/akcja | Ta operacja wyodrębnia bogaty zestaw funkcji wizualnych na podstawie zawartości obrazu.  |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Ta operacja zwraca pole ograniczenia wokół najważniejszego obszaru obrazu. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Ta operacja generuje opis obrazu w języku czytelnym dla ludzi z pełnymi zdaniami.<br> Opis jest oparty na kolekcji tagów zawartości, które są również zwracane przez operację.<br>Dla każdego obrazu można wygenerować więcej niż jeden opis.<br> Opisy są uporządkowane według ich wyniku ufności.<br>Wszystkie opisy są w języku angielskim. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Ta operacja służy do wykrywania obiektów na określonym obrazie.  |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Ta operacja generuje obraz miniatury z szerokością i wysokością określoną przez użytkownika.<br> Domyślnie usługa analizuje obraz, identyfikuje region zainteresowania (ROI) i generuje współrzędne inteligentnego przycinania w oparciu o ten region.<br> Inteligentne przycinanie pomaga w przypadku określenia współczynnika proporcji, który różni się od obrazu wejściowego |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Ta operacja rozpoznaje zawartość w obrazie, stosując model specyficzny dla domeny.<br> Listę modeli specyficznych dla domeny, które są obsługiwane przez interfejs API przetwarzania obrazów, można pobrać przy użyciu żądania GET/models.<br> Obecnie interfejs API udostępnia następujące modele specyficzne dla domeny: osobistości, dzielnice. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Ta operacja zwraca listę modeli specyficznych dla domeny, które są obsługiwane przez interfejs API przetwarzania obrazów.  Obecnie interfejs API obsługuje następujące modele specyficzne dla domeny: aparat rozpoznawania osobistości, punkt rozpoznawania punktu orientacyjnego. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | Optyczne rozpoznawanie znaków (OCR) wykrywa tekst w obrazie i wyodrębnia rozpoznane znaki do strumienia znaków do użycia maszynowego.    |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/Read/Analizuj/Action | Użyj tego interfejsu, aby wykonać operację odczytu, korzystając ze stanu algorytmów optycznego rozpoznawania znaków (OCR) zoptymalizowanych pod kątem dokumentów intensywnie korzystających z tekstu.<br>Może obsługiwać dokumenty pisane ręcznie, drukowane lub mieszane.<br>W przypadku korzystania z interfejsu odczytu odpowiedź zawiera nagłówek o nazwie "Operation-Location".<br>Nagłówek "Operation-Location" zawiera adres URL, którego należy użyć dla operacji pobierania wyniku odczytu w celu uzyskania dostępu do wyników OCR. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/Read/analyzeresults/Read | Użyj tego interfejsu, aby pobrać stan i wynik OCR operacji odczytu.  Adres URL zawierający "operationId" jest zwracany w nagłówku odpowiedzi operacji odczytu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/Read/Core/asyncbatchanalyze/Action | Użyj tego interfejsu, aby uzyskać wynik operacji odczytu pliku wsadowego, wykorzystując w tym celu znak optyczny |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/Read/Operations/Read | Ten interfejs jest używany do pobierania wyników OCR operacji odczytu. Adres URL do tego interfejsu powinien zostać pobrany z pola <b>"operacja-lokalizacja"</b> zwróconego z pliku wsadowego odczytywania partii. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Użyj tego interfejsu, aby uzyskać wynik operacji Rozpoznawanie tekstu. Gdy używasz interfejsu Rozpoznawanie tekstu, odpowiedź zawiera pole o nazwie "Operation-Location". Pole "operacja — lokalizacja" zawiera adres URL, którego należy użyć dla operacji pobierania Rozpoznawanie tekstu. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Ta operacja generuje listę słów lub tagów, które są istotne dla zawartości dostarczonego obrazu.<br>Interfejs API przetwarzania obrazów może zwracać Tagi na podstawie obiektów, żywych, scenerii lub akcji znalezionych w obrazach.<br>W przeciwieństwie do kategorii, Tagi nie są zorganizowane zgodnie z hierarchicznym systemem klasyfikacji, ale odpowiadają zawartości obrazu.<br>Tagi mogą zawierać wskazówki, aby uniknąć niejednoznaczności lub zapewnienia kontekstu, na przykład tag "Cello" może towarzyszyć wskazówki "instrument muzyczny".<br>Wszystkie Tagi są w języku angielskim. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Ten interfejs jest używany do pobierania wyniku operacji tekstu. Adres URL tego interfejsu powinien zostać pobrany z pola <b>"Lokalizacja operacji"</b> zwróconego z interfejsu rozpoznawanie tekstu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/Action | Utwórz listę obrazów. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/Delete | Listy obrazów — usuwanie |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/images/Delete | Usuń obraz z listy obrazów. Lista obrazów może służyć do dopasowywania rozmytego do innych obrazów podczas korzystania z interfejsu API obrazów/dopasowywania. Usuń wszystkie obrazy z listy. Lista obrazów może służyć do dopasowywania rozmytego do innych obrazów podczas korzystania z interfejsu API obrazów/dopasowywania. * |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/images/Read | Obraz — Pobierz wszystkie identyfikatory obrazów |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/images/Write | Dodaj obraz do listy obrazów. Lista obrazów może służyć do dopasowywania rozmytego do innych obrazów podczas korzystania z interfejsu API obrazów/dopasowywania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/Read | Listy obrazów — Pobierz szczegóły — listy obrazów — Pobierz wszystko |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/refreshindex/Action | Listy obrazów — Odśwież indeks wyszukiwania |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/Write | Listy obrazów — Szczegóły aktualizacji |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/processimage/Szacuj/akcję | Zwraca prawdopodobieństwa obrazu zawierającego erotycznej lub zawartość dla dorosłych. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/processimage/findfaces/Action | Znajdź twarze na obrazach. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/processimage/Match/Action | Fuzzily dopasowuje obraz do jednej z niestandardowych list obrazów.<br>Możesz tworzyć niestandardowe listy obrazów i zarządzać nimi za pomocą tego interfejsu API.<br> |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/processimage/OCR/akcja | Zwraca dowolny tekst znaleziony w obrazie dla określonego języka. Jeśli w polu dane wejściowe nie określono żadnego języka, domyślnie zostanie wykrycie. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/ProcessText/detectlanguage/Action | Ta operacja spowoduje wykrycie języka danej zawartości wejściowej.<br>Zwraca kod ISO 639-3 dla języka przeważa zawierającego przesłany tekst.<br>Obsługiwane są ponad 110 języków. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/ProcessText/ekran/akcja | Operacja wykrywa przekleństwa w ponad 100 językach i dopasowuje je do niestandardowych i niestandardowych list elementów zabronionych. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/zespoły/zadania/akcja | Identyfikator zadania zostanie zwrócony dla zawartości obrazu opublikowanej w tym punkcie końcowym.  |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/zespoły/Jobs/Read | Pobierz szczegóły zadania dla identyfikatora zadania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/zespoły/przeglądy/AccessKey/odczyt | Pobierz klucz dostępu do zawartości dla zespołu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ContentModerator/zespoły/przeglądy/akcja | Utworzone Recenzje będą widoczne dla recenzentów w zespole. Gdy recenzenci ukończyją przegląd, wyniki przeglądu zostaną opublikowane (tj. POST HTTP) w określonym CallBackEndpoint. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/frames/read | *NotDefined* |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/frames/write | Use this method to add frames for a video review. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/publish/action | Video reviews are initially created in an unpublished state - which means it is not available for reviewers on your team to review yet. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/read | Returns review details for the review Id passed. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcript/action | This API adds a transcript file (text version of all the words spoken in a video) to a video review. The file should be a valid WebVTT format. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcriptmoderationresult/action | This API adds a transcript screen text result file for a video review. Transcript screen text result file is a result of Screen Text API . In order to generate transcript screen text result file , a transcript file has to be screened for profanity using Screen Text API. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/delete | Delete a template in your team |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/read | Returns an array of review templates provisioned on this team. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/write | Creates or updates the specified template |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/read | Get the details of a specific Workflow on your Team. Get all the Workflows available for you Team* |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/write | Create a new workflow or update an existing one. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/action | Create term list. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/bulkupdate/action | Term Lists - Bulk Update |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/delete | Term Lists - Delete |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/read | Term Lists - Get All - Term Lists - Get Details |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/refreshindex/action | Term Lists - Refresh Search Index |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/delete | Term - Delete - Term - Delete All Terms |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/read | Term - Get All Terms |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/write | Term - Add Term |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/write | Term Lists - Update Details |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/action | Classify an image and saves the result. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/nostore/action | Classify an image without saving the result. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/action | Classify an image url and saves the result. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/nostore/action | Classify an image url without saving the result. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/action | Detect objects in an image and saves the result. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/nostore/action | Detect objects in an image without saving the result. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/action | Detect objects in an image url and saves the result. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/nostore/action | Detect objects in an image url without saving the result. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/domains/read | Get information about a specific domain. Get a list of the available domains.* |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/action | Set pool size of Label Proposal. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/read | Get pool size of Label Proposal for this project. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/project/migrate/action | *NotDefined* |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/action | Utwórz projekt. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/delete | Delete a specific project. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/akcja | Ten interfejs API akceptuje zawartość treści jako wieloczęściowy/form-Data i application/octet-stream. W przypadku korzystania z wieloczęściowego |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/usuwanie | Usuń obrazy z zestawu obrazów szkoleniowych. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/pliki/akcja | Ten interfejs API akceptuje partię plików i opcjonalnie Tagi do tworzenia obrazów. Istnieje limit wynoszący 64 obrazy i 20 tagów. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. predykcyjny/projects/images/ID/Read | Ten interfejs API zwróci zestaw obrazów dla określonych tagów i opcjonalnie iteracji. Jeśli nie określono iteracji, |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/przewidywania/akcja | Ten interfejs API tworzy partię obrazów z określonych zapowiadanych obrazów. Istnieje limit wynoszący 64 obrazy i 20 tagów. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/regionproposals/akcja | Ten interfejs API będzie uzyskiwać propozycje regionów dla obrazu oraz zaufanie dla regionu. Zwraca pustą tablicę, jeśli nie znaleziono żadnych propozycji. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/regiony/akcja | Ten interfejs API akceptuje partie regionów obrazu oraz opcjonalne Tagi, aby aktualizować istniejące obrazy z informacjami o regionie. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/regiony/usuwanie | Usuń zestaw regionów obrazu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. predykcyjny/projects/images/sugerował/Action | Ten interfejs API spowoduje pobranie nieoznakowanych obrazów filtrowanych według sugerowanych identyfikatorów tagów. Zwraca pustą tablicę, jeśli nie znaleziono obrazów. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/sugerowane/liczba/akcja | Ten interfejs API przyjmuje tagIds, aby uzyskać liczbę nieoznakowanych obrazów według sugerowanych tagów dla danego progu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/znaczniki/liczba/odczyt | Filtrowanie dotyczy relacji i/lub. Na przykład, jeśli podane identyfikatory tagów są dla "Dog" i |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/oznakowanie/odczyt | Ten interfejs API obsługuje przetwarzanie wsadowe i wybór zakresu. Domyślnie zwróci on tylko pierwsze 50 obrazów zgodnych z obrazami. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/Tagi/akcja | Skojarz zestaw obrazów z zestawem tagów. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/Tagi/usuwanie | Usuwanie zestawu tagów z zestawu obrazów. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/nieoznakowane/licznik/odczyt | Ten interfejs API zwraca obrazy, które nie mają tagów dla danego projektu i opcjonalnie iteracji. Jeśli nie określono iteracji, |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/Odczytaj/odczytane | Ten interfejs API obsługuje przetwarzanie wsadowe i wybór zakresu. Domyślnie zwróci on tylko pierwsze 50 obrazów zgodnych z obrazami. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/obrazy/adresy URL/akcja | Ten interfejs API akceptuje wsadowe adresy URL i opcjonalne Tagi do tworzenia obrazów. Istnieje limit wynoszący 64 obrazy i 20 tagów. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/usuwanie | Usuń określoną iterację projektu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/eksport/akcja | Eksportuj przeszkolone iteracje. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/eksportowanie/odczyt | Pobierz listę eksportów dla określonej iteracji. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/wydajność/obrazy/liczba/odczyt | Filtrowanie dotyczy relacji i/lub. Na przykład, jeśli podane identyfikatory tagów są dla "Dog" i |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/wydajność/obrazy/odczyt | Ten interfejs API obsługuje przetwarzanie wsadowe i wybór zakresu. Domyślnie zwróci on tylko pierwsze 50 obrazów zgodnych z obrazami. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/odczyt | Uzyskaj szczegółowe informacje o wydajności iteracji. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/publikowanie/akcja | Publikuj konkretną iterację. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/publikowanie/usuwanie | Cofnij publikację określonej iteracji. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/odczyt | Pobierz określoną iterację. Pobierz iteracje dla projektu. * |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/iteracje/zapis | Zaktualizuj określoną iterację. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/przewidywania/usuwanie | Usuń zestaw zapowiadanych obrazów i powiązane z nimi wyniki przewidywania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/przewidywania/kwerenda/akcja | Pobierz obrazy, które zostały wysłane do punktu końcowego przewidywania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/Quicktest/obraz/akcja | Szybkie testowanie obrazu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/Quicktest/URL/akcja | Szybkie testowanie adresu URL obrazu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/odczyt | Pobierz konkretny projekt. Pobierz swoje projekty. * |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/Tagi/akcja | Utwórz tag dla projektu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/Tagi/usuwanie | Usuń tag z projektu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/Tagi/odczyt | Uzyskaj informacje dotyczące określonego tagu. Pobierz Tagi dla danego projektu i iteracji. * |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/Tagi/zapis | Zaktualizuj tag. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/tagsandregions/sugestie/akcja | Ten interfejs API będzie pobierać sugerowane Tagi i regiony dla tablicy/partii nieoznakowanych obrazów, a także pewności dla tagów. Zwraca pustą tablicę, jeśli nie znaleziono żadnych tagów. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/szkolenie/akcja | Kolejkuje projekt do szkoleń. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/projekty/zapis | Aktualizowanie określonego projektu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/przydział/akcja | *NotDefined* |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/przydziały/usuwanie | *NotDefined* |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/przydziały/odświeżanie/zapisywanie | *NotDefined* |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/użycie/przewidywanie/użytkownik/odczyt | Uzyskaj użycie dla zasobu predykcyjnego dla użytkownika Oxford |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/użycie/szkolenia/zasób/warstwa/odczyt | Pobierz użycie dla zasobu szkoleniowego dla użytkownika platformy Azure |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/użycie/szkolenia/użytkownik/odczyt | Pobierz użycie dla zasobu szkoleniowego dla użytkownika Oxford |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/użytkownik/akcja | *NotDefined* |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/użytkownik/usuwanie | *NotDefined* |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/użytkownika/stanu/zapisu | Aktualizowanie stanu użytkownika |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/użytkownik/warstwa/zapis | *NotDefined* |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/użytkownicy/odczyt | *NotDefined* |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/dozwolonych/Delete | Usuwa użytkownika listy dozwolonych z określoną możliwością |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/dozwolonych/odczyt | Pobiera listę użytkowników listy dozwolonych z określoną możliwością |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/CustomVision. przewidywania/dozwolonych/Write | Aktualizuje lub tworzy użytkownika w dozwolonych z określoną możliwością |
> | Działanie | Microsoft.CognitiveServices/accounts/delete | Usuwa konta interfejsu API |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/EntitySearch/Search/Action | Pobierz jednostki i umieści wyniki dla danego zapytania. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/detect/action | Wykrywaj ludzkie twarze na obrazie, zwracają prostokąty twarzy i opcjonalnie z faceIds, dzielnicami i atrybutami. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Usuń określoną listę czołową. Obrazy powiązanej części na liście czołowej zostaną również usunięte. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Usuń miarę z listy czołowej według określonych faceListId i persisitedFaceId. Obraz powiązanej ze stroną czołową zostanie również usunięty. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Dodaj twarzy do określonej listy twarzy, do 1 000 twarze. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Pobierz faceListId, nazwę, userData i twarze listy twarzy na liście twarzy. Lista czołowa lists "faceListId, Name i userData". |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Utwórz pustą listę czołową z faceListId określonymi przez użytkownika, nazwą i opcjonalnym postawce userData. Do 64 list czołowych są dozwolone informacje o aktualizacji listy czołowej, w tym nazwa i userData. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Podaną faceId twarzy zapytania, aby wyszukiwać podobne buźki z tablicy faceId, listy twarzy lub dużej listy twarzy. faceId |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/group/action | Podziel powierzchnie kandydatów na grupy w oparciu o podobieństwo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/Identyfikuj/Action | Identyfikacja 1-do-wielu, aby znaleźć najbliższe dopasowania konkretnej osoby, której dotyczy zapytanie, od grupy osób lub dużej osoby. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Usuń określoną dużą listę czołową. Powiązane obrazy ze stroną ze znaczną stroną są również usuwane. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Usuń miarę z dużej listy czołowej według określonych largeFaceListId i persisitedFaceId. Obraz powiązanej ze stroną czołową zostanie również usunięty. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Pobierz utrwaloną twarzy na dużą listę twarzy przez largeFaceListId i persistedFaceId. Lista twarzy persistedFaceId i userData na określonej dużej liście |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Dodaj twarz do określonej dużej listy twarzy, do 1 000 000 twarzy. Zaktualizuj pole userData określonego kroju na dużą listę czołową przez jego persistedFaceId. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Pobierz largeFaceListId, imię i nazwisko, userData dla dużej listy czołowej. Wyświetlanie listy dużych elementów z listą kroju "informacje o largeFaceListId, nazwie i userData. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Prześlij zadanie szkoleniowe o dużym rozmiarze. Szkolenie to kluczowy krok, którego może używać tylko przeszkolonej dużej listy czołowej. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Aby sprawdzić stan szkolenia z dużą ilością, zakończono lub nadal trwa. Szkolenie LargeFaceList jest operacją asynchroniczną |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Utwórz pustą listę dużej kroju z largeFaceListId określonymi przez użytkownika, nazwą i opcjonalnym postawce userData. Aktualizowanie informacji o dużej liście czołowej, w tym nazwa i userData. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Usuń istniejącą grupę dużej osoby z określonym personGroupId. Dane utrwalone w tej dużej grupie osób zostaną usunięte. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Utwórz nową osobę w określonej grupie dużej osoby. Aby dodać miarę do tej osoby, zadzwoń |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Usuń istniejącą osobę z grupy dużej osoby. Wszystkie przechowywane dane osoby i obrazy do wprowadzania danych zostaną usunięte. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Usuń miarę z osoby w grupie dużej osoby. Dane dotyczące kroju i obrazu związane z tym wpisem na tej samej rachunku zostaną również usunięte. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Pobierz informacje o głowie osoby. Głowa utrwalonej osoby jest określana przez jej largePersonGroupId, personId i persistedFaceId. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Dodaj obraz przedstawiający do osoby do grupy dużych osób na potrzeby identyfikacji lub weryfikacji. Aby rozwiązać ten obraz, należy zaktualizować pole userData twarzy dla osoby. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Pobierz imię i nazwisko osoby, a utrwalony faceIds reprezentujący zarejestrowanego obrazu twarzy osoby. Wyświetl listę wszystkich informacji o osobach w określonej grupie dużych osób, w tym personId, Name, userData i persistedFaceIds. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Nazwa aktualizacji lub userData osoby. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Pobierz informacje o dużej grupie osób, w tym jej nazwę i userData. Ten interfejs API zwraca listę informacji o dużej grupie osób, których wszystkie istniejące grupy osób mają largePesonGroupId, nazwę i userData. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Prześlij zadanie szkoleniowe dotyczące dużej grupy osób. Szkolenie to kluczowy krok, który może być używany tylko przez przeszkolonej grupy osób. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Aby sprawdzić, czy stan szkolenia dla dużej grupy osób zakończył się lub nadal trwa. Szkolenie LargePersonGroup jest operacją asynchroniczną |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Utwórz nową grupę dużych osób z largePersonGroupId określonymi przez użytkownika, nazwą i opcjonalnym postawce userData. Zaktualizuj nazwę istniejącej grupy osób o dużej osobie. Właściwości są przechowywane bez zmian, jeśli nie znajdują się w treści żądania. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Usuń istniejącą grupę osób z określonym personGroupId. Utrwalone dane w tej grupie osób zostaną usunięte. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Utwórz nową osobę w określonej grupie osób. Aby dodać miarę do tej osoby, zadzwoń |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Usuń istniejącą osobę z grupy osób. Wszystkie przechowywane dane osoby i obrazy do wprowadzania danych zostaną usunięte. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Usuń miarę z osoby w grupie osób. Dane dotyczące kroju i obrazu związane z tym wpisem na tej samej rachunku zostaną również usunięte. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Pobierz informacje o głowie osoby. Głowa utrwalonej osoby jest określana przez jej personGroupId, personId i persistedFaceId. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Dodawanie obrazu do osoby do grupy osób w celu identyfikacji lub weryfikacji. Aby obsłużyć obraz wielu aktualizacji pola userData w trwałej twarzy dla osoby. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Pobierz imię i nazwisko osoby, a utrwalony faceIds reprezentujący zarejestrowanego obrazu twarzy osoby. Wyświetl listę wszystkich informacji o osobach w określonej grupie osób, w tym personId, Name, userData i persistedFaceIds zarejestrowanych. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Nazwa aktualizacji lub userData osoby. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Pobierz nazwę grupy osób i userData. Aby uzyskać informacje o osobach w ramach tej grupy osób, użyj list pesonGroupId, Name i userData grup osób. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Prześlij zadanie szkoleniowe dotyczące grupy osób. Szkolenie to kluczowy krok, który może być używany tylko przez przeszkolonej grupy osób. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Aby sprawdzić, czy stan szkolenia grupy osób zakończył się lub nadal trwa. Uczenie użytkownika jest wyzwalane przez operację asynchroniczną |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Utwórz nową grupę osób o określonej personGroupId, nazwie i podanym przez użytkownika postawce. Zaktualizuj nazwę istniejącej grupy osób i userData. Właściwości są przechowywane bez zmian, jeśli nie znajdują się w treści żądania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/verify/Action | Sprawdź, czy dwie twarze należą do tej samej osoby, czy jedna z nich należy do osoby. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/models/analiza/akcja | Wyodrębnij pary klucz-wartość z danego dokumentu. Dokument wejściowy musi być jednym z obsługiwanych typów zawartości — "Application/PDF", "Image/JPEG" lub "Image/png". W formacie JSON zostanie zwrócona odpowiedź o powodzeniu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/models/Delete | Usuń artefakty modelu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/modele/klucze/odczyt | Pobierz klucze dla modelu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/models/Read | Pobierz informacje o modelu. Uzyskaj informacje na temat wszystkich przeszkolonych modeli niestandardowych * |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/pociąg/akcja | Tworzenie i uczenie modelu niestandardowego.<br>Żądanie uczenia musi zawierać parametr źródłowy, który jest zewnętrznym dostępnym identyfikatorem URI kontenera obiektów BLOB usługi Azure Storage (najlepiej jest identyfikator URI sygnatury dostępu współdzielonego) lub prawidłową ścieżką do folderu danych na dysku zainstalowanym lokalnie.<br>Gdy ścieżki lokalne są określone, muszą być zgodne z formatem ścieżki systemu Linux/UNIX i być ścieżką bezwzględną do konfiguracji instalacji wejściowej |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/FormRecognizer/prekompilacja/pokwitowanie/asyncbatchanalyze/akcja | Wyodrębnij wartości tekstowe pola i semantyki z danego dokumentu paragonu. Dokument wejściowy obrazu musi być jednym z obsługiwanych typów zawartości — JPEG, PNG, BMP, PDF lub TIFF. Odpowiedź powiedzie się to kod JSON zawierający pole o nazwie "Operation-Location", które zawiera adres URL dla operacji pobierania wyniku odbioru w celu asynchronicznego pobrania wyników. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/FormRecognizer/preskompilowana/pokwitowania/działania/akcja | Zbadaj stan i Pobierz wynik operacji analizy przychodu. Adres URL do tego interfejsu można uzyskać z nagłówka "Operation-Location" w odpowiedzi na potwierdzenie odbioru. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImageSearch/szczegóły/akcja | Zwraca szczegółowe informacje o obrazie, takie jak strony sieci Web, które zawierają obraz. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImageSearch/Search/Action | Pobierz odpowiednie obrazy dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImageSearch/trendy/akcja | Pobierz obecnie popularne obrazy. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/Action | Tworzy sesję czytnika immersyjny |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/InkRecognizer/Rozpoznaj/akcja | Dane zestawu pociągnięć analizują zawartość i generują listę rozpoznanych jednostek, w tym rozpoznany tekst. |
> | Działanie | Microsoft.CognitiveServices/accounts/listKeys/action | Klucze list |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Pobiera opublikowaną prognozę punktów końcowych dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/categorysearch/Action | Zwraca wiadomości dla podanej kategorii. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/Search/Action | Uzyskaj artykuły z wiadomościami istotnymi dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/Trendingtopics/Action | Poznaj tematy trendów identyfikowane przez usługę Bing. Są to te same tematy widoczne na transparencie u dołu strony głównej usługi Bing. |
> | Działanie | Microsoft.CognitiveServices/accounts/read | Odczytuje konta interfejsu API. |
> | Działanie | Microsoft.CognitiveServices/accounts/regenerateKey/action | Wygeneruj ponownie klucz |
> | Działanie | Microsoft.CognitiveServices/accounts/skus/read | Odczytuje dostępne jednostki SKU dla istniejącego zasobu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/sprawdzanie pisowni/sprawdzanie pisowni/akcja | Pobierz wynik zapytania sprawdzania pisowni za pomocą GET lub POST. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | Interfejs API zwraca listę znanych jednostek i ogólnych nazwanych jednostek (\"osoby\", \"lokalizację\", \"organizacji\" itp.) w danym dokumencie. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | Interfejs API zwraca listę ciągów wskazujących najważniejsze punkty w tekście wejściowym. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | Interfejs API zwraca wykryty język i wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 oznaczają 100% pewności, że określony język jest poprawny. Obsługiwanych jest 120 języków. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | Interfejs API zwraca wynik liczbowy z zakresu od 0 do 1.<br>Wyniki zbliżone do wartości 1 wskazują na pozytywną tonację, a wyniki zbliżone do wartości 0 wskazują na negatywną tonację.<br>Wynik 0,5 wskazuje brak tonacji (np.<br>Instrukcja factoid). |
> | Działanie | Microsoft.CognitiveServices/accounts/usages/read | Pobierz użycie przydziału dla istniejącego zasobu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/szczegóły/akcja | Uzyskaj wgląd w dane wideo, takie jak powiązane wideo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/Search/Action | Pobierz wideo odpowiednie dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/trendy/akcja | Pobierz obecnie trendy wideo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VisualSearch/Search/Action | Zwraca listę tagów odnoszących się do podanego obrazu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/WebSearch/Search/Action | Pobierz wyniki dla danego zapytania z Internetu, obrazu, wiadomości & wideo. |
> | Działanie | Microsoft.CognitiveServices/accounts/write | Zapisuje konta interfejsu API. |
> | Działanie | Microsoft.CognitiveServices/checkDomainAvailability/action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Działanie | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Działanie | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Działanie | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Powiadomienie z firmy Microsoft. Network usuwającej VirtualNetworks lub podsieci. |
> | Działanie | Microsoft. CognitiveServices/Locations/operationresults/Read | Odczytaj stan operacji asynchronicznej. |
> | Działanie | Microsoft.CognitiveServices/Operations/read | Wyświetl wszystkie dostępne operacje |
> | Działanie | Microsoft.CognitiveServices/register/action | Rejestruje subskrypcję dla Cognitive Services |
> | Działanie | Microsoft.CognitiveServices/register/action | Rejestruje subskrypcję dla Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Commerce/RateCard/read | Zwraca dane oferty, metadane zasobów/licznika i stawki dla danej subskrypcji. |
> | Działanie | Microsoft. Commerce/rejestr/akcja | Zarejestruj subskrypcję usługi Microsoft Commerce UsageAggregate |
> | Działanie | Microsoft. Commerce/Wyrejestrowanie/akcja | Wyrejestruj subskrypcję usługi Microsoft Commerce UsageAggregate |
> | Działanie | Microsoft.Commerce/UsageAggregates/read | Pobiera użycie Microsoft Azure przez subskrypcję. Wynik zawiera zagregowane dane użycia, informacje dotyczące subskrypcji i zasobów, w określonym przedziale czasu. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. COMPUTE/availabilitySets/Delete | Usuwa zestaw dostępności |
> | Działanie | Microsoft.Compute/availabilitySets/read | Pobierz właściwości zestawu dostępności |
> | Działanie | Microsoft.Compute/availabilitySets/vmSizes/read | Wyświetl dostępne rozmiary dla tworzenia lub aktualizowania maszyny wirtualnej w zestawie dostępności |
> | Działanie | Microsoft. COMPUTE/availabilitySets/Write | Tworzy nowy zestaw dostępności lub aktualizuje istniejący |
> | Działanie | Microsoft. COMPUTE/diskEncryptionSets/Delete | Usuń zestaw szyfrowania dysków |
> | Działanie | Microsoft. COMPUTE/diskEncryptionSets/Read | Pobierz właściwości zestawu szyfrowania dysków |
> | Działanie | Microsoft. COMPUTE/diskEncryptionSets/Write | Utwórz nowy zestaw szyfrowania dysków lub zaktualizuj istniejący |
> | Działanie | Microsoft. COMPUTE/disks/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dysku na potrzeby usługi dostęp do obiektów BLOB |
> | Działanie | Microsoft. COMPUTE/disks/Delete | Usuwa dysk |
> | Działanie | Microsoft.Compute/disks/endGetAccess/action | Odwołaj identyfikator URI sygnatury dostępu współdzielonego dysku |
> | Działanie | Microsoft.Compute/disks/read | Pobieranie właściwości dysku |
> | Działanie | Microsoft.Compute/disks/write | Tworzy nowy dysk lub aktualizuje istniejący |
> | Działanie | Microsoft. COMPUTE/Galerie/aplikacje/usuwanie | Usuwa aplikację galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/aplikacje/odczyt | Pobiera właściwości aplikacji galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/aplikacje/wersje/Usuń | Usuwa wersję aplikacji galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/aplikacje/wersje/odczyt | Pobiera właściwości wersji aplikacji galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/aplikacje/wersje/zapis | Tworzy nową wersję aplikacji galerii lub aktualizuje istniejącą |
> | Działanie | Microsoft. COMPUTE/Galerie/aplikacje/zapis | Tworzy nową aplikację galerii lub aktualizuje istniejącą |
> | Działanie | Microsoft.Compute/galleries/delete | Usuwa galerię |
> | Działanie | Microsoft.Compute/galleries/images/delete | Usuwa obraz galerii |
> | Działanie | Microsoft.Compute/galleries/images/read | Pobiera właściwości obrazu galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/obrazy/wersje/usuwanie | Usuwa wersję obrazu galerii |
> | Działanie | Microsoft.Compute/galleries/images/versions/read | Pobiera właściwości wersji obrazu galerii |
> | Działanie | Microsoft.Compute/galleries/images/versions/write | Tworzy nową wersję obrazu galerii lub aktualizuje istniejącą |
> | Działanie | Microsoft.Compute/galleries/images/write | Tworzy nowy obraz galerii lub aktualizuje istniejący |
> | Działanie | Microsoft.Compute/galleries/read | Pobiera właściwości galerii |
> | Działanie | Microsoft.Compute/galleries/write | Tworzy nową galerię lub aktualizuje istniejącą. |
> | Działanie | Microsoft. COMPUTE/hostGroups/Delete | Usuwa grupę hostów |
> | Działanie | Microsoft. COMPUTE/hostGroups/hosts/Delete | Usuwa hosta |
> | Działanie | Microsoft. COMPUTE/hostGroups/hosty/odczyt | Pobierz właściwości hosta |
> | Działanie | Microsoft. COMPUTE/hostGroups/hosts/Write | Tworzy nowy host lub aktualizuje istniejącego hosta |
> | Działanie | Microsoft.Compute/hostGroups/read | Pobierz właściwości grupy hostów |
> | Działanie | Microsoft.Compute/hostGroups/write | Tworzy nową grupę hostów lub aktualizuje istniejącą grupę hostów |
> | Działanie | Microsoft.Compute/images/delete | Usuwa obraz |
> | Działanie | Microsoft.Compute/images/read | Pobierz właściwości obrazu |
> | Działanie | Microsoft.Compute/images/write | Tworzy nowy obraz lub aktualizuje istniejący |
> | Działanie | Microsoft.Compute/locations/capsOperations/read | Pobiera stan operacji asynchronicznej Cap |
> | Działanie | Microsoft.Compute/locations/diskOperations/read | Pobiera stan operacji na dysku asynchronicznym |
> | Działanie | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Utwórz dzienniki, aby wyświetlić łączną liczbę żądań według przedziałów czasu, aby ułatwić diagnostykę. |
> | Działanie | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Utwórz dzienniki, aby wyświetlić zagregowane żądania ograniczane pogrupowane według resourceName, OperationName lub stosowanych zasad ograniczania. |
> | Działanie | Microsoft.Compute/locations/operations/read | Pobiera stan operacji asynchronicznej |
> | Działanie | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Pobierz właściwości oferty obrazu platformy |
> | Działanie | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Pobierz właściwości jednostki SKU obrazu platformy |
> | Działanie | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Pobierz właściwości wersji obrazu platformy |
> | Działanie | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Pobierz właściwości typu VMExtension |
> | Działanie | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Pobierz właściwości wersji VMExtension |
> | Działanie | Microsoft.Compute/locations/publishers/read | Pobierz właściwości wydawcy |
> | Działanie | Microsoft.Compute/locations/runCommands/read | Wyświetla dostępne polecenia uruchamiania w lokalizacji |
> | Działanie | Microsoft.Compute/locations/usages/read | Pobiera limity usługi i bieżące ilości użycia dla zasobów obliczeniowych subskrypcji w lokalizacji |
> | Działanie | Microsoft.Compute/locations/vmSizes/read | Wyświetla dostępne rozmiary maszyn wirtualnych w lokalizacji |
> | Działanie | Microsoft. COMPUTE/Locations/vsmOperations/Read | Pobiera stan operacji asynchronicznej zestawu skalowania maszyn wirtualnych z rozszerzeniem usługi środowiska uruchomieniowego maszyny wirtualnej |
> | Działanie | Microsoft. COMPUTE/Operations/Read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. COMPUTE |
> | Działanie | Microsoft. COMPUTE/proximityPlacementGroups/Delete | Usuwa grupę umieszczania zbliżeniowego |
> | Działanie | Microsoft. COMPUTE/proximityPlacementGroups/Read | Pobierz właściwości grupy położenia zbliżeniowe |
> | Działanie | Microsoft. COMPUTE/proximityPlacementGroups/Write | Tworzy nową grupę umieszczania w sąsiedztwie lub aktualizuje istniejącą |
> | Działanie | Microsoft. COMPUTE/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. COMPUTE |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/Delete | Usuwa kolekcję punktów przywracania i zawiera punkty przywracania |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/Read | Pobierz właściwości kolekcji punktów przywracania |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/restorePoints/Delete | Usuwa punkt przywracania |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/restorePoints/odczyt | Pobierz właściwości punktu przywracania |
> | Działanie | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Pobierz właściwości punktu przywracania wraz z identyfikatorami URI SAS obiektów BLOB |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/restorePoints/Write | Tworzy nowy punkt przywracania |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/Write | Tworzy nową kolekcję punktów przywracania lub aktualizuje istniejącą. |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/Delete | Usuwa element sharedvmimage |
> | Działanie | Microsoft.Compute/sharedVMImages/read | Pobierz właściwości elementu element sharedvmimage |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/wersje/usuwanie | Usuń element sharedvmimageversion |
> | Działanie | Microsoft.Compute/sharedVMImages/versions/read | Pobierz właściwości elementu element sharedvmimageversion |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/Versions/Replikacja/Akcja | Replikowanie element sharedvmimageversion do regionów docelowych |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/wersje/zapis | Utwórz nowy element sharedvmimageversion lub zaktualizuj istniejący |
> | Działanie | Microsoft.Compute/sharedVMImages/write | Tworzy nowy element sharedvmimage lub aktualizuje istniejący |
> | Działanie | Microsoft.Compute/skus/read | Pobiera listę jednostek SKU Microsoft. COMPUTE dostępnych dla Twojej subskrypcji |
> | Działanie | Microsoft. COMPUTE/snapshots/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dla migawki do obiektu BLOB |
> | Działanie | Microsoft. COMPUTE/snapshots/Delete | Usuń migawkę |
> | Działanie | Microsoft.Compute/snapshots/endGetAccess/action | Odwołaj identyfikator URI sygnatury dostępu współdzielonego dla migawki |
> | Działanie | Microsoft.Compute/snapshots/read | Pobierz właściwości migawki |
> | Działanie | Microsoft.Compute/snapshots/write | Utwórz nową migawkę lub zaktualizuj istniejącą |
> | Działanie | Microsoft. COMPUTE/Unregister/Action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. COMPUTE |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Capture/Action | Przechwytuje maszynę wirtualną przez kopiowanie wirtualnych dysków twardych i generuje szablon, którego można użyć do tworzenia podobnych maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachines/convertToManagedDisks/akcja | Konwertuje dyski oparte na obiektach Blob maszyny wirtualnej na dyski zarządzane |
> | Działanie | Microsoft.Compute/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Działanie | Microsoft.Compute/virtualMachines/delete | Usuwa maszynę wirtualną |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Extensions/Delete | Usuwa rozszerzenie maszyny wirtualnej |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Extensions/Read | Pobierz właściwości rozszerzenia maszyny wirtualnej |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Extensions/Write | Tworzy nowe rozszerzenie maszyny wirtualnej lub aktualizuje istniejące |
> | Działanie | Microsoft. COMPUTE/virtualMachines/generalize/akcja | Ustawia stan maszyny wirtualnej na uogólnione i przygotowuje maszynę wirtualną do przechwycenia |
> | Działanie | Microsoft.Compute/virtualMachines/instanceView/read | Pobiera szczegółowy stan środowiska uruchomieniowego maszyny wirtualnej i jej zasobów |
> | Akcja dataaction | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | Akcja dataaction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logowanie się do maszyny wirtualnej przy użyciu uprawnień administratora systemu Windows lub użytkownika root w systemie Linux |
> | Działanie | Microsoft. COMPUTE/virtualMachines/performMaintenance/akcja | Wykonuje operację konserwacji na maszynie wirtualnej. |
> | Działanie | Microsoft. COMPUTE/virtualMachines/wyłączenie/akcja | Wyłącza maszynę wirtualną. Pamiętaj, że opłaty za maszynę wirtualną będą kontynuowane. |
> | Działanie | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | Działanie | Microsoft. COMPUTE/virtualMachines/redeploy/Action | Ponownie wdraża maszynę wirtualną |
> | Działanie | Microsoft.Compute/virtualMachines/reimage/action | Odtwarza z obrazu maszynę wirtualną, która korzysta z dysku różnicowego. |
> | Działanie | Microsoft.Compute/virtualMachines/restart/action | Uruchamia ponownie maszynę wirtualną |
> | Działanie | Microsoft. COMPUTE/virtualMachines/runCommand/akcja | Wykonuje wstępnie zdefiniowany skrypt na maszynie wirtualnej |
> | Działanie | Microsoft.Compute/virtualMachines/start/action | Uruchamia maszynę wirtualną |
> | Działanie | Microsoft.Compute/virtualMachines/vmSizes/read | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
> | Działanie | Microsoft.Compute/virtualMachines/write | Tworzy nową maszynę wirtualną lub aktualizuje istniejącą maszynę wirtualną |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/deallocate/Action | Wyłącza i zwalnia zasoby obliczeniowe dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Delete | Usuwa zestaw skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Delete/Action | Usuwa wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Delete | Usuwa rozszerzenie zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Read | Pobiera właściwości rozszerzenia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write | Tworzy nowe rozszerzenie zestawu skalowania maszyn wirtualnych lub aktualizuje istniejące |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/akcja | Ręcznie przechodzenie do domeny aktualizacji platformy zestawu skalowania maszyn wirtualnych usługi Service Fabric w celu zakończenia oczekującej aktualizacji, która jest zablokowana |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Pobiera widok wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/manualUpgrade/akcja | Ręcznie aktualizuje wystąpienia do najnowszego modelu zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Pobierz właściwości wszystkich interfejsów sieciowych zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/osRollingUpgrade/akcja | Uruchamia uaktualnienie stopniowe, aby przenieść wszystkie wystąpienia zestawu skalowania maszyn wirtualnych do najnowszej dostępnej wersji systemu operacyjnego obrazu platformy. |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Pobiera historię uaktualnień systemu operacyjnego dla zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/performMaintenance/akcja | Przeprowadza planowaną konserwację na wystąpieniach zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Wyłącza wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Pobierz właściwości wszystkich publicznych adresów IP zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/read | Pobierz właściwości zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Wdróż ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Odtwarza z obrazu wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/restart/akcja | Uruchamia ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/rollingUpgrades/Cancel/Action | Anuluje stopniowe uaktualnianie zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Pobierz najnowszy stan stopniowego uaktualniania dla zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Skala/akcja | Sprawdź, czy istniejący zestaw skalowania maszyn wirtualnych może skalować w poziomie/skalować do określonej liczby wystąpień |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/skus/read | Wyświetla prawidłowe jednostki SKU dla istniejącego zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Start/akcja | Uruchamia wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/unallocate/Action | Wyłącza i zwalnia zasoby obliczeniowe dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Delete | Usuń konkretną maszynę wirtualną w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Pobiera widok wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Pobierz właściwości publicznego adresu IP utworzonego przy użyciu zestawu skalowania maszyn wirtualnych. Zestaw skalowania maszyn wirtualnych może utworzyć maksymalnie jeden publiczny adres IP na element ipconfiguration (prywatny adres IP) |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Pobierz właściwości jednej lub wszystkich konfiguracji protokołu IP interfejsu sieciowego utworzonego przy użyciu zestawu skalowania maszyn wirtualnych. Konfiguracje adresów IP reprezentują prywatne adresy IP |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Pobierz właściwości jednego lub wszystkich interfejsów sieciowych maszyny wirtualnej utworzonej przy użyciu zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/performMaintenance/Action | Przeprowadza planowaną konserwację na wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/wyłączenie/Action | Wyłącza wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Pobiera właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Ponownie wdraża wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Odtwarza z obrazu wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) dla wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/restart/akcja | Uruchamia ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/UruchomPolecenie/Action | Wykonuje wstępnie zdefiniowany skrypt w wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Start/akcja | Uruchamia wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Write | Aktualizuje właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Wyświetl dostępne rozmiary dla tworzenia lub aktualizowania maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Write | Tworzy nowy zestaw skalowania maszyn wirtualnych lub aktualizuje istniejący |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. zużycie/aggregatedcost/odczyt | Utwórz listę AggregatedCost dla grupy zarządzania. |
> | Działanie | Microsoft.Consumption/balances/read | Wyświetl podsumowanie użycia dla okresu rozliczeniowego dla grupy zarządzania. |
> | Działanie | Microsoft. zużycie/budżety/usuwanie | Usuń budżety przez subskrypcję lub grupę zarządzania. |
> | Działanie | Microsoft.Consumption/budgets/read | Wystaw budżety według subskrypcji lub grupy zarządzania. |
> | Działanie | Microsoft. zużycie/budżety/zapis | Tworzy i aktualizuje budżety za pomocą subskrypcji lub grupy zarządzania. |
> | Działanie | Microsoft. zużycie/opłaty/odczyt | Wystaw opłaty |
> | Działanie | Microsoft.Consumption/credits/read | Wystaw kredyty |
> | Działanie | Microsoft. użycie/zdarzenia/odczyt | Wyświetl listę zdarzeń |
> | Działanie | Microsoft. zużycie/externalBillingAccounts/Tagi/odczyt | Lista tagów dla umów EA i Subscriptions. |
> | Działanie | Microsoft. zużycie/externalSubscriptions/Tagi/odczyt | Lista tagów dla umów EA i Subscriptions. |
> | Działanie | Microsoft.Consumption/forecasts/read | Prognozowanie list |
> | Działanie | Microsoft.Consumption/lots/read | Lista partii |
> | Działanie | Microsoft.Consumption/marketplaces/read | Wyświetl listę szczegółów użycia zasobów portalu Marketplace dla zakresu subskrypcji EA i WebDirect. |
> | Działanie | Microsoft.Consumption/operationresults/read | Operationresults listy |
> | Działanie | Microsoft.Consumption/operations/read | Wyświetl listę wszystkich obsługiwanych operacji przez dostawcę zasobów Microsoft. zużycie. |
> | Działanie | Microsoft.Consumption/operationstatus/read | Operationstatus listy |
> | Działanie | Microsoft.Consumption/pricesheets/read | Wyświetl listę danych Pricesheets dla subskrypcji lub grupy zarządzania. |
> | Działanie | Microsoft. użycie/rejestrowanie/akcja | Zarejestruj się w celu użycia RP |
> | Działanie | Microsoft.Consumption/reservationDetails/read | Wyświetl listę szczegółów użycia dla wystąpień zarezerwowanych według kolejności rezerwacji lub grup zarządzania. Dane szczegółowe są na poziomie każdego wystąpienia dziennie. |
> | Działanie | Microsoft.Consumption/reservationRecommendations/read | Wyświetl listę pojedynczych lub udostępnionych zaleceń dla wystąpień zarezerwowanych dla subskrypcji. |
> | Działanie | Microsoft.Consumption/reservationSummaries/read | Wyświetl listę podsumowań użycia dla wystąpień zarezerwowanych według kolejności rezerwacji lub grup zarządzania. Dane podsumowujące są na poziomie co miesiąc lub codziennie. |
> | Działanie | Microsoft.Consumption/reservationTransactions/read | Wyświetl listę historii transakcji dla wystąpień zarezerwowanych według grup zarządzania. |
> | Działanie | Microsoft.Consumption/tags/read | Lista tagów dla umów EA i Subscriptions. |
> | Działanie | Microsoft. użycie/dzierżawy/odczyt | Wyświetlanie listy dzierżawców |
> | Działanie | Microsoft. użycie/dzierżawcy/rejestr/akcja | Zarejestruj akcję dla zakresu Microsoft. zużycie przez dzierżawcę. |
> | Działanie | Microsoft.Consumption/terms/read | Wyświetl listę warunków subskrypcji lub grupy zarządzania. |
> | Działanie | Microsoft.Consumption/usageDetails/read | Wyświetl listę szczegółów użycia zakresu dla subskrypcji EA i WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Containers/buildlogs/Read | Pobierz dzienniki kompilacji dla określonego kontenera. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec do określonego kontenera. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Pobierz dzienniki dla określonego kontenera. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/delete | Usuń określoną grupę kontenerów. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Pobierz wynik operacji asynchronicznej |
> | Działanie | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla grupy kontenerów. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla grupy kontenerów. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla grupy kontenerów. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/read | Pobierz wszystkie grupy kontenerów. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/restart/action | Uruchamia ponownie określoną grupę kontenerów. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/start/action | Uruchamia określoną grupę kontenerów. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/stop/action | Kończy określoną grupę kontenerów. Zasoby obliczeniowe zostaną cofnięte i rozliczenia zostaną zatrzymane. |
> | Działanie | Microsoft.ContainerInstance/containerGroups/write | Utwórz lub zaktualizuj określoną grupę kontenerów. |
> | Działanie | Microsoft.ContainerInstance/locations/cachedImages/read | Pobiera buforowane obrazy dla subskrypcji w regionie. |
> | Działanie | Microsoft.ContainerInstance/locations/capabilities/read | Uzyskaj możliwości dla regionu. |
> | Działanie | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft. ContainerInstance, że sieć wirtualna lub podsieć jest usuwana. |
> | Działanie | Microsoft. ContainerInstance/Locations/Operations/Read | Wyświetl listę operacji usługi wystąpienia kontenera platformy Azure. |
> | Działanie | Microsoft.ContainerInstance/locations/usages/read | Pobierz użycie dla określonego regionu. |
> | Działanie | Microsoft. ContainerInstance/Operations/Read | Wyświetl listę operacji usługi wystąpienia kontenera platformy Azure. |
> | Działanie | Microsoft.ContainerInstance/register/action | Rejestruje subskrypcję dostawcy zasobów wystąpienia kontenera i umożliwia tworzenie grup kontenerów. |
> | Działanie | Microsoft. ContainerInstance/serviceassociationlinks/Delete | Usuń link skojarzenia usługi utworzony przez dostawcę zasobów wystąpienia kontenera platformy Azure w podsieci. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.ContainerRegistry/checkNameAvailability/read | Sprawdza, czy nazwa rejestru kontenerów jest dostępna do użycia. |
> | Działanie | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft. ContainerRegistry, że sieć wirtualna lub podsieć jest usuwana |
> | Działanie | Microsoft.ContainerRegistry/locations/operationResults/read | Pobiera wynik operacji asynchronicznej |
> | Działanie | Microsoft.ContainerRegistry/operations/read | Wyświetla wszystkie dostępne Azure Container Registry operacje interfejsu API REST |
> | Działanie | Microsoft.ContainerRegistry/register/action | Rejestruje subskrypcję dostawcy zasobów rejestru kontenerów i umożliwia tworzenie rejestrów kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/artefakty/usuwanie | Usuń artefakt w rejestrze kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/kompilacje/akcja | Anuluje istniejącą kompilację. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/kompilacje/getLogLink/akcja | Pobiera link umożliwiający pobranie dzienników kompilacji. |
> | Działanie | Microsoft.ContainerRegistry/registries/builds/read | Pobiera właściwości określonej kompilacji lub wyświetla wszystkie kompilacje dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/kompilacje/zapisywanie | Aktualizuje kompilację dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft.ContainerRegistry/registries/buildTasks/delete | Usuwa zadanie kompilacji z rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Wyświetla listę właściwości kontroli źródła dla zadania kompilacji. |
> | Działanie | Microsoft.ContainerRegistry/registries/buildTasks/read | Pobiera właściwości określonego zadania kompilacji lub wyświetla wszystkie zadania kompilacji dla określonego rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Usuwa krok kompilacji z zadania kompilacji. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/buildTasks/etapy/listBuildArguments/akcja | Wyświetla listę argumentów kompilacji dla kroku kompilacji, w tym argumentów tajnych. |
> | Działanie | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Pobiera właściwości określonego kroku kompilacji lub wyświetla listę wszystkich kroków kompilacji dla określonego zadania kompilacji. |
> | Działanie | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Tworzy lub aktualizuje krok kompilacji dla zadania kompilacji o określonych parametrach. |
> | Działanie | Microsoft.ContainerRegistry/registries/buildTasks/write | Tworzy lub aktualizuje zadanie kompilacji dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/usuwanie | Usuwa rejestr kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Usuwa filtr usługi Event Grid z rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Pobiera właściwości określonego filtru usługi Event Grid lub wyświetla listę wszystkich filtrów usługi Event Grid dla określonego rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Tworzy lub aktualizuje filtr usługi Event Grid dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/generateCredentials/akcja | Generuj klucze dla tokenu określonego rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Pobiera lokalizację przekazywania dla użytkownika, aby mógł przekazać źródło. |
> | Działanie | Microsoft.ContainerRegistry/registries/importImage/action | Zaimportuj obraz do rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Pobierz lokalizację adresu URL przekazywania źródła dla rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/listCredentials/action | Wyświetla listę poświadczeń logowania dla określonego rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/listPolicies/read | Wyświetla listę zasad dla określonego rejestru kontenerów |
> | Działanie | Microsoft.ContainerRegistry/registries/listUsages/read | Wyświetla listę użycia przydziału dla określonego rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/metadata/read | Pobiera metadane określonego repozytorium dla rejestru kontenerów |
> | Działanie | Microsoft. ContainerRegistry/kancelarie/metadane/zapis | Aktualizuje metadane repozytorium dla rejestru kontenerów |
> | Działanie | Microsoft.ContainerRegistry/registries/operationStatuses/read | Pobiera stan operacji asynchronicznej rejestru |
> | Działanie | Microsoft.ContainerRegistry/registries/pull/read | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/push/write | Wypchnij lub Zapisz obrazy do rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/kancelarie/Kwarantanna/odczyt | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
> | Działanie | Microsoft. ContainerRegistry/kancelarie/Kwarantanna/zapis | Zapisywanie/modyfikowanie stanu kwarantanny obrazów z kwarantanny |
> | Działanie | Microsoft.ContainerRegistry/registries/queueBuild/action | Tworzy nową kompilację opartą na parametrach żądania i Dodaj ją do kolejki kompilacji. |
> | Działanie | Microsoft.ContainerRegistry/registries/read | Pobiera właściwości określonego rejestru kontenerów lub wyświetla listę wszystkich rejestrów kontenerów w ramach określonej grupy zasobów lub subskrypcji. |
> | Działanie | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Generuje ponownie jedno z poświadczeń logowania dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/replikacje/usuwanie | Usuwa replikację z rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Pobiera stan operacji asynchronicznej replikacji |
> | Działanie | Microsoft.ContainerRegistry/registries/replications/read | Pobiera właściwości określonej replikacji lub wyświetla wszystkie replikacje dla określonego rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/replications/write | Tworzy lub aktualizuje replikację dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft.ContainerRegistry/registries/runs/cancel/action | Anuluj istniejące uruchomienie. |
> | Działanie | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Pobiera adres URL sygnatury dostępu współdzielonego dziennika dla uruchomienia. |
> | Działanie | Microsoft.ContainerRegistry/registries/runs/read | Pobiera właściwości przebiegu dotyczącego przebiegu rejestru lub listy kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/runs/write | Aktualizuje przebieg. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/scheduleRun/akcja | Zaplanuj przebieg dla rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/scopeMaps/usuwanie | Usuwa mapę zakresu z rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/scopeMaps/operationStatuses/odczyt | Pobiera stan operacji asynchronicznej mapowania zakresu. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/scopeMaps/odczyt | Pobiera właściwości określonej mapy zakresu lub wyświetla wszystkie mapy zakresu dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/scopeMaps/zapis | Tworzy lub aktualizuje mapę zakresu dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/rejestrowanie/zapisywanie | Wypychanie/Ściąganie metadanych zaufania zawartości dla rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/zadania/usuwanie | Usuwa zadanie dla rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Wyświetl listę wszystkich szczegółów zadania dla rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/tasks/read | Pobiera zadanie dla rejestru kontenerów lub wyświetla listę wszystkich zadań. |
> | Działanie | Microsoft.ContainerRegistry/registries/tasks/write | Tworzy lub aktualizuje zadanie dla rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/tokeny/usuwanie | Usuwa token z rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/tokeny/operationStatuses/odczyt | Pobiera stan operacji asynchronicznej tokenu. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/tokeny/odczyt | Pobiera właściwości określonego tokenu lub wyświetla wszystkie tokeny dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/tokeny/zapis | Tworzy lub aktualizuje token dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft.ContainerRegistry/registries/updatePolicies/write | Aktualizuje zasady dla określonego rejestru kontenerów |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/usuwanie | Usuwa element webhook z rejestru kontenerów. |
> | Działanie | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Pobiera konfigurację identyfikatora URI usługi i nagłówków niestandardowych dla elementu webhook. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/listEvents/akcja | Wyświetla listę ostatnich zdarzeń dla określonego elementu webhook. |
> | Działanie | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Pobiera stan operacji asynchronicznej elementu webhook |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/polecenie ping/akcja | Wyzwala zdarzenie ping do wysłania do elementu webhook. |
> | Działanie | Microsoft.ContainerRegistry/registries/webhooks/read | Pobiera właściwości określonego elementu webhook lub wyświetla listę wszystkich elementów webhook dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/zapis | Tworzy lub aktualizuje element webhook dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft.ContainerRegistry/registries/write | Tworzy lub aktualizuje rejestr kontenerów o określonych parametrach. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.ContainerService/containerServices/delete | Usuwa usługę kontenera |
> | Działanie | Microsoft.ContainerService/containerServices/read | Pobierz usługę kontenera |
> | Działanie | Microsoft.ContainerService/containerServices/write | Tworzy nową usługę kontenera lub aktualizuje istniejącą |
> | Działanie | Microsoft.ContainerService/locations/operationresults/read | Pobiera stan wyniku operacji asynchronicznej |
> | Działanie | Microsoft.ContainerService/locations/operations/read | Pobiera stan operacji asynchronicznej |
> | Działanie | Microsoft.ContainerService/locations/orchestrators/read | Wyświetla listę obsługiwanych programów Orchestrator |
> | Działanie | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Uzyskiwanie zarządzanego profilu dostępu do klastra według nazwy roli przy użyciu poświadczeń listy |
> | Działanie | Microsoft.ContainerService/managedClusters/accessProfiles/read | Uzyskaj zarządzany Profil dostępu do klastra według nazwy roli |
> | Działanie | Microsoft.ContainerService/managedClusters/agentPools/delete | Usuwa pulę agentów |
> | Działanie | Microsoft.ContainerService/managedClusters/agentPools/read | Pobiera pulę agentów |
> | Działanie | Microsoft. ContainerService/managedClusters/agentPools/upgradeProfiles/Read | Pobiera profil uaktualnienia puli agentów |
> | Działanie | Microsoft.ContainerService/managedClusters/agentPools/write | Tworzy nową pulę agentów lub aktualizuje istniejącą. |
> | Działanie | Microsoft. ContainerService/managedClusters/availableAgentPoolVersions/Read | Pobiera dostępne wersje puli agentów klastra |
> | Działanie | Microsoft.ContainerService/managedClusters/delete | Usuwa zarządzany klaster |
> | Działanie | Microsoft. ContainerService/managedClusters/detektory/odczyt | Pobierz detektor zarządzanego klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/diagnosticsState/Read | Pobiera stan diagnostyki klastra |
> | Działanie | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Wyświetlanie clusterAdmin poświadczenia zarządzanego klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/listClusterMonitoringUserCredential/akcja | Wyświetlanie clusterMonitoringUser poświadczenia zarządzanego klastra |
> | Działanie | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Wyświetlanie clusterUser poświadczenia zarządzanego klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft.ContainerService/managedClusters/read | Pobierz zarządzany klaster |
> | Działanie | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Resetowanie profilu usługi AAD w zarządzanym klastrze |
> | Działanie | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Resetowanie profilu jednostki usługi zarządzanego klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/rotateClusterCertificates/akcja | Obracanie certyfikatów zarządzanego klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/upgradeProfiles/Read | Pobiera profil uaktualnienia klastra |
> | Działanie | Microsoft.ContainerService/managedClusters/write | Tworzy nowy zarządzany klaster lub aktualizuje istniejący. |
> | Działanie | Microsoft.ContainerService/openShiftClusters/delete | Usuń otwarty klaster przesunięcia |
> | Działanie | Microsoft.ContainerService/openShiftClusters/read | Pobierz otwarty klaster przesunięcia |
> | Działanie | Microsoft.ContainerService/openShiftClusters/write | Tworzy nowy otwarty klaster przesunięcia lub aktualizuje istniejący |
> | Działanie | Microsoft.ContainerService/openShiftManagedClusters/delete | Usuń otwarty klaster z zarządzaną przesunięciami |
> | Działanie | Microsoft.ContainerService/openShiftManagedClusters/read | Pobierz klaster zarządzany za otwartym przesunięciami |
> | Działanie | Microsoft.ContainerService/openShiftManagedClusters/write | Tworzy nowy klaster zarządzany przez przesunięcia lub aktualizuje istniejący |
> | Działanie | Microsoft.ContainerService/operations/read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. ContainerService |
> | Działanie | Microsoft.ContainerService/register/action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. ContainerService |
> | Działanie | Microsoft.ContainerService/unregister/action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. ContainerService |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. CostManagement/alerty/zapis | Aktualizowanie alertów. |
> | Działanie | Microsoft.CostManagement/cloudConnectors/delete | Usuń określony cloudConnector. |
> | Działanie | Microsoft.CostManagement/cloudConnectors/read | Wyświetl listę cloudConnectors dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft.CostManagement/cloudConnectors/write | Utwórz lub zaktualizuj określony cloudConnector. |
> | Działanie | Microsoft.CostManagement/dimensions/read | Wyświetl listę wszystkich obsługiwanych wymiarów według zakresu. |
> | Działanie | Microsoft. CostManagement/eksporty/akcja | Uruchom określony eksport. |
> | Działanie | Microsoft. CostManagement/exports/Delete | Usuń określony eksport. |
> | Działanie | Microsoft.CostManagement/exports/read | Wyświetl listę eksportów według zakresu. |
> | Działanie | Microsoft. CostManagement/exports/Run/Action | Uruchom eksporty. |
> | Działanie | Microsoft. CostManagement/exports/Write | Utwórz lub zaktualizuj określony eksport. |
> | Działanie | Microsoft. CostManagement/externalBillingAccounts/wymiar/odczyt | Wyświetl listę wszystkich obsługiwanych wymiarów dla zewnętrznego BillingAccounts. |
> | Działanie | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Wyświetl listę externalSubscriptions w externalBillingAccount dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft. CostManagement/externalBillingAccounts/Prognoza/akcja | Prognozowanie danych użycia dla zewnętrznego BillingAccounts. |
> | Działanie | Microsoft. CostManagement/externalBillingAccounts/Prognoza/odczyt | Prognozowanie danych użycia dla zewnętrznego BillingAccounts. |
> | Działanie | Microsoft. CostManagement/externalBillingAccounts/kwerenda/akcja | Wykonywanie zapytań dotyczących danych użycia dla zewnętrznego BillingAccounts. |
> | Działanie | Microsoft. CostManagement/externalBillingAccounts/Query/Read | Wykonywanie zapytań dotyczących danych użycia dla zewnętrznego BillingAccounts. |
> | Działanie | Microsoft. CostManagement/externalBillingAccounts/odczyt | Wyświetl listę externalBillingAccounts dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft. CostManagement/externalSubscriptions/Dimensions/Read | Wyświetl listę wszystkich obsługiwanych wymiarów dla zewnętrznej subskrypcji. |
> | Działanie | Microsoft. CostManagement/externalSubscriptions/Prognoza/akcja | Prognozowanie danych użycia dla zewnętrznego BillingAccounts. |
> | Działanie | Microsoft. CostManagement/externalSubscriptions/Prognoza/odczyt | Prognozowanie danych użycia dla zewnętrznego BillingAccounts. |
> | Działanie | Microsoft. CostManagement/externalSubscriptions/kwerenda/akcja | Wykonywanie zapytań dotyczących danych użycia dla zewnętrznej subskrypcji. |
> | Działanie | Microsoft. CostManagement/externalSubscriptions/Query/Read | Wykonywanie zapytań dotyczących danych użycia dla zewnętrznej subskrypcji. |
> | Działanie | Microsoft.CostManagement/externalSubscriptions/read | Wyświetl listę externalSubscriptions dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft.CostManagement/externalSubscriptions/write | Aktualizuj skojarzoną grupę zarządzania externalSubscription |
> | Działanie | Microsoft. CostManagement/Prognoza/akcja | Prognozowanie danych użycia według zakresu. |
> | Działanie | Microsoft. CostManagement/Prognoza/odczyt | Prognozowanie danych użycia według zakresu. |
> | Działanie | Microsoft. CostManagement/Operations/Read | Wyświetl listę wszystkich obsługiwanych operacji przez dostawcę zasobów Microsoft. CostManagement. |
> | Działanie | Microsoft. CostManagement/kwerenda/akcja | Wykonywanie zapytań o dane użycia według zakresu. |
> | Działanie | Microsoft.CostManagement/query/read | Wykonywanie zapytań o dane użycia według zakresu. |
> | Działanie | Microsoft. CostManagement/Register/Action | Zarejestruj akcję dla zakresu Microsoft. CostManagement przez subskrypcję. |
> | Działanie | Microsoft. CostManagement/raporty/akcja | Zaplanuj raporty dotyczące danych użycia według zakresu. |
> | Działanie | Microsoft.CostManagement/reports/read | Zaplanuj raporty dotyczące danych użycia według zakresu. |
> | Działanie | Microsoft. CostManagement/dzierżawcy/rejestr/akcja | Zarejestruj akcję dla zakresu Microsoft. CostManagement przez dzierżawcę. |
> | Działanie | Microsoft. CostManagement/widoki/akcja | Utwórz widok. |
> | Działanie | Microsoft. CostManagement/widoki/usuwanie | Usuń zapisane widoki. |
> | Działanie | Microsoft. CostManagement/widoki/odczyt | Wyświetl listę wszystkich zapisanych widoków. |
> | Działanie | Microsoft. CostManagement/widoki/zapis | Aktualizuj widok. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DataBox/Jobs/bookShipmentPickUp/akcja | Umożliwia zaksięgowanie pobrania dla wysyłek zwrotnych. |
> | Działanie | Microsoft. DataBox/Jobs/Cancel/Action | Anuluje zamówienie w toku. |
> | Działanie | Microsoft. DataBox/Jobs/Delete | Usuwanie zamówień |
> | Działanie | Microsoft.DataBox/jobs/listCredentials/action | Wyświetla listę nieszyfrowanych poświadczeń związanych z kolejnością. |
> | Działanie | Microsoft.DataBox/jobs/read | Wyświetl lub Pobierz zamówienia |
> | Działanie | Microsoft. DataBox/Jobs/Write | Utwórz lub zaktualizuj zamówienia |
> | Działanie | Microsoft. DataBox/Locations/availableSkus/Action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | Działanie | Microsoft. DataBox/Locations/availableSkus/Read | Wyświetl lub uzyskaj dostępne jednostki SKU |
> | Działanie | Microsoft.DataBox/locations/operationResults/read | Wyświetl lub Pobierz wyniki operacji |
> | Działanie | Microsoft. DataBox/Locations/regionConfiguration/Action | Ta metoda zwraca konfiguracje dla regionu. |
> | Działanie | Microsoft.DataBox/locations/validateAddress/action | Sprawdza poprawność adresu wysyłkowego i udostępnia alternatywne adresy, jeśli istnieją. |
> | Działanie | Microsoft. DataBox/Locations/validateInputs/Action | Ta metoda wykonuje wszystkie typy walidacji. |
> | Działanie | Microsoft. DataBox/Operations/Read | Wyświetl lub Pobierz operacje |
> | Działanie | Microsoft. DataBox/Register/Action | Zarejestruj dostawcę Microsoft. DATAbox |
> | Działanie | Microsoft. DataBox/subscriptions/resourceGroups/moveResources/Action | Ta metoda służy do przenoszenia zasobu. |
> | Działanie | Microsoft. DataBox/subscriptions/resourceGroups/validateMoveResources/Action | Ta metoda sprawdza, czy przenoszenie zasobów jest dozwolone. |
> | Działanie | Microsoft. DataBox/Unregister/Action | Wyrejestruj dostawcę Microsoft. DATAbox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Wyświetla lub pobiera alerty |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Usuwa harmonogramy przepustowości |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Wyświetla lub pobiera harmonogramy przepustowości |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Wyświetla lub pobiera harmonogramy przepustowości |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Tworzy lub aktualizuje harmonogramy przepustowości |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Usuwa urządzenia Data Box Edge |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Pobierz aktualizacje w urządzeniu |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Pobiera rozszerzone informacje o zasobach |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Zainstaluj aktualizacje na urządzeniu |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Wyświetla lub pobiera zadania |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Wyświetla lub pobiera ustawienia sieci urządzenia |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/nodes/Read | Wyświetla lub pobiera węzły |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Wyświetla lub Pobiera stan operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Usuwa zamówienia |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Wyświetla lub pobiera zamówienia |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Wyświetla lub pobiera zamówienia |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Tworzy lub aktualizuje zamówienia |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Usuwa role |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Wyświetla lub pobiera role |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Wyświetla lub pobiera role |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Tworzy lub aktualizuje role |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Skanuj w poszukiwaniu aktualizacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Aktualizowanie ustawień zabezpieczeń |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Usuwa udziały |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Wyświetla lub pobiera udziały |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Wyświetla lub pobiera udziały |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Odświeżanie metadanych udziału przy użyciu danych z chmury |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Tworzy lub aktualizuje udziały |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Usuwa poświadczenia konta magazynu |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Tworzy lub aktualizuje poświadczenia konta magazynu |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Usuwa wyzwalacze |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Wyświetla lub pobiera wyzwalacze |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Wyświetla lub pobiera wyzwalacze |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Tworzy lub aktualizuje wyzwalacze |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Wyświetla lub pobiera podsumowanie aktualizacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Przekaż certyfikat do rejestracji urządzenia |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Usuwa użytkowników udziału |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Wyświetla lub pobiera użytkowników udziału |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Wyświetla lub pobiera użytkowników udziału |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Tworzy lub aktualizuje udział użytkowników |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Tworzy lub aktualizuje urządzenia Data Box Edge |
> | Działanie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Tworzy lub aktualizuje urządzenia Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Databricks/locations/getNetworkPolicies/action | Pobierz zasady dotyczące zastosowania sieci dla podsieci na podstawie lokalizacji używanej przez dostawcy NRP |
> | Działanie | Microsoft. datakosteks/Locations/operationstatuses/Read | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft. datakosteks/Operations/Read | Pobiera listę operacji. |
> | Działanie | Microsoft. datacegły/rejestr/akcja | Zarejestruj się w kostkach. |
> | Działanie | Microsoft.Databricks/workspaces/delete | Usuwa obszar roboczy datakosteks. |
> | Działanie | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Ustawia dostępne ustawienia diagnostyczne dla obszaru roboczego datakosteks |
> | Działanie | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne definicje dzienników dla obszaru roboczego datakosteks |
> | Działanie | Microsoft.Databricks/workspaces/read | Pobiera listę obszarów roboczych elementów datakosteks. |
> | Działanie | Microsoft. datacegły/obszary robocze/refreshPermissions/akcja | Odświeżanie uprawnień dla obszaru roboczego |
> | Działanie | Microsoft. datacegły/obszary robocze/updateDenyAssignment/akcja | Aktualizuj przypisanie Odmów nie akcje dla zarządzanej grupy zasobów obszaru roboczego |
> | Działanie | Microsoft. datacegły/obszary robocze/virtualNetworkPeerings/Usuń | Usuwa komunikację równorzędną sieci wirtualnej |
> | Działanie | Microsoft. datacegły/obszary robocze/virtualNetworkPeerings/odczyt | Pobiera komunikację równorzędną sieci wirtualnej. |
> | Działanie | Microsoft. datacegły/obszary robocze/virtualNetworkPeerings/Write | Dodawanie lub modyfikowanie komunikacji równorzędnej sieci wirtualnej |
> | Działanie | Microsoft.Databricks/workspaces/write | Tworzy obszar roboczy datakosteks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.DataCatalog/catalogs/delete | Usuń zasób wykazów dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft.DataCatalog/catalogs/read | Zasób wykazów odczytu dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft.DataCatalog/catalogs/write | Zasób katalogu zapisu dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft.DataCatalog/checkNameAvailability/read | Sprawdź dostępność nazwy wykazu dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft.DataCatalog/datacatalogs/delete | Usuń zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft.DataCatalog/datacatalogs/read | Odczytaj zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft.DataCatalog/datacatalogs/write | Zapisz zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft. datacatalog/Operations/Read | Odczytuje wszystkie dostępne operacje w Data Catalog dostawcy zasobów. |
> | Działanie | Microsoft. datacatalog/Register/Action | Zarejestruj subskrypcję dla dostawcy zasobów Data Catalog |
> | Działanie | Microsoft. datacatalog/Unregister/Action | Wyrejestrowywanie subskrypcji dla dostawcy zasobów Data Catalog |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Sprawdza, czy nazwa Data Factory jest dostępna do użycia. |
> | Działanie | Microsoft.DataFactory/datafactories/activitywindows/read | Odczytuje okna działania w Data Factory z określonymi parametrami. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Activities/activitywindows/Read | Odczytuje okna aktywności dla działania potoku z określonymi parametrami. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/activitywindows/Read | Odczytuje okna działań dla potoku z określonymi parametrami. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Delete | Usuwa każdy potok. |
> | Działanie | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Wstrzymuje wszystkie potoki. |
> | Działanie | Microsoft.DataFactory/datafactories/datapipelines/read | Odczytuje wszystkie potoki. |
> | Działanie | Microsoft. DataFactory/datafactors///Resume/akcja | Wznawia wszystkie potoki. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Update/Action | Aktualizuje każdy potok. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Write | Tworzy lub aktualizuje każdy potok. |
> | Działanie | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Odczytuje okna działań dla zestawu danych z określonymi parametrami. |
> | Działanie | Microsoft.DataFactory/datafactories/datasets/delete | Usuwa dowolny zestaw danych. |
> | Działanie | Microsoft.DataFactory/datafactories/datasets/read | Odczytuje dowolny zestaw danych. |
> | Działanie | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Odczytuje wycinek danych uruchomienia dla danego zestawu danych z danym czasem rozpoczęcia. |
> | Działanie | Microsoft.DataFactory/datafactories/datasets/slices/read | Pobiera wycinki danych w danym okresie. |
> | Działanie | Microsoft.DataFactory/datafactories/datasets/slices/write | Zaktualizuj stan wycinka danych. |
> | Działanie | Microsoft.DataFactory/datafactories/datasets/write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/datafactors/Delete | Usuwa Data Factory. |
> | Działanie | Microsoft. DataFactory/datafactors/Gateways/ConnectionInfo/Action | Odczytuje informacje o połączeniu dla każdej bramy. |
> | Działanie | Microsoft. DataFactory/datafactors/Gateways/Delete | Usuwa wszystkie bramy. |
> | Działanie | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Wyświetla listę kluczy uwierzytelniania dla każdej bramy. |
> | Działanie | Microsoft.DataFactory/datafactories/gateways/read | Odczytuje każdą bramę. |
> | Działanie | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Generuje ponownie klucze uwierzytelniania dla każdej bramy. |
> | Działanie | Microsoft. DataFactory/datafabrykas/Gateways/Write | Tworzy lub aktualizuje bramę. |
> | Działanie | Microsoft.DataFactory/datafactories/linkedServices/delete | Usuwa wszystkie połączone usługi. |
> | Działanie | Microsoft.DataFactory/datafactories/linkedServices/read | Odczytuje wszystkie połączone usługi. |
> | Działanie | Microsoft.DataFactory/datafactories/linkedServices/write | Tworzy lub aktualizuje każdą połączoną usługę. |
> | Działanie | Microsoft.DataFactory/datafactories/read | Odczytuje Data Factory. |
> | Działanie | Microsoft.DataFactory/datafactories/runs/loginfo/read | Odczytuje identyfikator URI sygnatury dostępu współdzielonego do kontenera obiektów BLOB zawierającego dzienniki. |
> | Działanie | Microsoft. DataFactory/datafactors/Tables/Delete | Usuwa dowolny zestaw danych. |
> | Działanie | Microsoft.DataFactory/datafactories/tables/read | Odczytuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/datafactors/Tables/Write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/datafactors/Write | Tworzy lub aktualizuje Data Factory. |
> | Działanie | Microsoft. DataFactory/Factors/addDataFlowToDebugSession/Action | Dodaj przepływ danych do sesji debugowania na potrzeby wersji zapoznawczej. |
> | Działanie | Microsoft. DataFactory/Factors/cancelpipelinerun/Action | Anuluje uruchomienie potoku określone przez identyfikator uruchomienia. |
> | Działanie | Microsoft. DataFactory/Factors/cancelSandboxPipelineRun/Action | Anuluje uruchomienie debugowania dla potoku. |
> | Działanie | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Tworzy sesję debugowania przepływu danych. |
> | Działanie | Microsoft.DataFactory/factories/dataflows/delete | Usuwa przepływ danych. |
> | Działanie | Microsoft.DataFactory/factories/dataflows/read | Odczytuje przepływ danych. |
> | Działanie | Microsoft.DataFactory/factories/dataflows/write | Utwórz lub zaktualizuj przepływ danych |
> | Działanie | Microsoft.DataFactory/factories/datasets/delete | Usuwa dowolny zestaw danych. |
> | Działanie | Microsoft.DataFactory/factories/datasets/read | Odczytuje dowolny zestaw danych. |
> | Działanie | Microsoft.DataFactory/factories/datasets/write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/Factors/debugpipelineruns/Cancel/Action | Anuluje uruchomienie debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/Factors/Delete | Usuwa Data Factory. |
> | Działanie | Microsoft. DataFactory/Factors/deletedataflowdebugsession/Action | Usuwa sesję debugowania przepływu danych. |
> | Działanie | Microsoft. DataFactory/Factors/executeDataFlowDebugCommand/Action | Polecenie wykonania debugowania przepływu danych. |
> | Działanie | Microsoft. DataFactory/Factors/getDataPlaneAccess/Action | Uzyskuje dostęp do usługi dataplaning ADF. |
> | Działanie | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Odczytuje dostęp do usługi dataplaning ADF. |
> | Działanie | Microsoft. DataFactory/Factors/getFeatureValue/Action | Wartość funkcji kontroli ekspozycji dla określonej lokalizacji. |
> | Działanie | Microsoft. DataFactory/Factors/getFeatureValue/Read | Odczytuje wartość funkcji kontroli zagrożeń dla określonej lokalizacji. |
> | Działanie | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Pobiera token dostępu usługi GitHub. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Delete | Usuwa wszystkie Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/GetConnectionInfo/Read | Odczytuje Integration Runtime informacji o połączeniu. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/getObjectMetadata/Action | Pobierz metadane usług SSIS Integration Runtime dla określonego Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/GetStatus/Read | Odczytuje Integration Runtime stanu. |
> | Działanie | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Utwórz połączone odwołanie Integration Runtime w określonym Integration Runtime udostępnionym. |
> | Działanie | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Wyświetla listę kluczy uwierzytelniania dla każdej Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/monitoringdata/Read | Pobiera dane monitorowania dla dowolnych Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Delete | Usuwa węzeł dla podanej Integration Runtime. |
> | Działanie | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Zwraca adres IP dla określonego węzła Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Read | Odczytuje węzeł dla określonego Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Write | Aktualizuje węzeł Integration Runtime samoobsługowego. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Read | Odczytuje wszystkie Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/refreshObjectMetadata/Action | Odśwież Integration Runtime metadanych dla określonego Integration Runtime. |
> | Działanie | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Generuje ponownie klucze uwierzytelniania dla podanej Integration Runtime. |
> | Działanie | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Usuwa połączone Integration Runtime odwołania z określonego Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Start/Action | Uruchamia dowolne Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Stop/Action | Powoduje zatrzymanie wszelkich Integration Runtime. |
> | Działanie | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synchronizuje poświadczenia dla określonego Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/upgrade/Action | Uaktualnia określony Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Write | Tworzy lub aktualizuje wszystkie Integration Runtime. |
> | Działanie | Microsoft.DataFactory/factories/linkedServices/delete | Usuwa połączoną usługę. |
> | Działanie | Microsoft.DataFactory/factories/linkedServices/read | Odczytuje połączoną usługę. |
> | Działanie | Microsoft.DataFactory/factories/linkedServices/write | Utwórz lub zaktualizuj połączoną usługę |
> | Działanie | Microsoft. DataFactory/Factors/operationResults/Read | Pobiera wyniki operacji. |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/activityruns/Read | Odczytuje uruchomienia działania dla określonego identyfikatora uruchomienia potoku. |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/Cancel/Action | Anuluje uruchomienie potoku określone przez identyfikator uruchomienia. |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/queryactivityruns/Action | Wysyła zapytanie do przebiegów działania dla określonego identyfikatora uruchomienia potoku. |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/queryactivityruns/Read | Odczytuje wynik przebiegu działania zapytania dla określonego identyfikatora uruchomienia potoku. |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/Read | Odczytuje uruchomienia potoku. |
> | Działanie | Microsoft.DataFactory/factories/pipelines/createrun/action | Tworzy przebieg dla potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/usuwanie | Usuwa potok. |
> | Działanie | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Pobiera postęp uruchomienia działania. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/pipelineruns/odczyt | Odczytuje uruchomienie potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/odczyt | Odczytuje potok. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/piaskownica/akcja | Tworzy środowisko uruchamiania debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/piaskownica/Utwórz/akcję | Tworzy środowisko uruchamiania debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/piaskownica/Uruchom/akcja | Tworzy przebieg debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/zapis | Utwórz lub zaktualizuj potok |
> | Działanie | Microsoft. DataFactory/Factors/querydataflowdebugsessions/Action | Wysyła zapytanie do sesji debugowania przepływu danych. |
> | Działanie | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Wysyła zapytanie do przebiegów potoku debugowania. |
> | Działanie | Microsoft. DataFactory/Factors/querypipelineruns/Action | Wykonuje zapytanie o uruchomienia potoku. |
> | Działanie | Microsoft.DataFactory/factories/querypipelineruns/read | Odczytuje wynik uruchomienia potoku zapytania. |
> | Działanie | Microsoft. DataFactory/Factors/querytriggerruns/Action | Wysyła zapytanie do uruchomienia wyzwalacza. |
> | Działanie | Microsoft.DataFactory/factories/querytriggerruns/read | Odczytuje wynik uruchomienia wyzwalacza. |
> | Działanie | Microsoft.DataFactory/factories/read | Odczytuje Data Factory. |
> | Działanie | Microsoft. DataFactory/Factors/sandboxpipelineruns/Action | Wysyła zapytanie do przebiegów potoku debugowania. |
> | Działanie | Microsoft. DataFactory/Factors/sandboxpipelineruns/Read | Pobiera informacje o uruchamianiu debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/Factors/sandboxpipelineruns/sandboxActivityRuns/Read | Pobiera informacje o uruchamianiu debugowania dla działania. |
> | Działanie | Microsoft. DataFactory/Factors/startdataflowdebugsession/Action | Uruchamia sesję debugowania przepływu danych. |
> | Działanie | Microsoft.DataFactory/factories/triggerruns/read | Odczytuje uruchomienia wyzwalacza. |
> | Działanie | Microsoft. DataFactory/fabryki/wyzwalacze/usuwanie | Usuwa każdy wyzwalacz. |
> | Działanie | Microsoft. DataFactory/fabryki/wyzwalacze/geteventsubscriptionstatus/akcja | Stan subskrypcji zdarzenia. |
> | Działanie | Microsoft. DataFactory/fabryki/wyzwalacze/odczyt | Odczytuje dowolny wyzwalacz. |
> | Działanie | Microsoft. DataFactory/Factors/Triggers/Start/Action | Uruchamia dowolny wyzwalacz. |
> | Działanie | Microsoft. DataFactory/Factors/Triggers/akcja | Powoduje zatrzymanie dowolnego wyzwalacza. |
> | Działanie | Microsoft. DataFactory/fabryki/wyzwalacze/SubscribeToEvents/akcja | Subskrybuj zdarzenia. |
> | Działanie | Microsoft. DataFactory/Factors/Triggers/triggerruns/Read | Odczytuje uruchomienia wyzwalacza. |
> | Działanie | Microsoft. DataFactory/fabryki/wyzwalacze/UnsubscribeFromEvents/akcja | Anuluj subskrypcję zdarzeń. |
> | Działanie | Microsoft. DataFactory/fabryki/wyzwalacze/zapis | Tworzy lub aktualizuje dowolny wyzwalacz. |
> | Działanie | Microsoft. DataFactory/Factors/Write | Utwórz lub zaktualizuj Data Factory |
> | Działanie | Microsoft.DataFactory/locations/configureFactoryRepo/action | Konfiguruje repozytorium dla fabryki. |
> | Działanie | Microsoft. DataFactory/Locations/getFeatureValue/Action | Wartość funkcji kontroli ekspozycji dla określonej lokalizacji. |
> | Działanie | Microsoft.DataFactory/locations/getFeatureValue/read | Odczytuje wartość funkcji kontroli zagrożeń dla określonej lokalizacji. |
> | Działanie | Microsoft.DataFactory/operations/read | Odczytuje wszystkie operacje w programie Microsoft Data Factory Provider. |
> | Działanie | Microsoft. DataFactory/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów Data Factory. |
> | Działanie | Microsoft. DataFactory/Unregister/Action | Wyrejestrowuje subskrypcję dla dostawcy zasobów Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Usuń zasady obliczeń. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Uzyskaj informacje na temat zasad obliczeniowych. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Utwórz lub zaktualizuj zasady obliczeniowe. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Odłączanie konta kontach datalakestore z konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Uzyskaj informacje o połączonym koncie kontach datalakestore konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Utwórz lub zaktualizuj połączone konto kontach datalakestore konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/delete | Usuń konto DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Usuń regułę zapory. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Uzyskaj informacje na temat reguły zapory. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Utwórz lub zaktualizuj regułę zapory. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Pobierz wynik operacji konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/read | Pobierz informacje o istniejącym koncie DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Wyświetl listę tokenów sygnatury dostępu współdzielonego dla kontenerów magazynu połączonego konta magazynu konta usługi DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Pobierz kontenery połączonego konta magazynu dla konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Odłączanie konta magazynu od konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Uzyskaj informacje o połączonym koncie magazynu konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Utwórz lub zaktualizuj połączone konto magazynu dla konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Udziel uprawnień do anulowania zadań przesłanych przez innych użytkowników. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Przenoszenie SystemMaxAnalyticsUnits między kontami DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Usuń regułę sieci wirtualnej. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Uzyskaj informacje na temat reguły sieci wirtualnej. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Działanie | Microsoft.DataLakeAnalytics/accounts/write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/locations/capability/read | Uzyskaj informacje o możliwościach subskrypcji dotyczące korzystania z DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/locations/operationResults/read | Pobierz wynik operacji konta DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/locations/usages/read | Uzyskaj informacje dotyczące użycia przydziału dla subskrypcji dotyczącej korzystania z DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/operations/read | Pobierz dostępne operacje DataLakeAnalytics. |
> | Działanie | Microsoft.DataLakeAnalytics/register/action | Zarejestruj subskrypcję usługi DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. kontach datalakestore/accounts/Delete | Usuń konto kontach datalakestore. |
> | Działanie | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Włącz Magazyn kluczy dla konta kontach datalakestore. |
> | Działanie | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Usuń filtr EventGrid. |
> | Działanie | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Pobierz filtr EventGrid. |
> | Działanie | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Utwórz lub zaktualizuj filtr EventGrid. |
> | Działanie | Microsoft. kontach datalakestore/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | Działanie | Microsoft. kontach datalakestore/accounts/firewallRules/Read | Uzyskaj informacje na temat reguły zapory. |
> | Działanie | Microsoft. kontach datalakestore/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | Działanie | Microsoft.DataLakeStore/accounts/operationResults/read | Pobierz wynik operacji konta kontach datalakestore. |
> | Działanie | Microsoft.DataLakeStore/accounts/read | Pobierz informacje o istniejącym koncie kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/accounts/shares/Delete | Usuń udział. |
> | Działanie | Microsoft. kontach datalakestore/accounts/shares/Read | Pobierz informacje o udziale. |
> | Działanie | Microsoft. kontach datalakestore/accounts/shares/Write | Utwórz lub zaktualizuj udział. |
> | Działanie | Microsoft.DataLakeStore/accounts/Superuser/action | Przyznaj administratora na Data Lake Store po udzieleniu z firmą Microsoft. Authorization/roleAssignments/Write. |
> | Działanie | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Usuń zaufanego dostawcę tożsamości. |
> | Działanie | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Uzyskaj informacje na temat zaufanego dostawcy tożsamości. |
> | Działanie | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Utwórz lub zaktualizuj zaufanego dostawcę tożsamości. |
> | Działanie | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Usuń regułę sieci wirtualnej. |
> | Działanie | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Uzyskaj informacje na temat reguły sieci wirtualnej. |
> | Działanie | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Działanie | Microsoft. kontach datalakestore/accounts/Write | Utwórz lub zaktualizuj konto kontach datalakestore. |
> | Działanie | Microsoft.DataLakeStore/locations/capability/read | Uzyskaj informacje o możliwościach subskrypcji dotyczące korzystania z kontach datalakestore. |
> | Działanie | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta kontach datalakestore. |
> | Działanie | Microsoft.DataLakeStore/locations/operationResults/read | Pobierz wynik operacji konta kontach datalakestore. |
> | Działanie | Microsoft.DataLakeStore/locations/usages/read | Uzyskaj informacje dotyczące użycia przydziału dla subskrypcji dotyczącej korzystania z kontach datalakestore. |
> | Działanie | Microsoft.DataLakeStore/operations/read | Pobierz dostępne operacje kontach datalakestore. |
> | Działanie | Microsoft.DataLakeStore/register/action | Zarejestruj subskrypcję usługi kontach datalakestore. |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.DataMigration/locations/operationResults/read | Pobieranie stanu długotrwałej operacji związanej z zaakceptowaną odpowiedzią 202 |
> | Działanie | Microsoft.DataMigration/locations/operationStatuses/read | Pobieranie stanu długotrwałej operacji związanej z zaakceptowaną odpowiedzią 202 |
> | Działanie | Microsoft. datamigration/Register/Action | Rejestruje subskrypcję za pomocą dostawcy Azure Database Migration Service |
> | Działanie | Microsoft. datamigration/Services/addworker/Action | Dodaje proces roboczy DMS do dostępny procesów roboczych usługi |
> | Działanie | Microsoft. datamigration/Services/checkStatus/Action | Sprawdź, czy usługa jest wdrożona i uruchomiona |
> | Działanie | Microsoft. datamigration/Services/configureWorker/Action | Konfiguruje proces roboczy DMS dla procesów roboczych dostępny usługi |
> | Działanie | Microsoft. datamigration/Services/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. datamigration/Services/projects/accessArtifacts/Action | Generowanie adresu URL, którego można użyć do pobierania lub UMIESZCZAnia artefaktów projektu |
> | Działanie | Microsoft. datamigration/Services/projects/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. datamigration/Services/projects/Files/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. datamigration/Services/projects/Files/Read | Przeczytaj informacje o zasobach |
> | Działanie | Microsoft.DataMigration/services/projects/files/read/action | Uzyskaj adres URL, za pomocą którego można odczytać zawartość pliku |
> | Działanie | Microsoft. datamigration/Services/projects/Files/readWrite/Action | Uzyskaj adres URL, za pomocą którego można odczytywać lub zapisywać zawartość pliku |
> | Działanie | Microsoft. datamigration/Services/projects/Files/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Działanie | Microsoft. datamigration/Services/projects/Read | Przeczytaj informacje o zasobach |
> | Działanie | Microsoft. datamigration/Services/projects/Tasks/Cancel/Action | Anuluj zadanie, jeśli jest aktualnie uruchomione |
> | Działanie | Microsoft. datamigration/Services/projects/Tasks/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft.DataMigration/services/projects/tasks/read | Przeczytaj informacje o zasobach |
> | Działanie | Microsoft. datamigration/Services/projects/Tasks/Write | Uruchom zadania Azure Database Migration Service zadania |
> | Działanie | Microsoft. datamigration/Services/projects/Write | Uruchom zadania Azure Database Migration Service zadania |
> | Działanie | Microsoft. datamigration/Services/Read | Przeczytaj informacje o zasobach |
> | Działanie | Microsoft. datamigration/Services/removeWorker/Action | Usuwa proces roboczy DMS do dostępny procesów roboczych usługi |
> | Działanie | Microsoft. datamigration/Services/servicetasks/Cancel/Action | Anuluj zadanie, jeśli jest aktualnie uruchomione |
> | Działanie | Microsoft. datamigration/Services/servicetasks/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. datamigration/Services/servicetasks/Read | Przeczytaj informacje o zasobach |
> | Działanie | Microsoft. datamigration/Services/servicetasks/Write | Uruchom zadania Azure Database Migration Service zadania |
> | Działanie | Microsoft. datamigration/Services/Slots/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. datamigration/Services/Slots/Read | Przeczytaj informacje o zasobach |
> | Działanie | Microsoft. datamigration/Services/Slots/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Działanie | Microsoft. datamigration/Services/Start/akcja | Uruchom usługę DMS, aby umożliwić jej ponowne przetworzenie migracji |
> | Działanie | Microsoft. datamigration/Services/Stop/akcja | Zatrzymaj usługę DMS, aby zminimalizować jej koszt |
> | Działanie | Microsoft.DataMigration/services/updateAgentConfig/action | Aktualizuje konfigurację agenta DMS przy użyciu podanych wartości. |
> | Działanie | Microsoft. datamigration/Services/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Działanie | Microsoft. datamigration/SKU/Read | Pobierz listę jednostek SKU obsługiwanych przez zasoby DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DBforMariaDB/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Działanie | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Zwróć wyniki operacji serwera MariaDB |
> | Działanie | Microsoft.DBforMariaDB/locations/operationResults/read | Zwróć wyniki operacji serwera MariaDB opartego na źródle zasobów |
> | Działanie | Microsoft.DBforMariaDB/locations/operationResults/read | Zwróć wyniki operacji serwera MariaDB |
> | Działanie | Microsoft.DBforMariaDB/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionOperationResults/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyOperationResults/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft. DBforMariaDB/Locations/serverKeyAzureAsyncOperation/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft. DBforMariaDB/Locations/serverKeyOperationResults/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft.DBforMariaDB/operations/read | Zwróć listę operacji MariaDB. |
> | Działanie | Microsoft.DBforMariaDB/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft.DBforMariaDB/register/action | Zarejestruj dostawcę zasobów MariaDB |
> | Działanie | Microsoft.DBforMariaDB/servers/administrators/delete | Usuwa istniejącego administratora serwera MariaDB. |
> | Działanie | Microsoft.DBforMariaDB/servers/administrators/read | Pobiera listę administratorów serwera MariaDB. |
> | Działanie | Microsoft.DBforMariaDB/servers/administrators/write | Tworzy lub aktualizuje administratora serwera MariaDB o określonych parametrach. |
> | Działanie | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Utwórz nową sesję akcji rekomendacji |
> | Działanie | Microsoft.DBforMariaDB/servers/advisors/read | Zwróć listę doradców |
> | Działanie | Microsoft.DBforMariaDB/servers/advisors/read | Zwracanie klasyfikatora |
> | Działanie | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Zwróć zalecaną akcję |
> | Działanie | Microsoft.DBforMariaDB/servers/configurations/read | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Działanie | Microsoft.DBforMariaDB/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Działanie | Microsoft.DBforMariaDB/servers/databases/delete | Usuwa istniejącą bazę danych MariaDB. |
> | Działanie | Microsoft.DBforMariaDB/servers/databases/read | Zwróć listę baz danych MariaDB lub pobiera właściwości dla określonej bazy danych. |
> | Działanie | Microsoft.DBforMariaDB/servers/databases/write | Tworzy bazę danych MariaDB z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Działanie | Microsoft.DBforMariaDB/servers/delete | Usuwa istniejący serwer. |
> | Działanie | Microsoft.DBforMariaDB/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | Działanie | Microsoft.DBforMariaDB/servers/firewallRules/read | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Działanie | Microsoft.DBforMariaDB/servers/firewallRules/write | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Działanie | Microsoft. DBforMariaDB/serwery/klucze/usuwanie | Usuwa istniejący klucz serwera. |
> | Działanie | Microsoft. DBforMariaDB/serwery/klucze/odczyt | Zwróć listę kluczy serwera lub pobiera właściwości dla określonego klucza serwera. |
> | Działanie | Microsoft. DBforMariaDB/serwery/klucze/zapis | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza serwera. |
> | Działanie | Microsoft.DBforMariaDB/servers/logFiles/read | Zwróć listę plików dziennika MariaDB. |
> | Działanie | Microsoft. DBforMariaDB/serwery/privateEndpointConnectionProxies/usuwanie | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMariaDB/serwery/privateEndpointConnectionProxies/odczyt | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Validate/Action | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Działanie | Microsoft. DBforMariaDB/serwery/privateEndpointConnectionProxies/zapis | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforMariaDB/serwery/privateEndpointConnections/usuwanie | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMariaDB/serwery/privateEndpointConnections/odczyt | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforMariaDB/serwery/privateEndpointConnections/zapis | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMariaDB/serwery/privateLinkResources/odczyt | Pobierz zasoby linku prywatnego dla odpowiedniego serwera MariaDB |
> | Działanie | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie disagnostic dla zasobu |
> | Działanie | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów MariaDB |
> | Działanie | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft.DBforMariaDB/servers/queryTexts/action | Zwróć teksty dla listy zapytań |
> | Działanie | Microsoft.DBforMariaDB/servers/queryTexts/action | Zwróć tekst zapytania |
> | Działanie | Microsoft.DBforMariaDB/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft.DBforMariaDB/servers/recoverableServers/read | Zwróć informacje o odzyskiwanym serwerze MariaDB |
> | Działanie | Microsoft.DBforMariaDB/servers/replicas/read | Pobierz repliki serwera MariaDB |
> | Działanie | Microsoft. DBforMariaDB/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Działanie | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Działanie | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Działanie | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Zwróć statystykę zapytania |
> | Działanie | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Zaktualizuj konfiguracje dla określonego serwera |
> | Działanie | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę Virtual Network |
> | Działanie | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft.DBforMariaDB/servers/waitStatistics/read | Zwróć statystykę oczekiwania dla wystąpienia |
> | Działanie | Microsoft.DBforMariaDB/servers/waitStatistics/read | Zwróć statystykę oczekiwania |
> | Działanie | Microsoft.DBforMariaDB/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DBforMySQL/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Działanie | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Zwróć wyniki operacji serwera MySQL |
> | Działanie | Microsoft.DBforMySQL/locations/operationResults/read | Zwróć wyniki operacji serwera MySQL na podstawie zasobów |
> | Działanie | Microsoft.DBforMySQL/locations/operationResults/read | Zwróć wyniki operacji serwera MySQL |
> | Działanie | Microsoft.DBforMySQL/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforMySQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMySQL/Locations/privateEndpointConnectionOperationResults/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft. DBforMySQL/Locations/serverKeyAzureAsyncOperation/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft. DBforMySQL/Locations/serverKeyOperationResults/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft.DBforMySQL/operations/read | Zwróć listę operacji programu MySQL. |
> | Działanie | Microsoft.DBforMySQL/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforMySQL/Register/Action | Zarejestruj dostawcę zasobów MySQL |
> | Działanie | Microsoft.DBforMySQL/servers/administrators/delete | Usuwa istniejącego administratora serwera MySQL. |
> | Działanie | Microsoft.DBforMySQL/servers/administrators/read | Pobiera listę administratorów serwera MySQL. |
> | Działanie | Microsoft.DBforMySQL/servers/administrators/write | Tworzy lub aktualizuje administratora serwera MySQL o określonych parametrach. |
> | Działanie | Microsoft. DBforMySQL/serwery/doradcy/createRecommendedActionSession/akcja | Utwórz nową sesję akcji rekomendacji |
> | Działanie | Microsoft.DBforMySQL/servers/advisors/read | Zwróć listę doradców |
> | Działanie | Microsoft.DBforMySQL/servers/advisors/read | Zwracanie klasyfikatora |
> | Działanie | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Zwróć zalecaną akcję |
> | Działanie | Microsoft.DBforMySQL/servers/configurations/read | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Działanie | Microsoft.DBforMySQL/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Działanie | Microsoft.DBforMySQL/servers/databases/delete | Usuwa istniejącą bazę danych MySQL. |
> | Działanie | Microsoft.DBforMySQL/servers/databases/read | Zwróć listę baz danych MySQL lub pobiera właściwości dla określonej bazy danych. |
> | Działanie | Microsoft.DBforMySQL/servers/databases/write | Tworzy bazę danych MySQL z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Działanie | Microsoft.DBforMySQL/servers/delete | Usuwa istniejący serwer. |
> | Działanie | Microsoft. DBforMySQL/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Działanie | Microsoft. DBforMySQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Działanie | Microsoft. DBforMySQL/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Działanie | Microsoft. DBforMySQL/serwery/klucze/usuwanie | Usuwa istniejący klucz serwera. |
> | Działanie | Microsoft. DBforMySQL/serwery/klucze/odczyt | Zwróć listę kluczy serwera lub pobiera właściwości dla określonego klucza serwera. |
> | Działanie | Microsoft. DBforMySQL/serwery/klucze/zapis | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza serwera. |
> | Działanie | Microsoft.DBforMySQL/servers/logFiles/read | Zwróć listę plików dziennika programu MySQL. |
> | Działanie | Microsoft. DBforMySQL/serwery/privateEndpointConnectionProxies/usuwanie | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMySQL/serwery/privateEndpointConnectionProxies/odczyt | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Validate/Action | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Działanie | Microsoft. DBforMySQL/serwery/privateEndpointConnectionProxies/zapis | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforMySQL/serwery/privateEndpointConnections/usuwanie | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMySQL/serwery/privateEndpointConnections/odczyt | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforMySQL/serwery/privateEndpointConnections/zapis | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforMySQL/serwery/privateLinkResources/odczyt | Pobierz zasoby linku prywatnego dla odpowiedniego serwera MySQL |
> | Działanie | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie disagnostic dla zasobu |
> | Działanie | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów MySQL |
> | Działanie | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft. DBforMySQL/serwery/queryTexts/akcja | Zwróć teksty dla listy zapytań |
> | Działanie | Microsoft. DBforMySQL/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Działanie | Microsoft.DBforMySQL/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft.DBforMySQL/servers/recoverableServers/read | Zwróć informacje o odzyskiwanym serwerze MySQL |
> | Działanie | Microsoft.DBforMySQL/servers/replicas/read | Uzyskaj odczyt replik serwera MySQL |
> | Działanie | Microsoft. DBforMySQL/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Działanie | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Działanie | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Działanie | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Zwróć statystykę zapytania |
> | Działanie | Microsoft.DBforMySQL/servers/updateConfigurations/action | Zaktualizuj konfiguracje dla określonego serwera |
> | Działanie | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę Virtual Network |
> | Działanie | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft.DBforMySQL/servers/waitStatistics/read | Zwróć statystykę oczekiwania dla wystąpienia |
> | Działanie | Microsoft.DBforMySQL/servers/waitStatistics/read | Zwróć statystykę oczekiwania |
> | Działanie | Microsoft.DBforMySQL/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DBforPostgreSQL/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Działanie | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Zwróć wyniki operacji serwera PostgreSQL |
> | Działanie | Microsoft.DBforPostgreSQL/locations/operationResults/read | Zwróć wyniki operacji serwera PostgreSQL opartego na źródle zasobów |
> | Działanie | Microsoft.DBforPostgreSQL/locations/operationResults/read | Zwróć wyniki operacji serwera PostgreSQL |
> | Działanie | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/serverKeyAzureAsyncOperation/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/serverKeyOperationResults/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft.DBforPostgreSQL/operations/read | Zwróć listę operacji PostgreSQL. |
> | Działanie | Microsoft.DBforPostgreSQL/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforPostgreSQL/Register/Action | Zarejestruj dostawcę zasobów PostgreSQL |
> | Działanie | Microsoft.DBforPostgreSQL/servers/administrators/delete | Usuwa istniejącego administratora serwera PostgreSQL. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/administrators/read | Pobiera listę administratorów serwera PostgreSQL. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/administrators/write | Tworzy lub aktualizuje administratora serwera PostgreSQL o określonych parametrach. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/advisors/read | Zwróć listę doradców |
> | Działanie | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/doradcy/recommendedActionSessions/akcja | Wykonaj zalecenia |
> | Działanie | Microsoft.DBforPostgreSQL/servers/configurations/read | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/konfiguracje/zapis | Zaktualizuj wartość dla określonej konfiguracji |
> | Działanie | Microsoft.DBforPostgreSQL/servers/databases/delete | Usuwa istniejącą bazę danych PostgreSQL. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/databases/read | Zwróć listę baz danych PostgreSQL lub pobiera właściwości dla określonej bazy danych. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/databases/write | Tworzy bazę danych PostgreSQL z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/delete | Usuwa istniejący serwer. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/klucze/usuwanie | Usuwa istniejący klucz serwera. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/klucze/odczyt | Zwróć listę kluczy serwera lub pobiera właściwości dla określonego klucza serwera. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/klucze/zapis | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza serwera. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/logFiles/read | Zwróć listę plików dziennika PostgreSQL. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/usuwanie | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/odczyt | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Validate/Action | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/zapis | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/usuwanie | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/odczyt | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/zapis | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateLinkResources/odczyt | Pobierz zasoby linku prywatnego dla odpowiedniego serwera PostgreSQL |
> | Działanie | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie disagnostic dla zasobu |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Działanie | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Działanie | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Zwróć teksty dla listy zapytań |
> | Działanie | Microsoft.DBforPostgreSQL/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Zwróć informacje o odzyskiwanym serwerze PostgreSQL |
> | Działanie | Microsoft.DBforPostgreSQL/servers/replicas/read | Pobierz repliki serwera PostgreSQL |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Działanie | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Zaktualizuj konfiguracje dla określonego serwera |
> | Działanie | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę Virtual Network |
> | Działanie | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Zwróć statystykę oczekiwania dla wystąpienia |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |
> | Działanie | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Działanie | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Działanie | Microsoft.DBforPostgreSQL/serversv2/delete | Usuwa istniejący serwer. |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Delete | Usuwa istniejącą regułę zapory. |
> | Działanie | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Write | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Działanie | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie disagnostic dla zasobu |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Działanie | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft.DBforPostgreSQL/serversv2/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Zaktualizuj konfiguracje dla określonego serwera |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/Write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Devices/Account/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/Account/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/Account/logDefinitions/Read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Działanie | Microsoft. Devices/Account/metricDefinitions/Read | Pobiera dostępne metryki dla usługi IotHub |
> | Działanie | Microsoft.Devices/checkNameAvailability/Action | Sprawdź, czy nazwa IotHub jest dostępna |
> | Działanie | Microsoft.Devices/checkNameAvailability/Action | Sprawdź, czy nazwa IotHub jest dostępna |
> | Działanie | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Sprawdź, czy nazwa usługi aprowizacji jest dostępna |
> | Działanie | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Sprawdź, czy nazwa usługi aprowizacji jest dostępna |
> | Działanie | Microsoft. Devices/digitalTwins/Delete | Usuń istniejące konto bliźniaczych reprezentacji Digital i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. Devices/digitalTwins/operationresults/odczyt | Pobieranie stanu operacji przy użyciu konta bliźniaczych reprezentacji Digital |
> | Działanie | Microsoft. Devices/digitalTwins/odczyt | Pobiera listę kont bliźniaczych reprezentacji cyfrowych skojarzonych z subskrypcją |
> | Działanie | Microsoft. Devices/digitalTwins/SKU/odczyt | Pobierz listę prawidłowych jednostek SKU dla kont Digital bliźniaczych reprezentacji |
> | Działanie | Microsoft. Devices/digitalTwins/Write | Utwórz nowe konto bliźniaczych reprezentacji z cyframi |
> | Działanie | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/elasticPools/eventGridFilters/Delete | Usuwa filtr Event Grid Pula elastyczna |
> | Działanie | Microsoft. Devices/elasticPools/eventGridFilters/odczyt | Pobiera Filtr Event Grid Pula elastyczna |
> | Działanie | Microsoft. Devices/elasticPools/eventGridFilters/Write | Utwórz nowy lub zaktualizuj istniejący filtr Event Grid Pula elastyczna |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Usuwa certyfikat |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Pobiera certyfikat |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Usuwanie zasobu dzierżawy IotHub |
> | Działanie | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Usuń grupę odbiorców EventHub |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Utwórz grupę odbiorców EventHub |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eksportuj urządzenia |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Pobiera zasób statystyk dzierżawy IotHub |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importuj urządzenia |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Pobiera klucz dzierżawy IotHub |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Pobierz szczegóły zadań przesłane w danym IotHub |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Pobiera klucze dzierżawy IotHub |
> | Działanie | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Działanie | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Pobierz metryki przydziału |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/Read | Pobiera zasób dzierżawy IotHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Routing/trasy/$testall/Action | Przetestuj komunikat dla wszystkich istniejących tras |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Przetestuj komunikat w odniesieniu do podanej trasy testowej |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla IotHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | Pobierz wynik operacji asynchronicznego Put dla usługi IoT dzierżawca SecuritySettings |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Read | Pobieranie ustawień Azure Security Center w usłudze IoT dzierżawcy |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Write | Aktualizowanie ustawień Azure Security Center w usłudze centrum dzierżaw IoT |
> | Działanie | Microsoft.Devices/elasticPools/iotHubTenants/Write | Utwórz lub zaktualizuj zasób dzierżawy IotHub |
> | Działanie | Microsoft.Devices/ElasticPools/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | Działanie | Microsoft.Devices/iotHubs/certificates/Delete | Usuwa certyfikat |
> | Działanie | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Działanie | Microsoft.Devices/iotHubs/certificates/Read | Pobiera certyfikat |
> | Działanie | Microsoft. Devices/iotHubs/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Działanie | Microsoft.Devices/iotHubs/certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Działanie | Microsoft.Devices/iotHubs/Delete | Usuń zasób IotHub |
> | Działanie | Microsoft.Devices/IotHubs/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.Devices/IotHubs/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Usuwa filtr Event Grid |
> | Działanie | Microsoft.Devices/iotHubs/eventGridFilters/Read | Pobiera Filtr Event Grid |
> | Działanie | Microsoft.Devices/iotHubs/eventGridFilters/Write | Utwórz nowy lub zaktualizuj istniejący filtr Event Grid |
> | Działanie | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Usuń grupę odbiorców EventHub |
> | Działanie | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Działanie | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Utwórz grupę odbiorców EventHub |
> | Działanie | Microsoft.Devices/iotHubs/exportDevices/Action | Eksportuj urządzenia |
> | Działanie | Microsoft.Devices/iotHubs/importDevices/Action | Importuj urządzenia |
> | Działanie | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Pobierz klucz IotHub dla podaną nazwę |
> | Działanie | Microsoft.Devices/iotHubs/iotHubStats/Read | Pobierz statystyki IotHub |
> | Działanie | Microsoft.Devices/iotHubs/jobs/Read | Pobierz szczegóły zadań przesłane w danym IotHub |
> | Działanie | Microsoft.Devices/iotHubs/listkeys/Action | Pobierz wszystkie klucze IotHub |
> | Działanie | Microsoft.Devices/IotHubs/logDefinitions/read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Działanie | Microsoft.Devices/IotHubs/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | Działanie | Microsoft.Devices/iotHubs/operationresults/Read | Pobierz wynik operacji (przestarzały interfejs API) |
> | Działanie | Microsoft.Devices/iotHubs/quotaMetrics/Read | Pobierz metryki przydziału |
> | Działanie | Microsoft.Devices/iotHubs/Read | Pobiera zasoby IotHub |
> | Działanie | Microsoft.Devices/iotHubs/routing/$testall/Action | Przetestuj komunikat dla wszystkich istniejących tras |
> | Działanie | Microsoft.Devices/iotHubs/routing/$testnew/Action | Przetestuj komunikat w odniesieniu do podanej trasy testowej |
> | Działanie | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla IotHub |
> | Działanie | Microsoft. Devices/iotHubs/securitySettings/operationResults/Read | Pobieranie wyniku asynchronicznej operacji Put dla usługi IoT Hub SecuritySettings |
> | Działanie | Microsoft. Devices/iotHubs/securitySettings/odczyt | Pobieranie ustawień Azure Security Center w usłudze IoT Hub |
> | Działanie | Microsoft. Devices/iotHubs/securitySettings/Write | Aktualizowanie ustawień Azure Security Center w usłudze IoT Hub |
> | Działanie | Microsoft.Devices/iotHubs/skus/Read | Pobieranie prawidłowych jednostek SKU IotHub |
> | Działanie | Microsoft.Devices/iotHubs/Write | Utwórz lub zaktualizuj zasób IotHub |
> | Działanie | Microsoft.Devices/locations/operationresults/Read | Pobierz wynik operacji na podstawie lokalizacji |
> | Działanie | Microsoft. Devices/operationresults/odczyt | Pobierz wynik operacji |
> | Działanie | Microsoft. Devices/Operations/Read | Pobierz wszystkie operacje ResourceProvider |
> | Działanie | Microsoft.Devices/provisioningServices/certificates/Delete | Usuwa certyfikat |
> | Działanie | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Działanie | Microsoft.Devices/provisioningServices/certificates/Read | Pobiera certyfikat |
> | Działanie | Microsoft. Devices/provisioningServices/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Działanie | Microsoft.Devices/provisioningServices/certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Działanie | Microsoft.Devices/provisioningServices/Delete | Usuń zasób IotDps |
> | Działanie | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Pobierz klucze IotDps dla nazwy klucza |
> | Działanie | Microsoft.Devices/provisioningServices/listkeys/Action | Pobierz wszystkie klucze IotDps |
> | Działanie | Microsoft.Devices/provisioningServices/logDefinitions/read | Pobiera dostępne definicje dzienników dla usługi aprowizacji |
> | Działanie | Microsoft.Devices/provisioningServices/metricDefinitions/read | Pobiera dostępne metryki dla usługi aprowizacji |
> | Działanie | Microsoft.Devices/provisioningServices/operationresults/Read | Pobierz wynik operacji DPS |
> | Działanie | Microsoft.Devices/provisioningServices/Read | Pobierz zasób IotDps |
> | Działanie | Microsoft.Devices/provisioningServices/skus/Read | Pobieranie prawidłowych jednostek SKU IotDps |
> | Działanie | Microsoft.Devices/provisioningServices/Write | Utwórz zasób IotDps |
> | Działanie | Microsoft. Devices/Register/Action | Zarejestruj subskrypcję dostawcy zasobów IotHub i Włącz tworzenie zasobów IotHub |
> | Działanie | Microsoft.Devices/usages/Read | Pobierz szczegóły użycia subskrypcji dla tego dostawcy. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DevSpaces/controllers/Delete | Usuwanie kontrolera Azure Dev Spaces i usług dataplaning |
> | Działanie | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Wyświetl szczegóły połączenia dla infrastruktury kontrolera Azure Dev Spaces |
> | Działanie | Microsoft.DevSpaces/controllers/read | Odczytaj właściwości kontrolera Azure Dev Spaces |
> | Działanie | Microsoft. DevSpaces/controllers/Write | Utwórz lub zaktualizuj właściwości kontrolera Azure Dev Spaces |
> | Działanie | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Sprawdź istniejące mapowanie kontrolera dla hosta kontenera |
> | Działanie | Microsoft.DevSpaces/locations/operationresults/read | Stan odczytu operacji asynchronicznej |
> | Działanie | Microsoft. DevSpaces/Register/Action | Rejestrowanie dostawcy zasobów Microsoft dev Spaces z subskrypcją |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.DevTestLab/labCenters/delete | Usuń centra laboratoryjne. |
> | Działanie | Microsoft.DevTestLab/labCenters/read | Odczytywanie centrów laboratoryjnych. |
> | Działanie | Microsoft.DevTestLab/labCenters/write | Dodawanie lub modyfikowanie centrów laboratoryjnych. |
> | Działanie | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Odczytywanie szablonów usługi Azure Resource Manager. |
> | Działanie | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generuje szablon Azure Resource Manager dla danego artefaktu, przekazuje wymagane pliki na konto magazynu i sprawdza poprawność wygenerowanego artefaktu. |
> | Działanie | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Odczytaj artefakty. |
> | Działanie | Microsoft.DevTestLab/labs/artifactSources/delete | Usuń źródła artefaktów. |
> | Działanie | Microsoft.DevTestLab/labs/artifactSources/read | Odczytaj źródła artefaktów. |
> | Działanie | Microsoft.DevTestLab/labs/artifactSources/write | Dodaj lub zmodyfikuj źródła artefaktów. |
> | Działanie | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Zarezerwuj losową maszynę wirtualną z dodaną odpowiedzialnością w laboratorium. |
> | Działanie | Microsoft.DevTestLab/labs/costs/read | Zapoznaj się z kosztami. |
> | Działanie | Microsoft.DevTestLab/labs/costs/write | Dodawanie lub modyfikowanie kosztów. |
> | Działanie | Microsoft.DevTestLab/labs/CreateEnvironment/action | Twórz maszyny wirtualne w laboratorium. |
> | Działanie | Microsoft.DevTestLab/labs/customImages/delete | Usuń obrazy niestandardowe. |
> | Działanie | Microsoft.DevTestLab/labs/customImages/read | Odczytaj obrazy niestandardowe. |
> | Działanie | Microsoft.DevTestLab/labs/customImages/write | Dodawanie lub modyfikowanie obrazów niestandardowych. |
> | Działanie | Microsoft.DevTestLab/labs/delete | Delete labs. |
> | Działanie | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Upewnij się, że bieżący użytkownik ma prawidłowy profil w laboratorium. |
> | Działanie | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Eksportuje użycie zasobów laboratorium do konta magazynu |
> | Działanie | Microsoft.DevTestLab/labs/formulas/delete | Usuń formuły. |
> | Działanie | Microsoft.DevTestLab/labs/formulas/read | Odczytaj formuły. |
> | Działanie | Microsoft.DevTestLab/labs/formulas/write | Dodaj lub zmodyfikuj formuły. |
> | Działanie | Microsoft.DevTestLab/labs/galleryImages/read | Read gallery images. |
> | Działanie | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generowanie identyfikatora URI na potrzeby przekazywania niestandardowych obrazów dysków do laboratorium. |
> | Działanie | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Zaimportuj maszynę wirtualną do innego laboratorium. |
> | Działanie | Microsoft.DevTestLab/labs/ListVhds/action | Wyświetlanie listy obrazów dysków dostępnych do tworzenia obrazów niestandardowych. |
> | Działanie | Microsoft.DevTestLab/labs/notificationChannels/delete | Usuwanie kanałów powiadomień. |
> | Działanie | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Wyślij powiadomienie do podanego kanału. |
> | Działanie | Microsoft.DevTestLab/labs/notificationChannels/read | Odczytaj kanały powiadomień. |
> | Działanie | Microsoft.DevTestLab/labs/notificationChannels/write | Dodawanie lub modyfikowanie kanałów powiadomień. |
> | Działanie | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Szacuje zasady laboratorium. |
> | Działanie | Microsoft.DevTestLab/labs/policySets/policies/delete | Usuń zasady. |
> | Działanie | Microsoft.DevTestLab/labs/policySets/policies/read | Odczytaj zasady. |
> | Działanie | Microsoft.DevTestLab/labs/policySets/policies/write | Dodawanie lub modyfikowanie zasad. |
> | Działanie | Microsoft.DevTestLab/labs/policySets/read | Odczytaj zestawy zasad. |
> | Działanie | Microsoft.DevTestLab/labs/read | Read labs. |
> | Działanie | Microsoft.DevTestLab/labs/schedules/delete | Delete schedules. |
> | Działanie | Microsoft. wspólny/Labs/harmonogramy/wykonywanie/akcja | Wykonaj harmonogram. |
> | Działanie | Microsoft. wspólny/Labs/Schedules/ListApplicable/Action | Wyświetla wszystkie odpowiednie harmonogramy |
> | Działanie | Microsoft.DevTestLab/labs/schedules/read | Read schedules. |
> | Działanie | Microsoft.DevTestLab/labs/schedules/write | Add or modify schedules. |
> | Działanie | Microsoft.DevTestLab/labs/serviceRunners/delete | Usuwanie modułu uruchamiającego usługi. |
> | Działanie | Microsoft.DevTestLab/labs/serviceRunners/read | Odczytywanie modułu uruchamiającego usługi. |
> | Działanie | Microsoft.DevTestLab/labs/serviceRunners/write | Dodawanie lub modyfikowanie modułu uruchamiającego usługi. |
> | Działanie | Microsoft.DevTestLab/labs/sharedGalleries/delete | Usuń udostępnione galerie. |
> | Działanie | Microsoft.DevTestLab/labs/sharedGalleries/read | Odczytuj udostępnione galerie. |
> | Działanie | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Usuń obrazy udostępnione. |
> | Działanie | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Odczytuj obrazy udostępnione. |
> | Działanie | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Dodaj lub Modyfikuj obrazy udostępnione. |
> | Działanie | Microsoft.DevTestLab/labs/sharedGalleries/write | Dodaj lub zmodyfikuj udostępnione galerie. |
> | Działanie | Microsoft.DevTestLab/labs/users/delete | Usuń profile użytkowników. |
> | Działanie | Microsoft.DevTestLab/labs/users/disks/Attach/action | Dołącz i Utwórz dzierżawę dysku dla maszyny wirtualnej. |
> | Działanie | Microsoft.DevTestLab/labs/users/disks/delete | Usuń dyski. |
> | Działanie | Microsoft.DevTestLab/labs/users/disks/Detach/action | Odłącz i Przerwij dzierżawę dysku dołączonego do maszyny wirtualnej. |
> | Działanie | Microsoft.DevTestLab/labs/users/disks/read | Odczytywanie dysków. |
> | Działanie | Microsoft.DevTestLab/labs/users/disks/write | Dodawanie lub modyfikowanie dysków. |
> | Działanie | Microsoft.DevTestLab/labs/users/environments/delete | Delete environments. |
> | Działanie | Microsoft.DevTestLab/labs/users/environments/read | Read environments. |
> | Działanie | Microsoft.DevTestLab/labs/users/environments/write | Add or modify environments. |
> | Działanie | Microsoft.DevTestLab/labs/users/read | Odczytaj profile użytkowników. |
> | Działanie | Microsoft.DevTestLab/labs/users/secrets/delete | Usuń wpisy tajne. |
> | Działanie | Microsoft.DevTestLab/labs/users/secrets/read | Odczytaj wpisy tajne. |
> | Działanie | Microsoft.DevTestLab/labs/users/secrets/write | Dodaj lub zmodyfikuj wpisy tajne. |
> | Działanie | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Usuwanie sieci szkieletowej usług. |
> | Działanie | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Działanie | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Odczytaj sieci szkieletowe usług. |
> | Działanie | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Delete schedules. |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Execute/Action | Wykonaj harmonogram. |
> | Działanie | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Read schedules. |
> | Działanie | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Add or modify schedules. |
> | Działanie | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Uruchom sieć szkieletową usług. |
> | Działanie | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zatrzymaj sieć szkieletową usług |
> | Działanie | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Dodawanie lub modyfikowanie sieci szkieletowej usług. |
> | Działanie | Microsoft.DevTestLab/labs/users/write | Dodaj lub zmodyfikuj profile użytkowników. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Dołącz nowy lub istniejący dysk danych do maszyny wirtualnej. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Zastosuj artefakty do maszyny wirtualnej. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Przejmowanie na własność istniejącej maszyny wirtualnej |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/ClearArtifactResults/Action | Czyści wyniki artefaktu maszyny wirtualnej. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/delete | Usuwanie maszyn wirtualnych. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Odłącz określony dysk od maszyny wirtualnej. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Pobiera ciąg, który reprezentuje zawartość pliku RDP dla maszyny wirtualnej |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/read | Odczytaj maszyny wirtualne. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Ponowne wdrażanie maszyny wirtualnej |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/Zmień rozmiar/akcję | Zmień rozmiar maszyny wirtualnej. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/restart/akcja | Uruchom ponownie maszynę wirtualną. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Delete schedules. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/Schedules/Execute/Action | Wykonaj harmonogram. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Read schedules. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Add or modify schedules. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Uruchom maszynę wirtualną. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Zatrzymaj maszynę wirtualną |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Przenosi wszystkie dyski z danymi dołączone do maszyny wirtualnej, które mają należeć do bieżącego użytkownika. |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Zwolnij własność istniejącej maszyny wirtualnej |
> | Działanie | Microsoft.DevTestLab/labs/virtualMachines/write | Dodaj lub zmodyfikuj maszyny wirtualne. |
> | Działanie | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Delete | Usuń bastionhosts. |
> | Działanie | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Read | Odczytaj bastionhosts. |
> | Działanie | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Write | Dodaj lub zmodyfikuj bastionhosts. |
> | Działanie | Microsoft.DevTestLab/labs/virtualNetworks/delete | Usuń sieci wirtualne. |
> | Działanie | Microsoft.DevTestLab/labs/virtualNetworks/read | Odczytaj sieci wirtualne. |
> | Działanie | Microsoft.DevTestLab/labs/virtualNetworks/write | Dodaj lub zmodyfikuj sieci wirtualne. |
> | Działanie | Microsoft.DevTestLab/labs/vmPools/delete | Usuń pule maszyn wirtualnych. |
> | Działanie | Microsoft.DevTestLab/labs/vmPools/read | Odczytaj pule maszyn wirtualnych. |
> | Działanie | Microsoft.DevTestLab/labs/vmPools/write | Dodaj lub zmodyfikuj pule maszyn wirtualnych. |
> | Działanie | Microsoft.DevTestLab/labs/write | Add or modify labs. |
> | Działanie | Microsoft.DevTestLab/locations/operations/read | Read operations. |
> | Działanie | Microsoft.DevTestLab/register/action | Rejestruje subskrypcję |
> | Działanie | Microsoft.DevTestLab/schedules/delete | Delete schedules. |
> | Działanie | Microsoft. wspólny/harmonogramy/wykonywanie/akcja | Wykonaj harmonogram. |
> | Działanie | Microsoft.DevTestLab/schedules/read | Read schedules. |
> | Działanie | Microsoft.DevTestLab/schedules/Retarget/action | Aktualizuje identyfikator zasobu docelowego harmonogramu. |
> | Działanie | Microsoft.DevTestLab/schedules/write | Add or modify schedules. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.DocumentDB/databaseAccountNames/read | Sprawdza dostępność nazw. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Delete | Usuwanie kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Read | Odczytaj kolekcję lub Wyświetl listę wszystkich kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Odczytaj przepływność kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Aktualizowanie przepływności kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Write | Utwórz lub zaktualizuj kolekcję. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Delete | Usuń kontener. Dotyczy tylko typów interfejsu API: "SQL". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Read | Odczytaj kontener lub Wyświetl listę wszystkich kontenerów. Dotyczy tylko typów interfejsu API: "SQL". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Odczytaj przepływność kontenera. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Zaktualizuj przepływność kontenera. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Write | Utwórz lub zaktualizuj kontener. Dotyczy tylko typów interfejsu API: "SQL". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Delete | Usuń bazę danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Usuwanie grafu. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Odczytaj wykres lub Wyświetl wszystkie wykresy. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Odczytaj przepływność grafu. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Aktualizowanie przepływności grafu. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/bazy danych/wykresy/zapis | Utwórz lub zaktualizuj Graf. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Odczytaj bazę danych lub listę wszystkich baz danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Odczytaj przepływność bazy danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Aktualizowanie przepływności bazy danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Write | Tworzenie bazy danych Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Usuń przestrzeń kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Odczytaj miejsce na dysku lub Wyświetl wszystkie spacje. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Odczytaj przepustowość przestrzeni kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Aktualizowanie przepływności przestrzeni kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Usuń tabelę. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Odczytaj tabelę lub listę wszystkich tabel. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Odczytaj tabelę przepływność. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Aktualizowanie przepływności tabeli. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Utwórz lub zaktualizuj tabelę. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Utwórz miejsce na dysku. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Usuń tabelę. Dotyczy tylko typów interfejsu API: "Table". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Table". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Odczytaj tabelę lub listę wszystkich tabel. Dotyczy tylko typów interfejsu API: "Table". |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Odczytaj tabelę przepływność. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Aktualizowanie przepływności tabeli. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/zapis | Utwórz lub zaktualizuj tabelę. Dotyczy tylko typów interfejsu API: "Table". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/kopia zapasowa/akcja | Prześlij żądanie skonfigurowania kopii zapasowej |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Zmień grupę zasobów konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Odczytuje definicje metryk kolekcji. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Odczytuje metryki kolekcji. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Odczytaj metryki na poziomie klucza partycji konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Odczytaj metryki na poziomie partycji konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Odczytywanie partycji konta bazy danych w kolekcji |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Odczytaj użycie poziomów partycji konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Odczytuje użycie kolekcji. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Odczytuje definicje metryk bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Odczytuje metryki bazy danych. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Odczytuje użycie bazy danych. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/delete | Usuwa konta bazy danych. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Zmień priorytety trybu failover regionów konta bazy danych. Służy do wykonywania ręcznej operacji przełączania do trybu failover |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/akcja | Pobieranie zasad tworzenia kopii zapasowej konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Pobieranie parametrów połączenia dla konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Lista kluczy konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Odczytuje definicje metryk konta bazy danych. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/metrics/read | Odczytuje metryki konta bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/offlineRegion/akcja | Offline w regionie konta bazy danych.  |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online region konta bazy danych. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Stan odczytu operacji asynchronicznej |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Metryki opóźnienia odczytu |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/percentile/read | Odczytaj percentyle opóźnień replikacji |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Odczytaj metryki opóźnienia dla określonego źródła i regionu docelowego |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Odczytaj metryki opóźnienia dla określonego regionu docelowego |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Usuń serwer proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | Odczyt stanu operacji asynchronicznej serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Odczytaj prywatny serwer proxy połączenia dla konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Weryfikowanie serwera proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Utwórz lub zaktualizuj serwer proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Delete | Usuwanie połączenia prywatnego punktu końcowego konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/Read | Odczytaj stan operacji asynchronicznej privateEndpointConnenctions |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Read | Odczytaj połączenie prywatnego punktu końcowego lub Wyświetl listę wszystkich połączeń prywatnych punktów końcowych konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Write | Utwórz lub zaktualizuj połączenie prywatnego punktu końcowego konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/privateLinkResources/Read | Odczytaj zasób link prywatny lub Wyświetl listę wszystkich prywatnych zasobów linku konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/read | Odczytuje konto bazy danych. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Obróć klucze konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Odczytuje metryki kolekcji regionalnej. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Odczytaj metryki na poziomie klucza partycji konta bazy danych |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Odczytaj metryki na poziomie partycji konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/region/bazy danych/kolekcje/partycje/odczyt | Odczytywanie partycji konta bazy danych regionalnych w kolekcji |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Odczytuje metryki konta regionu i bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Prześlij żądanie przywrócenia |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/usages/read | Odczytuje użycie konta bazy danych. |
> | Działanie | Microsoft.DocumentDB/databaseAccounts/write | Zaktualizuj konta bazy danych. |
> | Działanie | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft. DocumentDB, że VirtualNetwork lub podsieć jest usuwana |
> | Działanie | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Odczytaj stan operacji asynchronicznej deleteVirtualNetworkOrSubnets |
> | Działanie | Microsoft.DocumentDB/locations/operationsStatus/read | Odczytuje stan operacji asynchronicznych |
> | Działanie | Microsoft.DocumentDB/operationResults/read | Stan odczytu operacji asynchronicznej |
> | Działanie | Microsoft.DocumentDB/operations/read | Operacje odczytu dostępne dla programu Microsoft DocumentDBe  |
> | Działanie | Microsoft. DocumentDB/Register/Action |  Zarejestruj dostawcę zasobów Microsoft DocumentDB dla subskrypcji |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DomainRegistration/checkDomainAvailability/Action | Sprawdź, czy domena jest dostępna do zakupu |
> | Działanie | Microsoft. DomainRegistration/domeny/usuwanie | Usuń istniejącą domenę. |
> | Działanie | Microsoft. DomainRegistration/domens/domainownershipidentifiers/Delete | Usuwanie identyfikatora własności |
> | Działanie | Microsoft. DomainRegistration/domens/domainownershipidentifiers/Read | Identyfikatory własności listy |
> | Działanie | Microsoft. DomainRegistration/domens/domainownershipidentifiers/Read | Uzyskaj identyfikator własności |
> | Działanie | Microsoft. DomainRegistration/domens/domainownershipidentifiers/Write | Utwórz lub zaktualizuj identyfikator |
> | Działanie | Microsoft. DomainRegistration/domens/operationresults/Read | Pobierz operację domeny |
> | Działanie | Microsoft. DomainRegistration/domeny/odczyt | Pobierz listę domen |
> | Działanie | Microsoft. DomainRegistration/domeny/odczyt | Pobierz domenę |
> | Działanie | Microsoft. DomainRegistration/domeny/Odnów/akcja | Odnów istniejącą domenę. |
> | Działanie | Microsoft. DomainRegistration/domeny/zapis | Dodaj nową domenę lub zaktualizuj istniejącą |
> | Działanie | Microsoft. DomainRegistration/generateSsoRequest/Action | Wygeneruj żądanie zalogowania się do centrum sterowania domeną. |
> | Działanie | Microsoft. DomainRegistration/listDomainRecommendations/Action | Pobierz zalecenia dotyczące domeny listy na podstawie słów kluczowych |
> | Działanie | Microsoft. DomainRegistration/Operations/Read | Wyświetl listę wszystkich operacji z rejestracji domeny usługi App Service |
> | Działanie | Microsoft. DomainRegistration/Register/Action | Zarejestruj dostawcę zasobów domen firmy Microsoft dla subskrypcji |
> | Działanie | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Lista akcja umowy |
> | Działanie | Microsoft.DomainRegistration/topLevelDomains/Read | Pobierz domeny Toplevel |
> | Działanie | Microsoft.DomainRegistration/topLevelDomains/Read | Pobierz domenę Toplevel |
> | Działanie | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Sprawdzanie poprawności obiektu zakupu domeny bez jego przesyłania |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. EventGrid/domeny/usuwanie | Usuwanie domeny |
> | Działanie | Microsoft. EventGrid/domeny/listKeys/akcja | Wyświetl klucze dla domeny |
> | Działanie | Microsoft. EventGrid/domeny/dostawcy/Microsoft. Insights/logDefinitions/Read | Zezwala na dostęp do dzienników diagnostycznych |
> | Działanie | Microsoft. EventGrid/domeny/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla domen |
> | Działanie | Microsoft. EventGrid/domeny/odczyt | Odczytaj domenę |
> | Działanie | Microsoft. EventGrid/domeny/regenerateKey/akcja | Wygeneruj ponownie klucz dla domeny |
> | Działanie | Microsoft. EventGrid/domen/tematów/tematy/Usuń | Usuwanie tematu domeny |
> | Działanie | Microsoft.EventGrid/domains/topics/read | Odczytaj temat domeny |
> | Działanie | Microsoft. EventGrid/domen/tematy/Zapisz | Utwórz lub zaktualizuj temat domeny |
> | Działanie | Microsoft. EventGrid/domeny/zapis | Utwórz lub zaktualizuj domenę |
> | Działanie | Microsoft. EventGrid/eventSubscriptions/Delete | Usuń eventSubscription |
> | Działanie | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Pobierz pełny adres URL dla subskrypcji zdarzeń |
> | Działanie | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Działanie | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Działanie | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla eventSubscriptions |
> | Działanie | Microsoft.EventGrid/eventSubscriptions/read | Odczytaj eventSubscription |
> | Działanie | Microsoft.EventGrid/eventSubscriptions/write | Utwórz lub zaktualizuj eventSubscription |
> | Działanie | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla tematów |
> | Działanie | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Działanie | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla tematów |
> | Działanie | Microsoft.EventGrid/extensionTopics/read | Odczytaj extensionTopic. |
> | Działanie | Microsoft.EventGrid/locations/eventSubscriptions/read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | Działanie | Microsoft.EventGrid/locations/operationResults/read | Przeczytaj wynik operacji regionalnej |
> | Działanie | Microsoft.EventGrid/locations/operationsStatus/read | Odczytywanie stanu operacji regionalnej |
> | Działanie | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | Działanie | Microsoft.EventGrid/operationResults/read | Odczytaj wynik operacji |
> | Działanie | Microsoft.EventGrid/operations/read | Utwórz listę operacji EventGrid. |
> | Działanie | Microsoft.EventGrid/operationsStatus/read | Odczytywanie stanu operacji |
> | Działanie | Microsoft. EventGrid/Register/Action | Rejestruje subskrypcję dostawcy zasobów EventGrid. |
> | Działanie | Microsoft. EventGrid/tematy/Usuń | Usuwanie tematu |
> | Działanie | Microsoft.EventGrid/topics/listKeys/action | Lista kluczy tematu |
> | Działanie | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla tematów |
> | Działanie | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Działanie | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/logDefinitions/Read | Zezwala na dostęp do dzienników diagnostycznych |
> | Działanie | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla tematów |
> | Działanie | Microsoft.EventGrid/topics/read | Zapoznaj się z tematem |
> | Działanie | Microsoft.EventGrid/topics/regenerateKey/action | Wygeneruj ponownie klucz dla tematu |
> | Działanie | Microsoft. EventGrid/tematy/zapis | Utwórz lub zaktualizuj temat |
> | Działanie | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | Działanie | Microsoft.EventGrid/topictypes/eventtypes/read | Odczytaj zdarzenia typu obsługiwane przez element temattype |
> | Działanie | Microsoft. EventGrid/topictypes/odczyt | Odczytaj element tematu |
> | Działanie | Microsoft.EventGrid/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. EventHub/availableClusterRegions/odczyt | Operacja odczytu, aby wyświetlić listę dostępnych wstępnie inicjowanych klastrów według regionów platformy Azure. |
> | Działanie | Microsoft. EventHub/checkNameAvailability/akcja | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft. EventHub/checkNamespaceAvailability/akcja | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Działanie | Microsoft. EventHub/klastry/usuwanie | Usuwa istniejący zasób klastra. |
> | Działanie | Microsoft. EventHub/klastry/przestrzenie nazw/odczyt | Wyświetl listę identyfikatorów Azure Resource Manager nazw dla przestrzeni nazw w klastrze. |
> | Działanie | Microsoft.EventHub/clusters/operationresults/read | Pobierz stan asynchronicznej operacji klastra. |
> | Działanie | Microsoft. EventHub/klastry/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk klastra |
> | Działanie | Microsoft.EventHub/clusters/read | Pobiera opis zasobu klastra |
> | Działanie | Microsoft. EventHub/klastry/zapis | Tworzy lub modyfikuje istniejący zasób klastra. |
> | Działanie | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Usuwa reguły sieci wirtualnej w dostawcy zasobów EventHub dla określonej sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Działanie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Działanie | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Działanie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Działanie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Action | Operacja aktualizowania centrum EventHub. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji EventHub |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do centrum EventHub |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/odczyt |  Pobierz listę reguł autoryzacji EventHub |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Write | Utwórz reguły autoryzacji EventHub i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Działanie | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operacja usuwania zasobu w ramach odbiorcy |
> | Działanie | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Pobierz listę opisów zasobów dla odbiorców |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventHubs/consumergroups/Write | Utwórz lub zaktualizuj właściwości odbiorcy. |
> | Działanie | Microsoft.EventHub/namespaces/eventhubs/Delete | Operacja usuwania zasobu EventHub |
> | Działanie | Microsoft.EventHub/namespaces/eventhubs/read | Pobierz listę opisów zasobów EventHub |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/Write | Utwórz lub zaktualizuj właściwości EventHub. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Delete | Usuń zasób filtru IP |
> | Działanie | Microsoft.EventHub/namespaces/ipFilterRules/read | Pobierz zasób filtru IP |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Write | Utwórz zasób filtru IP |
> | Akcja dataaction | Microsoft. EventHub/przestrzenie nazw/komunikaty/odbieranie/akcja | Odbieranie komunikatów |
> | Akcja dataaction | Microsoft. EventHub/przestrzenie nazw/komunikaty/wysyłanie/akcja | Wysyłanie komunikatów |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/networkruleset/Delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/networkruleset/Read | Pobiera zasób NetworkRuleSet |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/networkruleset/Write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft.EventHub/namespaces/networkrulesets/delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft.EventHub/namespaces/networkrulesets/read | Pobiera zasób NetworkRuleSet |
> | Działanie | Microsoft.EventHub/namespaces/networkrulesets/write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/operationresults/Read | Pobierz stan operacji przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobierz listę opisów zasobów dzienników przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Działanie | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw ACS |
> | Działanie | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Pobiera zasób reguły sieci wirtualnej |
> | Działanie | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Działanie | Microsoft.EventHub/operations/read | Pobierz operacje |
> | Działanie | Microsoft. EventHub/rejestr/akcja | Rejestruje subskrypcję dostawcy zasobów centrum zdarzeń i umożliwia tworzenie zasobów EventHub |
> | Działanie | Microsoft.EventHub/sku/read | Pobierz listę opisów zasobów jednostki SKU |
> | Działanie | Microsoft.EventHub/sku/regions/read | Pobierz listę opisów zasobów SkuRegions |
> | Działanie | Microsoft. EventHub/Wyrejestruj/akcja | Rejestruje dostawcę zasobów centrum zdarzeń |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Features/features/read | Pobiera funkcje subskrypcji. |
> | Działanie | Microsoft. Features/Operations/Read | Pobiera listę operacji. |
> | Działanie | Microsoft. Features/Providers/Features/Read | Pobiera funkcję subskrypcji dla danego dostawcy zasobów. |
> | Działanie | Microsoft. Features/Providers/Features/Register/Action | Rejestruje funkcję subskrypcji dla danego dostawcy zasobów. |
> | Działanie | Microsoft. Features/Providers/Features/Unregister/Action | Wyrejestrowuje funkcję subskrypcji w ramach danego dostawcy zasobów. |
> | Działanie | Microsoft. Features/Register/Action | Rejestruje funkcję subskrypcji. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. GuestConfiguration/guestConfigurationAssignments/Delete | Usuń przypisanie konfiguracji gościa. |
> | Działanie | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Pobierz przypisanie konfiguracji gościa. |
> | Działanie | Microsoft. GuestConfiguration/guestConfigurationAssignments/Reports/Read | Pobierz raport dotyczący przypisywania konfiguracji gościa. |
> | Działanie | Microsoft. GuestConfiguration/guestConfigurationAssignments/Write | Utwórz nowe przypisanie konfiguracji gościa. |
> | Działanie | Microsoft. GuestConfiguration/Operations/Read | Pobiera operacje dla dostawcy zasobów Microsoft. GuestConfiguration |
> | Działanie | Microsoft. GuestConfiguration/Register/Action | Rejestruje subskrypcję dostawcy zasobów Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. HDInsight/Klastry/aplikacje/usuwanie | Usuwanie aplikacji dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/aplikacje/odczyt | Pobierz aplikację dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/aplikacje/zapis | Utwórz lub zaktualizuj aplikację dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/changerdpsetting/akcja | Zmiana ustawienia protokołu RDP dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/konfiguracje/akcja | Aktualizowanie konfiguracji klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/konfiguracje/akcja | Pobierz konfiguracje klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/konfiguracje/odczyt | Pobierz konfiguracje klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/usuwanie | Usuwanie klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/rozszerzenia/usuwanie | Usuwanie rozszerzenia klastra dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/rozszerzenia/odczyt | Pobierz rozszerzenie klastra dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/rozszerzenia/zapis | Utwórz rozszerzenie klastra dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/getGatewaySettings/akcja | Pobierz ustawienia bramy dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla klastra usługi HDInsight zasobów |
> | Działanie | Microsoft. HDInsight/Klastry/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla klastra usługi HDInsight zasobów |
> | Działanie | Microsoft. HDInsight/Klastry/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/odczyt | Pobierz szczegóły dotyczące klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/role/zmiana/akcja | Zmiana rozmiaru klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/updateGatewaySettings/akcja | Aktualizowanie ustawień bramy dla klastra usługi HDInsight |
> | Działanie | Microsoft. HDInsight/Klastry/zapis | Utwórz lub zaktualizuj klaster usługi HDInsight |
> | Działanie | Microsoft. HDInsight/lokalizacje/możliwości/odczyt | Uzyskaj możliwości subskrypcji |
> | Działanie | Microsoft. HDInsight/lokalizacje/checkNameAvailability/odczyt | Sprawdź dostępność nazwy |
> | Działanie | Microsoft. HDInsight/rejestr/akcja | Zarejestruj dostawcę zasobów usługi HDInsight dla subskrypcji |
> | Działanie | Microsoft. HDInsight/Wyrejestruj/akcja | Wyrejestruj dostawcę zasobów usługi HDInsight dla subskrypcji |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.ImportExport/jobs/delete | Usuwa istniejące zadanie. |
> | Działanie | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Pobiera klucze funkcji BitLocker dla określonego zadania. |
> | Działanie | Microsoft.ImportExport/jobs/read | Pobiera właściwości określonego zadania lub zwraca listę zadań. |
> | Działanie | Microsoft.ImportExport/jobs/write | Tworzy zadanie z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego zadania. |
> | Działanie | Microsoft. ImportExport/lokalizacje/odczyt | Pobiera właściwości dla określonej lokalizacji lub zwraca listę lokalizacji. |
> | Działanie | Microsoft. ImportExport/Operations/Read | Pobiera operacje obsługiwane przez dostawcę zasobów. |
> | Działanie | Microsoft. ImportExport/Register/Action | Rejestruje subskrypcję dostawcy zasobów importu/eksportu i umożliwia tworzenie zadań importu/eksportu. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Insights/ActionGroups/Delete | Usuwanie grupy akcji |
> | Działanie | Microsoft. Insights/ActionGroups/Read | Odczytaj grupę akcji |
> | Działanie | Microsoft. Insights/ActionGroups/Write | Utwórz lub Zaktualizuj grupę akcji |
> | Działanie | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Uaktywniono alert dziennika aktywności |
> | Działanie | Microsoft.Insights/ActivityLogAlerts/Delete | Usuwanie alertu dziennika aktywności |
> | Działanie | Microsoft.Insights/ActivityLogAlerts/Read | Odczytaj alert dotyczący dziennika aktywności |
> | Działanie | Microsoft.Insights/ActivityLogAlerts/Write | Utwórz lub zaktualizuj alert dotyczący dziennika aktywności |
> | Działanie | Microsoft.Insights/AlertRules/Activated/Action | Uaktywniono alert dotyczący metryki klasycznej |
> | Działanie | Microsoft.Insights/AlertRules/Delete | Usuwanie alertu dotyczącego klasycznej metryki |
> | Działanie | Microsoft.Insights/AlertRules/Incidents/Read | Odczytaj zdarzenie klasycznego alertu dotyczącego metryki |
> | Działanie | Microsoft.Insights/AlertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | Działanie | Microsoft.Insights/AlertRules/Resolved/Action | Rozwiązano alert dotyczący metryki klasycznej |
> | Działanie | Microsoft.Insights/AlertRules/Throttled/Action | Ograniczona reguła alertu dotyczącego metryki klasycznej |
> | Działanie | Microsoft. Insights/AlertRules/Write | Utwórz lub zaktualizuj klasyczny alert dotyczący metryki |
> | Działanie | Microsoft.Insights/AutoscaleSettings/Delete | Usuń ustawienie automatycznego skalowania |
> | Działanie | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Odczytaj definicje dzienników |
> | Działanie | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft.Insights/AutoscaleSettings/Read | Odczytaj ustawienie automatycznego skalowania |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Scaledown/Action | Inicjowano skalowanie w poziomie automatycznego skalowania |
> | Działanie | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Ukończono Skalowanie automatyczne w dół |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Scaleup/Action | Zainicjowanie skalowania automatycznego skalowania w górę |
> | Działanie | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Ukończenie skalowania automatycznego skalowania w górę |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Write | Utwórz lub zaktualizuj ustawienie automatycznego skalowania |
> | Działanie | Microsoft. Insights/Baseline/Read | Odczytaj linię bazową metryki (wersja zapoznawcza) |
> | Działanie | Microsoft. Insights/CalculateBaseline/Read | Oblicz linię bazową dla wartości metryk (wersja zapoznawcza) |
> | Działanie | Microsoft. Insights/Components/AnalyticsItems/Delete | Usuwanie elementu analizy Application Insights |
> | Działanie | Microsoft. Insights/Components/AnalyticsItems/Read | Odczytywanie elementu analizy Application Insights |
> | Działanie | Microsoft. Insights/Components/AnalyticsItems/Write | Pisanie elementu analizy Application Insights |
> | Działanie | Microsoft. Insights/Components/AnalyticsTables/Action | Akcja tabeli Application Insights Analytics |
> | Działanie | Microsoft. Insights/Components/AnalyticsTables/Delete | Usuwanie schematu tabeli analizy Application Insights |
> | Działanie | Microsoft. Insights/Components/AnalyticsTables/Read | Odczytywanie schematu tabeli analizy Application Insights |
> | Działanie | Microsoft. Insights/Components/AnalyticsTables/Write | Pisanie schematu tabeli analizy Application Insights |
> | Działanie | Microsoft. Insights/Components/Annotations/Delete | Usuwanie adnotacji Application Insights |
> | Działanie | Microsoft. Insights/Components/Annotations/Read | Odczytywanie adnotacji Application Insights |
> | Działanie | Microsoft. Insights/Components/Annotations/Write | Pisanie adnotacji Application Insights |
> | Działanie | Microsoft. Insights/Components/API/Read | Odczytywanie interfejsu API danych Application Insights składników |
> | Działanie | Microsoft. Insights/Components/ApiKeys/Action | Generowanie klucza interfejsu API Application Insights |
> | Działanie | Microsoft. Insights/Components/ApiKeys/Delete | Usuwanie klucza interfejsu API Application Insights |
> | Działanie | Microsoft. Insights/Components/ApiKeys/Read | Odczytywanie klucza interfejsu API Application Insights |
> | Działanie | Microsoft. Insights/Components/BillingPlanForComponent/Read | Odczytywanie planu rozliczeniowego dla składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/CurrentBillingFeatures/Read | Odczytywanie bieżących funkcji rozliczeń dla składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/CurrentBillingFeatures/Write | Pisanie bieżących funkcji rozliczeń dla składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/DailyCapReached/Action | Osiągnięto dzienny limit dla składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/DailyCapWarningThresholdReached/Action | Osiągnięto próg ostrzeżenia dziennego limitu dla składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/DefaultWorkItemConfig/Read | Odczytywanie Application Insights domyślnej konfiguracji integracji ALM |
> | Działanie | Microsoft. Insights/Components/Delete | Usuwanie konfiguracji składnika usługi Application Insights |
> | Działanie | Microsoft. Insights/Components/Events/Read | Pobierz dzienniki z Application Insights przy użyciu formatu zapytania OData |
> | Działanie | Microsoft. Insights/Components/ExportConfiguration/Action | Akcja eksportowania ustawień Application Insights |
> | Działanie | Microsoft. Insights/Components/ExportConfiguration/Delete | Usuwanie ustawień eksportu Application Insights |
> | Działanie | Microsoft. Insights/Components/ExportConfiguration/Read | Odczytywanie ustawień eksportu Application Insights |
> | Działanie | Microsoft. Insights/Components/ExportConfiguration/Write | Zapisywanie Application Insights ustawienia eksportu |
> | Działanie | Microsoft. Insights/Components/ExtendQueries/Read | Odczytywanie rozszerzonych wyników zapytania składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/Ulubione/Usuń | Usuwanie Application Insights ulubionych |
> | Działanie | Microsoft. Insights/Components/Ulubione/Odczytaj | Odczytywanie Application Insights ulubionych |
> | Działanie | Microsoft. Insights/Components/Ulubione/zapisuj | Pisanie Application Insights ulubionych |
> | Działanie | Microsoft. Insights/Components/FeatureCapabilities/Read | Odczytywanie możliwości funkcji składników Application Insights |
> | Działanie | Microsoft. Insights/Components/GetAvailableBillingFeatures/Read | Odczytywanie dostępnych funkcji rozliczeń Application Insights składników |
> | Działanie | Microsoft. Insights/Components/GetToken/Read | Odczytywanie tokenu składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/MetricDefinitions/Read | Odczytywanie definicji metryk składników Application Insights |
> | Działanie | Microsoft. Insights/Components/Metrics/Read | Odczytywanie metryk Application Insights składników |
> | Działanie | Microsoft. Insights/Components/Move/Action | Przenoszenie składnika Application Insights do innej grupy zasobów lub subskrypcji |
> | Działanie | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Usuwanie Application Insights osobistego elementu analizy |
> | Działanie | Microsoft. Insights/Components/MyAnalyticsItems/Read | Odczytywanie Application Insights osobistego elementu analizy |
> | Działanie | Microsoft. Insights/Components/MyAnalyticsItems/Write | Pisanie Application Insights osobistego elementu analizy |
> | Działanie | Microsoft. Insights/Components/webulubionych/odczyt | Odczytywanie Application Insights osobistego elementu ulubionego |
> | Działanie | Microsoft. Insights/Components/Operations/Read | Pobieranie stanu długotrwałych operacji w Application Insights |
> | Działanie | Microsoft. Insights/Components/PricingPlans/Read | Odczytywanie planu cenowego składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/PricingPlans/Write | Pisanie planu cenowego składnika Application Insights |
> | Działanie | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Odczytywanie konfiguracji wykrywania proaktywnego Application Insights |
> | Działanie | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Pisanie Application Insights konfiguracji wykrywania proaktywnego |
> | Działanie | Microsoft. Insights/Components/Providers/Microsoft. Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft. Insights/Components/Przeczyść/akcja | Przeczyszczanie danych z Application Insights |
> | Działanie | Microsoft. Insights/Components/Query/Read | Uruchamianie zapytań względem dzienników Application Insights |
> | Działanie | Microsoft. Insights/Components/QuotaStatus/Read | Odczytywanie stanu przydziału składników Application Insights |
> | Działanie | Microsoft. Insights/Components/Read | Odczytywanie konfiguracji składnika usługi Application Insights |
> | Działanie | Microsoft. Insights/Components/SyntheticMonitorLocations/Read | Odczytywanie Application Insights lokalizacji WebTest |
> | Działanie | Microsoft. Insights/Components/webtests/Read | Reading a webtest configuration |
> | Działanie | Microsoft.Insights/Components/WorkItemConfigs/Delete | Usuwanie Application Insights ALM Integration Configuration |
> | Działanie | Microsoft. Insights/Components/WorkItemConfigs/Read | Odczytywanie konfiguracji integracji Application Insights ALM |
> | Działanie | Microsoft.Insights/Components/WorkItemConfigs/Write | Pisanie Application Insights ALM Integration Configuration |
> | Działanie | Microsoft. Insights/Components/Write | Zapisywanie w konfiguracji składnika usługi Application Insights |
> | Działanie | Microsoft. Insights/DataCollectionRuleAssociations/Delete | Usuwanie skojarzenia zasobu z regułą zbierania danych |
> | Działanie | Microsoft. Insights/DataCollectionRuleAssociations/Read | Odczytaj skojarzenie zasobu z regułą zbierania danych |
> | Działanie | Microsoft. Insights/DataCollectionRuleAssociations/Write | Utwórz lub zaktualizuj skojarzenie zasobu z regułą zbierania danych |
> | Akcja dataaction | Microsoft. Insights/DataCollectionRules/Data/zapis | Wysyłanie danych do reguły zbierania danych |
> | Działanie | Microsoft. Insights/DataCollectionRules/Delete | Usuwanie reguły zbierania danych |
> | Działanie | Microsoft. Insights/DataCollectionRules/Read | Odczytaj regułę zbierania danych |
> | Działanie | Microsoft. Insights/DataCollectionRules/Write | Utwórz lub zaktualizuj regułę zbierania danych |
> | Działanie | Microsoft. Insights/DiagnosticSettings/Delete | Usuń ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft. Insights/DiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft. Insights/DiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft.Insights/EventCategories/Read | Odczytaj dostępne kategorie zdarzeń dziennika aktywności |
> | Działanie | Microsoft.Insights/eventtypes/digestevents/Read | Odczytaj podsumowanie typu zdarzenia zarządzania |
> | Działanie | Microsoft.Insights/eventtypes/values/Read | Odczytaj zdarzenia dziennika aktywności |
> | Działanie | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Usuń ustawienie diagnostyczne dziennika przepływu sieci |
> | Działanie | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne dziennika przepływu sieci |
> | Działanie | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne dziennika przepływu sieci |
> | Działanie | Microsoft. Insights/ListMigrationDate/Action | Pobierz datę migracji subskrypcji |
> | Działanie | Microsoft. Insights/ListMigrationDate/Read | Pobierz datę migracji subskrypcji |
> | Działanie | Microsoft.Insights/LogDefinitions/Read | Odczytaj definicje dzienników |
> | Działanie | Microsoft.Insights/LogProfiles/Delete | Usuwanie profilu dziennika aktywności |
> | Działanie | Microsoft.Insights/LogProfiles/Read | Odczytaj profil dziennika aktywności |
> | Działanie | Microsoft. Insights/LogProfiles/Write | Utwórz lub zaktualizuj profil dziennika aktywności |
> | Działanie | Microsoft. Insights/Logs/ADAssessmentRecommendation/Read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | Działanie | Microsoft.Insights/Logs/ADReplicationResult/Read | Odczytaj dane z tabeli ADReplicationResult |
> | Działanie | Microsoft. Insights/Logs/ADSecurityAssessmentRecommendation/Read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/alerty/odczyt | Odczytaj dane z tabeli alertów |
> | Działanie | Microsoft. Insights/Logs/AlertHistory/Read | Odczytaj dane z tabeli AlertHistory |
> | Działanie | Microsoft. Insights/Logs/ApplicationInsights/Read | Odczytaj dane z tabeli ApplicationInsights |
> | Działanie | Microsoft.Insights/Logs/AzureActivity/Read | Odczytywanie danych z tabeli platformy Azure |
> | Działanie | Microsoft.Insights/Logs/AzureMetrics/Read | Odczytaj dane z tabeli AzureMetrics |
> | Działanie | Microsoft. Insights/Logs/BoundPort/Read | Odczytaj dane z tabeli BoundPort |
> | Działanie | Microsoft.Insights/Logs/CommonSecurityLog/Read | Odczytaj dane z tabeli CommonSecurityLog |
> | Działanie | Microsoft.Insights/Logs/ComputerGroup/Read | Odczytywanie danych z tabeli komputerów |
> | Działanie | Microsoft. Insights/Logs/Zmianakonfiguracji/Read | Odczytaj dane z tabeli Zmianakonfiguracji |
> | Działanie | Microsoft. Insights/Logs/ConfigurationData/Read | Odczytaj dane z tabeli ConfigurationData |
> | Działanie | Microsoft. Insights/Logs/ContainerImageInventory/Read | Odczytaj dane z tabeli ContainerImageInventory |
> | Działanie | Microsoft. Insights/Logs/ContainerInventory/Read | Odczytaj dane z tabeli ContainerInventory |
> | Działanie | Microsoft. Insights/Logs/ContainerLog/Read | Odczytaj dane z tabeli ContainerLog |
> | Działanie | Microsoft.Insights/Logs/ContainerServiceLog/Read | Odczytaj dane z tabeli ContainerServiceLog |
> | Działanie | Microsoft.Insights/Logs/DeviceAppCrash/Read | Odczytaj dane z tabeli DeviceAppCrash |
> | Działanie | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Odczytaj dane z tabeli DeviceAppLaunch |
> | Działanie | Microsoft.Insights/Logs/DeviceCalendar/Read | Odczytaj dane z tabeli DeviceCalendar |
> | Działanie | Microsoft. Insights/Logs/DeviceCleanup/Read | Odczytaj dane z tabeli DeviceCleanup |
> | Działanie | Microsoft.Insights/Logs/DeviceConnectSession/Read | Odczytaj dane z tabeli DeviceConnectSession |
> | Działanie | Microsoft.Insights/Logs/DeviceEtw/Read | Odczytaj dane z tabeli DeviceEtw |
> | Działanie | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | Działanie | Microsoft.Insights/Logs/DeviceHealth/Read | Odczytaj dane z tabeli zameldowaniem |
> | Działanie | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Odczytaj dane z tabeli DeviceHeartbeat |
> | Działanie | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | Działanie | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | Działanie | Microsoft.Insights/Logs/DeviceSleepState/Read | Odczytaj dane z tabeli DeviceSleepState |
> | Działanie | Microsoft. Insights/Logs/DHAppFailure/Read | Odczytaj dane z tabeli DHAppFailure |
> | Działanie | Microsoft.Insights/Logs/DHAppReliability/Read | Odczytaj dane z tabeli DHAppReliability |
> | Działanie | Microsoft.Insights/Logs/DHDriverReliability/Read | Odczytaj dane z tabeli DHDriverReliability |
> | Działanie | Microsoft. Insights/Logs/DHLogonFailures/Read | Odczytaj dane z tabeli DHLogonFailures |
> | Działanie | Microsoft.Insights/Logs/DHLogonMetrics/Read | Odczytaj dane z tabeli DHLogonMetrics |
> | Działanie | Microsoft. Insights/Logs/DHOSCrashData/Read | Odczytaj dane z tabeli DHOSCrashData |
> | Działanie | Microsoft. Insights/Logs/DHOSReliability/Read | Odczytaj dane z tabeli DHOSReliability |
> | Działanie | Microsoft.Insights/Logs/DHWipAppLearning/Read | Odczytaj dane z tabeli DHWipAppLearning |
> | Działanie | Microsoft.Insights/Logs/DnsEvents/Read | Odczytaj dane z tabeli DnsEvents |
> | Działanie | Microsoft.Insights/Logs/DnsInventory/Read | Odczytaj dane z tabeli DnsInventory |
> | Działanie | Microsoft. Insights/Logs/ETWEvent/Read | Odczytaj dane z tabeli ETWEvent |
> | Działanie | Microsoft. Insights/Logs/Event/Read | Odczytaj dane z tabeli zdarzeń |
> | Działanie | Microsoft. Insights/Logs/ExchangeAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | Działanie | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | Działanie | Microsoft. Insights/dzienniki/puls/odczyt | Odczytaj dane z tabeli pulsu |
> | Działanie | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/InboundConnection/Read | Odczytaj dane z tabeli InboundConnection |
> | Działanie | Microsoft. Insights/Logs/KubeNodeInventory/Read | Odczytaj dane z tabeli KubeNodeInventory |
> | Działanie | Microsoft. Insights/Logs/KubePodInventory/Read | Odczytaj dane z tabeli KubePodInventory |
> | Działanie | Microsoft.Insights/Logs/LinuxAuditLog/Read | Odczytaj dane z tabeli LinuxAuditLog |
> | Działanie | Microsoft. Insights/Logs/MAApplication/Read | Odczytaj dane z tabeli MAApplication |
> | Działanie | Microsoft.Insights/Logs/MAApplicationHealth/Read | Odczytaj dane z tabeli MAApplicationHealth |
> | Działanie | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | Działanie | Microsoft. Insights/Logs/MAApplicationHealthIssues/Read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | Działanie | Microsoft. Insights/Logs/MAApplicationInstance/Read | Odczytaj dane z tabeli MAApplicationInstance |
> | Działanie | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | Działanie | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Odczytaj dane z tabeli MAApplicationReadiness |
> | Działanie | Microsoft.Insights/Logs/MADeploymentPlan/Read | Odczytaj dane z tabeli MADeploymentPlan |
> | Działanie | Microsoft.Insights/Logs/MADevice/Read | Odczytaj dane z tabeli MADevice |
> | Działanie | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Odczytaj dane z tabeli MADevicePnPHealth |
> | Działanie | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | Działanie | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | Działanie | Microsoft.Insights/Logs/MADeviceReadiness/Read | Odczytaj dane z tabeli MADeviceReadiness |
> | Działanie | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | Działanie | Microsoft.Insights/Logs/MADriverReadiness/Read | Odczytaj dane z tabeli MADriverReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddin/Read | Odczytaj dane z tabeli MAOfficeAddin |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddinHealth/Read | Odczytaj dane z tabeli MAOfficeAddinHealth |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddinHealthIssues/Read | Odczytaj dane z tabeli MAOfficeAddinHealthIssues |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddinInstance/Read | Odczytaj dane z tabeli MAOfficeAddinInstance |
> | Działanie | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinInstanceReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddinReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeApp/Read | Odczytaj dane z tabeli MAOfficeApp |
> | Działanie | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Odczytaj dane z tabeli MAOfficeAppHealth |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAppInstance/Read | Odczytaj dane z tabeli MAOfficeAppInstance |
> | Działanie | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Odczytaj dane z tabeli MAOfficeAppReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeBuildInfo/Read | Odczytaj dane z tabeli MAOfficeBuildInfo |
> | Działanie | Microsoft. Insights/Logs/MAOfficeCurrencyAssessment/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessment |
> | Działanie | Microsoft. Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | Działanie | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroHealth/Read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroHealthIssues/Read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroIssueReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroSummary/Read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | Działanie | Microsoft.Insights/Logs/MAOfficeSuite/Read | Odczytaj dane z tabeli MAOfficeSuite |
> | Działanie | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | Działanie | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | Działanie | Microsoft. Insights/Logs/MAWindowsBuildInfo/Read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | Działanie | Microsoft. Insights/Logs/MAWindowsCurrencyAssessment/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | Działanie | Microsoft. Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Działanie | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | Działanie | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | Działanie | Microsoft.Insights/Logs/NetworkMonitoring/Read | Odczytaj dane z tabeli NetworkMonitoring |
> | Działanie | Microsoft.Insights/Logs/OfficeActivity/Read | Odczytaj dane z tabeli pakietu Office |
> | Działanie | Microsoft. Insights/Logs/dzienników/operacji/odczytu | Odczytaj dane z tabeli operacji |
> | Działanie | Microsoft. Insights/Logs/OutboundConnection/Read | Odczytaj dane z tabeli OutboundConnection |
> | Działanie | Microsoft. Insights/Logs/perf/Read | Odczytaj dane z tabeli wydajności |
> | Działanie | Microsoft.Insights/Logs/ProtectionStatus/Read | Odczytaj dane z tabeli ProtectionStatus |
> | Działanie | Microsoft. Insights/Logs/odczyt | Odczytywanie danych ze wszystkich dzienników |
> | Działanie | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | Działanie | Microsoft.Insights/Logs/ReservedCommonFields/Read | Odczytaj dane z tabeli ReservedCommonFields |
> | Działanie | Microsoft. Insights/Logs/SCCMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SCOMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SecurityAlert/Read | Odczytaj dane z tabeli SecurityAlert |
> | Działanie | Microsoft. Insights/Logs/SecurityBaseline/Read | Odczytaj dane z tabeli SecurityBaseline |
> | Działanie | Microsoft. Insights/Logs/SecurityBaselineSummary/Read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | Działanie | Microsoft.Insights/Logs/SecurityDetection/Read | Odczytaj dane z tabeli SecurityDetection |
> | Działanie | Microsoft. Insights/Logs/SecurityEvent/Read | Odczytaj dane z tabeli SecurityEvent |
> | Działanie | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | Działanie | Microsoft. Insights/Logs/ServiceFabricReliableActorEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | Działanie | Microsoft. Insights/Logs/ServiceFabricReliableServiceEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | Działanie | Microsoft. Insights/Logs/SfBAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | Działanie | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SPAssessmentRecommendation/Read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | Działanie | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SQLQueryPerformance/Read | Odczytaj dane z tabeli SQLQueryPerformance |
> | Działanie | Microsoft.Insights/Logs/Syslog/Read | Odczytaj dane z tabeli dziennika systemowego |
> | Działanie | Microsoft. Insights/Logs/SysmonEvent/Read | Odczytaj dane z tabeli SysmonEvent |
> | Działanie | Microsoft. Insights/dzienniki/tabele. Custom/Read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | Działanie | Microsoft.Insights/Logs/UAApp/Read | Odczytaj dane z tabeli UAApp |
> | Działanie | Microsoft. Insights/Logs/UAComputer/Read | Odczytaj dane z tabeli UAComputer |
> | Działanie | Microsoft. Insights/Logs/UAComputerRank/Read | Odczytaj dane z tabeli UAComputerRank |
> | Działanie | Microsoft.Insights/Logs/UADriver/Read | Odczytaj dane z tabeli UADriver |
> | Działanie | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Odczytaj dane z tabeli UADriverProblemCodes |
> | Działanie | Microsoft. Insights/Logs/UAFeedback/Read | Odczytaj dane z tabeli UAFeedback |
> | Działanie | Microsoft. Insights/Logs/UAHardwareSecurity/Read | Odczytaj dane z tabeli UAHardwareSecurity |
> | Działanie | Microsoft. Insights/Logs/UAIESiteDiscovery/Read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | Działanie | Microsoft. Insights/Logs/UAOfficeAddIn/Read | Odczytaj dane z tabeli UAOfficeAddIn |
> | Działanie | Microsoft. Insights/Logs/UAProposedActionPlan/Read | Odczytaj dane z tabeli UAProposedActionPlan |
> | Działanie | Microsoft. Insights/Logs/UASysReqIssue/Read | Odczytaj dane z tabeli UASysReqIssue |
> | Działanie | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Odczytaj dane z tabeli UAUpgradedComputer |
> | Działanie | Microsoft. Insights/Logs/Update/Read | Odczytaj dane z tabeli aktualizacji |
> | Działanie | Microsoft.Insights/Logs/UpdateRunProgress/Read | Odczytaj dane z tabeli UpdateRunProgress |
> | Działanie | Microsoft. Insights/Logs/UpdateSummary/Read | Odczytaj dane z tabeli UpdateSummary |
> | Działanie | Microsoft. Insights/Logs/dzienniki/użycie/odczyt | Odczytywanie danych z tabeli użycia |
> | Działanie | Microsoft. Insights/Logs/W3CIISLog/Read | Odczytaj dane z tabeli W3CIISLog |
> | Działanie | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | Działanie | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | Działanie | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | Działanie | Microsoft. Insights/Logs/WDAVStatus/Read | Odczytaj dane z tabeli WDAVStatus |
> | Działanie | Microsoft. Insights/Logs/WDAVThreat/Read | Odczytaj dane z tabeli WDAVThreat |
> | Działanie | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | Działanie | Microsoft.Insights/Logs/WindowsFirewall/Read | Odczytaj dane z tabeli WindowsFirewall |
> | Działanie | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | Działanie | Microsoft.Insights/Logs/WireData/Read | Odczytaj dane z tabeli typowe |
> | Działanie | Microsoft. Insights/Logs/WUDOAggregatedStatus/Read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | Działanie | Microsoft. Insights/Logs/WUDOStatus/Read | Odczytaj dane z tabeli WUDOStatus |
> | Działanie | Microsoft.Insights/MetricAlerts/Delete | Usuwanie alertu metryki |
> | Działanie | Microsoft.Insights/MetricAlerts/Read | Odczytaj alert dotyczący metryki |
> | Działanie | Microsoft. Insights/MetricAlerts/status/Read | Odczytaj stan alertu metryki |
> | Działanie | Microsoft. Insights/MetricAlerts/Write | Utwórz lub zaktualizuj alert dotyczący metryki |
> | Działanie | Microsoft. Insights/MetricBaselines/Read | Odczytaj linie bazowe metryki |
> | Działanie | Microsoft. Insights/MetricDefinitions/Microsoft. Insights/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft. Insights/MetricDefinitions/Providers/Microsoft. Insights/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft.Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft. Insights/Metricnamespaces/Read | Odczytaj przestrzenie nazw metryk |
> | Działanie | Microsoft. Insights/Metrics/Action | Akcja metryki |
> | Działanie | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Odczytaj metryki |
> | Działanie | Microsoft. Insights/Metrics/Providers/Metrics/Read | Odczytaj metryki |
> | Działanie | Microsoft.Insights/Metrics/Read | Odczytaj metryki |
> | Akcja dataaction | Microsoft. Insights/Metrics/Write | Metryki zapisu |
> | Działanie | Microsoft. Insights/MigrateToNewpricingModel/Action | Migruj subskrypcję do nowego modelu cen |
> | Działanie | Microsoft. Insights/Operations/Read | Operacje odczytu |
> | Działanie | Microsoft.Insights/Register/Action | Register the Microsoft Insights provider |
> | Działanie | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Rollback subscription to legacy pricing model |
> | Działanie | Microsoft.Insights/ScheduledQueryRules/Delete | Deleting a scheduled query rule |
> | Działanie | Microsoft.Insights/ScheduledQueryRules/Read | Reading a scheduled query rule |
> | Działanie | Microsoft.Insights/ScheduledQueryRules/Write | Writing a scheduled query rule |
> | Działanie | Microsoft.Insights/Tenants/Register/Action | Initializes the Microsoft Insights provider |
> | Działanie | Microsoft.Insights/Unregister/Action | Register the Microsoft Insights provider |
> | Działanie | Microsoft.Insights/Webtests/Delete | Deleting a webtest configuration |
> | Działanie | Microsoft.Insights/Webtests/GetToken/Read | Reading a webtest token |
> | Działanie | Microsoft.Insights/Webtests/MetricDefinitions/Read | Reading a webtest metric definitions |
> | Działanie | Microsoft.Insights/Webtests/Metrics/Read | Reading a webtest metrics |
> | Działanie | Microsoft.Insights/Webtests/Read | Reading a webtest configuration |
> | Działanie | Microsoft.Insights/Webtests/Write | Writing to a webtest configuration |
> | Działanie | Microsoft.Insights/Workbooks/Delete | Usuwanie skoroszytu |
> | Działanie | Microsoft.Insights/Workbooks/Read | Read a workbook |
> | Działanie | Microsoft.Insights/Workbooks/Write | Create or update a workbook |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Intune/diagnosticsettings/delete | Usuwanie ustawień diagnostycznych |
> | Działanie | Microsoft.Intune/diagnosticsettings/read | Odczytywanie ustawień diagnostycznych |
> | Działanie | Microsoft.Intune/diagnosticsettings/write | Zapisywanie ustawień diagnostycznych |
> | Działanie | Microsoft.Intune/diagnosticsettingscategories/read | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.IoTCentral/appTemplates/action | Gets all the available application templates on Azure IoT Central |
> | Działanie | Microsoft.IoTCentral/checkNameAvailability/action | Checks if an IoT Central Application name is available |
> | Działanie | Microsoft.IoTCentral/checkSubdomainAvailability/action | Checks if an IoT Central Application subdomain is available |
> | Działanie | Microsoft.IoTCentral/IoTApps/delete | Deletes an IoT Central Applications |
> | Działanie | Microsoft.IoTCentral/IoTApps/read | Gets a single IoT Central Application |
> | Działanie | Microsoft.IoTCentral/IoTApps/write | Creates or Updates an IoT Central Applications |
> | Działanie | Microsoft.IoTCentral/operations/read | Gets all the available operations on IoT Central Applications |
> | Działanie | Microsoft.IoTCentral/register/action | Register the subscription for Azure IoT Central resource provider |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.IoTSpaces/Graph/delete | Deletes Microsoft.IoTSpaces Graph resource |
> | Działanie | Microsoft.IoTSpaces/Graph/read | Gets the Microsoft.IoTSpaces Graph resource(s) |
> | Działanie | Microsoft.IoTSpaces/Graph/write | Create Microsoft.IoTSpaces Graph resource |
> | Działanie | Microsoft.IoTSpaces/register/action | Register subscription for Microsoft.IoTSpaces Graph resource provider to enable creating of resources |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.KeyVault/checkNameAvailability/read | Checks that a key vault name is valid and is not in use |
> | Działanie | Microsoft.KeyVault/deletedVaults/read | View the properties of soft deleted key vaults |
> | Działanie | Microsoft.KeyVault/hsmPools/delete | Delete an HSM pool |
> | Działanie | Microsoft.KeyVault/hsmPools/joinVault/action | Join a key vault to an HSM pool |
> | Działanie | Microsoft.KeyVault/hsmPools/read | View the properties of an HSM pool |
> | Działanie | Microsoft.KeyVault/hsmPools/write | Create a new HSM pool of update the properties of an existing HSM pool |
> | Działanie | Microsoft.KeyVault/locations/deletedVaults/purge/action | Purge a soft deleted key vault |
> | Działanie | Microsoft.KeyVault/locations/deletedVaults/read | View the properties of a soft deleted key vault |
> | Działanie | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Notifies Microsoft.KeyVault that a virtual network or subnet is being deleted |
> | Działanie | Microsoft.KeyVault/locations/operationResults/read | Check the result of a long run operation |
> | Działanie | Microsoft.KeyVault/operations/read | Lists operations available on Microsoft.KeyVault resource provider |
> | Działanie | Microsoft.KeyVault/register/action | Registers a subscription |
> | Działanie | Microsoft.KeyVault/unregister/action | Unregisters a subscription |
> | Działanie | Microsoft.KeyVault/vaults/accessPolicies/write | Update an existing access policy by merging or replacing, or add a new access policy to a vault. |
> | Działanie | Microsoft.KeyVault/vaults/delete | Usuń magazyn kluczy |
> | Działanie | Microsoft.KeyVault/vaults/deploy/action | Enables access to secrets in a key vault when deploying Azure resources |
> | Działanie | Microsoft.KeyVault/vaults/eventGridFilters/delete | Notifies Microsoft.KeyVault that an EventGrid Subscription for Key Vault is being deleted |
> | Działanie | Microsoft.KeyVault/vaults/eventGridFilters/read | Notifies Microsoft.KeyVault that an EventGrid Subscription for Key Vault is being viewed |
> | Działanie | Microsoft.KeyVault/vaults/eventGridFilters/write | Notifies Microsoft.KeyVault that a new EventGrid Subscription for Key Vault is being created |
> | Działanie | Microsoft.KeyVault/vaults/read | View the properties of a key vault |
> | Działanie | Microsoft.KeyVault/vaults/secrets/read | View the properties of a secret, but not its value. |
> | Działanie | Microsoft.KeyVault/vaults/secrets/write | Create a new secret or update the value of an existing secret. |
> | Działanie | Microsoft.KeyVault/vaults/write | Create a new key vault or update the properties of an existing key vault |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Kusto/Clusters/Activate/action | Starts the cluster. |
> | Działanie | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Deletes an attached database configuration resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Reads an attached database configuration resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Writes an attached database configuration resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Checks the cluster name availability. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Adds database principals. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Checks name availability for a given type. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Deletes a data connections resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Reads a data connections resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Writes a data connections resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Validates database data connection. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/delete | Deletes a database resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Deletes an Event Hub connections resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Reads an Event Hub connections resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Writes an Event Hub connections resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Validates database Event Hub connection. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Lists database principals. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/delete | Deletes a database principal assignments resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/read | Reads a database principal assignments resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/write | Writes a database principal assignments resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/read | Reads a database resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Removes database principals. |
> | Działanie | Microsoft.Kusto/Clusters/Databases/write | Writes a database resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Deactivate/action | Stops the cluster. |
> | Działanie | Microsoft.Kusto/Clusters/delete | Deletes a cluster resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/DetachFollowerDatabases/action | Detaches follower's databases. |
> | Działanie | Microsoft.Kusto/Clusters/DiagnoseVirtualNetwork/action | Diagnoses network connectivity status for external resources on which the service is depedent on. |
> | Działanie | Microsoft.Kusto/Clusters/ListFollowerDatabases/action | Lists the follower's databases. |
> | Działanie | Microsoft.Kusto/Clusters/read | Reads a cluster resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/SKUs/read | Reads a cluster SKU resourceCopy. |
> | Działanie | Microsoft.Kusto/Clusters/Start/action | Starts the cluster. |
> | Działanie | Microsoft.Kusto/Clusters/Stop/action | Stops the cluster. |
> | Działanie | Microsoft.Kusto/Clusters/write | Writes a cluster resourceCopy. |
> | Działanie | Microsoft.Kusto/Locations/CheckNameAvailability/action | Checks resourceCopy name availability. |
> | Działanie | Microsoft.Kusto/Locations/GetNetworkPolicies/action | Gets Network Intent Policies |
> | Działanie | Microsoft.Kusto/locations/operationresults/read | Reads operations resourceCopys |
> | Działanie | Microsoft.Kusto/Operations/read | Reads operations resourceCopys |
> | Działanie | Microsoft.Kusto/register/action | Subscription Registration Action |
> | Działanie | Microsoft.Kusto/Register/action | Registers the subscription to the Kusto Resource Provider. |
> | Działanie | Microsoft.Kusto/SKUs/read | Reads a SKU resourceCopy. |
> | Działanie | Microsoft.Kusto/Unregister/action | Unregisters the subscription to the Kusto Resource Provider. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.LabServices/labAccounts/CreateLab/action | Create a lab in a lab account. |
> | Działanie | Microsoft.LabServices/labAccounts/delete | Delete lab accounts. |
> | Działanie | Microsoft.LabServices/labAccounts/galleryImages/delete | Delete gallery images. |
> | Działanie | Microsoft.LabServices/labAccounts/galleryImages/read | Read gallery images. |
> | Działanie | Microsoft.LabServices/labAccounts/galleryImages/write | Add or modify gallery images. |
> | Działanie | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Get regional availability information for each size category configured under a lab account |
> | Działanie | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Add users to a lab |
> | Działanie | Microsoft.LabServices/labAccounts/labs/delete | Delete labs. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Delete environment setting. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Delete environments. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Read environments. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Resets the user password on an environment |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Starts an environment by starting all resources inside the environment. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Stops an environment by stopping all resources inside the environment |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Add or modify environments. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Provisions/deprovisions required resources for an environment setting based on current state of the lab/environment setting. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Read environment setting. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Resets password on the template virtual machine. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Saves current template image to the shared gallery in the lab account |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Delete schedules. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Read schedules. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Add or modify schedules. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Starts a template by starting all resources inside the template. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Stops a template by stopping all resources inside the template. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Add or modify environment setting. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/read | Read labs. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Send email with registration link to the lab |
> | Działanie | Microsoft.LabServices/labAccounts/labs/users/delete | Delete users. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/users/read | Read users. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/users/write | Add or modify users. |
> | Działanie | Microsoft.LabServices/labAccounts/labs/write | Add or modify labs. |
> | Działanie | Microsoft.LabServices/labAccounts/read | Read lab accounts. |
> | Działanie | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Delete sharedgalleries. |
> | Działanie | Microsoft.LabServices/labAccounts/sharedGalleries/read | Read sharedgalleries. |
> | Działanie | Microsoft.LabServices/labAccounts/sharedGalleries/write | Add or modify sharedgalleries. |
> | Działanie | Microsoft.LabServices/labAccounts/sharedImages/delete | Delete sharedimages. |
> | Działanie | Microsoft.LabServices/labAccounts/sharedImages/read | Read sharedimages. |
> | Działanie | Microsoft.LabServices/labAccounts/sharedImages/write | Add or modify sharedimages. |
> | Działanie | Microsoft.LabServices/labAccounts/write | Add or modify lab accounts. |
> | Działanie | Microsoft.LabServices/locations/operations/read | Read operations. |
> | Działanie | Microsoft.LabServices/register/action | Rejestruje subskrypcję |
> | Działanie | Microsoft.LabServices/users/ListAllEnvironments/action | List all Environments for the user |
> | Działanie | Microsoft.LabServices/users/Register/action | Register a user to a managed lab |
> | Działanie | Microsoft.LabServices/users/ResetPassword/action | Resets the user password on an environment |
> | Działanie | Microsoft.LabServices/users/StartEnvironment/action | Starts an environment by starting all resources inside the environment. |
> | Działanie | Microsoft.LabServices/users/StopEnvironment/action | Stops an environment by stopping all resources inside the environment |
> | Działanie | Microsoft.LabServices/users/UserSettings/action | Updates and returns personal user settings. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Logic/integrationAccounts/agreements/delete | Deletes the agreement in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Gets the callback URL for agreement content in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/agreements/read | Reads the agreement in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/agreements/write | Creates or updates the agreement in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/assemblies/delete | Deletes the assembly in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Gets the callback URL for assembly content in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/assemblies/read | Reads the assembly in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/assemblies/write | Creates or updates the assembly in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Deletes the batch configuration in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Odczytuje konfigurację partii na koncie integracji. |
> | Działanie | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Creates or updates the batch configuration in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/certificates/delete | Deletes the certificate in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/certificates/read | Reads the certificate in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/certificates/write | Creates or updates the certificate in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/delete | Deletes the integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/join/action | Joins the Integration Account. |
> | Działanie | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Gets the callback URL for integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Gets the keys in the key vault. |
> | Działanie | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Logs the tracking events in the integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/maps/delete | Deletes the map in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Gets the callback URL for map content in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/maps/read | Reads the map in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/maps/write | Creates or updates the map in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/partners/delete | Deletes the partner in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Gets the callback URL for partner content in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/partners/read | Reads the partner in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/partners/write | Creates or updates the partner in integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Reads the Integration Account log definitions. |
> | Działanie | Microsoft.Logic/integrationAccounts/read | Reads the integration account. |
> | Działanie | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Działanie | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/Delete | Usuwa konfigurację procesu RosettaNet na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/odczyt | Odczytuje konfigurację procesu RosettaNet na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/Write | Tworzy lub aktualizuje konfigurację procesu RosettaNet na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/schematy/usuwanie | Usuwa schemat na koncie integracji. |
> | Działanie | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości schematu na koncie integracji. |
> | Działanie | Microsoft.Logic/integrationAccounts/schemas/read | Odczytuje schemat na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/schematy/zapis | Tworzy lub aktualizuje schemat na koncie integracji. |
> | Działanie | Microsoft.Logic/integrationAccounts/sessions/delete | Usuwa sesję na koncie integracji. |
> | Działanie | Microsoft.Logic/integrationAccounts/sessions/read | Odczytuje konfigurację partii na koncie integracji. |
> | Działanie | Microsoft.Logic/integrationAccounts/sessions/write | Tworzy lub aktualizuje sesję na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Write | Tworzy lub aktualizuje konto integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/availableManagedApis/odczyt | Odczytuje dostępne zarządzane interfejsy API środowiska usługi integracji. |
> | Działanie | Microsoft.Logic/integrationServiceEnvironments/delete | Usuwa środowisko usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/Join/Action | Sprzęga środowisko usługi integracji. |
> | Działanie | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Odczytuje operację zarządzanego interfejsu API środowiska usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/managedApis/Join/Action | Sprzęga środowisko usługi integracji zarządzanym interfejsem API. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/managedApis/operationStatuses/Read | Odczytuje stan operacji zarządzanego interfejsu API środowiska usługi integracji. |
> | Działanie | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Odczytuje zarządzany interfejs API środowiska usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/managedApis/Write | Tworzy lub aktualizuje zarządzany interfejs API środowiska usługi integracji. |
> | Działanie | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Odczytuje stan operacji środowiska usługi integracji. |
> | Działanie | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje definicje metryk środowiska usługi integracji. |
> | Działanie | Microsoft.Logic/integrationServiceEnvironments/read | Odczytuje środowisko usługi integracji. |
> | Działanie | Microsoft.Logic/integrationServiceEnvironments/write | Tworzy lub aktualizuje środowisko usługi integracji. |
> | Działanie | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Pobiera grupy operacji zalecane dla przepływu pracy. |
> | Działanie | Microsoft.Logic/locations/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | Działanie | Microsoft.Logic/operations/read | Pobiera operację. |
> | Działanie | Microsoft. Logic/Register/Action | Rejestruje dostawcę zasobów Microsoft. Logic dla danej subskrypcji. |
> | Działanie | Microsoft. Logic/Workflows/accessKeys/Delete | Usuwa klucz dostępu. |
> | Działanie | Microsoft. Logic/Workflows/accessKeys/list/Action | Wyświetla wpisy tajne klucza dostępu. |
> | Działanie | Microsoft.Logic/workflows/accessKeys/read | Odczytuje klucz dostępu. |
> | Działanie | Microsoft. Logic/Workflows/accessKeys/Regenerate/Action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Działanie | Microsoft. Logic/Workflows/accessKeys/Write | Tworzy lub aktualizuje klucz dostępu. |
> | Działanie | Microsoft. Logic/przepływy pracy/usuwanie | Usuwa przepływ pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/detektory/odczyt | Odczytuje Detektor przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/Disable/Action | Wyłącza przepływ pracy. |
> | Działanie | Microsoft.Logic/workflows/enable/action | Włącza przepływ pracy. |
> | Działanie | Microsoft.Logic/workflows/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/listSwagger/action | Pobiera definicje struktury Swagger przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/przenoszenie/akcja | Przenosi przepływ pracy z istniejącego identyfikatora subskrypcji, grupy zasobów i/lub nazwy do innego identyfikatora subskrypcji, grupy zasobów i/lub nazwy. |
> | Działanie | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Odczytuje ustawienia diagnostyki przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyki przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Odczytuje definicje dziennika przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/metricDefinitions/Read | Odczytuje definicje metryk przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/read | Odczytuje przepływ pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/regenerateAccessKey/akcja | Generuje ponownie wpisy tajne klucza dostępu. |
> | Działanie | Microsoft. Logic/Workflows/Run/Action | Uruchamia przebieg przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Pobiera ślady wyrażenia akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/runs/actions/read | Odczytuje akcję przebiegu przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Pobiera ślady wyrażenia powtórzenia akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/runs/actions/repetitions/read | Odczytuje powtórzenie akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia/requestHistories/odczyt | Odczytuje historię żądań akcji powtórzenia przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/uruchomienia/Actions/requestHistories/Read | Odczytuje historię żądań akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/uruchomienia/Actions/scoperepetitions/Read | Odczytuje powtórzenie zakresu akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/uruchomienia/Anuluj/akcja | Anuluje uruchomienie przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/runs/delete | Usuwa przebieg przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/runs/operations/read | Odczytuje stan operacji przebiegu przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/runs/read | Odczytuje przebieg przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/suspend/action | Wstrzymuje przepływ pracy. |
> | Działanie | Microsoft.Logic/workflows/triggers/histories/read | Odczytuje historie wyzwalacza. |
> | Działanie | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Przesyła ponownie wyzwalacz przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Działanie | Microsoft.Logic/workflows/triggers/read | Odczytuje wyzwalacz. |
> | Działanie | Microsoft.Logic/workflows/triggers/reset/action | Resetuje wyzwalacz. |
> | Działanie | Microsoft. Logic/Workflows/Triggers/Run/Action | Wykonuje wyzwalacz. |
> | Działanie | Microsoft.Logic/workflows/triggers/setState/action | Ustawia stan wyzwalacza. |
> | Działanie | Microsoft.Logic/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/versions/read | Odczytuje wersję przepływu pracy. |
> | Działanie | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Działanie | Microsoft.Logic/workflows/write | Tworzy lub aktualizuje przepływ pracy. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/Move/Action | Przenieś dowolne Machine Learning skojarzenie planu zobowiązania |
> | Działanie | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/Read | Odczytaj wszystkie Machine Learning skojarzenie planu zobowiązania |
> | Działanie | Microsoft. MachineLearning/commitmentPlans/Delete | Usuń dowolny Machine Learning plan zobowiązania |
> | Działanie | Microsoft. MachineLearning/commitmentPlans/Join/Action | Dołącz dowolny plan zobowiązania Machine Learning |
> | Działanie | Microsoft. MachineLearning/commitmentPlans/odczyt | Przeczytaj dowolny plan zobowiązania Machine Learning |
> | Działanie | Microsoft. MachineLearning/commitmentPlans/Write | Utwórz lub zaktualizuj dowolny plan zobowiązania Machine Learning |
> | Działanie | Microsoft. MachineLearning/Locations/operationresults/Read | Pobierz wynik operacji Machine Learning |
> | Działanie | Microsoft. MachineLearning/Locations/operationsstatus/Read | Pobierz stan trwającej Machine Learning operacji |
> | Działanie | Microsoft. MachineLearning/Operations/Read | Pobierz operacje Machine Learning |
> | Działanie | Microsoft. MachineLearning/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi sieci Web Machine Learning i umożliwia tworzenie usług sieci Web. |
> | Działanie | Microsoft.MachineLearning/skus/read | Pobierz jednostki SKU planu zobowiązania Machine Learning |
> | Działanie | Microsoft. MachineLearning/WebServices/Action | Tworzenie regionalnych właściwości usługi sieci Web dla obsługiwanych regionów |
> | Działanie | Microsoft. MachineLearning/WebServices/Delete | Usuń wszystkie Machine Learning usługę sieci Web |
> | Działanie | Microsoft. MachineLearning/WebServices/ListKeys/Read | Pobierz klucze do usługi sieci Web Machine Learning |
> | Działanie | Microsoft. MachineLearning/WebServices/Read | Odczytaj dowolny Machine Learning usługę sieci Web |
> | Działanie | Microsoft. MachineLearning/WebServices/Write | Utwórz lub zaktualizuj dowolną Machine Learning usługę sieci Web |
> | Działanie | Microsoft. MachineLearning/obszary robocze/usuwanie | Usuń wszystkie Obszar roboczy usługi Machine Learning |
> | Działanie | Microsoft. MachineLearning/obszary robocze/listworkspacekeys/akcja | Lista kluczy dla Obszar roboczy usługi Machine Learning |
> | Działanie | Microsoft. MachineLearning/obszary robocze/odczyt | Odczytaj wszystkie Obszar roboczy usługi Machine Learning |
> | Działanie | Microsoft. MachineLearning/obszary robocze/resyncstoragekeys/akcja | Ponowne synchronizowanie kluczy konta magazynu skonfigurowanego dla Obszar roboczy usługi Machine Learning |
> | Działanie | Microsoft. MachineLearning/obszary robocze/zapis | Utwórz lub zaktualizuj dowolne Obszar roboczy usługi Machine Learning |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. MachineLearningServices/Locations/computeoperationsstatus/Read | Pobiera stan określonej operacji obliczeniowej |
> | Działanie | Microsoft. MachineLearningServices/lokalizacje/przydziały/odczyt | Pobiera przydziały aktualnie przypisanych obszarów roboczych na podstawie VMFamily. |
> | Działanie | Microsoft. MachineLearningServices/Locations/updateQuotas/Action | Aktualizowanie przydziału dla każdej rodziny maszyn wirtualnych w obszarze roboczym. |
> | Działanie | Microsoft.MachineLearningServices/locations/usages/read | Raport użycia dla zasobów obliczeniowych AML w ramach subskrypcji |
> | Działanie | Microsoft.MachineLearningServices/locations/vmsizes/read | Pobierz obsługiwane rozmiary maszyn wirtualnych |
> | Działanie | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Pobiera stan określonej operacji obszaru roboczego |
> | Działanie | Microsoft. MachineLearningServices/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów Machine Learning Services |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/obliczenia/usuwanie | Usuwa zasoby obliczeniowe w Machine Learning Services obszarach roboczych |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/obliczenia/listKeys/akcja | Wyświetlanie wpisów tajnych dla zasobów obliczeniowych w obszarze roboczym Machine Learning Services |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/obliczenia/listNodes/akcja | Wyświetlanie listy węzłów dla zasobu obliczeniowego w obszarze roboczym Machine Learning Services |
> | Działanie | Microsoft.MachineLearningServices/workspaces/computes/read | Pobiera zasoby obliczeniowe w Machine Learning Services obszarach roboczych |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/obliczenia/zapis | Tworzy lub aktualizuje zasoby obliczeniowe w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/Workspaces/datadriftdetectors/Read | Pobiera detektory dryfowania danych w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/Workspaces/datadriftdetectors/Write | Tworzy lub aktualizuje detektory dryfowania danych w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/zarejestrowane/usunięte | Usuwa zarejestrowane zestawy danych w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/zarejestrowane/Podgląd/odczyt | Pobiera Podgląd zestawu danych dla zarejestrowanej funkcji w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/zarejestrowane/profil/odczyt | Pobiera profile DataSet dla zarejestrowanych zestawów danych w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/zarejestrowane/profil/zapis | Tworzy lub aktualizuje profile zestawów danych w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/zarejestrowane/odczytane | Pobiera zarejestrowane zestawy danych w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/zarejestrowane/zapisane | Tworzy lub aktualizuje zarejestrowane zestawy danych w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/Wyrejestrowanie/usuwanie | Usuwa niezarejestrowane zestawy danych w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/wyrejestrowane/Podgląd/odczyt | Pobiera Podgląd zestawu danych dla niezarejestrowanego zestawu rekordów w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/Workspaces/datadatasets/Unregistered/Read | Pobiera profile DataSet dla niezarejestrowanych zestawów danych w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/wyrejestrowane/odczytane | Pobiera niezarejestrowane zestawy danych w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/zestawy danych/wyrejestrowano/Zapisz | Tworzy lub aktualizuje niezarejestrowane zestawy danych w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/magazyny danych/usuwanie | Usuwa magazyny danych w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/magazyny danych/Odczyt | Pobiera magazyny danych w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/magazyny danych/zapis | Tworzy lub aktualizuje magazyny danych w Machine Learning Services obszarach roboczych |
> | Działanie | Microsoft.MachineLearningServices/workspaces/delete | Usuwa obszary robocze Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/punkty końcowe/potoki/odczyt | Pobiera opublikowane potoki i punkty końcowe potoku w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/punkty końcowe/potoki/zapis | Tworzy lub aktualizuje opublikowane potoki i punkty końcowe potoku w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/środowiska/kompilacja/akcja | Tworzy środowiska w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/środowiska/odczyt | Pobiera środowiska w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/środowiska/readSecrets/akcja | Pobiera środowiska z wpisami tajnymi w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/środowiska/zapis | Tworzy lub aktualizuje środowiska w obszarze roboczym Machine Learning Services |
> | Działanie | Microsoft. MachineLearningServices/Workspaces/eventGridFilters/Read | Pobierz filtr Event Grid dla określonego obszaru roboczego |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/usuwanie | Usuwa eksperymenty w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/odczyt | Pobiera eksperymenty w Machine Learning Servicesych obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/uruchomienia/odczyt | Pobiera przebiegi w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/uruchomienia/scriptRun/Prześlij/akcja | Tworzy lub aktualizuje uruchomienia skryptów w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/uruchomienia/zapisu | Tworzy lub aktualizuje uruchomienia w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/zapis | Tworzy lub aktualizuje eksperymenty w Machine Learning Services obszarach roboczych |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/listKeys/akcja | Wyświetlanie wpisów tajnych dla obszaru roboczego Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/Workspaces/Metadata/artefakty/Usuń | Usuwa artefakty w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/Workspaces/Metadata/artefakty/odczyt | Pobiera artefakty w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/Metadata/artefakty/zapis | Tworzy lub aktualizuje artefakty w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/metadane/migawki/usuwanie | Usuwa migawki w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/Workspaces/Metadata/snapshots/Read | Pobiera migawki w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/metadane/migawki/zapis | Tworzy lub aktualizuje migawki w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/modele/usuwanie | Usuwa modele w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/modele/pobieranie/akcja | Pobiera modele w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/modele/pakiet/akcja | Modele pakietów w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/modele/odczyt | Pobiera modele w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/modele/zapis | Tworzy lub aktualizuje modele w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/moduły/odczyt | Pobiera moduły w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/moduły/zapis | Tworzy lub aktualizuje moduł w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/Workspaces/pipelinedrafts/Delete | Usuwa wersje robocze potoku w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/Workspaces/pipelinedrafts/Read | Pobiera wersje robocze potoku w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/Workspaces/pipelinedrafts/Write | Tworzy lub aktualizuje wersje robocze potoku w Machine Learning Services obszarach roboczych |
> | Działanie | Microsoft.MachineLearningServices/workspaces/read | Pobiera Machine Learning Services obszary robocze |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/usługi/ACI/usuwanie | Usuwa usługi ACI w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/usługi/ACI/ListKeys/akcja | Wyświetla listę kluczy dla usług ACI w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/usługi/ACI/zapis | Tworzy lub aktualizuje usługi ACI w obszarze roboczym Machine Learning Services |
> | Akcja dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/delete | Deletes devtest AKS services in Machine Learning Services Workspace(s) |
> | Akcja dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/listkeys/action | Lists keys for devtest AKS services in Machine Learning Services Workspace(s) |
> | Akcja dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/score/action | Scores devtest AKS services in Machine Learning Services Workspace(s) |
> | Akcja dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/write | Creates or updates devtest AKS services in Machine Learning Services Workspace(s) |
> | Akcja dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/delete | Deletes prod AKS services in Machine Learning Services Workspace(s) |
> | Akcja dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/listkeys/action | Lists keys for prod AKS services in Machine Learning Services Workspace(s) |
> | Akcja dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/score/action | Scores prod AKS services in Machine Learning Services Workspace(s) |
> | Akcja dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/write | Creates or updates prod AKS services in Machine Learning Services Workspace(s) |
> | Akcja dataaction | Microsoft.MachineLearningServices/workspaces/services/read | Gets services in Machine Learning Services Workspace(s) |
> | Działanie | Microsoft.MachineLearningServices/workspaces/write | Creates or updates a Machine Learning Services Workspace(s) |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.ManagedIdentity/identities/read | Gets an existing system assigned identity |
> | Działanie | Microsoft.ManagedIdentity/operations/read | Lists operations available on Microsoft.ManagedIdentity resource provider |
> | Działanie | Microsoft.ManagedIdentity/register/action | Registers the subscription for the managed identity resource provider |
> | Działanie | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC action for assigning an existing user assigned identity to a resource |
> | Działanie | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Deletes an existing user assigned identity |
> | Działanie | Microsoft.ManagedIdentity/userAssignedIdentities/read | Gets an existing user assigned identity |
> | Działanie | Microsoft.ManagedIdentity/userAssignedIdentities/write | Creates a new user assigned identity or updates the tags associated with an existing user assigned identity |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.ManagedServices/marketplaceRegistrationDefinitions/read | Retrieves a list of Managed Services registration definitions. |
> | Działanie | Microsoft.ManagedServices/operations/read | Retrieves a list of Managed Services operations. |
> | Działanie | Microsoft.ManagedServices/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft.ManagedServices/register/action | Register to Managed Services. |
> | Działanie | Microsoft.ManagedServices/registrationAssignments/delete | Removes Managed Services registration assignment. |
> | Działanie | Microsoft.ManagedServices/registrationAssignments/read | Retrieves a list of Managed Services registration assignments. |
> | Działanie | Microsoft.ManagedServices/registrationAssignments/write | Add or modify Managed Services registration assignment. |
> | Działanie | Microsoft.ManagedServices/registrationDefinitions/delete | Removes Managed Services registration definition. |
> | Działanie | Microsoft.ManagedServices/registrationDefinitions/read | Retrieves a list of Managed Services registration definitions. |
> | Działanie | Microsoft.ManagedServices/registrationDefinitions/write | Add or modify Managed Services registration definition. |
> | Działanie | Microsoft.ManagedServices/unregister/action | Unregister from Managed Services. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Management/checkNameAvailability/action | Checks if the specified management group name is valid and unique. |
> | Działanie | Microsoft.Management/getEntities/action | List all entities (Management Groups, Subscriptions, etc.) for the authenticated user. |
> | Działanie | Microsoft.Management/managementGroups/delete | Delete management group. |
> | Działanie | Microsoft.Management/managementGroups/descendants/read | Gets all the descendants (Management Groups, Subscriptions) of a Management Group. |
> | Działanie | Microsoft.Management/managementGroups/read | List management groups for the authenticated user. |
> | Działanie | Microsoft.Management/managementGroups/subscriptions/delete | De-associates subscription from the management group. |
> | Działanie | Microsoft.Management/managementGroups/subscriptions/write | Associates existing subscription with the management group. |
> | Działanie | Microsoft.Management/managementGroups/write | Create or update a management group. |
> | Działanie | Microsoft.Management/register/action | Register the specified subscription with Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja dataaction | Microsoft.Maps/accounts/data/read | Grants data read access to a maps account. |
> | Działanie | Microsoft.Maps/accounts/delete | Delete a Maps Account. |
> | Działanie | Microsoft.Maps/accounts/eventGridFilters/delete | Delete an Event Grid filter |
> | Działanie | Microsoft.Maps/accounts/eventGridFilters/read | Get an Event Grid filter |
> | Działanie | Microsoft.Maps/accounts/eventGridFilters/write | Create or update an Event Grid filter |
> | Działanie | Microsoft.Maps/accounts/listKeys/action | List Maps Account keys |
> | Działanie | Microsoft.Maps/accounts/read | Get a Maps Account. |
> | Działanie | Microsoft.Maps/accounts/regenerateKey/action | Generate new Maps Account primary or secondary key |
> | Działanie | Microsoft.Maps/accounts/write | Create or update a Maps Account. |
> | Działanie | Microsoft.Maps/operations/read | Read the provider operations |
> | Działanie | Microsoft.Maps/register/action | Register the provider |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Returns an Agreement. |
> | Działanie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Accepts a signed agreement. |
> | Działanie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Imports an image to the end user's ACR. |
> | Działanie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Returns a config. |
> | Działanie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Saves a config. |
> | Działanie | Microsoft.Marketplace/register/action | Registers Microsoft.Marketplace resource provider in the subscription. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.MarketplaceApps/ClassicDevServices/delete | Does a DELETE operation on a classic dev service resource. |
> | Działanie | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Gets a classic dev service resource management keys. |
> | Działanie | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Gets the Single Sign On URL for a classic dev service. |
> | Działanie | Microsoft.MarketplaceApps/ClassicDevServices/read | Does a GET operation on a classic dev service. |
> | Działanie | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generates a classic dev service resource management keys. |
> | Działanie | Microsoft.MarketplaceApps/Operations/read | Read the operations for all resource types. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancel an agreement for a given marketplace item |
> | Działanie | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Return an agreement for a given marketplace item |
> | Działanie | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Sign an agreement for a given marketplace item |
> | Działanie | Microsoft.MarketplaceOrdering/agreements/read | Return all agreements under given subscription |
> | Działanie | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Get an agreement for a given marketplace virtual machine item |
> | Działanie | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Sign or Cancel an agreement for a given marketplace virtual machine item |
> | Działanie | Microsoft.MarketplaceOrdering/operations/read | List all possible operations in the API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Media/checknameavailability/action | Checks if a Media Services account name is available |
> | Działanie | Microsoft.Media/mediaservices/accountfilters/delete | Delete any Account Filter |
> | Działanie | Microsoft.Media/mediaservices/accountfilters/read | Read any Account Filter |
> | Działanie | Microsoft.Media/mediaservices/accountfilters/write | Create or Update any Account Filter |
> | Działanie | Microsoft.Media/mediaservices/assets/assetfilters/delete | Delete any Asset Filter |
> | Działanie | Microsoft.Media/mediaservices/assets/assetfilters/read | Read any Asset Filter |
> | Działanie | Microsoft.Media/mediaservices/assets/assetfilters/write | Create or Update any Asset Filter |
> | Działanie | Microsoft.Media/mediaservices/assets/delete | Delete any Asset |
> | Działanie | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Get Asset Encryption Key |
> | Działanie | Microsoft.Media/mediaservices/assets/listContainerSas/action | List Asset Container SAS URLs |
> | Działanie | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | List Streaming Locators for Asset |
> | Działanie | Microsoft.Media/mediaservices/assets/read | Read any Asset |
> | Działanie | Microsoft.Media/mediaservices/assets/write | Create or Update any Asset |
> | Działanie | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Delete any Content Key Policy |
> | Działanie | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Get Policy Properties With Secrets |
> | Działanie | Microsoft.Media/mediaservices/contentKeyPolicies/read | Read any Content Key Policy |
> | Działanie | Microsoft.Media/mediaservices/contentKeyPolicies/write | Create or Update any Content Key Policy |
> | Działanie | Microsoft.Media/mediaservices/delete | Delete any Media Services Account |
> | Działanie | Microsoft.Media/mediaservices/eventGridFilters/delete | Delete any Event Grid Filter |
> | Działanie | Microsoft.Media/mediaservices/eventGridFilters/read | Read any Event Grid Filter |
> | Działanie | Microsoft.Media/mediaservices/eventGridFilters/write | Create or Update any Event Grid Filter |
> | Działanie | Microsoft.Media/mediaservices/liveEventOperations/read | Read any Live Event Operation |
> | Działanie | Microsoft.Media/mediaservices/liveEvents/delete | Delete any Live Event |
> | Działanie | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Delete any Live Output |
> | Działanie | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Read any Live Output |
> | Działanie | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Create or Update any Live Output |
> | Działanie | Microsoft.Media/mediaservices/liveEvents/read | Read any Live Event |
> | Działanie | Microsoft.Media/mediaservices/liveEvents/reset/action | Reset any Live Event Operation |
> | Działanie | Microsoft.Media/mediaservices/liveEvents/start/action | Start any Live Event Operation |
> | Działanie | Microsoft.Media/mediaservices/liveEvents/stop/action | Stop any Live Event Operation |
> | Działanie | Microsoft.Media/mediaservices/liveEvents/write | Create or Update any Live Event |
> | Działanie | Microsoft.Media/mediaservices/liveOutputOperations/read | Read any Live Output Operation |
> | Działanie | Microsoft.Media/mediaservices/read | Read any Media Services Account |
> | Działanie | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Read any Streaming Endpoint Operation |
> | Działanie | Microsoft.Media/mediaservices/streamingEndpoints/delete | Delete any Streaming Endpoint |
> | Działanie | Microsoft.Media/mediaservices/streamingEndpoints/read | Read any Streaming Endpoint |
> | Działanie | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Scale any Streaming Endpoint Operation |
> | Działanie | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Start any Streaming Endpoint Operation |
> | Działanie | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Stop any Streaming Endpoint Operation |
> | Działanie | Microsoft.Media/mediaservices/streamingEndpoints/write | Create or Update any Streaming Endpoint |
> | Działanie | Microsoft.Media/mediaservices/streamingLocators/delete | Delete any Streaming Locator |
> | Działanie | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | List Content Keys |
> | Działanie | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | List Paths |
> | Działanie | Microsoft.Media/mediaservices/streamingLocators/read | Read any Streaming Locator |
> | Działanie | Microsoft.Media/mediaservices/streamingLocators/write | Create or Update any Streaming Locator |
> | Działanie | Microsoft.Media/mediaservices/streamingPolicies/delete | Delete any Streaming Policy |
> | Działanie | Microsoft.Media/mediaservices/streamingPolicies/read | Read any Streaming Policy |
> | Działanie | Microsoft.Media/mediaservices/streamingPolicies/write | Create or Update any Streaming Policy |
> | Działanie | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronize the Storage Keys for an attached Azure Storage account |
> | Działanie | Microsoft.Media/mediaservices/transforms/delete | Delete any Transform |
> | Działanie | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Anuluj zadanie |
> | Działanie | Microsoft.Media/mediaservices/transforms/jobs/delete | Delete any Job |
> | Działanie | Microsoft.Media/mediaservices/transforms/jobs/read | Read any Job |
> | Działanie | Microsoft.Media/mediaservices/transforms/jobs/write | Create or Update any Job |
> | Działanie | Microsoft.Media/mediaservices/transforms/read | Read any Transform |
> | Działanie | Microsoft.Media/mediaservices/transforms/write | Create or Update any Transform |
> | Działanie | Microsoft.Media/mediaservices/write | Create or Update any Media Services Account |
> | Działanie | Microsoft.Media/operations/read | Pobierz dostępne operacje |
> | Działanie | Microsoft.Media/register/action | Registers the subscription for the Media Services resource provider and enables the creation of Media Services accounts |
> | Działanie | Microsoft.Media/unregister/action | Unregisters the subscription for the Media Services resource provider |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Migrate/assessmentprojects/assessmentOptions/read | Gets the assessment options which are available in the given location |
> | Działanie | Microsoft.Migrate/assessmentprojects/assessments/read | Lists assessments within a project |
> | Działanie | Microsoft.Migrate/assessmentprojects/delete | Deletes the assessment project |
> | Działanie | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Get the properties of an assessed machine |
> | Działanie | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Deletes an assessment |
> | Działanie | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Downloads an assessment report's URL |
> | Działanie | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Gets the properties of an assessment |
> | Działanie | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Creates a new assessment or updates an existing assessment |
> | Działanie | Microsoft.Migrate/assessmentprojects/groups/delete | Deletes a group |
> | Działanie | Microsoft.Migrate/assessmentprojects/groups/read | Get the properties of a group |
> | Działanie | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Update group by adding or removing machines |
> | Działanie | Microsoft.Migrate/assessmentprojects/groups/write | Creates a new group or updates an existing group |
> | Działanie | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Deletes the HyperV collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Gets the properties of HyperV collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Creates a new HyperV collector or updates an existing HyperV collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/importcollectors/delete | Deletes the Import collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/importcollectors/read | Gets the properties of Import collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/importcollectors/write | Creates a new Import collector or updates an existing Import collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/machines/read | Gets the properties of a machine |
> | Działanie | Microsoft.Migrate/assessmentprojects/read | Gets the properties of assessment project |
> | Działanie | Microsoft.Migrate/assessmentprojects/servercollectors/read | Gets the properties of Server collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/servercollectors/write | Creates a new Server collector or updates an existing Server collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Deletes the VMware collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Gets the properties of VMware collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Creates a new VMware collector or updates an existing VMware collector |
> | Działanie | Microsoft.Migrate/assessmentprojects/write | Creates a new assessment project or updates an existing assessment project |
> | Działanie | Microsoft.Migrate/locations/assessmentOptions/read | Gets the assessment options which are available in the given location |
> | Działanie | Microsoft.Migrate/locations/checknameavailability/action | Checks availability of the resource name for the given subscription in the given location |
> | Działanie | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Gets the properties of a database instance |
> | Działanie | Microsoft.Migrate/migrateprojects/Databases/read | Gets the properties of a database |
> | Działanie | Microsoft.Migrate/migrateprojects/delete | Deletes a migrate project |
> | Działanie | Microsoft.Migrate/migrateprojects/machines/read | Gets the properties of a machine |
> | Działanie | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Deletes a migrate event |
> | Działanie | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Gets the properties of a migrate events. |
> | Działanie | Microsoft.Migrate/migrateprojects/read | Gets the properties of migrate project |
> | Działanie | Microsoft.Migrate/migrateprojects/RefreshSummary/action | Refreshes the migrate project summary |
> | Działanie | Microsoft.Migrate/migrateprojects/registerTool/action | Registers tool to a migrate project |
> | Działanie | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Clean up the migrate project solution data |
> | Działanie | Microsoft.Migrate/migrateprojects/solutions/Delete | Deletes a  migrate project solution |
> | Działanie | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Gets the migrate project solution configuration |
> | Działanie | Microsoft.Migrate/migrateprojects/solutions/read | Gets the properties of migrate project solution |
> | Działanie | Microsoft.Migrate/migrateprojects/solutions/write | Creates a new migrate project solution or updates an existing migrate project solution |
> | Działanie | Microsoft.Migrate/migrateprojects/write | Creates a new migrate project or updates an existing migrate project |
> | Działanie | Microsoft.Migrate/Operations/read | Lists operations available on Microsoft.Migrate resource provider |
> | Działanie | Microsoft.Migrate/projects/assessments/read | Lists assessments within a project |
> | Działanie | Microsoft.Migrate/projects/delete | Deletes the project |
> | Działanie | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Get the properties of an assessed machine |
> | Działanie | Microsoft.Migrate/projects/groups/assessments/delete | Deletes an assessment |
> | Działanie | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Downloads an assessment report's URL |
> | Działanie | Microsoft.Migrate/projects/groups/assessments/read | Gets the properties of an assessment |
> | Działanie | Microsoft.Migrate/projects/groups/assessments/write | Creates a new assessment or updates an existing assessment |
> | Działanie | Microsoft.Migrate/projects/groups/delete | Deletes a group |
> | Działanie | Microsoft.Migrate/projects/groups/read | Get the properties of a group |
> | Działanie | Microsoft.Migrate/projects/groups/write | Creates a new group or updates an existing group |
> | Działanie | Microsoft.Migrate/projects/keys/action | Gets shared keys for the project |
> | Działanie | Microsoft.Migrate/projects/machines/read | Gets the properties of a machine |
> | Działanie | Microsoft.Migrate/projects/read | Gets the properties of a project |
> | Działanie | Microsoft.Migrate/projects/write | Creates a new project or updates an existing project |
> | Działanie | Microsoft.Migrate/register/action | Registers Subscription with Microsoft.Migrate resource provider |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.MixedReality/register/action | Registers a subscription for the Mixed Reality resource provider. |
> | Akcja dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Create spatial anchors |
> | Akcja dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Delete spatial anchors |
> | Akcja dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Discover nearby spatial anchors |
> | Akcja dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Get properties of spatial anchors |
> | Działanie | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Działanie | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Działanie | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Akcja dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Locate spatial anchors |
> | Akcja dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Submit diagnostics data to help improve the quality of the Azure Spatial Anchors service |
> | Akcja dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Update spatial anchors properties |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.NetApp/locations/checkfilepathavailability/action | Check if file path is available |
> | Działanie | Microsoft.NetApp/locations/checknameavailability/action | Check if resource name is available |
> | Działanie | Microsoft.NetApp/locations/operationresults/read | Reads an operation result resource. |
> | Działanie | Microsoft.NetApp/locations/read | Reads an availability check resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/backupPolicies/delete | Deletes a backup policy resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/backupPolicies/read | Reads a backup policy resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/backupPolicies/write | Writes a backup policy resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Deletes a pool resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/read | Reads a pool resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/delete | Deletes a backup resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/read | Reads a backup resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/write | Writes a backup resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/break/action | Break volume replication relations |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Deletes a volume resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Reads a mount target resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Reads a volume resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/action | Reads the statuses of the Volume Replication. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/revert/action | Revert volume to specific snapshot |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Deletes a snapshot resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Reads a snapshot resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Writes a snapshot resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Writes a volume resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/capacityPools/write | Writes a pool resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/delete | Deletes an account resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/read | Reads an account resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/vaults/read | Reads a vault resource. |
> | Działanie | Microsoft.NetApp/netAppAccounts/write | Writes an account resource. |
> | Działanie | Microsoft.NetApp/Operations/read | Reads an operation resources. |
> | Działanie | Microsoft.NetApp/register/action | Registers Subscription with Microsoft.NetApp resource provider |
> | Działanie | Microsoft.NetApp/unregister/action | Unregisters Subscription with Microsoft.NetApp resource provider |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Get Application Gateway available Request Headers |
> | Działanie | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Get Application Gateway available Response Header |
> | Działanie | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Get Application Gateway available Server Variables |
> | Działanie | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl Predefined Policy |
> | Działanie | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway available Ssl Options |
> | Działanie | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Gets Application Gateway Available Waf Rule Sets |
> | Działanie | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joins an application gateway backend address pool. Brak alertów. |
> | Działanie | Microsoft.Network/applicationGateways/backendhealth/action | Gets an application gateway backend health |
> | Działanie | Microsoft.Network/applicationGateways/delete | Deletes an application gateway |
> | Działanie | Microsoft.Network/applicationGateways/getBackendHealthOnDemand/action | Gets an application gateway backend health on demand for given http setting and backend pool |
> | Działanie | Microsoft.Network/applicationGateways/read | Gets an application gateway |
> | Działanie | Microsoft.Network/applicationGateways/start/action | Starts an application gateway |
> | Działanie | Microsoft.Network/applicationGateways/stop/action | Stops an application gateway |
> | Działanie | Microsoft.Network/applicationGateways/write | Creates an application gateway or updates an application gateway |
> | Działanie | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Deletes an Application Gateway WAF policy |
> | Działanie | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Gets an Application Gateway WAF policy |
> | Działanie | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Creates an Application Gateway WAF policy or updates an Application Gateway WAF policy |
> | Działanie | Microsoft.Network/applicationSecurityGroups/delete | Deletes an Application Security Group |
> | Działanie | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Joins an IP Configuration to Application Security Groups. Brak alertów. |
> | Działanie | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Joins a Security Rule to Application Security Groups. Brak alertów. |
> | Działanie | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Lists IP Configurations in the ApplicationSecurityGroup |
> | Działanie | Microsoft.Network/applicationSecurityGroups/read | Gets an Application Security Group ID. |
> | Działanie | Microsoft.Network/applicationSecurityGroups/write | Creates an Application Security Group, or updates an existing Application Security Group. |
> | Działanie | Microsoft.Network/azureFirewallFqdnTags/read | Gets Azure Firewall FQDN Tags |
> | Działanie | Microsoft.Network/azurefirewalls/delete | Delete Azure Firewall |
> | Działanie | Microsoft.Network/azurefirewalls/read | Get Azure Firewall |
> | Działanie | Microsoft.Network/azurefirewalls/write | Creates or updates an Azure Firewall |
> | Działanie | Microsoft.Network/bastionHosts/createbsl/action | Creates shareable urls for the VMs under a bastion and returns the urls |
> | Działanie | Microsoft.Network/bastionHosts/delete | Deletes a Bastion Host |
> | Działanie | Microsoft.Network/bastionHosts/deletebsl/action | Deletes shareable urls for the provided VMs under a bastion |
> | Działanie | Microsoft.Network/bastionHosts/disconnectactivesessions/action | Disconnect given Active Sessions in the Bastion Host |
> | Działanie | Microsoft.Network/bastionHosts/getactivesessions/action | Get Active Sessions in the Bastion Host |
> | Działanie | Microsoft.Network/bastionHosts/getbsl/action | Zwraca adresy URL możliwe do udostępniania dla określonych maszyn wirtualnych w podsieci bastionu pod warunkiem, że ich adresy URL są tworzone |
> | Działanie | Microsoft.Network/bastionHosts/read | Pobiera hosta bastionu |
> | Działanie | Microsoft. Network/bastionHosts/Write | Tworzenie lub aktualizowanie hosta bastionu |
> | Działanie | Microsoft.Network/bgpServiceCommunities/read | Pobierz społeczności usługi BGP |
> | Działanie | Microsoft.Network/checkFrontDoorNameAvailability/action | Sprawdza, czy nazwa drzwi czołowych jest dostępna |
> | Działanie | Microsoft.Network/checkTrafficManagerNameAvailability/action | Sprawdza dostępność Traffic Manager względnej nazwy DNS. |
> | Działanie | Microsoft. Network/Connections/Delete | Deletes VirtualNetworkGatewayConnection |
> | Działanie | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Działanie | Microsoft. Network/Connections/odwoływanie/akcja | Oznacza stan połączenia usługi Express Route jako odwołane |
> | Działanie | Microsoft. Network/Connections/SharedKey/Action | Pobierz VirtualNetworkGatewayConnection SharedKey |
> | Działanie | Microsoft. Network/Connections/sharedKey/Read | Pobiera VirtualNetworkGatewayConnection SharedKey |
> | Działanie | Microsoft. Network/Connections/sharedKey/Write | Tworzy lub aktualizuje istniejące VirtualNetworkGatewayConnection SharedKey |
> | Działanie | Microsoft. Network/Connections/startpacketcapture/Action | Uruchamia przechwycenie pakietu połączenia bramy Virtual Network. |
> | Działanie | Microsoft. Network/Connections/stoppacketcapture/Action | Powoduje zatrzymanie przechwytywania pakietu połączenia bramy Virtual Network. |
> | Działanie | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Pobiera konfigurację urządzenia sieci VPN VirtualNetworkGatewayConnection |
> | Działanie | Microsoft. Network/Connections/Write | Tworzy lub aktualizuje istniejący VirtualNetworkGatewayConnection |
> | Działanie | Microsoft.Network/ddosCustomPolicies/delete | Usuwa zasady niestandardowe DDoS |
> | Działanie | Microsoft.Network/ddosCustomPolicies/read | Pobiera DDoS dostosowaną definicję zasad |
> | Działanie | Microsoft.Network/ddosCustomPolicies/write | Tworzy dostosowane zasady DDoS lub aktualizuje istniejące dostosowane zasady DDoS |
> | Działanie | Microsoft.Network/ddosProtectionPlans/delete | Usuwa plan DDoS Protection |
> | Działanie | Microsoft.Network/ddosProtectionPlans/join/action | Dołącza do planu DDoS Protection. Brak alertów. |
> | Działanie | Microsoft.Network/ddosProtectionPlans/read | Pobiera plan DDoS Protection |
> | Działanie | Microsoft.Network/ddosProtectionPlans/write | Tworzy plan DDoS Protection lub aktualizuje plan DDoS Protection  |
> | Działanie | Microsoft.Network/dnsoperationresults/read | Pobiera wyniki operacji DNS |
> | Działanie | Microsoft.Network/dnsoperationstatuses/read | Pobiera stan operacji DNS  |
> | Działanie | Microsoft. Network/dnszones/A/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "A" ze strefy DNS. |
> | Działanie | Microsoft.Network/dnszones/A/read | Pobierz zestaw rekordów typu "A" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/A/Write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/dnszones/AAAA/delete | Usuń zestaw rekordów o danej nazwie i wpisz "AAAA" ze strefy DNS. |
> | Działanie | Microsoft.Network/dnszones/AAAA/read | Pobierz zestaw rekordów typu "AAAA" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/dnszones/AAAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/dnszones/all/read | Pobiera zestawy rekordów DNS dla różnych typów |
> | Działanie | Microsoft. Network/dnszones/CAA/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "CAA" ze strefy DNS. |
> | Działanie | Microsoft.Network/dnszones/CAA/read | Pobierz zestaw rekordów typu "CAA" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/CAA/Write | Utwórz lub zaktualizuj zestaw rekordów typu "CAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/dnszones/CNAME/delete | Usuń zestaw rekordów o danej nazwie i wpisz "CNAME" ze strefy DNS. |
> | Działanie | Microsoft.Network/dnszones/CNAME/read | Pobierz zestaw rekordów typu "CNAME" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/dnszones/CNAME/write | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/dnszones/delete | Usuń strefę DNS w formacie JSON. Właściwości strefy obejmują Tagi, ETag, numberOfRecordSets i maxNumberOfRecordSets. |
> | Działanie | Microsoft. Network/dnszones/MX/Delete | Usuń zestaw rekordów o danej nazwie i typie "MX" ze strefy DNS. |
> | Działanie | Microsoft. Network/dnszones/MX/odczyt | Pobierz zestaw rekordów typu "MX" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/MX/Write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/dnszones/NS/delete | Usuwa zestaw rekordów DNS typu NS |
> | Działanie | Microsoft.Network/dnszones/NS/read | Pobiera zestaw rekordów DNS typu NS |
> | Działanie | Microsoft.Network/dnszones/NS/write | Tworzy lub aktualizuje zestaw rekordów DNS typu NS |
> | Działanie | Microsoft. Network/dnszones/PTR/usuwanie | Usuń zestaw rekordów o danej nazwie i typie "PTR" ze strefy DNS. |
> | Działanie | Microsoft.Network/dnszones/PTR/read | Pobierz zestaw rekordów typu "PTR" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/PTR/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/dnszones/read | Pobierz strefę DNS w formacie JSON. Właściwości strefy obejmują Tagi, ETag, numberOfRecordSets i maxNumberOfRecordSets. Należy zauważyć, że to polecenie nie pobiera zestawów rekordów zawartych w strefie. |
> | Działanie | Microsoft.Network/dnszones/recordsets/read | Pobiera zestawy rekordów DNS dla różnych typów |
> | Działanie | Microsoft.Network/dnszones/SOA/read | Pobiera zestaw rekordów DNS typu SOA |
> | Działanie | Microsoft.Network/dnszones/SOA/write | Tworzy lub aktualizuje zestaw rekordów DNS typu SOA |
> | Działanie | Microsoft.Network/dnszones/SRV/delete | Usuń zestaw rekordów o danej nazwie i typie "SRV" ze strefy DNS. |
> | Działanie | Microsoft.Network/dnszones/SRV/read | Pobierz zestaw rekordów typu "SRV" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/dnszones/SRV/write | Utwórz lub zaktualizuj zestaw rekordów typu SRV |
> | Działanie | Microsoft.Network/dnszones/TXT/delete | Usuń zestaw rekordów o danej nazwie i typie "TXT" ze strefy DNS. |
> | Działanie | Microsoft.Network/dnszones/TXT/read | Pobierz zestaw rekordów typu "TXT" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/dnszones/TXT/write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/dnszones/write | Utwórz lub zaktualizuj strefę DNS w grupie zasobów.  Służy do aktualizowania tagów w zasobie strefy DNS. Należy pamiętać, że tego polecenia nie można użyć do tworzenia lub aktualizowania zestawów rekordów w strefie. |
> | Działanie | Microsoft. Network/expressRouteCircuits/autoryzacje/usuwanie | Usuwa autoryzację ExpressRoute |
> | Działanie | Microsoft.Network/expressRouteCircuits/authorizations/read | Pobiera autoryzację ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/autoryzacje/zapis | Tworzy lub aktualizuje istniejącą autoryzację ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Delete | Usuwa ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Join/Action | Sprzęga obwód trasy Express. Brak alertów. |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/arpTables/Read | Pobiera ExpressRoute komunikacji równorzędnej ArpTable |
> | Działanie | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Usuwa połączenie ExpressRoute |
> | Działanie | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Pobiera połączenie ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/Connections/Write | Tworzy lub aktualizuje istniejący zasób połączenia usługi ExpressRoute |
> | Działanie | Microsoft.Network/expressRouteCircuits/peerings/delete | Usuwa komunikację równorzędną ExpressRoute |
> | Działanie | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Pobiera połączenie obwodu trasy usługi peer Express |
> | Działanie | Microsoft.Network/expressRouteCircuits/peerings/read | Pobiera komunikację równorzędną ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/routeTables/Read | Pobiera trasę komunikacji równorzędnej ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/routeTablesSummary/Read | Pobiera podsumowanie tras komunikacji równorzędnej ExpressRoute |
> | Działanie | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Pobiera stat ExpressRoute komunikacji równorzędnej |
> | Działanie | Microsoft.Network/expressRouteCircuits/peerings/write | Tworzy lub aktualizuje istniejącą komunikację równorzędną ExpressRoute |
> | Działanie | Microsoft.Network/expressRouteCircuits/read | Pobierz ExpressRoute |
> | Działanie | Microsoft.Network/expressRouteCircuits/stats/read | Pobiera ExpressRoute stat |
> | Działanie | Microsoft.Network/expressRouteCircuits/write | Tworzy lub aktualizuje istniejący ExpressRoute |
> | Działanie | Microsoft.Network/expressRouteCrossConnections/join/action | Sprzęga połączenie krzyżowe usługi Express Route. Brak alertów. |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Peers/arpTables/Read | Pobiera tabelę ARP komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Usuwa komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft.Network/expressRouteCrossConnections/peerings/read | Pobiera komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Peers/routeTables/Read | Pobiera tabelę tras komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Peers/routeTableSummary/Read | Pobiera podsumowanie tabeli tras komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft.Network/expressRouteCrossConnections/peerings/write | Tworzy komunikację równorzędną połączenia krzyżowego usługi Express Route lub aktualizuje istniejącą komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft.Network/expressRouteCrossConnections/read | Pobierz połączenie krzyżowe usługi Express Route |
> | Działanie | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Usuwa połączenie usługi Express Route |
> | Działanie | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Pobiera połączenie usługi Express Route |
> | Działanie | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Tworzy połączenie usługi Express Route lub aktualizuje istniejące połączenie usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteGateways/Join/Action | Umożliwia dołączenie do bramy Express Route Gateway. Brak alertów. |
> | Działanie | Microsoft.Network/expressRouteGateways/read | Pobierz bramę Express Route Gateway |
> | Działanie | Microsoft. Network/expressRoutePorts/Delete | Usuwa ExpressRoutePorts |
> | Działanie | Microsoft. Network/expressRoutePorts/Join/Action | Sprzęga porty routingu Express. Brak alertów. |
> | Działanie | Microsoft.Network/expressRoutePorts/links/read | Gets ExpressRouteLink |
> | Działanie | Microsoft.Network/expressRoutePorts/read | Gets ExpressRoutePorts |
> | Działanie | Microsoft. Network/expressRoutePorts/Write | Tworzy lub aktualizuje ExpressRoutePorts |
> | Działanie | Microsoft.Network/expressRoutePortsLocations/read | Pobierz lokalizacje portów Express Route |
> | Działanie | Microsoft.Network/expressRouteServiceProviders/read | Pobiera dostawców Route Service ekspresowych |
> | Działanie | Microsoft. Network/firewallPolicies/Delete | Usuwa zasady zapory |
> | Działanie | Microsoft. Network/firewallPolicies/Join/Action | Przyłącza zasady zapory. Brak alertów. |
> | Działanie | Microsoft. Network/firewallPolicies/odczyt | Pobiera zasady zapory |
> | Działanie | Microsoft. Network/firewallPolicies/ruleGroups/Delete | Usuwa grupę reguł zasad zapory |
> | Działanie | Microsoft. Network/firewallPolicies/ruleGroups/odczyt | Pobiera grupę reguł zasad zapory |
> | Działanie | Microsoft. Network/firewallPolicies/ruleGroups/Write | Tworzy grupę reguł zasad zapory lub aktualizuje istniejącą grupę reguł zasad zapory |
> | Działanie | Microsoft. Network/firewallPolicies/Write | Tworzy zasady zapory lub aktualizuje istniejące zasady zapory |
> | Działanie | Microsoft.Network/frontDoors/backendPools/delete | Usuwa pulę zaplecza |
> | Działanie | Microsoft.Network/frontDoors/backendPools/read | Pobiera pulę zaplecza |
> | Działanie | Microsoft.Network/frontDoors/backendPools/write | Tworzy lub aktualizuje pulę zaplecza |
> | Działanie | Microsoft.Network/frontDoors/delete | Usuwa drzwiczki z przodu |
> | Działanie | Microsoft. Network/usługi frontdoor/frontendEndpoints/Delete | Usuwa punkt końcowy frontonu |
> | Działanie | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Wyłącza protokół HTTPS w punkcie końcowym frontonu |
> | Działanie | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Włącza protokół HTTPS w punkcie końcowym frontonu |
> | Działanie | Microsoft.Network/frontDoors/frontendEndpoints/read | Pobiera punkt końcowy frontonu |
> | Działanie | Microsoft.Network/frontDoors/frontendEndpoints/write | Tworzy lub aktualizuje punkt końcowy frontonu |
> | Działanie | Microsoft.Network/frontDoors/healthProbeSettings/delete | Usuwa ustawienia sondy kondycji |
> | Działanie | Microsoft.Network/frontDoors/healthProbeSettings/read | Pobiera ustawienia sondy kondycji |
> | Działanie | Microsoft.Network/frontDoors/healthProbeSettings/write | Tworzy lub aktualizuje ustawienia sondy kondycji |
> | Działanie | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Działanie | Microsoft.Network/frontDoors/loadBalancingSettings/read | Pobiera ustawienia równoważenia obciążenia |
> | Działanie | Microsoft.Network/frontDoors/loadBalancingSettings/write | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Działanie | Microsoft. Network/usługi frontdoor/przeczyszczanie/akcja | Przeczyszczanie zawartości w pamięci podręcznej z drzwi przednich |
> | Działanie | Microsoft.Network/frontDoors/read | Pobiera tylne drzwi |
> | Działanie | Microsoft.Network/frontDoors/routingRules/delete | Usuwa regułę routingu |
> | Działanie | Microsoft.Network/frontDoors/routingRules/read | Pobiera regułę routingu |
> | Działanie | Microsoft.Network/frontDoors/routingRules/write | Tworzy lub aktualizuje regułę routingu |
> | Działanie | Microsoft.Network/frontDoors/validateCustomDomain/action | Sprawdza poprawność punktu końcowego frontonu dla drzwi z przodu |
> | Działanie | Microsoft.Network/frontDoors/write | Tworzy lub aktualizuje tylne drzwi |
> | Działanie | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Pobiera zestawy reguł zarządzane przez zaporę aplikacji sieci Web |
> | Działanie | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Usuwa zasady zapory aplikacji sieci Web |
> | Działanie | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Join/Action | Sprzęga zasady zapory aplikacji sieci Web. Brak alertów. |
> | Działanie | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Pobiera zasady zapory aplikacji sieci Web |
> | Działanie | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Tworzy lub aktualizuje zasady zapory aplikacji sieci Web |
> | Działanie | Microsoft. Network/ipGroups/Delete | Usuwa IpGroup |
> | Działanie | Microsoft. Network/ipGroups/Join/Action | Sprzęga IpGroup. Brak alertów. |
> | Działanie | Microsoft. Network/ipGroups/odczyt | Pobiera IpGroup |
> | Działanie | Microsoft. Network/ipGroups/Write | Tworzy IpGroup lub aktualizuje istniejący IpGroups |
> | Działanie | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | Działanie | Microsoft.Network/loadBalancers/backendAddressPools/read | Pobiera definicję puli adresów zaplecza modułu równoważenia obciążenia |
> | Działanie | Microsoft.Network/loadBalancers/delete | Usuwa moduł równoważenia obciążenia |
> | Działanie | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Sprzęga konfigurację adresu IP frontonu Load Balancer. Brak alertów. |
> | Działanie | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Pobiera definicję konfiguracji adresu IP frontonu modułu równoważenia obciążenia |
> | Działanie | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Przyłącza do puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia. Brak alertów. |
> | Działanie | Microsoft.Network/loadBalancers/inboundNatPools/read | Pobiera definicję puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Działanie | Microsoft.Network/loadBalancers/inboundNatRules/delete | Deletes a load balancer inbound nat rule |
> | Działanie | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joins a load balancer inbound nat rule. Brak alertów. |
> | Działanie | Microsoft.Network/loadBalancers/inboundNatRules/read | Gets a load balancer inbound nat rule definition |
> | Działanie | Microsoft.Network/loadBalancers/inboundNatRules/write | Creates a load balancer inbound nat rule or updates an existing load balancer inbound nat rule |
> | Działanie | Microsoft.Network/loadBalancers/loadBalancingRules/read | Gets a load balancer load balancing rule definition |
> | Działanie | Microsoft.Network/loadBalancers/networkInterfaces/read | Gets references to all the network interfaces under a load balancer |
> | Działanie | Microsoft.Network/loadBalancers/outboundRules/read | Gets a load balancer outbound rule definition |
> | Działanie | Microsoft.Network/loadBalancers/probes/join/action | Allows using probes of a load balancer. For example, with this permission healthProbe property of VM scale set can reference the probe. Brak alertów. |
> | Działanie | Microsoft.Network/loadBalancers/probes/read | Gets a load balancer probe |
> | Działanie | Microsoft.Network/loadBalancers/read | Gets a load balancer definition |
> | Działanie | Microsoft.Network/loadBalancers/virtualMachines/read | Gets references to all the virtual machines under a load balancer |
> | Działanie | Microsoft.Network/loadBalancers/write | Creates a load balancer or updates an existing load balancer |
> | Działanie | Microsoft.Network/localnetworkgateways/delete | Deletes LocalNetworkGateway |
> | Działanie | Microsoft.Network/localnetworkgateways/read | Gets LocalNetworkGateway |
> | Działanie | Microsoft.Network/localnetworkgateways/write | Creates or updates an existing LocalNetworkGateway |
> | Działanie | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Gets Auto Approved Private Link Services |
> | Działanie | Microsoft.Network/locations/availableDelegations/read | Gets Available Delegations |
> | Działanie | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Gets available Private Endpoint resources |
> | Działanie | Microsoft.Network/locations/availableServiceAliases/read | Gets Available Service Aliases |
> | Działanie | Microsoft.Network/locations/bareMetalTenants/action | Allocates or validates a Bare Metal Tenant |
> | Działanie | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Checks Accelerated Networking support |
> | Działanie | Microsoft.Network/locations/checkDnsNameAvailability/read | Checks if dns label is available at the specified location |
> | Działanie | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Checks Private Link Service Visibility |
> | Działanie | Microsoft.Network/locations/operationResults/read | Gets operation result of an async POST or DELETE operation |
> | Działanie | Microsoft.Network/locations/operations/read | Gets operation resource that represents status of an asynchronous operation |
> | Działanie | Microsoft.Network/locations/serviceTags/read | Get Service Tags |
> | Działanie | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Gets supported virtual machines sizes |
> | Działanie | Microsoft.Network/locations/usages/read | Gets the resources usage metrics |
> | Działanie | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Gets a list of available Virtual Network Endpoint Services |
> | Działanie | Microsoft.Network/networkIntentPolicies/delete | Deletes an Network Intent Policy |
> | Działanie | Microsoft.Network/networkIntentPolicies/read | Gets an Network Intent Policy Description |
> | Działanie | Microsoft.Network/networkIntentPolicies/write | Creates an Network Intent Policy or updates an existing Network Intent Policy |
> | Działanie | Microsoft.Network/networkInterfaces/delete | Deletes a network interface |
> | Działanie | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Get Network Security Groups configured On Network Interface Of The Vm |
> | Działanie | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Get Route Table configured On Network Interface Of The Vm |
> | Działanie | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Joins a Network Interface IP Configuration. Brak alertów. |
> | Działanie | Microsoft.Network/networkInterfaces/ipconfigurations/read | Gets a network interface ip configuration definition.  |
> | Działanie | Microsoft.Network/networkInterfaces/join/action | Joins a Virtual Machine to a network interface. Brak alertów. |
> | Działanie | Microsoft.Network/networkInterfaces/loadBalancers/read | Gets all the load balancers that the network interface is part of |
> | Działanie | Microsoft.Network/networkInterfaces/read | Gets a network interface definition.  |
> | Działanie | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Deletes a Network Interface Tap Configuration. |
> | Działanie | Microsoft.Network/networkInterfaces/tapConfigurations/read | Gets a Network Interface Tap Configuration. |
> | Działanie | Microsoft.Network/networkInterfaces/tapConfigurations/write | Creates a Network Interface Tap Configuration or updates an existing Network Interface Tap Configuration. |
> | Działanie | Microsoft.Network/networkInterfaces/write | Creates a network interface or updates an existing network interface.  |
> | Działanie | Microsoft.Network/networkProfiles/delete | Deletes a Network Profile |
> | Działanie | Microsoft.Network/networkProfiles/read | Gets a Network Profile |
> | Działanie | Microsoft.Network/networkProfiles/removeContainers/action | Removes Containers |
> | Działanie | Microsoft.Network/networkProfiles/setContainers/action | Sets Containers |
> | Działanie | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Sets Container Network Interfaces |
> | Działanie | Microsoft.Network/networkProfiles/write | Creates or updates a Network Profile |
> | Działanie | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Gets a default security rule definition |
> | Działanie | Microsoft.Network/networkSecurityGroups/delete | Deletes a network security group |
> | Działanie | Microsoft.Network/networkSecurityGroups/join/action | Joins a network security group. Brak alertów. |
> | Działanie | Microsoft.Network/networkSecurityGroups/read | Gets a network security group definition |
> | Działanie | Microsoft.Network/networkSecurityGroups/securityRules/delete | Deletes a security rule |
> | Działanie | Microsoft.Network/networkSecurityGroups/securityRules/read | Gets a security rule definition |
> | Działanie | Microsoft.Network/networkSecurityGroups/securityRules/write | Creates a security rule or updates an existing security rule |
> | Działanie | Microsoft.Network/networkSecurityGroups/write | Creates a network security group or updates an existing network security group |
> | Działanie | Microsoft.Network/networkWatchers/availableProvidersList/action | Returns all available internet service providers for a specified Azure region. |
> | Działanie | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Returns the relative latency score for internet service providers from a specified location to Azure regions. |
> | Działanie | Microsoft.Network/networkWatchers/configureFlowLog/action | Configures flow logging for a target resource. |
> | Działanie | Microsoft.Network/networkWatchers/connectionMonitors/delete | Deletes a Connection Monitor |
> | Działanie | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Query monitoring connectivity between specified endpoints |
> | Działanie | Microsoft.Network/networkWatchers/connectionMonitors/read | Get Connection Monitor details |
> | Działanie | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Start monitoring connectivity between specified endpoints |
> | Działanie | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Stop/pause monitoring connectivity between specified endpoints |
> | Działanie | Microsoft.Network/networkWatchers/connectionMonitors/write | Creates a Connection Monitor |
> | Działanie | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifies the possibility of establishing a direct TCP connection from a virtual machine to a given endpoint including another VM or an arbitrary remote server. |
> | Działanie | Microsoft.Network/networkWatchers/delete | Deletes a network watcher |
> | Działanie | Microsoft.Network/networkWatchers/flowLogs/delete | Deletes a Flow Log |
> | Działanie | Microsoft.Network/networkWatchers/flowLogs/read | Get Flow Log details |
> | Działanie | Microsoft.Network/networkWatchers/flowLogs/write | Creates a Flow Log |
> | Działanie | Microsoft.Network/networkWatchers/ipFlowVerify/action | Returns whether the packet is allowed or denied to or from a particular destination. |
> | Działanie | Microsoft.Network/networkWatchers/lenses/delete | Deletes a Lens |
> | Działanie | Microsoft.Network/networkWatchers/lenses/query/action | Query monitoring network traffic on a specified endpoint |
> | Działanie | Microsoft.Network/networkWatchers/lenses/read | Get Lens details |
> | Działanie | Microsoft.Network/networkWatchers/lenses/start/action | Start monitoring network traffic on a specified endpoint |
> | Działanie | Microsoft.Network/networkWatchers/lenses/stop/action | Stop/pause monitoring network traffic on a specified endpoint |
> | Działanie | Microsoft.Network/networkWatchers/lenses/write | Creates a Lens |
> | Działanie | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostic of network configuration. |
> | Działanie | Microsoft.Network/networkWatchers/nextHop/action | For a specified target and destination IP address, return the next hop type and next hope IP address. |
> | Działanie | Microsoft.Network/networkWatchers/packetCaptures/delete | Deletes a packet capture |
> | Działanie | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Gets information about properties and status of a packet capture resource. |
> | Działanie | Microsoft.Network/networkWatchers/packetCaptures/read | Get the packet capture definition |
> | Działanie | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Stop the running packet capture session. |
> | Działanie | Microsoft.Network/networkWatchers/packetCaptures/write | Creates a packet capture |
> | Działanie | Microsoft.Network/networkWatchers/pingMeshes/delete | Deletes a PingMesh |
> | Działanie | Microsoft.Network/networkWatchers/pingMeshes/read | Get PingMesh details |
> | Działanie | Microsoft.Network/networkWatchers/pingMeshes/start/action | Start PingMesh between specified VMs |
> | Działanie | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Stop PingMesh between specified VMs |
> | Działanie | Microsoft.Network/networkWatchers/pingMeshes/write | Creates a PingMesh |
> | Działanie | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Batch query monitoring connectivity between specified endpoints |
> | Działanie | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Gets the status of flow logging on a resource. |
> | Działanie | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Gets the troubleshooting result from the previously run or currently running troubleshooting operation. |
> | Działanie | Microsoft.Network/networkWatchers/read | Pobierz definicję obserwatora sieciowego |
> | Działanie | Microsoft.Network/networkWatchers/securityGroupView/action | Wyświetl skonfigurowane i efektywne reguły sieciowej grupy zabezpieczeń zastosowane na maszynie wirtualnej. |
> | Działanie | Microsoft.Network/networkWatchers/topology/action | Pobiera widok poziomu sieci zasobów i ich relacje z grupą zasobów. |
> | Działanie | Microsoft.Network/networkWatchers/troubleshoot/action | Uruchamia rozwiązywanie problemów dotyczących zasobów sieciowych na platformie Azure. |
> | Działanie | Microsoft.Network/networkWatchers/write | Tworzy obserwatora sieciowego lub aktualizuje istniejący obserwator sieci |
> | Działanie | Microsoft.Network/operations/read | Pobierz dostępne operacje |
> | Działanie | Microsoft.Network/p2sVpnGateways/delete | Usuwa element P2SVpnGateway. |
> | Działanie | Microsoft. Network/p2sVpnGateways/disconnectp2svpnconnections/akcja | Rozłączanie połączeń sieci VPN P2S |
> | Działanie | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generuj profil sieci VPN dla P2SVpnGateway |
> | Działanie | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Pobiera kondycję połączenia sieci VPN P2S dla P2SVpnGateway |
> | Działanie | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/akcja | Pobiera szczegóły kondycji połączenia sieci VPN P2S dla P2SVpnGateway |
> | Działanie | Microsoft.Network/p2sVpnGateways/read | Pobiera P2SVpnGateway. |
> | Działanie | Microsoft.Network/p2sVpnGateways/write | P2SVpnGateway. |
> | Działanie | Microsoft.Network/privateDnsOperationResults/read | Pobiera wyniki operacji Prywatna strefa DNS |
> | Działanie | Microsoft.Network/privateDnsOperationStatuses/read | Pobiera stan operacji Prywatna strefa DNS |
> | Działanie | Microsoft.Network/privateDnsZones/A/delete | Usuń zestaw rekordów o danej nazwie i wpisz "A" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/A/read | Pobierz zestaw rekordów typu "A" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/privateDnsZones/A/write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/privateDnsZones/AAAA/delete | Usuń zestaw rekordów o danej nazwie i wpisz "AAAA" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/AAAA/read | Pobierz zestaw rekordów typu "AAAA" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/privateDnsZones/AAAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/privateDnsZones/ALL/read | Pobiera Prywatna strefa DNS zestawy rekordów dla różnych typów |
> | Działanie | Microsoft.Network/privateDnsZones/CNAME/delete | Usuń zestaw rekordów o danej nazwie i wpisz "CNAME" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/CNAME/read | Pobierz zestaw rekordów typu "CNAME" w ramach strefy Prywatna strefa DNS w formacie JSON. |
> | Działanie | Microsoft.Network/privateDnsZones/CNAME/write | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w ramach strefy Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/delete | Usuń strefę Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/MX/delete | Usuń zestaw rekordów o danej nazwie i typie "MX" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/MX/read | Pobierz zestaw rekordów typu "MX" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/privateDnsZones/MX/write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/privateDnsZones/PTR/delete | Usuń zestaw rekordów o danej nazwie i wpisz "PTR" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/PTR/read | Pobierz zestaw rekordów typu "PTR" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/privateDnsZones/PTR/write | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/privateDnsZones/read | Pobierz Prywatna strefa DNS właściwości strefy w formacie JSON. Należy zauważyć, że to polecenie nie pobiera sieci wirtualnych, do których jest połączona strefa Prywatna strefa DNS lub zestawy rekordów zawarte w strefie. |
> | Działanie | Microsoft.Network/privateDnsZones/recordsets/read | Pobiera Prywatna strefa DNS zestawy rekordów dla różnych typów |
> | Działanie | Microsoft.Network/privateDnsZones/SOA/read | Pobierz zestaw rekordów typu "SOA" w ramach strefy Prywatna strefa DNS w formacie JSON. |
> | Działanie | Microsoft.Network/privateDnsZones/SOA/write | Zaktualizuj zestaw rekordów typu "SOA" w ramach strefy Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/SRV/delete | Usuń zestaw rekordów o danej nazwie i typie "SRV" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/SRV/read | Pobierz zestaw rekordów typu "SRV" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/privateDnsZones/SRV/write | Utwórz lub zaktualizuj zestaw rekordów typu "SRV" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/privateDnsZones/TXT/delete | Usuń zestaw rekordów o danej nazwie i typie "TXT" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft.Network/privateDnsZones/TXT/read | Pobierz zestaw rekordów typu "TXT" w strefie Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft.Network/privateDnsZones/TXT/write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Usuń link strefy Prywatna strefa DNS do sieci wirtualnej. |
> | Działanie | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Pobierz łącze strefy Prywatna strefa DNS do właściwości sieci wirtualnej w formacie JSON. |
> | Działanie | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Utwórz lub zaktualizuj link strefy Prywatna strefa DNS do sieci wirtualnej. |
> | Działanie | Microsoft.Network/privateDnsZones/write | Utwórz lub zaktualizuj strefę Prywatna strefa DNS w grupie zasobów. Należy pamiętać, że tego polecenia nie można użyć do tworzenia lub aktualizowania linków sieci wirtualnej lub zestawów rekordów w strefie. |
> | Działanie | Microsoft. Network/privateEndpointRedirectMaps/odczyt | Pobiera prywatny punkt końcowy RedirectMap |
> | Działanie | Microsoft. Network/privateEndpointRedirectMaps/Write | Tworzy prywatny punkt końcowy RedirectMap lub aktualizuje istniejący prywatny punkt końcowy RedirectMap |
> | Działanie | Microsoft.Network/privateEndpoints/delete | Usuwa prywatny zasób punktu końcowego. |
> | Działanie | Microsoft.Network/privateEndpoints/read | Pobiera prywatny zasób punktu końcowego. |
> | Działanie | Microsoft.Network/privateEndpoints/write | Tworzy nowy prywatny punkt końcowy lub aktualizuje istniejący prywatny punkt końcowy. |
> | Działanie | Microsoft.Network/privateLinkServices/delete | Usuwa zasób usługi link prywatny. |
> | Działanie | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Usuwa połączenie prywatnego punktu końcowego. |
> | Działanie | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Pobiera definicję połączenia z prywatnym punktem końcowym. |
> | Działanie | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Tworzy nowe połączenie prywatnego punktu końcowego lub aktualizuje istniejące połączenie prywatnego punktu końcowego. |
> | Działanie | Microsoft.Network/privateLinkServices/read | Pobiera zasób usługi linku prywatnego. |
> | Działanie | Microsoft.Network/privateLinkServices/write | Tworzy nową usługę linku prywatnego lub aktualizuje istniejącą usługę linku prywatnego. |
> | Działanie | Microsoft.Network/publicIPAddresses/delete | Usuwa publiczny adres IP. |
> | Działanie | Microsoft.Network/publicIPAddresses/join/action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | Działanie | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Działanie | Microsoft.Network/publicIPAddresses/write | Tworzy publiczny adres IP lub aktualizuje istniejący publiczny adres IP.  |
> | Działanie | Microsoft. Network/publicIPPrefixes/Delete | Usuwa prefiks publicznego adresu IP |
> | Działanie | Microsoft. Network/publicIPPrefixes/Join/Action | Sprzęga PublicIPPrefix. Brak alertów. |
> | Działanie | Microsoft.Network/publicIPPrefixes/read | Pobiera definicję prefiksu publicznego adresu IP |
> | Działanie | Microsoft. Network/publicIPPrefixes/Write | Tworzy publiczny prefiks adresu IP lub aktualizuje istniejący prefiks publicznego adresu IP |
> | Działanie | Microsoft. Network/Register/Action | Rejestruje subskrypcję |
> | Działanie | Microsoft.Network/routeFilters/delete | Usuwa definicję filtru tras |
> | Działanie | Microsoft. Network/routeFilters/Join/Action | Sprzęga filtr tras. Brak alertów. |
> | Działanie | Microsoft.Network/routeFilters/read | Pobiera definicję filtru tras |
> | Działanie | Microsoft. Network/routeFilters/routeFilterRules/Delete | Usuwa definicję reguły filtru trasy |
> | Działanie | Microsoft.Network/routeFilters/routeFilterRules/read | Pobiera definicję reguły filtru trasy |
> | Działanie | Microsoft. Network/routeFilters/routeFilterRules/Write | Tworzy regułę filtru tras lub aktualizuje istniejącą regułę filtru tras |
> | Działanie | Microsoft.Network/routeFilters/write | Tworzy filtr trasy lub aktualizuje istniejący filtr tras |
> | Działanie | Microsoft.Network/routeTables/delete | Usuwa definicję tabeli tras |
> | Działanie | Microsoft.Network/routeTables/join/action | Sprzęga tabelę tras. Brak alertów. |
> | Działanie | Microsoft.Network/routeTables/read | Pobiera definicję tabeli tras |
> | Działanie | Microsoft.Network/routeTables/routes/delete | Usuwa definicję trasy |
> | Działanie | Microsoft.Network/routeTables/routes/read | Pobiera definicję trasy |
> | Działanie | Microsoft.Network/routeTables/routes/write | Tworzy trasę lub aktualizuje istniejącą trasę |
> | Działanie | Microsoft.Network/routeTables/write | Tworzy tabelę tras lub aktualizuje istniejącą tabelę tras |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/Delete | Usuwa zasady punktu końcowego usługi |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/Join/Action | Przyłącza zasady punktu końcowego usługi. Brak alertów. |
> | Działanie | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Przyłączanie podsieci do zasad punktu końcowego usługi. Brak alertów. |
> | Działanie | Microsoft.Network/serviceEndpointPolicies/read | Pobiera opis zasad punktu końcowego usługi |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Delete | Usuwa definicję zasad punktu końcowego usługi |
> | Działanie | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Pobiera opis definicji zasad punktu końcowego usługi |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Write | Tworzy definicję zasad punktu końcowego usługi lub aktualizuje istniejącą definicję zasad punktu końcowego usługi |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/Write | Tworzy zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi |
> | Działanie | Microsoft.Network/trafficManagerGeographicHierarchies/read | Pobiera Traffic Manager hierarchię geograficzną zawierającą regiony, które mogą być używane z metodą routingu ruchu geograficznego |
> | Działanie | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Usuwa punkt końcowy platformy Azure z istniejącego profilu Traffic Manager. Traffic Manager przestanie kierować ruch do usuniętego punkt końcowy platformy Azure. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Pobiera punkt końcowy platformy Azure, które należy do profilu Traffic Manager, w tym wszystkie właściwości tego punkt końcowy platformy Azure. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Dodaj nowy punkt końcowy platformy Azure w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącej punkt końcowy platformy Azure w tym profilu Traffic Manager. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/delete | Usuń profil Traffic Manager. Wszystkie ustawienia skojarzone z profilem Traffic Manager zostaną utracone i nie będzie można już używać profilu do kierowania ruchem. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Usuwa zewnętrzny punkt końcowy z istniejącego profilu Traffic Manager. Traffic Manager zatrzyma kierowanie ruchu do usuniętego zewnętrznego punktu końcowego. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Pobiera zewnętrzny punkt końcowy, który należy do profilu Traffic Manager, łącznie ze wszystkimi właściwościami tego zewnętrznego punktu końcowego. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Dodaj nowy zewnętrzny punkt końcowy w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącego zewnętrznego punktu końcowego w tym profilu Traffic Manager. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Pobiera Traffic Managerową mapę cieplną dla danego profilu Traffic Manager, który zawiera liczby zapytań i dane opóźnienia według lokalizacji i źródłowego adresu IP. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Usuwa zagnieżdżony punkt końcowy z istniejącego profilu Traffic Manager. Traffic Manager przestanie kierować ruch do usuniętego zagnieżdżonego punktu końcowego. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Pobiera zagnieżdżony punkt końcowy, który należy do profilu Traffic Manager, łącznie ze wszystkimi właściwościami tego zagnieżdżonego punktu końcowego. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Dodaj nowy zagnieżdżony punkt końcowy w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącego zagnieżdżonego punktu końcowego w tym Traffic Manager profilu. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/read | Pobierz konfigurację profilu Traffic Manager. Obejmuje to ustawienia DNS, ustawienia routingu ruchu, ustawienia monitorowania punktu końcowego oraz listę punktów końcowych kierowanych przez ten profil Traffic Manager. |
> | Działanie | Microsoft.Network/trafficManagerProfiles/write | Utwórz profil Traffic Manager lub zmodyfikuj konfigurację istniejącego profilu Traffic Manager.<br>Obejmuje to Włączanie lub wyłączanie profilu oraz modyfikowanie ustawień DNS, ustawień routingu ruchu lub ustawień monitorowania punktu końcowego.<br>Punkty końcowe kierowane przez profil Traffic Manager można dodawać, usuwać, włączać lub wyłączać. |
> | Działanie | Microsoft. Network/trafficManagerUserMetricsKeys/Delete | Usuwa klucz poziomu subskrypcji używany do zbierania metryk użytkownika w czasie rzeczywistym. |
> | Działanie | Microsoft.Network/trafficManagerUserMetricsKeys/read | Pobiera klucz poziomu subskrypcji używany na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Działanie | Microsoft.Network/trafficManagerUserMetricsKeys/write | Tworzy nowy klucz poziomu subskrypcji, który będzie używany na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Działanie | Microsoft. Network/Unregister/Action | Wyrejestrowuje subskrypcję |
> | Działanie | Microsoft.Network/virtualHubs/delete | Usuwa koncentrator wirtualny |
> | Działanie | Microsoft. Network/virtualHubs/effectiveRoutes/akcja | Pobiera obowiązującą trasę skonfigurowaną w koncentratorze wirtualnym |
> | Działanie | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Usuwa element HubVirtualNetworkConnection |
> | Działanie | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | Działanie | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Utwórz lub zaktualizuj HubVirtualNetworkConnection |
> | Działanie | Microsoft.Network/virtualHubs/read | Pobierz koncentrator wirtualny |
> | Działanie | Microsoft. Network/virtualHubs/routeTables/Delete | Usuń VirtualHubRouteTableV2 |
> | Działanie | Microsoft. Network/virtualHubs/routeTables/odczyt | Pobierz VirtualHubRouteTableV2 |
> | Działanie | Microsoft. Network/virtualHubs/routeTables/Write | Utwórz lub zaktualizuj VirtualHubRouteTableV2 |
> | Działanie | Microsoft.Network/virtualHubs/write | Tworzenie lub aktualizowanie koncentratora wirtualnego |
> | Działanie | Microsoft. Network/virtualnetworkgateways/Connections/Read | Get VirtualNetworkGatewayConnection |
> | Działanie | Microsoft. Network/virtualNetworkGateways/Delete | Usuwa element virtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/akcja | Rozłączanie połączeń sieci VPN bramy sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualnetworkgateways/generatevpnclientpackage/akcja | Generuj pakiet klienta dla virtualNetworkGateway |
> | Działanie | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Generuj pakiet VpnProfile dla VirtualNetworkGateway |
> | Działanie | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Pobiera trasy anonsowane przez virtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/getbgppeerstatus/akcja | Pobiera stan elementu równorzędnego BGP virtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/getlearnedroutes/akcja | Pobiera virtualnetworkgatewayne trasy |
> | Działanie | Microsoft. Network/virtualnetworkgateways/getvpnclientconnectionhealth/akcja | Pobierz kondycję połączenia klienta sieci VPN dla VirtualNetworkGateway |
> | Działanie | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Pobierz parametry klienta IPSec dla klienta VirtualNetworkGateway P2S. |
> | Działanie | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Pobiera adres URL wstępnie wygenerowanego pakietu profilu klienta VPN |
> | Działanie | Microsoft.Network/virtualNetworkGateways/read | Pobiera VirtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/Reset/akcja | Resetuje virtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/akcja | Zresetuj klucz współużytkowany klienta dla klienta VirtualNetworkGateway P2S. |
> | Działanie | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Ustaw parametry klienta IPSec dla klienta VirtualNetworkGateway P2S. |
> | Działanie | Microsoft. Network/virtualnetworkgateways/startpacketcapture/akcja | Uruchamia przechwytywanie pakietów bramy Virtual Network. |
> | Działanie | Microsoft. Network/virtualnetworkgateways/stoppacketcapture/akcja | Powoduje zatrzymanie przechwytywania pakietu Virtual Network Gateway. |
> | Działanie | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Wyświetla listę obsługiwanych urządzeń sieci VPN |
> | Działanie | Microsoft.Network/virtualNetworkGateways/write | Tworzy lub aktualizuje VirtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualNetworks/BastionHosts/akcja | Pobiera odwołania do hostów bastionu w Virtual Network. |
> | Działanie | Microsoft. Network/virtualNetworks/bastionHosts/default/Action | Pobiera odwołania do hostów bastionu w Virtual Network. |
> | Działanie | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Sprawdź, czy adres IP jest dostępny w określonej sieci wirtualnej |
> | Działanie | Microsoft.Network/virtualNetworks/delete | Usuwa sieć wirtualną |
> | Działanie | Microsoft. Network/virtualNetworks/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Działanie | Microsoft. Network/virtualNetworks/peer/Action | Tworzy równorzędną sieć wirtualną z inną siecią wirtualną |
> | Działanie | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Delete | Usuwa zasady punktu końcowego usługi kontekstowej |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Read | Pobiera zasady punktu końcowego usługi kontekstowej |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Write | Tworzy kontekstowe zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi kontekstowej |
> | Działanie | Microsoft.Network/virtualNetworks/subnets/delete | Usuwa podsieć sieci wirtualnej |
> | Działanie | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Działanie | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Działanie | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Przygotowuje podsieć przez zastosowanie niezbędnych zasad sieciowych |
> | Działanie | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/unprepareNetworkPolicies/Action | Cofnięcie przygotowania podsieci przez usunięcie zastosowanych zasad sieciowych |
> | Działanie | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w podsieci sieci wirtualnej |
> | Działanie | Microsoft.Network/virtualNetworks/subnets/write | Tworzy podsieć sieci wirtualnej lub aktualizuje istniejącą podsieć sieci wirtualnej |
> | Działanie | Microsoft.Network/virtualNetworks/usages/read | Uzyskaj użycie adresów IP dla każdej podsieci sieci wirtualnej |
> | Działanie | Microsoft.Network/virtualNetworks/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Delete | Usuwa komunikację równorzędną sieci wirtualnej |
> | Działanie | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Pobiera definicję komunikacji równorzędnej sieci wirtualnej |
> | Działanie | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Tworzy komunikację równorzędną sieci wirtualnej lub aktualizuje istniejącą komunikację równorzędną sieci wirtualnej |
> | Działanie | Microsoft.Network/virtualNetworks/write | Tworzy sieć wirtualną lub aktualizuje istniejącą sieć wirtualną |
> | Działanie | Microsoft.Network/virtualNetworkTaps/delete | Usuń naciśnięcie Virtual Network |
> | Działanie | Microsoft. Network/virtualNetworkTaps/Join/Action | Umożliwia dołączenie do programu TAP sieci wirtualnej. Brak alertów. |
> | Działanie | Microsoft.Network/virtualNetworkTaps/read | Pobierz Virtual Network naciśnij pozycję |
> | Działanie | Microsoft.Network/virtualNetworkTaps/write | Utwórz lub zaktualizuj Virtual Network TAP |
> | Działanie | Microsoft. Network/virtualRouters/Delete | Usuwa element VirtualRouter |
> | Działanie | Microsoft. Network/virtualRouters/Join/Action | Sprzęga VirtualRouter. Brak alertów. |
> | Działanie | Microsoft. Network/virtualRouters/Komunikacja równorzędna/usuwanie | Usuwa element VirtualRouterPeering |
> | Działanie | Microsoft. Network/virtualRouters/Peers/Read | Pobiera VirtualRouterPeering |
> | Działanie | Microsoft. Network/virtualRouters/Peers/Write | Tworzy VirtualRouterPeering lub aktualizuje istniejący VirtualRouterPeering |
> | Działanie | Microsoft. Network/virtualRouters/odczyt | Pobiera VirtualRouter |
> | Działanie | Microsoft. Network/virtualRouters/Write | Tworzy VirtualRouter lub aktualizuje istniejący VirtualRouter |
> | Działanie | Microsoft. Network/virtualWans/Delete | Usuwa wirtualną sieć WAN |
> | Działanie | Microsoft. Network/virtualwans/generateVpnProfile/akcja | Generuj VirtualWanVpnServerConfiguration VpnProfile |
> | Działanie | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Usuwa P2SVpnServerConfiguration wirtualnej sieci WAN |
> | Działanie | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Pobiera wirtualną P2SVpnServerConfiguration sieci WAN |
> | Działanie | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Tworzy wirtualną P2SVpnServerConfiguration sieci WAN lub aktualizuje istniejącą wirtualną sieć WAN P2SVpnServerConfiguration |
> | Działanie | Microsoft.Network/virtualWans/read | Uzyskaj wirtualną sieć WAN |
> | Działanie | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Pobiera obsługiwanych dostawców zabezpieczeń VirtualWan. |
> | Działanie | Microsoft.Network/virtualWans/virtualHubs/read | Pobiera wszystkie centra wirtualne odwołujące się do wirtualnej sieci WAN. |
> | Działanie | Microsoft.Network/virtualwans/vpnconfiguration/action | Pobiera konfigurację sieci VPN |
> | Działanie | Microsoft. Network/virtualwans/vpnServerConfigurations/akcja | Pobierz VirtualWanVpnServerConfigurations |
> | Działanie | Microsoft.Network/virtualWans/vpnSites/read | Pobiera wszystkie lokacje sieci VPN odwołujące się do wirtualnej sieci WAN. |
> | Działanie | Microsoft. Network/virtualWans/Write | Utwórz lub zaktualizuj wirtualną sieć WAN |
> | Działanie | Microsoft.Network/vpnGateways/delete | Usuwa element bramy vpngateway. |
> | Działanie | microsoft.network/vpngateways/listvpnconnectionshealth/action | Pobiera kondycję połączenia dla wszystkich lub podzbioru połączeń na bramy vpngateway |
> | Działanie | Microsoft.Network/vpnGateways/read | Pobiera bramy vpngateway. |
> | Działanie | microsoft.network/vpngateways/reset/action | Resetuje bramy vpngateway |
> | Działanie | microsoft.network/vpnGateways/vpnConnections/delete | Usuwa element VpnConnection. |
> | Działanie | microsoft.network/vpnGateways/vpnConnections/read | Pobiera VpnConnection. |
> | Działanie | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Pobiera połączenie sieci VPN |
> | Działanie | microsoft.network/vpnGateways/vpnConnections/write | VpnConnection. |
> | Działanie | Microsoft.Network/vpnGateways/write | Bramy vpngateway. |
> | Działanie | Microsoft. Network/vpnServerConfigurations/Delete | Usuń VpnServerConfiguration |
> | Działanie | Microsoft. Network/vpnServerConfigurations/odczyt | Pobierz VpnServerConfiguration |
> | Działanie | Microsoft. Network/vpnServerConfigurations/Write | Utwórz lub zaktualizuj VpnServerConfiguration |
> | Działanie | Microsoft.Network/vpnsites/delete | Usuwa zasób witryny sieci VPN. |
> | Działanie | Microsoft.Network/vpnsites/read | Pobiera zasób witryny sieci VPN. |
> | Działanie | Microsoft. Network/vpnSites/vpnSiteLinks/odczyt | Pobiera łącze do witryny sieci VPN |
> | Działanie | Microsoft.Network/vpnsites/write | Tworzy lub aktualizuje zasób witryny sieci VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Sprawdza, czy dana nazwa zasobu przestrzeni nazw jest dostępna w ramach usługi NotificationHub. |
> | Działanie | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Działanie | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Działanie | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia do przestrzeni nazw |
> | Działanie | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Działanie | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Reguła autoryzacji przestrzeni nazw Wygeneruj ponownie podstawowy/SecondaryKey, określ klucz, który należy ponownie wygenerować |
> | Działanie | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Działanie | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Sprawdza, czy dana nazwa NotificationHub jest dostępna wewnątrz przestrzeni nazw. |
> | Działanie | Microsoft.NotificationHubs/Namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Usuń reguły autoryzacji centrum powiadomień |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Pobierz parametry połączenia do centrum powiadomień |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Reguła autoryzacji centrum powiadomień Regenerate Primary/SecondaryKey, określ klucz, który należy ponownie wygenerować |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Utwórz reguły autoryzacji centrum powiadomień i zaktualizuj jego właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Wyślij testowe Powiadomienie wypychane. |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Usuwanie zasobu centrum powiadomień |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Pobierz wszystkie poświadczenia PNS centrum powiadomień. Obejmuje to, WNS, usługi MPNS, APNS, GCM i Baidu |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Pobierz listę opisów zasobów centrum powiadomień |
> | Działanie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Utwórz centrum powiadomień i zaktualizuj jego właściwości. Jego właściwości obejmują głównie poświadczenia PNS. Reguły autoryzacji i czas wygaśnięcia |
> | Działanie | Microsoft.NotificationHubs/Namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | Działanie | Microsoft.NotificationHubs/Namespaces/write | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Działanie | Microsoft.NotificationHubs/operationResults/read | Zwraca wyniki operacji dla dostawcy Notification Hubs |
> | Działanie | Microsoft.NotificationHubs/operations/read | Zwraca listę obsługiwanych operacji dla dostawcy Notification Hubs |
> | Działanie | Microsoft.NotificationHubs/register/action | Rejestruje subskrypcję dostawcy zasobów NotificationHubs i umożliwia tworzenie przestrzeni nazw i NotificationHubs |
> | Działanie | Microsoft.NotificationHubs/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów NotificationHubs i umożliwia tworzenie przestrzeni nazw i NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. OffAzure/HyperVSites/Clusters/Read | Pobiera właściwości klastra funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/klastrów/zapis | Tworzy lub aktualizuje klaster funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/Delete | Usuwa lokację funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/healthsummary/Read | Pobiera podsumowanie kondycji zasobu funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/hosty/odczyt | Pobiera właściwości hosta funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/hosty/zapis | Tworzy lub aktualizuje hosta funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/Jobs/odczyt | Pobiera właściwości zadań funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/Machines/Read | Pobiera właściwości maszyn funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/operationsstatus/Read | Pobiera właściwości stanu operacji funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/odczyt | Pobiera właściwości lokacji funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/Odśwież/akcja | Odświeża obiekty w lokacji funkcji Hyper-V |
> | Działanie | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Pobiera właściwości kont Uruchom jako funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/użycie/odczyt | Pobiera użycie lokacji funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/HyperVSites/Write | Tworzy lub aktualizuje lokację funkcji Hyper-V |
> | Działanie | Microsoft. OffAzure/Operations/Read | Odczytuje uwidocznione operacje |
> | Działanie | Microsoft. OffAzure/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. OffAzure |
> | Działanie | Microsoft. OffAzure/ServerSites/Jobs/odczyt | Pobiera właściwości zadań serwera |
> | Działanie | Microsoft. OffAzure/ServerSites/Machines/Read | Pobiera właściwości komputerów serwera |
> | Działanie | Microsoft. OffAzure/ServerSites/Machines/Write | Zapisz właściwości komputera serwera |
> | Działanie | Microsoft. OffAzure/ServerSites/operationsstatus/Read | Pobiera właściwości stanu operacji serwera |
> | Działanie | Microsoft. OffAzure/ServerSites/odczyt | Pobiera właściwości lokacji serwera |
> | Działanie | Microsoft. OffAzure/ServerSites/Odśwież/akcja | Odświeża obiekty w ramach lokacji serwera |
> | Działanie | Microsoft. OffAzure/ServerSites/runasaccounts/Read | Pobiera właściwości kont Uruchom jako serwera |
> | Działanie | Microsoft. OffAzure/ServerSites/użycie/odczyt | Pobiera użycie lokacji serwera |
> | Działanie | Microsoft. OffAzure/ServerSites/Write | Tworzy lub aktualizuje lokację serwera |
> | Działanie | Microsoft.OffAzure/VMwareSites/delete | Usuwa witrynę programu VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/healthsummary/Read | Pobiera podsumowanie kondycji zasobu VMware |
> | Działanie | Microsoft.OffAzure/VMwareSites/jobs/read | Pobiera właściwości zadań programu VMware |
> | Działanie | Microsoft.OffAzure/VMwareSites/machines/read | Pobiera właściwości maszyn VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/Machines/Start/Action | Uruchom maszyny VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/Machines/akcja | Powoduje zatrzymanie maszyn VMware |
> | Działanie | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Pobiera właściwości stanu operacji VMware |
> | Działanie | Microsoft.OffAzure/VMwareSites/read | Pobiera właściwości witryny programu VMware |
> | Działanie | Microsoft.OffAzure/VMwareSites/refresh/action | Odświeża obiekty w witrynie programu VMware |
> | Działanie | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Pobiera właściwości kont Uruchom jako VMware |
> | Działanie | Microsoft.OffAzure/VMwareSites/usage/read | Pobiera użycie witryny programu VMware |
> | Działanie | Microsoft.OffAzure/VMwareSites/vcenters/read | Pobiera właściwości programu VMware vCenter |
> | Działanie | Microsoft.OffAzure/VMwareSites/vcenters/write | Tworzy lub aktualizuje program VMware vCenter |
> | Działanie | Microsoft.OffAzure/VMwareSites/write | Tworzy lub aktualizuje witrynę programu VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.OperationalInsights/linkTargets/read | Wyświetla listę istniejących kont, które nie są skojarzone z subskrypcją platformy Azure. Aby połączyć tę subskrypcję platformy Azure z obszarem roboczym, użyj identyfikatora klienta zwróconego przez tę operację we właściwości identyfikator klienta operacji Utwórz obszar roboczy. |
> | Działanie | Microsoft. operationalinsights/Operations/Read | Wyświetla wszystkie dostępne operacje interfejsu API REST OperationalInsights. |
> | Działanie | Microsoft. operationalinsights/Register/Action | Rergisters subskrypcję. |
> | Działanie | Microsoft. OperationalInsights/Register/Action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Działanie | Microsoft. operationalinsights/Unregister/Action | Wyrejestrowuje subskrypcję. |
> | Działanie | Microsoft.OperationalInsights/workspaces/analytics/query/action | Wyszukaj przy użyciu nowego aparatu. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Analytics/Query/Schema/Read | Pobierz schemat wyszukiwania w wersji 2. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/interfejs API/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Działanie | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Pobierz schemat wyszukiwania w wersji 2. |
> | Działanie | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Usuń zakres konfiguracji |
> | Działanie | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Pobierz zakres konfiguracji |
> | Działanie | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Ustaw zakres konfiguracji |
> | Działanie | Microsoft.OperationalInsights/workspaces/datasources/delete | Usuń źródła danych w obszarze roboczym. |
> | Działanie | Microsoft.OperationalInsights/workspaces/datasources/read | Pobierz źródła danych w obszarze roboczym. |
> | Działanie | Microsoft.OperationalInsights/workspaces/datasources/write | Utwórz/zaktualizuj źródła danych w obszarze roboczym. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/usuwanie | Usuwa obszar roboczy. Jeśli obszar roboczy został połączony z istniejącym obszarem roboczym podczas tworzenia, a obszar roboczy, z którym został on połączony, nie został usunięty. |
> | Działanie | Microsoft.OperationalInsights/workspaces/gateways/delete | Usuwa bramę skonfigurowaną dla obszaru roboczego. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/generateregistrationcertificate/akcja | Generuje certyfikat rejestracji dla obszaru roboczego. Ten certyfikat służy do łączenia programu Microsoft System Center Operations Manager z obszarem roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Disable/Action | Wyłącza pakiet Intelligence Pack dla danego obszaru roboczego. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Enable/Action | Włącza pakiet Intelligence Pack dla danego obszaru roboczego. |
> | Działanie | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Wyświetla listę wszystkich pakietów analizy, które są widoczne dla danego obszaru roboczego, a także listę informacji o tym, czy pakiet jest włączony, czy wyłączony dla tego obszaru roboczego. |
> | Działanie | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Usuń połączone usługi w podanym obszarze roboczym. |
> | Działanie | Microsoft.OperationalInsights/workspaces/linkedServices/read | Pobierz połączone usługi w podanym obszarze roboczym. |
> | Działanie | Microsoft.OperationalInsights/workspaces/linkedServices/write | Utwórz/zaktualizuj połączone usługi w podanym obszarze roboczym. |
> | Działanie | Microsoft.OperationalInsights/workspaces/listKeys/action | Pobiera klucze list dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Działanie | Microsoft.OperationalInsights/workspaces/listKeys/read | Pobiera klucze list dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Działanie | Microsoft.OperationalInsights/workspaces/managementGroups/read | Pobiera nazwy i metadane dla System Center Operations Manager grup zarządzania połączonych z tym obszarem roboczym. |
> | Działanie | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Pobierz definicje metryki w obszarze roboczym |
> | Działanie | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Usuń ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Działanie | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Pobierz ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Działanie | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Ustaw ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Działanie | Microsoft. operationalinsights/obszary robocze/operacje/odczyt | Pobiera stan operacji obszaru roboczego OperationalInsights. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/przeczyszczanie/akcja | Usuń określone dane z obszaru roboczego |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountLogon/Read | Odczytaj dane z tabeli AADDomainServicesAccountLogon |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountManagement/Read | Odczytaj dane z tabeli AADDomainServicesAccountManagement |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Odczytaj dane z tabeli AADDomainServicesDirectoryServiceAccess |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesLogonLogoff/Read | Odczytaj dane z tabeli AADDomainServicesLogonLogoff |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPolicyChange/Read | Odczytaj dane z tabeli AADDomainServicesPolicyChange |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPrivilegeUse/Read | Odczytaj dane z tabeli AADDomainServicesPrivilegeUse |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesSystemSecurity/Read | Odczytaj dane z tabeli AADDomainServicesSystemSecurity |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupAlerts/Read | Odczytaj dane z tabeli AddonAzureBackupAlerts |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupJobs/Read | Odczytaj dane z tabeli AddonAzureBackupJobs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupPolicy/Read | Odczytaj dane z tabeli AddonAzureBackupPolicy |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupProtectedInstance/Read | Odczytaj dane z tabeli AddonAzureBackupProtectedInstance |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupStorage/Read | Odczytaj dane z tabeli AddonAzureBackupStorage |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ADFActivityRun/Read | Odczytaj dane z tabeli ADFActivityRun |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ADFPipelineRun/Read | Odczytaj dane z tabeli ADFPipelineRun |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Odczytaj dane z tabeli ADFTriggerRun |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Odczytaj dane z tabeli ADReplicationResult |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AegDeliveryFailureLogs/Read | Odczytaj dane z tabeli AegDeliveryFailureLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AegPublishFailureLogs/Read | Odczytaj dane z tabeli AegPublishFailureLogs |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/Alert/read | Odczytaj dane z tabeli alertów |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Odczytaj dane z tabeli AlertHistory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterEvent/Read | Odczytaj dane z tabeli AmlComputeClusterEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterNodeEvent/Read | Odczytaj dane z tabeli AmlComputeClusterNodeEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeJobEvent/Read | Odczytaj dane z tabeli AmlComputeJobEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ApiManagementGatewayLogs/Read | Odczytaj dane z tabeli ApiManagementGatewayLogs |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Odczytaj dane z tabeli AppCenterError |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Odczytaj dane z tabeli ApplicationInsights |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppPlatformLogsforSpring/Read | Odczytaj dane z tabeli AppPlatformLogsforSpring |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppPlatformSystemLogs/Read | Odczytaj dane z tabeli AppPlatformSystemLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppServiceAppLogs/Read | Odczytaj dane z tabeli AppServiceAppLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppServiceAuditLogs/Read | Odczytaj dane z tabeli AppServiceAuditLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppServiceConsoleLogs/Read | Odczytaj dane z tabeli AppServiceConsoleLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppServiceEnvironmentPlatformLogs/Read | Odczytaj dane z tabeli AppServiceEnvironmentPlatformLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppServiceHTTPLogs/Read | Odczytaj dane z tabeli AppServiceHTTPLogs |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Odczytaj dane z tabeli AuditLogs |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Odczytaj dane z tabeli AutoscaleEvaluationsLog |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Odczytaj dane z tabeli AutoscaleScaleActionsLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AWSCloudTrail/Read | Odczytaj dane z tabeli AWSCloudTrail |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/Azure — odczyt | Odczytywanie danych z tabeli platformy Azure |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Odczytaj dane z tabeli AzureAssessmentRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Odczytaj dane z tabeli AzureMetrics |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterEvent/Read | Odczytaj dane z tabeli BaiClusterEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterNodeEvent/Read | Odczytaj dane z tabeli BaiClusterNodeEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BaiJobEvent/Read | Odczytaj dane z tabeli BaiJobEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BlockchainApplicationLog/Read | Odczytaj dane z tabeli BlockchainApplicationLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BlockchainProxyLog/Read | Odczytaj dane z tabeli BlockchainProxyLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BoundPort/Read | Odczytaj dane z tabeli BoundPort |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/CommonSecurityLog/Read | Odczytaj dane z tabeli CommonSecurityLog |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Odczytywanie danych z tabeli komputerów |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/Zmianakonfiguracji/Read | Odczytaj dane z tabeli Zmianakonfiguracji |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Odczytaj dane z tabeli ConfigurationData |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Odczytaj dane z tabeli ContainerImageInventory |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Odczytaj dane z tabeli ContainerInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerLog/Read | Odczytaj dane z tabeli ContainerLog |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Odczytaj dane z tabeli ContainerNodeInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryLoginEvents/Read | Odczytaj dane z tabeli ContainerRegistryLoginEvents |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryRepositoryEvents/Read | Odczytaj dane z tabeli ContainerRegistryRepositoryEvents |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Odczytaj dane z tabeli ContainerServiceLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/CoreAzureBackup/Read | Odczytaj dane z tabeli CoreAzureBackup |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Odczytaj dane z tabeli DatabricksAccounts |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksClusters/Read | Odczytaj dane z tabeli DatabricksClusters |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksDBFS/Read | Odczytaj dane z tabeli DatabricksDBFS |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksInstancePools/Read | Odczytaj dane z tabeli DatabricksInstancePools |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Odczytaj dane z tabeli DatabricksJobs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksNotebook/Read | Odczytaj dane z tabeli DatabricksNotebook |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Odczytaj dane z tabeli DatabricksSecrets |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSQLPermissions/Read | Odczytaj dane z tabeli DatabricksSQLPermissions |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSSH/Read | Odczytaj dane z tabeli DatabricksSSH |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksTables/Read | Odczytaj dane z tabeli DatabricksTables |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Odczytaj dane z tabeli DatabricksWorkspace |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceAppCrash/Read | Odczytaj dane z tabeli DeviceAppCrash |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Odczytaj dane z tabeli DeviceAppLaunch |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Odczytaj dane z tabeli DeviceCalendar |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceCleanup/Read | Odczytaj dane z tabeli DeviceCleanup |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Odczytaj dane z tabeli DeviceConnectSession |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Odczytaj dane z tabeli DeviceEtw |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Odczytaj dane z tabeli zameldowaniem |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Odczytaj dane z tabeli DeviceHeartbeat |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Odczytaj dane z tabeli DeviceSleepState |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Odczytaj dane z tabeli DHAppFailure |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Odczytaj dane z tabeli DHAppReliability |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/DHCP/odczyt | Odczytywanie danych z tabeli DHCP |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHDriverReliability/Read | Odczytaj dane z tabeli DHDriverReliability |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Odczytaj dane z tabeli DHLogonFailures |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Odczytaj dane z tabeli DHLogonMetrics |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Odczytaj dane z tabeli DHOSCrashData |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHOSReliability/Read | Odczytaj dane z tabeli DHOSReliability |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Odczytaj dane z tabeli DHWipAppLearning |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Odczytaj dane z tabeli DnsEvents |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Odczytaj dane z tabeli DnsInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ETWEvent/Read | Odczytaj dane z tabeli ETWEvent |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytanie/zdarzenie/odczyt | Odczytaj dane z tabeli zdarzeń |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ExchangeAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/FailedIngestion/Read | Odczytaj dane z tabeli FailedIngestion |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/FunctionAppLogs/Read | Odczytaj dane z tabeli FunctionAppLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/puls/odczyt | Odczytaj dane z tabeli pulsu |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/HuntingBookmark/Read | Odczytaj dane z tabeli HuntingBookmark |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Odczytaj dane z tabeli InboundConnection |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Odczytaj dane z tabeli InsightsMetrics |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Odczytaj dane z tabeli IntuneAuditLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/IntuneDeviceComplianceOrg/Read | Odczytaj dane z tabeli IntuneDeviceComplianceOrg |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Odczytaj dane z tabeli IntuneOperationalLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/KubeEvents/Read | Odczytaj dane z tabeli KubeEvents |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/KubeHealth/Read | Odczytaj dane z tabeli KubeHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/KubeMonAgentEvents/Read | Odczytaj dane z tabeli KubeMonAgentEvents |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Odczytaj dane z tabeli KubeNodeInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/KubePodInventory/Read | Odczytaj dane z tabeli KubePodInventory |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Odczytaj dane z tabeli KubeServices |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Odczytaj dane z tabeli LinuxAuditLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplication/Read | Odczytaj dane z tabeli MAApplication |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealth/Read | Odczytaj dane z tabeli MAApplicationHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthAlternativeVersions/Read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthIssues/Read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationInstance/Read | Odczytaj dane z tabeli MAApplicationInstance |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Odczytaj dane z tabeli MAApplicationReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADeploymentPlan/Read | Odczytaj dane z tabeli MADeploymentPlan |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADevice/Read | Odczytaj dane z tabeli MADevice |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNotEnrolled/Read | Odczytaj dane z tabeli MADeviceNotEnrolled |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNRT/Read | Odczytaj dane z tabeli MADeviceNRT |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Odczytaj dane z tabeli MADevicePnPHealth |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Odczytaj dane z tabeli MADeviceReadiness |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Odczytaj dane z tabeli MADriverReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddin/Read | Odczytaj dane z tabeli MAOfficeAddin |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinEntityHealth/Read | Odczytaj dane z tabeli MAOfficeAddinEntityHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinHealth/Read | Odczytaj dane z tabeli MAOfficeAddinHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinHealthEventNRT/Read | Odczytaj dane z tabeli MAOfficeAddinHealthEventNRT |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinHealthIssues/Read | Odczytaj dane z tabeli MAOfficeAddinHealthIssues |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinInstance/Read | Odczytaj dane z tabeli MAOfficeAddinInstance |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinInstanceReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeApp/Read | Odczytaj dane z tabeli MAOfficeApp |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppCrashesNRT/Read | Odczytaj dane z tabeli MAOfficeAppCrashesNRT |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppHealth/Read | Odczytaj dane z tabeli MAOfficeAppHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppInstance/Read | Odczytaj dane z tabeli MAOfficeAppInstance |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppInstanceHealth/Read | Odczytaj dane z tabeli MAOfficeAppInstanceHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppReadiness/Read | Odczytaj dane z tabeli MAOfficeAppReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppSessionsNRT/Read | Odczytaj dane z tabeli MAOfficeAppSessionsNRT |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeBuildInfo/Read | Odczytaj dane z tabeli MAOfficeBuildInfo |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeCurrencyAssessment/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessment |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Odczytaj dane z tabeli MAOfficeDeploymentStatusNRT |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroErrorNRT/Read | Odczytaj dane z tabeli MAOfficeMacroErrorNRT |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroGlobalHealth/Read | Odczytaj dane z tabeli MAOfficeMacroGlobalHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealth/Read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealthIssues/Read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroSummary/Read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeSuite/Read | Odczytaj dane z tabeli MAOfficeSuite |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAProposedPilotDevices/Read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsBuildInfo/Read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsCurrencyAssessment/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Odczytaj dane z tabeli MAWindowsDeploymentStatusNRT |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/McasShadowItReporting/Read | Odczytaj dane z tabeli McasShadowItReporting |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftAzureBastionAuditLogs/Read | Odczytaj dane z tabeli MicrosoftAzureBastionAuditLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareReceivedSnapshotLog/Read | Odczytaj dane z tabeli MicrosoftDataShareReceivedSnapshotLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareSentSnapshotLog/Read | Odczytaj dane z tabeli MicrosoftDataShareSentSnapshotLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareShareLog/Read | Odczytaj dane z tabeli MicrosoftDataShareShareLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDynamicsTelemetryPerformanceLogs/Read | Odczytaj dane z tabeli MicrosoftDynamicsTelemetryPerformanceLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDynamicsTelemetrySystemMetricsLogs/Read | Odczytaj dane z tabeli MicrosoftDynamicsTelemetrySystemMetricsLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftHealthcareApisAuditLogs/Read | Odczytaj dane z tabeli MicrosoftHealthcareApisAuditLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftInsightsAzureActivityLog/Read | Odczytaj dane z tabeli MicrosoftInsightsAzureActivityLog |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Odczytaj dane z tabeli NetworkMonitoring |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/Office/odczyt | Odczytaj dane z tabeli pakietu Office |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/operacja/odczyt | Odczytaj dane z tabeli operacji |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Odczytaj dane z tabeli OutboundConnection |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/perf/Read | Odczytaj dane z tabeli wydajności |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Odczytaj dane z tabeli ProtectionStatus |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytanie/odczyt | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Odczytaj dane z tabeli ReservedCommonFields |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SCCMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SCOMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Odczytaj dane z tabeli SecurityAlert |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaseline/Read | Odczytaj dane z tabeli SecurityBaseline |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaselineSummary/Read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Odczytaj dane z tabeli SecurityDetection |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityEvent/Read | Odczytaj dane z tabeli SecurityEvent |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Odczytaj dane z tabeli SecurityIoTRawEvent |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Odczytaj dane z tabeli SecurityRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricReliableServiceEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SfBAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SignalRServiceDiagnosticLogs/Read | Odczytaj dane z tabeli SignalRServiceDiagnosticLogs |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Odczytaj dane z tabeli SigninLogs |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SQLQueryPerformance/Read | Odczytaj dane z tabeli SQLQueryPerformance |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Odczytaj dane z tabeli SqlThreatProtectionLoginAudits |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Odczytaj dane z tabeli SqlVulnerabilityAssessmentResult |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/StorageBlobLogs/Read | Odczytaj dane z tabeli StorageBlobLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/StorageFileLogs/Read | Odczytaj dane z tabeli StorageFileLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/StorageQueueLogs/Read | Odczytaj dane z tabeli StorageQueueLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/StorageTableLogs/Read | Odczytaj dane z tabeli StorageTableLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SucceededIngestion/Read | Odczytaj dane z tabeli SucceededIngestion |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/dziennik systemowy/odczyt | Odczytaj dane z tabeli dziennika systemowego |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Odczytaj dane z tabeli SysmonEvent |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/tabele. Custom/Read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ThreatIntelligenceIndicator/Read | Odczytaj dane z tabeli ThreatIntelligenceIndicator |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Odczytaj dane z tabeli UAApp |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAComputer/Read | Odczytaj dane z tabeli UAComputer |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAComputerRank/Read | Odczytaj dane z tabeli UAComputerRank |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UADriver/Read | Odczytaj dane z tabeli UADriver |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Odczytaj dane z tabeli UADriverProblemCodes |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAFeedback/Read | Odczytaj dane z tabeli UAFeedback |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAHardwareSecurity/Read | Odczytaj dane z tabeli UAHardwareSecurity |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAOfficeAddIn/Read | Odczytaj dane z tabeli UAOfficeAddIn |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAProposedActionPlan/Read | Odczytaj dane z tabeli UAProposedActionPlan |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Odczytaj dane z tabeli UASysReqIssue |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Odczytaj dane z tabeli UAUpgradedComputer |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/Update/read | Odczytaj dane z tabeli aktualizacji |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Odczytaj dane z tabeli UpdateRunProgress |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Odczytaj dane z tabeli UpdateSummary |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/użycie/odczyt | Odczytywanie danych z tabeli użycia |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/VMBoundPort/Read | Odczytaj dane z tabeli VMBoundPort |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/VMComputer/Read | Odczytaj dane z tabeli VMComputer |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/VMConnection/Read | Odczytaj dane z tabeli VMConnection |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/VMProcess/Read | Odczytaj dane z tabeli VMProcess |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/W3CIISLog/Read | Odczytaj dane z tabeli W3CIISLog |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Odczytaj dane z tabeli WDAVStatus |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WDAVThreat/Read | Odczytaj dane z tabeli WDAVThreat |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Odczytaj dane z tabeli WindowsEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WindowsFirewall/Read | Odczytaj dane z tabeli WindowsFirewall |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | Działanie | Microsoft.OperationalInsights/workspaces/query/WireData/read | Odczytaj dane z tabeli typowe |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WorkloadMonitoringPerf/Read | Odczytaj dane z tabeli WorkloadMonitoringPerf |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WUDOAggregatedStatus/Read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WUDOStatus/Read | Odczytaj dane z tabeli WUDOStatus |
> | Działanie | Microsoft.OperationalInsights/workspaces/read | Pobiera istniejący obszar roboczy |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/regeneratesharedkey/akcja | Ponownie generuje klucz współużytkowany określonego obszaru roboczego |
> | Działanie | Microsoft. operationalinsights/obszary robocze/reguły/odczyt | Pobierz wszystkie reguły alertów. |
> | Działanie | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Usuwa zapisane zapytanie wyszukiwania |
> | Działanie | Microsoft.OperationalInsights/workspaces/savedSearches/read | Pobiera zapisane zapytanie wyszukiwania |
> | Działanie | microsoft.operationalinsights/workspaces/savedsearches/results/read | Pobierz wyniki zapisanych wyszukiwań. Przestarzałe |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Delete | Usuń zaplanowane akcje wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Read | Pobierz zaplanowane akcje wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Write | Utwórz lub zaktualizuj zaplanowane akcje wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Delete | Usuń zaplanowane wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Read | Pobierz zaplanowane wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Write | Utwórz lub zaktualizuj zaplanowane wyszukiwania. |
> | Działanie | Microsoft.OperationalInsights/workspaces/savedSearches/write | Tworzy zapytanie zapisanego wyszukiwania |
> | Działanie | Microsoft.OperationalInsights/workspaces/schema/read | Pobiera schemat wyszukiwania dla obszaru roboczego.  Schemat wyszukiwania obejmuje uwidocznione pola i ich typy. |
> | Działanie | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Działanie | Microsoft. operationalinsights/obszary robocze/wyszukiwanie/odczyt | Pobierz wyniki wyszukiwania. Zaniechane. |
> | Działanie | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Działanie | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Delete | Usuwa konfigurację magazynu. Spowoduje to zatrzymanie przez program Microsoft Operational Insights odczytywania danych z konta magazynu. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Read | Pobiera konfigurację magazynu. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Write | Tworzy nową konfigurację magazynu. Te konfiguracje są używane do ściągania danych z lokalizacji na istniejącym koncie magazynu. |
> | Działanie | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Pobierz dziennik błędów tłumaczenia aktualizacji wyszukiwania dla obszaru roboczego |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/użycie/odczyt | Pobiera dane użycia dla obszaru roboczego, w tym ilość danych odczytanych przez obszar roboczy. |
> | Działanie | Microsoft.OperationalInsights/workspaces/write | Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącego obszaru roboczego. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. OperationsManagement/managementAssociations/Delete | Usuń istniejące skojarzenie zarządzania |
> | Działanie | Microsoft.OperationsManagement/managementAssociations/read | Pobierz istniejące skojarzenie zarządzania |
> | Działanie | Microsoft.OperationsManagement/managementAssociations/write | Utwórz nowe skojarzenie zarządzania |
> | Działanie | Microsoft.OperationsManagement/managementConfigurations/delete | Usuń istniejącą konfigurację zarządzania |
> | Działanie | Microsoft.OperationsManagement/managementConfigurations/read | Pobieranie istniejącej konfiguracji zarządzania |
> | Działanie | Microsoft.OperationsManagement/managementConfigurations/write | Utwórz nową konfigurację zarządzania |
> | Działanie | Microsoft.OperationsManagement/register/action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Działanie | Microsoft.OperationsManagement/solutions/delete | Usuń istniejące rozwiązanie OMS |
> | Działanie | Microsoft.OperationsManagement/solutions/read | Pobierz wyjście z rozwiązania OMS |
> | Działanie | Microsoft.OperationsManagement/solutions/write | Utwórz nowe rozwiązanie OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.PolicyInsights/asyncOperationResults/read | Pobiera wynik operacji asynchronicznej. |
> | Akcja dataaction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Sprawdź stan zgodności danego składnika względem zasad dotyczących danych. |
> | Działanie | Microsoft.PolicyInsights/operations/read | Pobiera obsługiwane operacje w przestrzeni nazw Microsoft. PolicyInsights |
> | Akcja dataaction | Microsoft. PolicyInsights/policyEvents/logDataEvents/akcja | Rejestruj zdarzenia zasad składnika zasobów. |
> | Działanie | Microsoft.PolicyInsights/policyEvents/queryResults/action | Zapytanie informacji o zdarzeniach zasad. |
> | Działanie | Microsoft.PolicyInsights/policyEvents/queryResults/read | Zapytanie informacji o zdarzeniach zasad. |
> | Działanie | Microsoft. PolicyInsights/policyMetadata/odczyt | Pobieranie zasobów metadanych zasad. |
> | Działanie | Microsoft.PolicyInsights/policyStates/queryResults/action | Zapytanie o informacje dotyczące stanów zasad. |
> | Działanie | Microsoft.PolicyInsights/policyStates/queryResults/read | Zapytanie o informacje dotyczące stanów zasad. |
> | Działanie | Microsoft. PolicyInsights/policyStates/podsumowanie/akcja | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych Stanów zasad. |
> | Działanie | Microsoft. PolicyInsights/policyStates/podsumowanie/odczyt | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych Stanów zasad. |
> | Działanie | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Wyzwala nową ocenę zgodności dla wybranego zakresu. |
> | Działanie | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Zapytanie dotyczące zasobów wymaganych przez zasady DeployIfNotExistsymi. |
> | Działanie | Microsoft. PolicyInsights/Register/Action | Rejestruje dostawcę zasobów usługi Microsoft Policy Insights i włącza na nim akcje. |
> | Działanie | Microsoft. PolicyInsights/korygowania/anulowanie/akcja | Anuluj korygowanie zasad firmy Microsoft w toku. |
> | Działanie | Microsoft.PolicyInsights/remediations/delete | Usuń korygowanie zasad. |
> | Działanie | Microsoft.PolicyInsights/remediations/listDeployments/read | Wyświetla listę wdrożeń wymaganych przez korygowanie zasad. |
> | Działanie | Microsoft.PolicyInsights/remediations/read | Pobierz korygowanie zasad. |
> | Działanie | Microsoft.PolicyInsights/remediations/write | Utwórz lub zaktualizuj korekty zasad firmy Microsoft. |
> | Działanie | Microsoft. PolicyInsights/Unregister/Action | Wyrejestrowuje dostawcę zasobów usługi Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Portal/konsole/usuwanie | Usuwa wystąpienie Cloud Shell. |
> | Działanie | Microsoft. Portal/konsole/odczyt | Odczytuje wystąpienie Cloud Shell. |
> | Działanie | Microsoft. Portal/konsole/zapis | Utwórz lub zaktualizuj wystąpienie Cloud Shell. |
> | Działanie | Microsoft. Portal/pulpity nawigacyjne/usuwanie | Usuwa pulpit nawigacyjny z subskrypcji. |
> | Działanie | Microsoft. Portal/pulpity nawigacyjne/odczyt | Odczytuje pulpity nawigacyjne subskrypcji. |
> | Działanie | Microsoft. Portal/pulpity nawigacyjne/zapis | Dodawanie lub modyfikowanie pulpitu nawigacyjnego do subskrypcji. |
> | Działanie | Microsoft. Portal/rejestr/akcja | Zarejestruj się w portalu |
> | Działanie | Microsoft.Portal/usersettings/delete | Usuwa Cloud Shell ustawienia użytkownika. |
> | Działanie | Microsoft.Portal/usersettings/read | Odczytuje ustawienia użytkownika Cloud Shell. |
> | Działanie | Microsoft.Portal/usersettings/write | Utwórz lub zaktualizuj ustawienie użytkownika Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.PowerBIDedicated/capacities/delete | Usuwa dedykowaną pojemność Power BI. |
> | Działanie | Microsoft.PowerBIDedicated/capacities/read | Pobiera informacje o określonej Power BI dedykowanej pojemności. |
> | Działanie | Microsoft.PowerBIDedicated/capacities/resume/action | Wznawia pojemność. |
> | Działanie | Microsoft.PowerBIDedicated/capacities/skus/read | Pobierz informacje o dostępnych jednostkach SKU dla pojemności |
> | Działanie | Microsoft.PowerBIDedicated/capacities/suspend/action | Wstrzymuje pojemność. |
> | Działanie | Microsoft.PowerBIDedicated/capacities/write | Tworzy lub aktualizuje określony Power BI dedykowanej pojemności. |
> | Działanie | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Sprawdza, czy dana nazwa dedykowanej pojemności Power BI jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft.PowerBIDedicated/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | Działanie | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | Działanie | Microsoft.PowerBIDedicated/operations/read | Pobiera informacje o operacjach |
> | Działanie | Microsoft.PowerBIDedicated/register/action | Rejestruje Power BI dedykowanego dostawcę zasobów. |
> | Działanie | Microsoft.PowerBIDedicated/skus/read | Pobiera informacje o jednostkach SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | Działanie | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Działanie | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Działanie | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Utwórz chroniony element kopii zapasowej |
> | Działanie | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Działanie | Microsoft.RecoveryServices/Locations/backupStatus/action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Działanie | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Weryfikuj funkcje |
> | Działanie | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Sprawdź dostępność nazwy zasobu jest interfejsem API, aby sprawdzić, czy nazwa zasobu jest dostępna |
> | Działanie | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Działanie | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Działanie | Microsoft. RecoveryServices/Register/Action | Rejestruje subskrypcję dla danego dostawcy zasobów |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupconfig/read | Zwraca konfigurację magazynu Recovery Services. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupconfig/write | Aktualizuje konfigurację magazynu Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupEncryptionConfigs/odczyt | Pobiera konfigurację szyfrowania zasobów kopii zapasowej. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupEncryptionConfigs/zapis | Aktualizuje konfigurację szyfrowania zasobów kopii zapasowej |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Usuń zamiar ochrony kopii zapasowej |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Pobierz cel ochrony kopii zapasowej |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Tworzenie opcji ochrony kopii zapasowej |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/operationsStatus/odczyt | Zwraca stan operacji |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery z ochroną |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Usuwa zarejestrowany kontener |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/operationsStatus/Read | Pobiera stan operacji wykonanej na kontenerze ochrony. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Wykonuje kopię zapasową dla chronionego elementu. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Usuwa chroniony element |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Zapewnij natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Przywracanie punktów odzyskiwania dla elementów chronionych. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odwołaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Utwórz chroniony element kopii zapasowej |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Tworzy zarejestrowany kontener |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Anuluj zadanie |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zadania. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupJobs/operationsStatus/odczyt | Zwraca stan operacji zadania. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadań |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportuj zadania |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Zwraca wynik operacji tworzenia kopii zapasowej dla magazynu Recovery Services. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupPolicies/usuwanie | Usuwanie zasad ochrony |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Tworzy zasady ochrony |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Zwraca listę wszystkich elementów podlegających ochronie. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Zwraca informacje zabezpieczającego numeru PIN dla magazynu Recovery Services. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Zwraca konfigurację magazynu dla magazynu Recovery Services. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Aktualizuje konfigurację magazynu dla magazynu Recovery Services. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Działanie | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Sprawdź poprawność operacji dla chronionego elementu |
> | Działanie | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Działanie | Microsoft.RecoveryServices/Vaults/delete | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Działanie | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Działanie | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Pobiera konfigurację powiadomień magazynu usługi Recovery Services. |
> | Działanie | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfiguruje powiadomienia e-mail dla magazynu usługi Recovery Services. |
> | Działanie | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Operacja Wyrejestruj kontener może służyć do wyrejestrowywania kontenera. |
> | Działanie | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Działanie | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Działanie | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operacji rejestrowania kontenera usługi można użyć do zarejestrowania kontenera z usługą odzyskiwania. |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Odczytaj wszystkie ustawienia alertów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Utwórz lub zaktualizuj ustawienia alertów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczytaj wszystkie zdarzenia |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność sieci szkieletowej |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Usuń wszystkie sieci szkieletowe |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Wdróż obraz serwera przetwarzania |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrowanie sieci szkieletowej do usługi AAD |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/operationresults/odczyt | Śledź wyniki operacji asynchronicznej w sieciach szkieletowych zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Odczytaj wszystkie sieci szkieletowe |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skojarz ponownie bramę |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Usuń sieć szkieletową |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Odnów certyfikat sieci szkieletowej |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Odczytaj wszystkie sieci logiczne |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Odczytaj wszystkie sieci |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Usuń wszystkie mapowania sieci |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytaj wszystkie mapowania sieci |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Utwórz lub zaktualizuj dowolne mapowania sieci |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Odkryj element objęty ochroną |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/operationresults/Read | Śledź wyniki operacji asynchronicznej w kontenerach ochrony zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytaj wszystkie kontenery ochrony |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Usuń kontener ochrony |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Usuń wszystkie elementy migracji |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrowanie elementu |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Odczytaj wszystkie punkty odzyskiwania migracji |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na elementach migracji zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Odczytaj wszystkie elementy migracji |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Migracja testowa |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Oczyszczanie migracji testów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Utwórz lub zaktualizuj dowolne elementy migracji |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczytaj dowolne elementy podlegające ochronie |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Dodawanie dysków |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Zastosuj punkt odzyskiwania |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Usuń wszystkie chronione elementy |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Zatwierdzanie trybu failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na elementach chronionych przez zasoby |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowane przełączanie do trybu failover |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczytaj wszystkie chronione elementy |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Usuń chroniony element |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Usuń dyski |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Napraw replikację |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Włącz ponownie ochronę chronionego elementu |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Prześlij opinię |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Odczytaj wszystkie docelowe rozmiary obliczeń |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test pracy w trybie failover |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Czyszczenie testu pracy w trybie failover |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizowanie usługi mobilności |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Utwórz lub zaktualizuj wszystkie chronione elementy |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Usuń wszystkie mapowania kontenerów ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na mapowaniach kontenerów ochrony zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Usuń mapowanie kontenera ochrony |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Utwórz lub zaktualizuj wszelkie mapowania kontenerów ochrony |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Przełącz kontener ochrony |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Tworzenie lub aktualizowanie wszelkich kontenerów ochrony |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Usuń wszystkich dostawców Recovery Services |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/operationresults/Read | Śledź wyniki operacji asynchronicznej dla dostawców Recovery Services zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczytaj wszystkich dostawców Recovery Services |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Odśwież dostawcę |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Usuń dostawcę Recovery Services |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Utwórz lub zaktualizuj wszelkich Recovery Services dostawców |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytaj wszystkie klasyfikacje magazynu |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Usuń wszystkie mapowania klasyfikacji magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na mapowaniach klasyfikacji magazynu zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Utwórz lub zaktualizuj dowolne mapowania klasyfikacji magazynu |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Usuń wszystkie vCenter |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/operationresults/Read | Śledź wyniki operacji asynchronicznej na serwerze vCenter zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Odczytaj dowolne vCenter |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Utwórz lub zaktualizuj dowolne vCenter |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Utwórz lub zaktualizuj wszystkie sieci szkieletowe |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Anuluj zadanie |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationJobs/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na zadaniach zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationJobs/read | Odczytaj wszystkie zadania |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationJobs/ponowne uruchomienie/akcja | Uruchom ponownie zadanie |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationJobs/wznowienie/akcja | Wznów zadanie |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Odczytaj wszystkie elementy migracji |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Odczytaj wszystkie mapowania sieci |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Odczytaj wszystkie sieci |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationOperationStatus/odczyt | Odczytaj wszystkie Stany operacji replikacji magazynu |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Usuń wszystkie zasady |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationPolicies/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na zasadach zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Odczytaj wszystkie zasady |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Tworzenie lub aktualizowanie zasad |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Odczytaj wszystkie chronione elementy |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Odczytaj wszystkie kontenery ochrony |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Usuń wszystkie plany odzyskiwania |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan odzyskiwania zatwierdzania w trybie failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na planach odzyskiwania zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan odzyskiwania planowanej pracy w trybie failover |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Zapoznaj się z planami odzyskiwania |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Ponowne włączanie ochrony planu odzyskiwania |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan odzyskiwania testowego trybu failover |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan odzyskiwania oczyszczania testów w trybie failover |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania w trybie failover |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Tworzenie lub aktualizowanie planów odzyskiwania |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Odczytaj wszystkich dostawców Recovery Services |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Odczytaj wszystkie klasyfikacje magazynu |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Odczytaj wszystkie  |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationUsages/read | Odczytaj wszystkie użycia replikacji magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na kondycji replikacji magazynu zasobów |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Odczytaj wszystkie kondycje replikacji magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/odświeżanie/akcja | Odśwież kondycję magazynu |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Odczytaj wszystkie  |
> | Działanie | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Utwórz lub zaktualizuj dowolne  |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationvCenters/odczyt | Odczytaj dowolne vCenter |
> | Działanie | Microsoft.RecoveryServices/vaults/usages/read | Odczytaj wszystkie użycia magazynu |
> | Działanie | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Działanie | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Działanie | Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Relay/checkNameAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft. Relay/checkNamespaceAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Działanie | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Działanie | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Działanie | Microsoft.Relay/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Działanie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Działanie | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Działanie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Działanie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Action | Operacja zaktualizowania HybridConnection. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji HybridConnection |
> | Działanie | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do HybridConnection |
> | Działanie | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji HybridConnection |
> | Działanie | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/regeneratekeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Write | Utwórz reguły autoryzacji HybridConnection i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Działanie | Microsoft.Relay/namespaces/HybridConnections/Delete | Operacja usuwania zasobu HybridConnection |
> | Działanie | Microsoft. Relay/przestrzenie nazw/HybridConnections/Read | Pobierz listę opisów zasobów HybridConnection |
> | Działanie | Microsoft. Relay/przestrzenie nazw/HybridConnections/Write | Utwórz lub zaktualizuj właściwości HybridConnection. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/operationresults/Read | Pobierz stan operacji przestrzeni nazw |
> | Działanie | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Działanie | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Write | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Działanie | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/logDefinitions/Read | Pobierz listę opisów zasobów dzienników przestrzeni nazw |
> | Działanie | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Działanie | Microsoft. Relay/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Działanie | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw ACS |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Action | Operacja zaktualizowania WcfRelay. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji WcfRelay |
> | Działanie | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do WcfRelay |
> | Działanie | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji WcfRelay |
> | Działanie | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/regeneratekeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Write | Utwórz reguły autoryzacji WcfRelay i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Działanie | Microsoft.Relay/namespaces/WcfRelays/Delete | Operacja usuwania zasobu WcfRelay |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/Read | Pobierz listę opisów zasobów WcfRelay |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/Write | Utwórz lub zaktualizuj właściwości WcfRelay. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Działanie | Microsoft.Relay/operations/read | Pobierz operacje |
> | Działanie | Microsoft. Relay/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i umożliwia tworzenie zasobów przekazywania |
> | Działanie | Microsoft. Relay/Unregister/Action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i umożliwia tworzenie zasobów przekazywania |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Pobiera stan dostępności dla określonego zasobu |
> | Działanie | Microsoft.ResourceHealth/AvailabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Działanie | Microsoft.ResourceHealth/events/read | Pobierz zdarzenia Service Health dla danej subskrypcji |
> | Działanie | Microsoft. Resourcehealth/healthevent/Action | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft.Resourcehealth/healthevent/Activated/action | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft.Resourcehealth/healthevent/InProgress/action | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft. Resourcehealth/healthevent/Pending/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft. Resourcehealth/healthevent/rozwiązane/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft. Resourcehealth/healthevent/zaktualizowane/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft.ResourceHealth/impactedResources/read | Pobierz zasoby, których dotyczy problem dla danej subskrypcji |
> | Działanie | Microsoft.ResourceHealth/metadata/read | Pobiera metadane |
> | Działanie | Microsoft. ResourceHealth/powiadomienia/odczyt | Odbiera powiadomienia Azure Resource Manager |
> | Działanie | Microsoft.ResourceHealth/Operations/read | Pobierz operacje dostępne dla programu Microsoft ResourceHealthe |
> | Działanie | Microsoft.ResourceHealth/register/action | Rejestruje subskrypcję programu Microsoft ResourceHealthe |
> | Działanie | Microsoft.ResourceHealth/unregister/action | Wyrejestrowuje subskrypcję programu Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. resources/calculateTemplateHash/Action | Oblicz wartość skrótu podanego szablonu. |
> | Działanie | Microsoft. resources/checkPolicyCompliance/Read | Sprawdź stan zgodności danego zasobu względem zasad zasobów. |
> | Działanie | Microsoft.Resources/checkResourceName/action | Sprawdź nazwę zasobu, aby sprawdzić poprawność. |
> | Działanie | Microsoft. resources/Deployments/Cancel/Action | Anuluje wdrożenie. |
> | Działanie | Microsoft. resources/Deployments/Delete | Usuwa wdrożenie. |
> | Działanie | Microsoft. resources/Deployments/exportTemplate/Action | Eksportuj szablon wdrożenia |
> | Działanie | Microsoft.Resources/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Działanie | Microsoft. resources/Deployments/operationstatuses/Read | Pobiera lub wyświetla stan operacji wdrożenia. |
> | Działanie | Microsoft.Resources/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | Działanie | Microsoft. resources/Deployments/Validate/Action | Sprawdza poprawność wdrożenia. |
> | Działanie | Microsoft. resources/Deployments/whatIf/Action | Przewiduje zmiany wdrożenia szablonu. |
> | Działanie | Microsoft. resources/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | Działanie | Microsoft. resources/deploymentScripts/Delete | Usuwa skrypt wdrożenia |
> | Działanie | Microsoft. resources/deploymentScripts/Logs/odczyt | Pobiera lub wyświetla dzienniki skryptów wdrażania |
> | Działanie | Microsoft. resources/deploymentScripts/Read | Pobiera lub wyświetla skrypty wdrażania |
> | Działanie | Microsoft. resources/deploymentScripts/Write | Tworzy lub aktualizuje skrypt wdrożenia |
> | Działanie | Microsoft.Resources/links/delete | Usuwa łącze zasobu. |
> | Działanie | Microsoft.Resources/links/read | Pobiera linki do zasobów lub wyświetla ich listę. |
> | Działanie | Microsoft.Resources/links/write | Tworzy lub aktualizuje łącze zasobu. |
> | Działanie | Microsoft. resources/Marketplace/Purchase/Action | Zakup zasobu z portalu Marketplace. |
> | Działanie | Microsoft.Resources/providers/read | Pobierz listę dostawców. |
> | Działanie | Microsoft.Resources/resources/read | Pobierz listę zasobów na podstawie filtrów. |
> | Działanie | Microsoft.Resources/subscriptions/locations/read | Pobiera listę obsługiwanych lokalizacji. |
> | Działanie | Microsoft.Resources/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | Działanie | Microsoft. resources/subscriptions/Providers/Read | Pobiera dostawców zasobów lub wyświetla ich listę. |
> | Działanie | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Działanie | Microsoft.Resources/subscriptions/resourceGroups/delete | Usuwa grupę zasobów i wszystkie jej zasoby. |
> | Działanie | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Działanie | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Pobiera lub wyświetla stan operacji wdrożenia. |
> | Działanie | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | Działanie | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | Działanie | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Przenosi zasoby z jednej grupy zasobów do innej. |
> | Działanie | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Działanie | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Pobiera zasoby dla grupy zasobów. |
> | Działanie | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Weryfikowanie przenoszenia zasobów z jednej grupy zasobów do innej. |
> | Działanie | Microsoft.Resources/subscriptions/resourceGroups/write | Tworzy lub aktualizuje grupę zasobów. |
> | Działanie | Microsoft. resources/subscriptions/sources/Read | Pobiera zasoby subskrypcji. |
> | Działanie | Microsoft.Resources/subscriptions/tagNames/delete | Usuwa tag subskrypcji. |
> | Działanie | Microsoft.Resources/subscriptions/tagNames/read | Pobiera lub wyświetla Tagi subskrypcji. |
> | Działanie | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Usuwa wartość tagu subskrypcji. |
> | Działanie | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Pobiera lub wyświetla wartości tagów subskrypcji. |
> | Działanie | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Dodaje wartość tagu subskrypcji. |
> | Działanie | Microsoft.Resources/subscriptions/tagNames/write | Dodaje tag subskrypcji. |
> | Działanie | Microsoft.Resources/tags/delete | Usuwa wszystkie znaczniki w zasobie. |
> | Działanie | Microsoft.Resources/tags/read | Pobiera wszystkie znaczniki w zasobie. |
> | Działanie | Microsoft.Resources/tags/write | Aktualizuje Tagi w zasobie, zastępując lub scalając istniejące Tagi nowym zestawem tagów lub usuwając istniejące Tagi. |
> | Działanie | Microsoft. resources/dzierżawcy/odczyt | Pobiera listę dzierżawców. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Scheduler/jobcollections/delete | Usuwa kolekcję zadań. |
> | Działanie | Microsoft. Scheduler/jobcollections/Disable/akcja | Wyłącza kolekcję zadań. |
> | Działanie | Microsoft. Scheduler/jobcollections/Włącz/akcja | Włącza zbieranie zadań. |
> | Działanie | Microsoft.Scheduler/jobcollections/jobs/delete | Usuwa zadanie. |
> | Działanie | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Generuje definicję aplikacji logiki na podstawie zadania usługi Scheduler. |
> | Działanie | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Pobiera historię zadania. |
> | Działanie | Microsoft.Scheduler/jobcollections/jobs/read | Pobiera zadanie. |
> | Działanie | Microsoft. Scheduler/jobcollections/Jobs/Run/Action | Uruchamia zadanie. |
> | Działanie | Microsoft. Scheduler/jobcollections/Jobs/Write | Tworzy lub aktualizuje zadanie. |
> | Działanie | Microsoft.Scheduler/jobcollections/read | Pobierz kolekcję zadań |
> | Działanie | Microsoft. Scheduler/jobcollections/Write | Tworzy lub aktualizuje kolekcję zadań. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Search/checkNameAvailability/action | Sprawdza dostępność nazwy usługi. |
> | Działanie | Microsoft.Search/operations/read | Wyświetla wszystkie dostępne operacje dostawcy Microsoft. Search. |
> | Działanie | Microsoft. Search/Register/Action | Rejestruje subskrypcję dostawcy zasobów wyszukiwania i umożliwia tworzenie usług wyszukiwania. |
> | Działanie | Microsoft.Search/searchServices/createQueryKey/action | Tworzy klucz zapytania. |
> | Działanie | Microsoft.Search/searchServices/delete | Usuwa usługę wyszukiwania. |
> | Działanie | Microsoft.Search/searchServices/deleteQueryKey/delete | Usuwa klucz zapytania. |
> | Działanie | Microsoft. Search/searchServices/listAdminKeys/Action | Odczytuje klucze administratora. |
> | Działanie | Microsoft. Search/searchServices/listQueryKeys/Action | Zwraca listę kluczy interfejsu API zapytania dla danej usługi Azure Search. |
> | Działanie | Microsoft.Search/searchServices/listQueryKeys/read | Zwraca listę kluczy interfejsu API zapytania dla danej usługi Azure Search. |
> | Działanie | Microsoft. Search/searchServices/privateEndpointConnectionProxies/Delete | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. Search/searchServices/privateEndpointConnectionProxies/Read | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. Search/searchServices/privateEndpointConnectionProxies/Validate/Action | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Działanie | Microsoft. Search/searchServices/privateEndpointConnectionProxies/Write | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.Search/searchServices/read | Odczytuje usługę wyszukiwania. |
> | Działanie | Microsoft.Search/searchServices/regenerateAdminKey/action | Generuje ponownie klucz administratora. |
> | Działanie | Microsoft. Search/searchServices/Start/akcja | Uruchamia usługę wyszukiwania. |
> | Działanie | Microsoft.Search/searchServices/stop/action | Powoduje zatrzymanie usługi wyszukiwania. |
> | Działanie | Microsoft.Search/searchServices/write | Tworzy lub aktualizuje usługę wyszukiwania. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Wymusza stosowanie zasad ograniczania ruchu przez tworzenie reguł zabezpieczeń zgodnych z podaną grupą zabezpieczeń sieci |
> | Działanie | Microsoft.Security/adaptiveNetworkHardenings/read | Pobiera adaptacyjne zalecenia dotyczące ograniczania przepustowości sieci chronionej przez platformę Azure |
> | Działanie | Microsoft.Security/advancedThreatProtectionSettings/read | Pobiera ustawienia zaawansowanej ochrony przed zagrożeniami dla zasobu |
> | Działanie | Microsoft.Security/advancedThreatProtectionSettings/write | Aktualizuje ustawienia zaawansowanej ochrony przed zagrożeniami dla zasobu |
> | Działanie | Microsoft. Security/Alerts/Read | Pobiera wszystkie dostępne alerty zabezpieczeń |
> | Działanie | Microsoft.Security/applicationWhitelistings/read | Pobiera aplikację whitelistings |
> | Działanie | Microsoft.Security/applicationWhitelistings/write | Tworzy nową aplikację listy dozwolonych lub aktualizuje istniejącą |
> | Działanie | Microsoft. Security/assessmentMetadata/Read | Uzyskaj dostępne metadane oceny zabezpieczeń w ramach subskrypcji |
> | Działanie | Microsoft. Security/assessmentMetadata/Write | Tworzenie lub aktualizowanie metadanych oceny zabezpieczeń |
> | Działanie | Microsoft. Security/Assessments/Read | Uzyskaj oceny zabezpieczeń w ramach subskrypcji |
> | Działanie | Microsoft. Security/Assessments/Write | Utwórz lub zaktualizuj oceny zabezpieczeń w ramach subskrypcji |
> | Działanie | Microsoft. Security/complianceResults/Read | Pobiera wyniki zgodności dla zasobu |
> | Działanie | Microsoft. Security/informationProtectionPolicies/Read | Pobiera zasady usługi Information Protection dla zasobu |
> | Działanie | Microsoft. Security/informationProtectionPolicies/Write | Aktualizuje zasady usługi Information Protection dla zasobu |
> | Działanie | Microsoft. Security/Locations/Alerts/Activate/Action | Aktywuj alert zabezpieczeń |
> | Działanie | Microsoft.Security/locations/alerts/dismiss/action | Odrzuć alert zabezpieczeń |
> | Działanie | Microsoft. Security/Locations/Alerts/Read | Pobiera wszystkie dostępne alerty zabezpieczeń |
> | Działanie | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Usuwa zasady dostępu just in Time do sieci |
> | Działanie | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicjuje żądanie zasady dostępu just in Time do sieci |
> | Działanie | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Pobiera zasady dostępu just in Time do sieci |
> | Działanie | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Tworzy nowe zasady dostępu just in Time do sieci lub aktualizuje istniejące |
> | Działanie | Microsoft. Security/Locations/Read | Pobiera lokalizację danych zabezpieczeń |
> | Działanie | Microsoft. Security/Locations/Tasks/Activate/Action | Aktywuj zalecenie dotyczące zabezpieczeń |
> | Działanie | Microsoft.Security/locations/tasks/dismiss/action | Odrzuć zalecenie dotyczące zabezpieczeń |
> | Działanie | Microsoft. Security/Locations/Tasks/Read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Działanie | Microsoft. zabezpieczenia/lokalizacje/zadania/Rozwiązywanie/akcja | Rozwiązywanie zalecenia dotyczącego zabezpieczeń |
> | Działanie | Microsoft. Security/Locations/Tasks/Start/Action | Rozpocznij zalecenie dotyczące zabezpieczeń |
> | Działanie | Microsoft. Security/Policies/Read | Pobiera zasady zabezpieczeń |
> | Działanie | Microsoft.Security/policies/write | Aktualizuje zasady zabezpieczeń |
> | Działanie | Microsoft. Security/cenniki/usuwanie | Usuwa ustawienia cen dla zakresu |
> | Działanie | Microsoft. zabezpieczenia/cenniki/odczyt | Pobiera ustawienia cen dla zakresu |
> | Działanie | Microsoft. zabezpieczenia/cenniki/zapis | Aktualizuje ustawienia cen dla zakresu |
> | Działanie | Microsoft. Security/Register/Action | Rejestruje subskrypcję dla Azure Security Center |
> | Działanie | Microsoft. Security/securityContacts/Delete | Usuwa kontakt zabezpieczeń |
> | Działanie | Microsoft. Security/securityContacts/Read | Pobiera kontakt zabezpieczeń |
> | Działanie | Microsoft. Security/securityContacts/Write | Aktualizuje kontakt zabezpieczeń |
> | Działanie | Microsoft.Security/securitySolutions/delete | Usuwa rozwiązanie zabezpieczeń |
> | Działanie | Microsoft.Security/securitySolutions/read | Pobiera rozwiązania zabezpieczeń |
> | Działanie | Microsoft.Security/securitySolutions/write | Tworzy nowe rozwiązanie zabezpieczeń lub aktualizuje istniejące |
> | Działanie | Microsoft.Security/securitySolutionsReferenceData/read | Pobiera dane referencyjne rozwiązań zabezpieczeń |
> | Działanie | Microsoft. Security/securityStatuses/Read | Pobiera stan kondycji zabezpieczeń dla zasobów platformy Azure |
> | Działanie | Microsoft. Security/securityStatusesSummaries/Read | Pobiera podsumowania stanu zabezpieczeń dla zakresu |
> | Działanie | Microsoft.Security/settings/read | Pobiera ustawienia dla zakresu |
> | Działanie | Microsoft.Security/settings/write | Aktualizuje ustawienia zakresu |
> | Działanie | Microsoft.Security/tasks/read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Działanie | Microsoft. Security/Unregister/Action | Wyrejestrowuje subskrypcję z Azure Security Center |
> | Działanie | Microsoft. Security/webApplicationFirewalls/Delete | Usuwa zaporę aplikacji sieci Web |
> | Działanie | Microsoft. Security/webApplicationFirewalls/Read | Pobiera zapory aplikacji sieci Web |
> | Działanie | Microsoft. Security/webApplicationFirewalls/Write | Tworzy nową zaporę aplikacji sieci Web lub aktualizuje istniejącą |
> | Działanie | Microsoft.Security/workspaceSettings/connect/action | Zmień ustawienia ponownego połączenia ustawień obszaru roboczego |
> | Działanie | Microsoft.Security/workspaceSettings/delete | Usuwa ustawienia obszaru roboczego |
> | Działanie | Microsoft.Security/workspaceSettings/read | Pobiera ustawienia obszaru roboczego |
> | Działanie | Microsoft.Security/workspaceSettings/write | Aktualizuje ustawienia obszaru roboczego |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.SecurityGraph/diagnosticsettings/delete | Usuwanie ustawień diagnostycznych |
> | Działanie | Microsoft.SecurityGraph/diagnosticsettings/read | Odczytywanie ustawień diagnostycznych |
> | Działanie | Microsoft.SecurityGraph/diagnosticsettings/write | Zapisywanie ustawień diagnostycznych |
> | Działanie | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. SecurityInsights/agregacje/odczyt | Pobiera informacje zagregowane |
> | Działanie | Microsoft. SecurityInsights/alertRules/Actions/Delete | Usuwa akcje odpowiedzi reguły alertu |
> | Działanie | Microsoft. SecurityInsights/alertRules/Actions/Read | Pobiera akcje odpowiedzi reguły alertu |
> | Działanie | Microsoft. SecurityInsights/alertRules/Actions/Write | Aktualizuje akcje odpowiedzi reguły alertu |
> | Działanie | Microsoft. SecurityInsights/alertRules/Delete | Usuwa reguły alertów |
> | Działanie | Microsoft. SecurityInsights/alertRules/odczyt | Pobiera reguły alertów |
> | Działanie | Microsoft. SecurityInsights/alertRules/Write | Aktualizuje reguły alertów |
> | Działanie | Microsoft. SecurityInsights/zakładki/Usuń | Usuwa zakładki |
> | Działanie | Microsoft. SecurityInsights/zakładki/rozwiń/akcja | Pobiera powiązane jednostki jednostki według określonego rozwinięcia |
> | Działanie | Microsoft. SecurityInsights/zakładki/odczyt | Pobiera zakładki |
> | Działanie | Microsoft. SecurityInsights/zakładki/zapis | Aktualizuje zakładki |
> | Działanie | Microsoft. SecurityInsights/sprawy/Komentarze/odczyt | Pobiera komentarze dotyczące wielkości liter |
> | Działanie | Microsoft. SecurityInsights/sprawy/Komentarze/zapis | Tworzy Komentarze do wielkości liter |
> | Działanie | Microsoft. SecurityInsights/przypadki/usuwanie | Usuwa wielkość liter |
> | Działanie | Microsoft. SecurityInsights/przypadki/badania/odczyt | Pobiera badania przypadku |
> | Działanie | Microsoft. SecurityInsights/przypadki/badania/zapis | Aktualizuje metadane przypadku |
> | Działanie | Microsoft. SecurityInsights/przypadki/odczyt | Pobiera wielkość liter |
> | Działanie | Microsoft. SecurityInsights/sprawy/zapis | Aktualizuje przypadek |
> | Działanie | Microsoft. SecurityInsights/dataconnecters/Delete | Usuwa Łącznik danych |
> | Działanie | Microsoft. SecurityInsights/dataconnecters/odczyt | Pobiera łączniki danych |
> | Działanie | Microsoft. SecurityInsights/dataconnecters/Write | Aktualizuje Łącznik danych |
> | Działanie | Microsoft. SecurityInsights/Register/Action | Rejestruje subskrypcję na platformie Azure wskaźnikowej |
> | Działanie | Microsoft. SecurityInsights/Settings/Read | Pobiera ustawienia |
> | Działanie | Microsoft. SecurityInsights/Settings/Write | Ustawienia aktualizacji |
> | Działanie | Microsoft. SecurityInsights/Unregister/Action | Wyrejestrowuje subskrypcję z platformy Azure — wskaźnik |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ServiceBus/checkNameAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft. ServiceBus/checkNamespaceAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Działanie | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Usuwa reguły sieci wirtualnej w dostawcy zasobów ServiceBus dla określonej sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Działanie | Microsoft.ServiceBus/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Działanie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Działanie | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Działanie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Działanie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Usuwa filtr Event Grid skojarzony z przestrzenią nazw. |
> | Działanie | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Pobiera Filtr Event Grid skojarzony z przestrzenią nazw. |
> | Działanie | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Tworzy lub aktualizuje filtr Event Grid skojarzony z przestrzenią nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/eventhubs/Read | Pobierz listę opisów zasobów EventHub |
> | Działanie | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Usuń zasób filtru IP |
> | Działanie | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Pobierz zasób filtru IP |
> | Działanie | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Utwórz zasób filtru IP |
> | Akcja dataaction | Microsoft. ServiceBus/przestrzenie nazw/komunikaty/odbieranie/akcja | Odbieranie komunikatów |
> | Akcja dataaction | Microsoft. ServiceBus/przestrzenie nazw/komunikaty/wysyłanie/akcja | Wysyłanie komunikatów |
> | Działanie | Microsoft. ServiceBus/Namespaces/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. ServiceBus/Namespaces/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/migracja/akcja | Operacja migracji przestrzeni nazw |
> | Działanie | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Usuwa konfigurację migracji. |
> | Działanie | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Pobiera konfigurację migracji, która wskazuje stan migracji i oczekujących operacji replikacji |
> | Działanie | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Przywróć/akcja | Przywraca migrację przestrzeni nazw Standard do warstwy Premium |
> | Działanie | Microsoft. ServiceBus/Namespaces/migrationConfigurations/upgrade/Action | Przypisuje serwer DNS skojarzony z przestrzenią nazw w warstwie Premium, która kończy migrację i kończy synchronizowanie zasobów ze standardowego do Premium przestrzeni nazw |
> | Działanie | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Tworzy lub aktualizuje konfigurację migracji. Spowoduje to rozpoczęcie synchronizowania zasobów ze standardowego do przestrzeni nazw Premium |
> | Działanie | Microsoft. ServiceBus/Namespaces/networkruleset/Delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/Namespaces/networkruleset/Read | Pobiera zasób NetworkRuleSet |
> | Działanie | Microsoft. ServiceBus/Namespaces/networkruleset/Write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft.ServiceBus/namespaces/networkrulesets/read | Pobiera zasób NetworkRuleSet |
> | Działanie | Microsoft.ServiceBus/namespaces/networkrulesets/write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/Namespaces/operationresults/Read | Pobierz stan operacji przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Write | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/Providers/Microsoft. Insights/logDefinitions/Read | Pobierz listę opisów zasobów dzienników przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/Providers/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/kolejki/reguł autoryzacji/akcja | Operacja aktualizacji kolejki. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Działanie | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji kolejki |
> | Działanie | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia z kolejką |
> | Działanie | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji kolejki |
> | Działanie | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/Write | Utwórz reguły autoryzacji kolejki i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Działanie | Microsoft.ServiceBus/namespaces/queues/Delete | Operacja usuwania zasobu kolejki |
> | Działanie | Microsoft.ServiceBus/namespaces/queues/read | Pobierz listę opisów zasobów kolejki |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/kolejki/zapis | Utwórz lub zaktualizuj właściwości kolejki. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Działanie | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw ACS |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/akcja | Operacja zaktualizowania tematu. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/usuwanie | Operacja usuwania tematu reguły autoryzacji |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/ListKeys/akcja | Pobierz parametry połączenia z tematem |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/odczyt |  Pobierz listę reguł autoryzacji tematów |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/regenerateKeys/akcja | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/zapis | Tworzenie reguł autoryzacji tematów i aktualizowanie ich właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Działanie | Microsoft.ServiceBus/namespaces/topics/Delete | Operacja usuwania zasobu tematu |
> | Działanie | Microsoft.ServiceBus/namespaces/topics/read | Pobierz listę opisów zasobów tematu |
> | Działanie | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operacja usuwania zasobu TopicSubscription |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/subskrypcje/odczyt | Pobierz listę opisów zasobów TopicSubscription |
> | Działanie | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operacja usuwania zasobu reguły |
> | Działanie | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/odczyt | Pobierz listę opisów zasobów reguł |
> | Działanie | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/zapis | Utwórz lub zaktualizuj właściwości reguły. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/subskrypcje/zapis | Utwórz lub zaktualizuj właściwości TopicSubscription. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/zapis | Utwórz lub zaktualizuj właściwości tematu. |
> | Działanie | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Pobiera zasób reguły sieci wirtualnej |
> | Działanie | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Działanie | Microsoft. ServiceBus/Operations/Read | Pobierz operacje |
> | Działanie | Microsoft. ServiceBus/Register/Action | Rejestruje subskrypcję dostawcy zasobów ServiceBus i umożliwia tworzenie zasobów ServiceBus |
> | Działanie | Microsoft.ServiceBus/sku/read | Pobierz listę opisów zasobów jednostki SKU |
> | Działanie | Microsoft.ServiceBus/sku/regions/read | Pobierz listę opisów zasobów SkuRegions |
> | Działanie | Microsoft. ServiceBus/Unregister/Action | Rejestruje subskrypcję dostawcy zasobów ServiceBus i umożliwia tworzenie zasobów ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.ServiceFabric/clusters/applications/delete | Usuń dowolną aplikację |
> | Działanie | Microsoft.ServiceFabric/clusters/applications/read | Odczytaj dowolną aplikację |
> | Działanie | Microsoft.ServiceFabric/clusters/applications/services/delete | Usuwanie dowolnej usługi |
> | Działanie | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Odczytaj dowolną partycję |
> | Działanie | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Odczytaj dowolną replikę |
> | Działanie | Microsoft.ServiceFabric/clusters/applications/services/read | Odczytaj dowolną usługę |
> | Działanie | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Odczytaj wszystkie stany usług |
> | Działanie | Microsoft.ServiceFabric/clusters/applications/services/write | Utwórz lub zaktualizuj dowolną usługę |
> | Działanie | Microsoft.ServiceFabric/clusters/applications/write | Utwórz lub zaktualizuj dowolną aplikację |
> | Działanie | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Usuń dowolny typ aplikacji |
> | Działanie | Microsoft.ServiceFabric/clusters/applicationTypes/read | Odczytaj dowolny typ aplikacji |
> | Działanie | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Usuń wszystkie wersje typu aplikacji |
> | Działanie | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Odczytaj dowolną wersję typu aplikacji |
> | Działanie | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Utwórz lub zaktualizuj dowolną wersję typu aplikacji |
> | Działanie | Microsoft.ServiceFabric/clusters/applicationTypes/write | Utwórz lub zaktualizuj dowolny typ aplikacji |
> | Działanie | Microsoft.ServiceFabric/clusters/delete | Usuń dowolny klaster |
> | Działanie | Microsoft.ServiceFabric/clusters/nodes/read | Odczytaj dowolny węzeł |
> | Działanie | Microsoft.ServiceFabric/clusters/read | Odczytaj dowolny klaster |
> | Działanie | Microsoft.ServiceFabric/clusters/statuses/read | Odczytaj wszystkie Stany klastra |
> | Działanie | Microsoft.ServiceFabric/clusters/write | Utwórz lub zaktualizuj dowolny klaster |
> | Działanie | Microsoft.ServiceFabric/locations/clusterVersions/read | Odczytaj dowolną wersję klastra |
> | Działanie | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Odczytaj dowolną wersję klastra dla określonego środowiska |
> | Działanie | Microsoft.ServiceFabric/locations/operationresults/read | Odczytaj wszystkie wyniki operacji |
> | Działanie | Microsoft.ServiceFabric/locations/operations/read | Odczytaj wszystkie operacje według lokalizacji |
> | Działanie | Microsoft.ServiceFabric/operations/read | Odczytaj wszystkie dostępne operacje |
> | Działanie | Microsoft.ServiceFabric/register/action | Zarejestruj dowolną akcję |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. SignalRService/Locations/checknameavailability/Action | Sprawdza, czy nazwa jest dostępna do użycia z nową usługą sygnalizującego |
> | Działanie | Microsoft.SignalRService/locations/operationresults/signalr/read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Działanie | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Działanie | Microsoft.SignalRService/locations/usages/read | Pobierz użycie przydziału dla usługi Azure Signal Service |
> | Działanie | Microsoft.SignalRService/operationresults/read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Działanie | Microsoft. SignalRService/Operations/Read | Wyświetl listę operacji usługi Azure Signal Service. |
> | Działanie | Microsoft.SignalRService/operationstatus/read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Działanie | Microsoft. SignalRService/Register/Action | Rejestruje dostawcę zasobów "Microsoft. SignalRService" z subskrypcją |
> | Działanie | Microsoft.SignalRService/SignalR/delete | Usuwanie całej usługi sygnalizującej |
> | Działanie | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Usuń filtr usługi Event Grid ze sygnalizującego. |
> | Działanie | Microsoft.SignalRService/SignalR/eventGridFilters/read | Pobierz właściwości określonego filtru usługi Event Grid lub Wyświetl listę wszystkich filtrów usługi Event Grid dla określonego sygnalizującego. |
> | Działanie | Microsoft.SignalRService/SignalR/eventGridFilters/write | Utwórz lub zaktualizuj filtr usługi Event Grid dla sygnalizującego o określonych parametrach. |
> | Działanie | Microsoft.SignalRService/SignalR/listkeys/action | Wyświetlanie wartości kluczy dostępu sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Działanie | Microsoft. SignalRService/Signaler/privateEndpointConnectionProxies/Delete | Usuwanie serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. SignalRService/Signaler/privateEndpointConnectionProxies/odczyt | Odczytaj prywatny serwer proxy Connetionego punktu końcowego |
> | Działanie | Microsoft. SignalRService/Signaler/privateEndpointConnectionProxies/Validate/akcja | Weryfikowanie prywatnego serwera proxy połączenia punktu końcowego |
> | Działanie | Microsoft. SignalRService/Signaler/privateEndpointConnectionProxies/Write | Tworzenie prywatnego serwera proxy połączenia punktu końcowego |
> | Działanie | Microsoft. SignalRService/Signaler/privateEndpointConnections/odczyt | Odczytaj połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. SignalRService/Signaler/privateEndpointConnections/Write | Zatwierdź lub Odrzuć połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. SignalRService/Signaler/privateLinkResources/odczyt | Wyświetl listę wszystkich prywatnych zasobów linku sygnalizującego |
> | Działanie | Microsoft.SignalRService/SignalR/read | Wyświetlanie ustawień i konfiguracji sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Działanie | Microsoft.SignalRService/SignalR/regeneratekey/action | Zmiana wartości kluczy dostępu sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Działanie | Microsoft. SignalRService/sygnalizujący/Uruchom ponownie/akcję | Aby ponownie uruchomić usługę sygnałów platformy Azure w portalu zarządzania lub za pomocą interfejsu API. Zostanie określony przestój. |
> | Działanie | Microsoft. SignalRService/sygnalizujący/Write | Modyfikowanie ustawień i konfiguracji sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Działanie | Microsoft. SignalRService/Unregister/Action | Wyrejestrowuje dostawcę zasobów "Microsoft. SignalRService" z subskrypcją |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Wyświetla listę artefaktów aplikacji definicji aplikacji. |
> | Działanie | Microsoft.Solutions/applicationDefinitions/delete | Usuwa definicję aplikacji. |
> | Działanie | Microsoft.Solutions/applicationDefinitions/read | Pobiera listę definicji aplikacji. |
> | Działanie | Microsoft.Solutions/applicationDefinitions/write | Dodaj lub zmodyfikuj definicję aplikacji. |
> | Działanie | Microsoft.Solutions/applications/applicationArtifacts/read | Wyświetla listę artefaktów aplikacji. |
> | Działanie | Microsoft.Solutions/applications/delete | Usuwa aplikację. |
> | Działanie | Microsoft.Solutions/applications/read | Pobiera listę aplikacji. |
> | Działanie | Microsoft.Solutions/applications/refreshPermissions/action | Odświeża uprawnienia aplikacji. |
> | Działanie | Microsoft.Solutions/applications/updateAccess/action | Aktualizuje dostęp do aplikacji. |
> | Działanie | Microsoft.Solutions/applications/write | Tworzy aplikację. |
> | Działanie | Microsoft.Solutions/jitRequests/delete | Usuń JitRequest |
> | Działanie | Microsoft.Solutions/jitRequests/read | Pobiera listę JitRequests |
> | Działanie | Microsoft.Solutions/jitRequests/write | Tworzy element JitRequest |
> | Działanie | Microsoft.Solutions/locations/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft. Solutions/Operations/Read | Pobiera listę operacji. |
> | Działanie | Microsoft. Solutions/Register/Action | Zarejestruj się w rozwiązaniach. |
> | Działanie | Microsoft. Solutions/Unregister/Action | Wyrejestrowuje z rozwiązań. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Sql/checkNameAvailability/action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Działanie | Microsoft.Sql/instancePools/delete | Usuwa pulę wystąpień |
> | Działanie | Microsoft.Sql/instancePools/read | Pobiera pulę wystąpień |
> | Działanie | Microsoft.Sql/instancePools/usages/read | Pobiera informacje o użyciu puli wystąpień |
> | Działanie | Microsoft.Sql/instancePools/write | Tworzy lub aktualizuje pulę wystąpień |
> | Działanie | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Działanie | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Pobierz wynik operacji ustawiania zasad inspekcji obiektu BLOB serwera |
> | Działanie | Microsoft. SQL/lokalizacje/możliwości/odczyt | Pobiera możliwości dla tej subskrypcji w danej lokalizacji |
> | Działanie | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | Działanie | Microsoft.Sql/locations/databaseOperationResults/read | Pobiera stan operacji bazy danych. |
> | Działanie | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Pobiera operacje w toku na usuniętym serwerze |
> | Działanie | Microsoft.Sql/locations/deletedServerOperationResults/read | Pobiera operacje w toku na usuniętym serwerze |
> | Działanie | Microsoft.Sql/locations/deletedServers/read | Zwróć listę usuniętych serwerów lub pobiera właściwości dla określonego usuniętego serwera. |
> | Działanie | Microsoft.Sql/locations/deletedServers/recover/action | Odzyskiwanie usuniętego serwera |
> | Działanie | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Pobiera asynchroniczną operację platformy Azure dla operacji asynchronicznej puli elastycznej |
> | Działanie | Microsoft.Sql/locations/elasticPoolOperationResults/read | Pobiera wynik operacji puli elastycznej. |
> | Działanie | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania danych transparentu |
> | Działanie | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania danych transparentu |
> | Działanie | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Działanie | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Działanie | Microsoft. SQL/Locations/firewallRulesAzureAsyncOperation/Read | Pobiera stan operacji reguły zapory. |
> | Działanie | Microsoft.Sql/locations/firewallRulesOperationResults/read | Pobiera stan operacji reguły zapory. |
> | Działanie | Microsoft.Sql/locations/instanceFailoverGroups/delete | Usuwa istniejącą grupę trybu failover wystąpienia. |
> | Działanie | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Wykonuje planowaną pracę w trybie failover w istniejącej grupie trybu failover wystąpienia. |
> | Działanie | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszone przejście w tryb failover w istniejącej grupie trybu failover wystąpienia. |
> | Działanie | Microsoft.Sql/locations/instanceFailoverGroups/read | Zwraca listę grup trybu failover wystąpienia lub pobiera właściwości dla określonego wystąpienia grupy trybu failover. |
> | Działanie | Microsoft.Sql/locations/instanceFailoverGroups/write | Tworzy grupę trybu failover wystąpienia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej grupy trybu failover. |
> | Działanie | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Pobiera stan operacji puli wystąpień |
> | Działanie | Microsoft.Sql/locations/instancePoolOperationResults/read | Pobiera wynik operacji puli wystąpień |
> | Działanie | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Zwraca szczegółowe informacje o określonym interfejsie operacji asynchronicznej platformy Azure |
> | Działanie | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Zwraca szczegóły podanej operacji profilu punktu końcowego interfejsu |
> | Działanie | Microsoft. SQL/Locations/jobAgentAzureAsyncOperation/Read | Pobiera stan operacji agenta zadań. |
> | Działanie | Microsoft.Sql/locations/jobAgentOperationResults/read | Pobiera wynik operacji agenta zadań. |
> | Działanie | Microsoft.Sql/locations/longTermRetentionBackups/read | Wyświetla długoterminowe kopie zapasowe przechowywania dla każdej bazy danych na każdym serwerze w lokalizacji |
> | Działanie | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Wyświetla kopie zapasowe przechowywania długoterminowego dla każdej bazy danych na serwerze |
> | Działanie | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Usuwa długoterminową kopię zapasową przechowywania |
> | Działanie | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Wyświetla kopie zapasowe przechowywania długoterminowego dla bazy danych |
> | Działanie | Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/Action | Kończy operację przywracania zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania wystąpienia zarządzanego szyfrowania danych |
> | Działanie | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania wystąpienia zarządzanego szyfrowania danych |
> | Działanie | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Pobiera operacje w toku na przezroczystych kluczach wystąpienia zarządzanego szyfrowania danych |
> | Działanie | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Pobiera operacje w toku na przezroczystych kluczach wystąpienia zarządzanego szyfrowania danych |
> | Działanie | Microsoft. SQL/Locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/Read | Pobiera stan długotrwałej operacji zasad przechowywania dla zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/Locations/managedInstanceLongTermRetentionPolicyOperationResults/Read | Pobiera stan długotrwałej operacji zasad przechowywania dla zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/Locations/managedShortTermRetentionPolicyOperationResults/Read | Pobiera stan krótkoterminowej operacji zasad przechowywania |
> | Działanie | Microsoft. SQL/Locations/managedTransparentDataEncryptionAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych zarządzanej bazy danych |
> | Działanie | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych zarządzanej bazy danych |
> | Działanie | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.Sql/locations/read | Pobiera dostępne lokalizacje dla danej subskrypcji |
> | Działanie | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft.Sql/locations/serverKeyOperationResults/read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft. SQL/Locations/shortTermRetentionPolicyOperationResults/Read | Pobiera stan krótkoterminowej operacji zasad przechowywania |
> | Działanie | Microsoft.Sql/locations/syncAgentOperationResults/read | Pobierz wynik operacji zasobu agenta synchronizacji |
> | Działanie | Microsoft.Sql/locations/syncDatabaseIds/read | Pobieranie identyfikatorów bazy danych synchronizacji dla określonego regionu i subskrypcji |
> | Działanie | Microsoft.Sql/locations/syncGroupOperationResults/read | Pobierz wynik operacji zasobu grupy synchronizacji |
> | Działanie | Microsoft.Sql/locations/syncMemberOperationResults/read | Pobierz wynik operacji synchronizacji zasobu elementu członkowskiego |
> | Działanie | Microsoft.Sql/locations/usages/read | Pobiera kolekcję metryk użycia dla tej subskrypcji w lokalizacji |
> | Działanie | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Zwraca szczegóły określonych reguł sieci wirtualnej Azure asynchronicznej operacji  |
> | Działanie | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Zwraca szczegóły określonej operacji dotyczącej reguł sieci wirtualnej  |
> | Działanie | Microsoft. SQL/managedInstances/Administratorzy/usuwanie | Usuwa istniejącego administratora wystąpienia zarządzanego. |
> | Działanie | Microsoft. SQL/managedInstances/Administratorzy/odczyt | Pobiera listę administratorów wystąpienia zarządzanego. |
> | Działanie | Microsoft. SQL/managedInstances/Administratorzy/zapis | Tworzy lub aktualizuje administratora wystąpienia zarządzanego o określonych parametrach. |
> | Działanie | Microsoft. SQL/managedInstances/Databases/backupLongTermRetentionPolicies/Read | Pobiera długoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/backupLongTermRetentionPolicies/Write | Aktualizuje długoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Pobiera krótkoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Aktualizuje krótkoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/kolumny/odczyt | Zwracanie listy kolumn dla zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/completeRestore/Action | Kończy operację przywracania zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/Write | Etykiety czułości aktualizacji partii |
> | Działanie | Microsoft.Sql/managedInstances/databases/delete | Usuwa istniejącą zarządzaną bazę danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla baz danych wystąpienia zarządzanego |
> | Działanie | Microsoft.Sql/managedInstances/databases/read | Pobiera istniejącą zarządzaną bazę danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/Write | Etykiety czułości zalecanych aktualizacji wsadowych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/restoreDetails/Read | Zwraca szczegóły przywracania zarządzanej bazy danych, gdy przywracanie jest w toku. |
> | Działanie | Microsoft.Sql/managedInstances/databases/schemas/read | Pobierz schemat zarządzanej bazy danych. (tylko schemat) |
> | Działanie | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Pobierz kolumnę zarządzanej bazy danych (tylko schemat) |
> | Działanie | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Usuń etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Disable/Action | Wyłącz zalecenia dotyczące czułości w danej kolumnie |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Włącz/akcja | Włącz zalecenia dotyczące czułości w danej kolumnie |
> | Działanie | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Pobierz etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/zapis | Utwórz lub zaktualizuj etykietę czułości danej kolumny |
> | Działanie | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Pobierz tabelę zarządzanej bazy danych (tylko schemat) |
> | Działanie | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń zarządzanych baz danych skonfigurowanych dla danego serwera |
> | Działanie | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń bazy danych dla danej zarządzanej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/securityEvents/read | Pobiera zdarzenia zabezpieczeń zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/Read | Pobierz szczegóły Transparent Data Encryption bazy danych w danej zarządzanej bazie danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/Write | Zmień Transparent Data Encryption bazy danych dla danej zarządzanej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Usuń ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Pobieranie zasad oceny luk w zabezpieczeniach na givendatabase |
> | Działanie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Pobierz linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Zmień linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Przekonwertuj istniejący wynik skanowania na format możliwy do odczytania przez człowieka. Jeśli już nie istnieje, nic się nie dzieje |
> | Działanie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Działanie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Zwróć listę rekordów skanowania oceny luk w zabezpieczeniach bazy danych lub Pobierz rekord skanowania pod kątem określonego identyfikatora skanowania. |
> | Działanie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/databases/write | Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych. |
> | Działanie | Microsoft.Sql/managedInstances/delete | Usuwa istniejące wystąpienie zarządzane. |
> | Działanie | Microsoft.Sql/managedInstances/encryptionProtector/read | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/akcja | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft.Sql/managedInstances/encryptionProtector/write | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft. SQL/managedInstances/inaccessibleManagedDatabases/odczyt | Pobiera listę niedostępnych zarządzanych baz danych w wystąpieniu zarządzanym |
> | Działanie | Microsoft.Sql/managedInstances/keys/delete | Usuwa istniejący klucz wystąpienia zarządzanego Azure SQL. |
> | Działanie | Microsoft.Sql/managedInstances/keys/read | Zwróć listę kluczy wystąpienia zarządzanego lub pobiera właściwości dla określonego klucza wystąpienia zarządzanego. |
> | Działanie | Microsoft.Sql/managedInstances/keys/write | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza wystąpienia zarządzanego. |
> | Działanie | Microsoft.Sql/managedInstances/metricDefinitions/read | Pobierz definicje metryk wystąpienia zarządzanego |
> | Działanie | Microsoft.Sql/managedInstances/metrics/read | Pobierz metryki wystąpienia zarządzanego |
> | Działanie | Microsoft. SQL/managedInstances/Operations/Cancel/Action | Anuluje wystąpienie zarządzane usługi Azure SQL oczekujące na operację asynchroniczną, która nie została jeszcze zakończona. |
> | Działanie | Microsoft. SQL/managedInstances/Operations/Read | Pobierz operacje wystąpienia zarządzanego |
> | Działanie | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla wystąpień zarządzanych |
> | Działanie | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla wystąpień zarządzanych |
> | Działanie | Microsoft.Sql/managedInstances/read | Zwróć listę wystąpień zarządzanych lub pobiera właściwości dla określonego wystąpienia zarządzanego. |
> | Działanie | Microsoft.Sql/managedInstances/recoverableDatabases/read | Zwraca listę możliwych do odzyskania zarządzanych baz danych |
> | Działanie | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Pobiera krótkoterminowe zasady przechowywania dla porzuconej zarządzanej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Aktualizuje krótkoterminowe zasady przechowywania dla porzuconej zarządzanej bazy danych |
> | Działanie | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Zwraca listę dostępnych usuniętych zarządzanych baz danych. |
> | Działanie | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń serwera zarządzanego skonfigurowanych dla danego serwera |
> | Działanie | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera zarządzanego dla danego serwera zarządzanego |
> | Działanie | Microsoft.Sql/managedInstances/tdeCertificates/action | Utwórz/zaktualizuj certyfikat TDE |
> | Działanie | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Usuń ocenę luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Działanie | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Pobieranie zasad oceny luk w zabezpieczeniach w danym wystąpieniu zarządzanym |
> | Działanie | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Działanie | Microsoft.Sql/managedInstances/write | Tworzy wystąpienie zarządzane o określonych parametrach lub aktualizuje właściwości lub Tagi dla określonego wystąpienia zarządzanego. |
> | Działanie | Microsoft.Sql/operations/read | Pobiera dostępne operacje REST |
> | Działanie | Microsoft. SQL/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft.Sql/register/action | Rejestruje subskrypcję dostawcy zasobów programu Microsoft SQL Database i umożliwia tworzenie baz danych Microsoft SQL. |
> | Działanie | Microsoft.Sql/servers/administrators/delete | Usuwa określony obiekt administratora Azure Active Directory |
> | Działanie | Microsoft.Sql/servers/administrators/read | Pobiera określony obiekt administratora Azure Active Directory |
> | Działanie | Microsoft.Sql/servers/administrators/write | Dodaje lub aktualizuje określony obiekt administratora Azure Active Directory |
> | Działanie | Microsoft.Sql/servers/advisors/read | Zwraca listę doradców dostępnych dla serwera |
> | Działanie | Microsoft.Sql/servers/advisors/recommendedActions/read | Zwraca listę zalecanych akcji określonego klasyfikatora dla serwera |
> | Działanie | Microsoft.Sql/servers/advisors/recommendedActions/write | Zastosuj zalecaną akcję na serwerze |
> | Działanie | Microsoft.Sql/servers/advisors/write | Aktualizuje stan autouruchomienia klasyfikatora na poziomie serwera. |
> | Działanie | Microsoft.Sql/servers/auditingPolicies/read | Pobierz szczegóły domyślnych zasad inspekcji tabeli serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft.Sql/servers/auditingPolicies/write | Zmień domyślną inspekcję tabeli serwera dla danego serwera |
> | Działanie | Microsoft.Sql/servers/auditingSettings/operationResults/read | Pobierz wynik operacji ustawiania zasad inspekcji obiektu BLOB serwera |
> | Działanie | Microsoft.Sql/servers/auditingSettings/read | Pobierz szczegóły zasad inspekcji obiektów BLOB serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft.Sql/servers/auditingSettings/write | Zmień inspekcję obiektu BLOB serwera dla danego serwera |
> | Działanie | Microsoft.Sql/servers/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania dla serwera. |
> | Działanie | Microsoft.Sql/servers/automaticTuning/write | Aktualizuje ustawienia dostrajania automatycznego dla serwera i zwraca zaktualizowane ustawienia |
> | Działanie | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Usuwa istniejący magazyn archiwum kopii zapasowych. |
> | Działanie | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Ta operacja służy do pobierania magazynu długoterminowego przechowywania kopii zapasowych. Zwraca informacje o magazynie zarejestrowanym na tym serwerze |
> | Działanie | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ta operacja służy do rejestrowania magazynu długoterminowego przechowywania kopii zapasowej na serwerze programu. |
> | Działanie | Microsoft. SQL/serwery/communicationLinks/Delete | Usuwa istniejące łącze komunikacji z serwerem. |
> | Działanie | Microsoft. SQL/serwery/communicationLinks/odczyt | Zwróć listę linków komunikacyjnych określonego serwera. |
> | Działanie | Microsoft. SQL/serwery/communicationLinks/Write | Utwórz lub zaktualizuj łącze do komunikacji z serwerem. |
> | Działanie | Microsoft.Sql/servers/connectionPolicies/read | Zwróć listę zasad połączenia serwera określonego serwera. |
> | Działanie | Microsoft.Sql/servers/connectionPolicies/write | Utwórz lub zaktualizuj zasady połączenia z serwerem. |
> | Działanie | Microsoft.Sql/servers/databases/advisors/read | Zwraca listę doradców dostępnych dla bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Zwraca listę zalecanych akcji określonego klasyfikatora dla bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Zastosuj zalecaną akcję w bazie danych |
> | Działanie | Microsoft.Sql/servers/databases/advisors/write | Aktualizowanie stanu autouzupełniania usługi Advisor na poziomie bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/auditingPolicies/read | Pobierz szczegóły zasad inspekcji tabeli skonfigurowanych dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/auditingPolicies/write | Zmień zasady inspekcji tabeli dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/auditingSettings/read | Pobierz szczegóły zasad inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/auditingSettings/write | Zmień zasady inspekcji obiektów BLOB dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania dla bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualizuje ustawienia dostrajania automatycznego dla bazy danych i zwraca zaktualizowane ustawienia |
> | Działanie | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Zwróć listę zasad archiwizacji kopii zapasowych określonej bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Utwórz lub zaktualizuj Zasady archiwizacji kopii zapasowej bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Pobiera krótkoterminowe zasady przechowywania dla bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Aktualizuje krótkoterminowe zasady przechowywania dla bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/kolumny/odczyt | Zwracanie listy kolumn dla bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/connectionPolicies/read | Pobierz szczegóły zasad połączenia skonfigurowanych dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/connectionPolicies/write | Zmień zasady połączenia dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Etykiety czułości aktualizacji partii |
> | Działanie | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Zwróć listę zasad maskowania danych bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Usuń regułę zasad maskowania danych dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Pobierz szczegóły reguły zasad maskowania danych skonfigurowanej w danej bazie danych |
> | Działanie | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Zmień regułę zasad maskowania danych dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Zmień zasady maskowania danych dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Zwraca informacje o kroku zapytania rozproszonego dotyczące zapytania hurtowni danych dla wybranego identyfikatora kroku |
> | Działanie | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Zwraca informacje o zapytaniu dystrybucji magazynu danych dla wybranego identyfikatora zapytania |
> | Działanie | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Pobiera działania użytkownika wystąpienia SQL Data Warehouse, w tym uruchomione i wstrzymane zapytania |
> | Działanie | Microsoft.Sql/servers/databases/delete | Usuwa istniejącą bazę danych. |
> | Działanie | Microsoft.Sql/servers/databases/export/action | Eksportuj Azure SQL Database |
> | Działanie | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Pobierz szczegóły zasad rozszerzonej inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Zmiana zasad rozszerzonej inspekcji obiektów BLOB dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/extensions/read | Pobiera kolekcję rozszerzeń dla bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/rozszerzenia/zapis | Zmień rozszerzenie dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/tryb failover/akcja | Klient przełączenia w tryb failover bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Pobierz zasady geograficznej kopii zapasowej dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Tworzenie lub aktualizowanie zasad tworzenia kopii zapasowej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/importExportOperationResults/read | Pobiera operacje importu/eksportu w toku |
> | Działanie | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Pobiera listę dostępnych okien obsługi dla wybranej bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/maintenanceWindows/read | Pobiera ustawienia okien obsługi dla wybranej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/maintenanceWindows/Write | Ustawia ustawienia okien obsługi dla wybranej bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft.Sql/servers/databases/metrics/read | Metryki zwracane dla baz danych |
> | Działanie | Microsoft.Sql/servers/databases/move/action | Zmień nazwę istniejącej bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/operationResults/read | Pobiera stan operacji bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/operations/cancel/action | Anuluje Azure SQL Database oczekującej operacji asynchronicznej, która nie została jeszcze zakończona. |
> | Działanie | Microsoft.Sql/servers/databases/operations/read | Zwróć listę operacji wykonanych na bazie danych |
> | Działanie | Microsoft.Sql/servers/databases/pause/action | Wstrzymywanie bazy danych usługi Azure SQL DataWarehouse |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla baz danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Zwraca kolekcję tekstów zapytania, które odpowiadają określonym parametrom. |
> | Działanie | Microsoft.Sql/servers/databases/queryStore/read | Zwraca bieżące wartości ustawień magazynu zapytań dla bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/queryStore/write | Aktualizuje ustawienie magazynu zapytań dla bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/read | Zwróć listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/recommendedSensitivityLabels/Write | Etykiety czułości zalecanych aktualizacji wsadowych |
> | Działanie | Microsoft.Sql/servers/databases/replicationLinks/delete | Zakończenie wymuszonej relacji replikacji z potencjalną utratą danych |
> | Działanie | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Przełączenie w tryb failover po zsynchronizowaniu wszystkich zmian z poziomu podstawowego, co sprawia, że ta baza danych jest podzielona na relationship\u0027s replikacji i udostępnianie zdalnego elementu głównego do pomocniczego |
> | Działanie | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Natychmiastowe przejście w tryb failover z potencjalną utratą danych, dzięki czemu ta baza danych jest relationship\u0027s replikacją podstawową i udostępniająca ją jako pomocniczy. |
> | Działanie | Microsoft.Sql/servers/databases/replicationLinks/read | Zwróć listę linków replikacji lub pobiera właściwości dla określonych linków replikacji. |
> | Działanie | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Wymuszone zakończenie relacji replikacji lub po zsynchronizowaniu z partnerem |
> | Działanie | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Aktualizowanie trybu replikacji dla łącza do trybu synchronicznego lub asynchronicznego |
> | Działanie | Microsoft.Sql/servers/databases/restorePoints/action | Tworzy nowy punkt przywracania |
> | Działanie | Microsoft.Sql/servers/databases/restorePoints/delete | Usuwa punkt przywracania bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/restorePoints/read | Zwraca punkty przywracania bazy danych. |
> | Działanie | Microsoft.Sql/servers/databases/resume/action | Wznów bazę danych usługi Azure SQL DataWarehouse |
> | Działanie | Microsoft.Sql/servers/databases/schemas/read | Pobierz schemat bazy danych (tylko schemat). |
> | Działanie | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Pobierz kolumnę bazy danych (tylko schemat). |
> | Działanie | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Usuń etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Disable/Action | Wyłącz zalecenia dotyczące czułości w danej kolumnie |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Włącz/akcja | Włącz zalecenia dotyczące czułości w danej kolumnie |
> | Działanie | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Pobierz etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Write | Utwórz lub zaktualizuj etykietę czułości danej kolumny |
> | Działanie | Microsoft.Sql/servers/databases/schemas/tables/read | Pobierz tabelę bazy danych (tylko schemat). |
> | Działanie | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Pobierz listę zaleceń dotyczących indeksów w bazie danych |
> | Działanie | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Zastosuj zalecenie dotyczące indeksu |
> | Działanie | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń bazy danych skonfigurowanych dla danego serwera |
> | Działanie | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń bazy danych dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/securityMetrics/read | Pobiera kolekcję metryk zabezpieczeń bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/sensitivityLabels/read | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Zwróć sugestię dotyczącą skalowania bazy danych w górę lub w dół na podstawie statystyk wykonywania zapytań w celu zwiększenia wydajności lub obniżenia kosztów |
> | Działanie | Microsoft.Sql/servers/databases/skus/read | Pobiera kolekcję jednostek SKU dostępnych dla bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Anuluj synchronizację grupy synchronizacji |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/delete | Usuwa istniejącą grupę synchronizacji. |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Zwróć listę schematów bazy danych Centrum synchronizacji |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/logs/read | Zwróć listę dzienników grup synchronizacji |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/read | Zwróć listę grup synchronizacji lub pobiera właściwości dla określonej grupy synchronizacji. |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Odśwież schemat bazy danych Centrum synchronizacji |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu narzędzia Sync Hub |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Usuwa istniejący element członkowski synchronizacji. |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Zwróć listę elementów członkowskich synchronizacji lub pobiera właściwości dla określonego elementu członkowskiego synchronizacji. |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Odśwież schemat elementu członkowskiego synchronizacji |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu elementu członkowskiego synchronizacji |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Zwróć listę schematów bazy danych elementu członkowskiego synchronizacji |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Tworzy element członkowski synchronizacji z określonymi parametrami lub aktualizuje właściwości określonego elementu członkowskiego synchronizacji. |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Wyzwalanie synchronizacji grupy synchronizacji |
> | Działanie | Microsoft.Sql/servers/databases/syncGroups/write | Tworzy grupę synchronizacji z określonymi parametrami lub aktualizuje właściwości określonej grupy synchronizacji. |
> | Działanie | Microsoft.Sql/servers/databases/topQueries/queryText/action | Zwraca tekst Transact-SQL dla wybranego identyfikatora zapytania |
> | Działanie | Microsoft.Sql/servers/databases/topQueries/read | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Działanie | Microsoft.Sql/servers/databases/topQueries/statistics/read | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Działanie | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/odczyt | Pobierz stan i szczegóły funkcji zabezpieczenia szyfrowania danych przezroczystych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/Write | Zmień stan przezroczystego szyfrowania danych |
> | Działanie | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Uaktualnianie bazy danych usługi Azure SQL DataWarehouse |
> | Działanie | Microsoft.Sql/servers/databases/usages/read | Pobiera informacje o użyciach Azure SQL Database |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Usuń ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Pobieranie zasad oceny luk w zabezpieczeniach na givendatabase |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Pobierz linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Zmień linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Przekonwertuj istniejący wynik skanowania na format możliwy do odczytania przez człowieka. Jeśli już nie istnieje, nic się nie dzieje |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Zwróć listę rekordów skanowania oceny luk w zabezpieczeniach bazy danych lub Pobierz rekord skanowania pod kątem określonego identyfikatora skanowania. |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Pobieranie wyniku operacji skanowania oceny luk w zabezpieczeniach bazy danych |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Pobierz szczegóły oceny luk w zabezpieczeniach skonfigurowanej w danej bazie danych |
> | Działanie | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/workloadGroups/Usuń | Odrzuca określoną grupę obciążeń. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/workloadGroups/odczyt | Wyświetla listę grup obciążeń dla wybranej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/workloadGroups/workloadClassifiers/Delete | Odrzuca określony klasyfikator obciążeń. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/workloadGroups/workloadClassifiers/odczyt | Wyświetla listę klasyfikatorów obciążeń dla wybranej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/workloadGroups/workloadClassifiers/zapis | Ustawia właściwości dla określonego klasyfikatora obciążeń. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/workloadGroups/Write | Ustawia właściwości dla określonej grupy obciążeń. |
> | Działanie | Microsoft.Sql/servers/databases/write | Tworzy bazę danych z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej bazy danych. |
> | Działanie | Microsoft.Sql/servers/delete | Usuwa istniejący serwer. |
> | Działanie | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Usuwa istniejące konfiguracje odzyskiwania po awarii dla danego serwera |
> | Działanie | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Przełączenie w tryb failover DisasterRecoveryConfiguration |
> | Działanie | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Wymuś tryb failover DisasterRecoveryConfiguration |
> | Działanie | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Pobiera kolekcję konfiguracji odzyskiwania po awarii, która obejmuje ten serwer |
> | Działanie | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Zmień konfigurację odzyskiwania po awarii serwera |
> | Działanie | Microsoft.Sql/servers/elasticPoolEstimates/read | Zwraca listę oszacowań elastycznej puli już utworzonych dla tego serwera |
> | Działanie | Microsoft.Sql/servers/elasticPoolEstimates/write | Tworzy nowe szacowanie puli elastycznej dla listy dostarczonych baz danych |
> | Działanie | Microsoft.Sql/servers/elasticPools/advisors/read | Zwraca listę doradców dostępnych dla puli elastycznej |
> | Działanie | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Zwraca listę zalecanych akcji określonego klasyfikatora dla puli elastycznej |
> | Działanie | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Zastosuj zalecaną akcję w puli elastycznej |
> | Działanie | Microsoft.Sql/servers/elasticPools/advisors/write | Aktualizacja stanu autouruchomienia klasyfikatora na poziomie puli elastycznej. |
> | Działanie | Microsoft.Sql/servers/elasticPools/databases/read | Pobiera listę baz danych dla puli elastycznej |
> | Działanie | Microsoft.Sql/servers/elasticPools/delete | Usuń istniejącą pulę elastyczną |
> | Działanie | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Pobieranie działań i szczegółów w danej elastycznej puli baz danych |
> | Działanie | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Pobierz działania i szczegóły dotyczące danej bazy danych będącej częścią elastycznej puli baz danych |
> | Działanie | Microsoft.Sql/servers/elasticPools/failover/action | Klient zainicjował pracę awaryjną puli elastycznej. |
> | Działanie | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Działanie | Microsoft.Sql/servers/elasticPools/metrics/read | Metryki zwracane dla elastycznych pul baz danych |
> | Działanie | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Anuluje oczekującą operację asynchroniczną w puli elastycznej SQL platformy Azure, która nie została jeszcze zakończona. |
> | Działanie | Microsoft.Sql/servers/elasticPools/operations/read | Zwróć listę operacji wykonywanych w puli elastycznej |
> | Działanie | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Działanie | Microsoft.Sql/servers/elasticPools/read | Pobierz szczegóły puli elastycznej na danym serwerze |
> | Działanie | Microsoft.Sql/servers/elasticPools/skus/read | Pobiera kolekcję jednostek SKU dostępnych dla puli elastycznej |
> | Działanie | Microsoft.Sql/servers/elasticPools/write | Utwórz nowe lub zmień właściwości istniejącej puli elastycznej |
> | Działanie | Microsoft.Sql/servers/encryptionProtector/read | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft. SQL/serwery/encryptionProtector/ponownie Zweryfikuj/akcja | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft.Sql/servers/encryptionProtector/write | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft.Sql/servers/extendedAuditingSettings/read | Pobierz szczegóły zasad inspekcji rozszerzonego serwera obiektów BLOB skonfigurowanych na danym serwerze |
> | Działanie | Microsoft.Sql/servers/extendedAuditingSettings/write | Zmień rozszerzoną inspekcję obiektów BLOB serwera dla danego serwera |
> | Działanie | Microsoft.Sql/servers/failoverGroups/delete | Usuwa istniejącą grupę trybu failover. |
> | Działanie | Microsoft.Sql/servers/failoverGroups/failover/action | Wykonuje planowaną pracę w trybie failover w istniejącej grupie trybu failover. |
> | Działanie | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszone przejście w tryb failover w istniejącej grupie trybu failover. |
> | Działanie | Microsoft.Sql/servers/failoverGroups/read | Zwraca listę grup trybu failover lub pobiera właściwości dla określonej grupy trybu failover. |
> | Działanie | Microsoft.Sql/servers/failoverGroups/write | Tworzy grupę trybu failover z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej grupy trybu failover. |
> | Działanie | Microsoft. SQL/serwery/firewallRules/Delete | Usuwa istniejącą regułę zapory serwera. |
> | Działanie | Microsoft. SQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory serwera lub pobiera właściwości dla określonej reguły zapory serwera. |
> | Działanie | Microsoft. SQL/serwery/firewallRules/Write | Tworzy regułę zapory serwera z określonymi parametrami, aktualizuje właściwości określonej reguły lub zastępuje wszystkie istniejące reguły przy użyciu nowych reguł zapory serwera. |
> | Działanie | Microsoft. SQL/serwery/import/akcja | Utwórz nową bazę danych na serwerze i Wdróż schemat oraz dane z pakietu DacPac |
> | Działanie | Microsoft.Sql/servers/importExportOperationResults/read | Pobiera operacje importu/eksportu w toku |
> | Działanie | Microsoft. SQL/serwery/inaccessibleDatabases/odczyt | Zwraca listę niedostępnych baz danych na serwerze logicznym. |
> | Działanie | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Usuwa określony profil punktu końcowego interfejsu |
> | Działanie | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Zwraca właściwości dla określonego profilu punktu końcowego interfejsu |
> | Działanie | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Tworzy profil punktu końcowego interfejsu z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego punktu końcowego interfejsu |
> | Działanie | Microsoft.Sql/servers/jobAgents/delete | Usuwa agenta zadań usługi Azure SQL DB |
> | Działanie | Microsoft.Sql/servers/jobAgents/read | Pobiera agenta zadań usługi Azure SQL DB |
> | Działanie | Microsoft.Sql/servers/jobAgents/write | Tworzy lub aktualizuje agenta zadań usługi Azure SQL DB |
> | Działanie | Microsoft.Sql/servers/keys/delete | Usuwa istniejący klucz serwera. |
> | Działanie | Microsoft.Sql/servers/keys/read | Zwróć listę kluczy serwera lub pobiera właściwości dla określonego klucza serwera. |
> | Działanie | Microsoft.Sql/servers/keys/write | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza serwera. |
> | Działanie | Microsoft.Sql/servers/operationResults/read | Pobiera operacje serwera w toku |
> | Działanie | Microsoft. SQL/serwery/operacje/odczyt | Zwróć listę operacji wykonanych na serwerze |
> | Działanie | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Działanie | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft.Sql/servers/privateEndpointConnections/delete | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft.Sql/servers/privateEndpointConnections/read | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft.Sql/servers/privateEndpointConnections/write | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/serwery/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft.Sql/servers/privateLinkResources/read | Pobierz zasoby linku prywatnego dla odpowiedniego programu SQL Server |
> | Działanie | Microsoft. SQL/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla serwerów |
> | Działanie | Microsoft.Sql/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Pobierz metryki dla zalecanych elastycznych pul baz danych dla danego serwera |
> | Działanie | Microsoft.Sql/servers/recommendedElasticPools/read | Pobieranie zalecenia dla elastycznych pul baz danych w celu ograniczenia kosztów lub zwiększenia wydajności w oparciu o historyczne wykorzystanie zasobów |
> | Działanie | Microsoft.Sql/servers/recoverableDatabases/read | Ta operacja służy do odzyskiwania po awarii usługi Live Database w celu przywrócenia bazy danych do ostatniego znanego dobrego punktu kopii zapasowej. Zwraca informacje o ostatniej dobrej kopii zapasowej, ale doesn\u0027t w rzeczywistości przywraca bazę danych. |
> | Działanie | Microsoft.Sql/servers/replicationLinks/read | Zwróć listę linków replikacji lub pobiera właściwości dla określonych linków replikacji. |
> | Działanie | Microsoft.Sql/servers/restorableDroppedDatabases/read | Pobierz listę baz danych, które zostały porzucone na danym serwerze, które są nadal w ramach zasad przechowywania. |
> | Działanie | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Pobierz wyniki operacji zapisu zasad wykrywania zagrożeń serwera |
> | Działanie | Microsoft.Sql/servers/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń serwera skonfigurowanych dla danego serwera |
> | Działanie | Microsoft.Sql/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Działanie | Microsoft.Sql/servers/serviceObjectives/read | Pobierz listę celów poziomu usług (nazywanych również warstwami wydajności) dostępnych na danym serwerze |
> | Działanie | Microsoft. SQL/serwery/syncAgents/Delete | Usuwa istniejącego agenta synchronizacji. |
> | Działanie | Microsoft.Sql/servers/syncAgents/generateKey/action | Generuj klucz rejestracji agenta synchronizacji |
> | Działanie | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Zwróć listę połączonych baz danych agenta synchronizacji |
> | Działanie | Microsoft.Sql/servers/syncAgents/read | Zwróć listę agentów synchronizacji lub pobiera właściwości dla określonego agenta synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/syncAgents/Write | Tworzy agenta synchronizacji z określonymi parametrami lub aktualizuje właściwości określonego agenta synchronizacji. |
> | Działanie | Microsoft.Sql/servers/tdeCertificates/action | Utwórz/zaktualizuj certyfikat TDE |
> | Działanie | Microsoft.Sql/servers/usages/read | Limit przydziału jednostek DTU serwera i bieżące użycie jednostek DTU przez wszystkie bazy danych na serwerze |
> | Działanie | Microsoft.Sql/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę Virtual Network |
> | Działanie | Microsoft.Sql/servers/virtualNetworkRules/read | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft.Sql/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Usuń ocenę luk w zabezpieczeniach dla danego serwera |
> | Działanie | Microsoft.Sql/servers/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na danym serwerze |
> | Działanie | Microsoft.Sql/servers/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danego serwera |
> | Działanie | Microsoft.Sql/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |
> | Działanie | Microsoft.Sql/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów SQL Database firmy Microsoft i umożliwia tworzenie baz danych Microsoft SQL. |
> | Działanie | Microsoft. SQL/virtualClusters/Delete | Usuwa istniejący klaster wirtualny. |
> | Działanie | Microsoft. SQL/virtualClusters/odczyt | Zwróć listę klastrów wirtualnych lub pobiera właściwości dla określonego klastra wirtualnego. |
> | Działanie | Microsoft. SQL/virtualClusters/Write | Aktualizuje Tagi klastra wirtualnego. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. SqlVirtualMachine/Locations/availabilityGroupListenerOperationResults/Read | Pobierz wynik operacji odbiornika grupy dostępności |
> | Działanie | Microsoft. SqlVirtualMachine/Locations/sqlVirtualMachineGroupOperationResults/Read | Pobierz wynik operacji grupy maszyn wirtualnych SQL |
> | Działanie | Microsoft. SqlVirtualMachine/Locations/sqlVirtualMachineOperationResults/Read | Pobierz wynik operacji maszyny wirtualnej SQL |
> | Działanie | Microsoft. SqlVirtualMachine/Operations/Read |  |
> | Działanie | Microsoft. SqlVirtualMachine/Register/Action | Zarejestruj subskrypcję za pomocą dostawcy zasobów Microsoft. SqlVirtualMachine |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Delete | Usuń istniejący odbiornik grupy dostępności |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Read | Pobierz szczegóły odbiornika grupy dostępności SQL w danej grupie maszyn wirtualnych SQL |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Write | Utwórz nowe lub zmiany właściwości istniejącego odbiornika grupy dostępności SQL |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Delete | Usuń istniejącą grupę maszyn wirtualnych SQL |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/odczyt | Szczegóły pobrania grupy maszyn wirtualnych SQL |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/Read | Wyświetlanie listy maszyn wirtualnych SQL według określonej grupy wirtualnych maszyn wirtualnych SQL |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Write | Utwórz nowe lub zmień właściwości istniejącej grupy maszyn wirtualnych SQL |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Delete | Usuń istniejącą maszynę wirtualną SQL |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachines/odczyt | Pobierz szczegóły maszyny wirtualnej SQL |
> | Działanie | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Write | Utwórz nowe lub zmień właściwości istniejącej maszyny wirtualnej SQL |
> | Działanie | Microsoft. SqlVirtualMachine/Unregister/Action | Wyrejestrowywanie subskrypcji z dostawcą zasobów Microsoft. SqlVirtualMachine |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Storage/checknameavailability/Read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft.Storage/locations/checknameavailability/read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft. Storage, że sieć wirtualna lub podsieć jest usuwana |
> | Działanie | Microsoft.Storage/locations/usages/read | Zwraca limit i bieżącą liczbę użycia dla zasobów w określonej subskrypcji |
> | Działanie | Microsoft. Storage/Operations/Read | Sonduje stan operacji asynchronicznej. |
> | Działanie | Microsoft. Storage/Register/Action | Rejestruje subskrypcję dostawcy zasobów magazynu i umożliwia tworzenie kont magazynu. |
> | Działanie | Microsoft.Storage/skus/read | Wyświetla listę jednostek SKU obsługiwanych przez firmę Microsoft. Storage. |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Dodawanie/działanie | Zwraca wynik dodania zawartości obiektu BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie | Zwraca wynik usunięcia obiektu BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/deleteBlobVersion/akcja | Zwraca wynik usunięcia wersji obiektu BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/Blobs/Filter/Action | Zwraca listę obiektów BLOB w ramach konta ze zgodnym filtrem tagów |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/runAsSuperUser/akcja | Zwraca wynik polecenia obiektu BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Tagi/odczyt | Zwraca wynik odczytywania tagów obiektów BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Tagi/zapis | Zwraca wynik zapisywania tagów obiektów BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/zapis | Zwraca wynik zapisania obiektu BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/clearLegalHold/akcja | Wyczyść blokadę prawną kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/usuwanie | Zwraca wynik usunięcia kontenera |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/immutabilityPolicies/Delete | Usuwanie zasad niezmienności kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/rozszerzając/Action | Rozwiń zasady niezmienności kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/Lock/Action | Blokuj zasady niezmienności kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/immutabilityPolicies/Read | Pobierz zasady niezmienności kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/immutabilityPolicies/Write | Umieść zasady niezmienności kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/dzierżawa/akcja | Zwraca wynik dzierżawy kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca kontener |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca listę kontenerów |
> | Działanie | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ustaw blokadę prawną kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik poprawki kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik umieszczenia kontenera obiektów BLOB. |
> | Działanie | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla usługi BLOB Service |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/odczyt | Zwraca statystykę lub właściwości usługi BLOB Service |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/Write | Zwraca wynik właściwości Put usługi BLOB Service |
> | Działanie | Microsoft. Storage/storageAccounts/Delete | Usuwa istniejące konto magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/encryptionScopes/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/encryptionScopes/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Działanie | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Działanie | Microsoft. Storage/storageAccounts/tryb failover/akcja | Klient może kontrolować pracę w trybie failover w przypadku problemów z dostępnością |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/Files/actassuperuser/Action | Uzyskaj uprawnienia administratora pliku |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/usuwanie | Zwraca wynik usunięcia pliku/folderu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/Files/modifypermissions/Action | Zwraca wynik modyfikacji uprawnienia do pliku/folderu. |
> | Akcja dataaction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/zapis | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | Działanie | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Działanie | Microsoft.Storage/storageAccounts/fileServices/read | Pobierz właściwości usługi plików |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/udziały/usuwanie |  |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/udziały/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/udziały/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/udziały/zapis |  |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/Write |  |
> | Działanie | Microsoft. Storage/storageAccounts/listAccountSas/akcja | Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu. |
> | Działanie | Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/listServiceSas/akcja | Zwraca token sygnatury dostępu współdzielonego usługi dla określonego konta magazynu. |
> | Działanie | Microsoft.Storage/storageAccounts/managementPolicies/delete | Usuwanie zasad zarządzania kontami magazynu |
> | Działanie | Microsoft.Storage/storageAccounts/managementPolicies/read | Pobierz Zasady konta zarządzania magazynem |
> | Działanie | Microsoft. Storage/storageAccounts/managementPolicies/Write | Umieść zasady zarządzania kontami magazynu |
> | Działanie | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Delete |  |
> | Działanie | Microsoft. Storage/storageAccounts/objectReplicationPolicies/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Działanie | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Usuwanie prywatnych serwerów proxy połączeń punktu końcowego |
> | Działanie | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/odczyt | Pobierz serwer proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Umieść proxy połączeń prywatnych punktów końcowych |
> | Działanie | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Usuń połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Pobierz połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Umieść połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/akcja | Approve Private Endpoint Connections |
> | Działanie | Microsoft.Storage/storageAccounts/privateLinkResources/read | Pobierz StorageAccount groupids |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/Queues/Delete | Zwraca wynik usunięcia kolejki. |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Add/Action | Zwraca wynik dodania komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Delete | Zwraca wynik usunięcia komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Process/Action | Zwraca wynik przetwarzania komunikatu |
> | Akcja dataaction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Zwraca komunikat |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Write | Zwraca wynik zapisania komunikatu |
> | Działanie | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/Queues/Write | Zwraca wynik zapisania kolejki |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/odczyt | Pobierz usługa kolejki właściwości |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/odczyt | Zwraca właściwości usługi kolejki lub dane statystyczne. |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/Write | Zwraca wynik ustawienia właściwości usługi kolejki |
> | Działanie | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Działanie | Microsoft.Storage/storageAccounts/regeneratekey/action | Generuje ponownie klucze dostępu dla określonego konta magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/restoreBlobRanges/akcja | Przywróć zakresy obiektów BLOB do stanu określonego czasu |
> | Działanie | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Odwołuje wszystkie klucze delegowania użytkowników dla określonego konta magazynu. |
> | Działanie | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Utwórz/zaktualizuj ustawienia diagnostyczne konta magazynu. |
> | Działanie | Microsoft.Storage/storageAccounts/tableServices/read | Pobierz Table service właściwości |
> | Działanie | Microsoft. Storage/storageAccounts/Write | Tworzy konto magazynu z określonymi parametrami lub aktualizuje właściwości lub Tagi albo dodaje niestandardową domenę dla określonego konta magazynu. |
> | Działanie | Microsoft.Storage/usages/read | Zwraca limit i bieżącą liczbę użycia dla zasobów w określonej subskrypcji |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | microsoft.storagesync/locations/checkNameAvailability/action | Sprawdza, czy nazwa usługi synchronizacji magazynu jest prawidłowa i czy nie jest używana. |
> | Działanie | microsoft.storagesync/locations/workflows/operations/read | Pobiera stan operacji asynchronicznej |
> | Działanie | Microsoft. storagesync/Operations/Read | Pobiera listę obsługiwanych operacji |
> | Działanie | Microsoft. storagesync/Register/Action | Rejestruje subskrypcję dostawcy synchronizacji magazynu |
> | Działanie | microsoft.storagesync/storageSyncServices/delete | Usuń wszystkie usługi synchronizacji magazynu |
> | Działanie | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla usług synchronizacji magazynu |
> | Działanie | microsoft.storagesync/storageSyncServices/read | Odczytaj wszystkie usługi synchronizacji magazynu |
> | Działanie | microsoft.storagesync/storageSyncServices/registeredServers/delete | Usuń wszystkie zarejestrowane serwery |
> | Działanie | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla zarejestrowanego serwera |
> | Działanie | microsoft.storagesync/storageSyncServices/registeredServers/read | Odczytaj wszystkie zarejestrowane serwery |
> | Działanie | microsoft.storagesync/storageSyncServices/registeredServers/write | Utwórz lub zaktualizuj dowolny zarejestrowany serwer |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Usuń wszystkie punkty końcowe chmury |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Pobiera stan asynchronicznej operacji tworzenia kopii zapasowej/przywracania |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Wywołaj tę akcję po utworzeniu kopii zapasowej |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Wywołaj tę akcję po przywróceniu |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Wywołaj tę akcję przed wykonaniem kopii zapasowej |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Wywołaj tę akcję przed przywróceniem |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Odczytaj wszystkie punkty końcowe w chmurze |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/Action | Przywróć puls |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/Action | Wywołaj tę akcję, aby wyzwolić wykrywanie zmian w udziale plików punktu końcowego w chmurze |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Utwórz lub zaktualizuj wszystkie punkty końcowe w chmurze |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/delete | Usuń wszystkie grupy synchronizacji |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla grup synchronizacji |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/read | Odczytaj wszystkie grupy synchronizacji |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Usuń wszystkie punkty końcowe serwera |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla punktów końcowych serwera |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Odczytaj wszystkie punkty końcowe serwera |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Wywołaj tę akcję, aby odwołać pliki do serwera |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Utwórz lub zaktualizuj wszystkie punkty końcowe serwera |
> | Działanie | microsoft.storagesync/storageSyncServices/syncGroups/write | Utwórz lub zaktualizuj wszystkie grupy synchronizacji |
> | Działanie | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Pobiera stan operacji asynchronicznej |
> | Działanie | microsoft.storagesync/storageSyncServices/workflows/operations/read | Pobiera stan operacji asynchronicznej |
> | Działanie | microsoft.storagesync/storageSyncServices/workflows/read | Odczytaj przepływy pracy |
> | Działanie | microsoft.storagesync/storageSyncServices/write | Utwórz lub zaktualizuj wszystkie usługi synchronizacji magazynu |
> | Działanie | Microsoft. storagesync/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy synchronizacji magazynu |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.StorSimple/managers/accessControlRecords/delete | Usuwa rekordy Access Control |
> | Działanie | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/accessControlRecords/read | Wyświetla lub pobiera Access Control rekordy |
> | Działanie | Microsoft. StorSimple/kierownicy/accessControlRecords/Write | Tworzenie lub aktualizowanie rekordów Access Control |
> | Działanie | Microsoft.StorSimple/managers/alerts/read | Wyświetla lub pobiera alerty |
> | Działanie | Microsoft.StorSimple/managers/backups/read | Lists or gets the Backup Set |
> | Działanie | Microsoft. StorSimple/menedżerowie/bandwidthSettings/Usuń | Usuwa istniejące ustawienia przepustowości (tylko seria 8000) |
> | Działanie | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft.StorSimple/managers/bandwidthSettings/read | Wyświetl listę ustawień przepustowości (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/kierownicy/bandwidthSettings/Write | Tworzy nowe lub aktualizuje ustawienia przepustowości (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/menedżerowie/certyfikaty/zapis | Utwórz lub zaktualizuj certyfikaty |
> | Działanie | Microsoft. StorSimple/menedżerowie/certyfikaty/zapis | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Działanie | Microsoft. StorSimple/kierownicy/clearAlerts/akcja | Wyczyść wszystkie alerty skojarzone z menedżerem urządzeń. |
> | Działanie | Microsoft. StorSimple/kierownicy/cloudApplianceConfigurations/odczyt | Wyświetl listę obsługiwanych konfiguracji urządzenia w chmurze |
> | Działanie | Microsoft.StorSimple/managers/configureDevice/action | Konfiguruje urządzenie |
> | Działanie | Microsoft. StorSimple/menedżerowie/usuwanie | Usuwa menedżerów urządzeń |
> | Działanie | Microsoft. StorSimple/menedżerowie/usuwanie | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/alertSettings/read | Wyświetla lub pobiera ustawienia alertu |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/alertSettings/zapis | Utwórz lub zaktualizuj ustawienia alertu |
> | Działanie | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Authorize for Service Encryption Key Rollover of Devices |
> | Działanie | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Take a manual backup to create an on-demand backup of all the volumes protected by the policy. |
> | Działanie | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Deletes an existing Backup Polices (8000 Series Only) |
> | Działanie | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/backupPolicies/read | List the Backup Polices (8000 Series Only) |
> | Działanie | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Deletes an existing Schedules |
> | Działanie | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | List the Schedules |
> | Działanie | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Creates a new or updates Schedules |
> | Działanie | Microsoft.StorSimple/managers/devices/backupPolicies/write | Creates a new or updates Backup Polices (8000 Series Only) |
> | Działanie | Microsoft.StorSimple/managers/devices/backups/delete | Deletes the Backup Set |
> | Działanie | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clone a share or volume using a backup element. |
> | Działanie | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/backups/read | Lists or gets the Backup Set |
> | Działanie | Microsoft.StorSimple/managers/devices/backups/restore/action | Restore all the volumes from a backup set. |
> | Działanie | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Deletes the Backup Schedule Groups |
> | Działanie | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Lists or gets the Backup Schedule Groups |
> | Działanie | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Create or update the Backup Schedule Groups |
> | Działanie | Microsoft.StorSimple/managers/devices/chapSettings/delete | Deletes the Chap Settings |
> | Działanie | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/chapSettings/read | Lists or gets the Chap Settings |
> | Działanie | Microsoft.StorSimple/managers/devices/chapSettings/write | Create or update the Chap Settings |
> | Działanie | Microsoft.StorSimple/managers/devices/deactivate/action | Deactivates a device. |
> | Działanie | Microsoft.StorSimple/managers/devices/delete | Deletes the Devices |
> | Działanie | Microsoft.StorSimple/managers/devices/disks/read | Lists or gets the Disks |
> | Działanie | Microsoft.StorSimple/managers/devices/download/action | Download updates for a device. |
> | Działanie | Microsoft.StorSimple/managers/devices/failover/action | Failover of the device. |
> | Działanie | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/failoverTargets/read | Lists or gets the Failover targets of the devices |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Take backup of an File Server. |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/delete | Deletes the File Servers |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/read | Wyświetla lub pobiera serwery plików |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Deletes the Shares |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Wyświetla lub pobiera udziały |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Create or update the Shares |
> | Działanie | Microsoft.StorSimple/managers/devices/fileservers/write | Create or update the File Servers |
> | Działanie | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Change controller power state of hardware component groups |
> | Działanie | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | List the Hardware Component Groups |
> | Działanie | Microsoft.StorSimple/managers/devices/install/action | Install updates on a device. |
> | Działanie | Microsoft.StorSimple/managers/devices/installUpdates/action | Installs updates on the devices (8000 Series Only). |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Take backup of an iSCSI server. |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Deletes the iSCSI Servers |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Deletes the Disks |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Lists or gets the Disks |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Create or update the Disks |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/read | Wyświetla lub pobiera serwery iSCSI |
> | Działanie | Microsoft.StorSimple/managers/devices/iscsiservers/write | Create or update the iSCSI Servers |
> | Działanie | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Cancel a running job |
> | Działanie | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/jobs/read | Wyświetla lub pobiera zadania |
> | Działanie | Microsoft.StorSimple/managers/devices/listFailoverSets/action | List the failover sets for an existing device (8000 Series Only). |
> | Działanie | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | List failover targets of the devices (8000 Series Only). |
> | Działanie | Microsoft.StorSimple/managers/devices/metrics/read | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Confirms a successful migration and commit it. |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | List the Confirm Migration Status |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Fetch the confirm status of migration. |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Fetch the status for the migration estimation job. |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Fetch the status for the migration. |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Import source configurations for migration |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | List the Migration Estimate |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | List the Migration Status |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Start migration using source configurations |
> | Działanie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Rozpocznij zadanie, aby oszacować czas trwania procesu migracji. |
> | Działanie | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/networkSettings/read | Wyświetla lub pobiera ustawienia sieci |
> | Działanie | Microsoft.StorSimple/managers/devices/networkSettings/write | Tworzy nowe lub aktualizuje ustawienia sieci |
> | Działanie | Microsoft.StorSimple/managers/devices/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Wyświetl publiczny klucz szyfrowania Menedżera urządzeń |
> | Działanie | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Opublikuj pakiet pomocy technicznej dla istniejącego urządzenia. Pakiet pomocy technicznej StorSimple to łatwy w użyciu mechanizm, który gromadzi wszystkie odpowiednie dzienniki, aby ułatwić pomoc techniczna firmy Microsoft Rozwiązywanie problemów z urządzeniami StorSimple. |
> | Działanie | Microsoft.StorSimple/managers/devices/read | Wyświetla lub pobiera urządzenia |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/scanForUpdates/akcja | Skanuj w poszukiwaniu aktualizacji na urządzeniu. |
> | Działanie | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/securitySettings/read | Wyświetl listę ustawień zabezpieczeń |
> | Działanie | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Zsynchronizuj certyfikat zarządzania zdalnego dla urządzenia. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/aktualizacja/akcja | Zaktualizuj ustawienia zabezpieczeń. |
> | Działanie | Microsoft.StorSimple/managers/devices/securitySettings/write | Tworzy nowe lub aktualizuje ustawienia zabezpieczeń |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/sendTestAlertEmail/akcja | Wyślij wiadomość e-mail z alertem testowym do skonfigurowanych adresatów wiadomości e-mail. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Działanie | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft.StorSimple/managers/devices/timeSettings/read | Wyświetla lub pobiera ustawienia czasu |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/timeSettings/zapis | Tworzy nowe lub zaktualizowane ustawienia czasu |
> | Działanie | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/updateSummary/odczyt | Wyświetla lub pobiera podsumowanie aktualizacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/Usuń | Usuwa istniejące kontenery woluminów (tylko seria 8000) |
> | Działanie | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Wyświetl metryki |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/metricsDefinitions/odczyt | Wyświetl listę definicji metryk |
> | Działanie | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/odczyt | Utwórz listę kontenerów woluminów (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/usuwanie | Usuwa istniejące woluminy |
> | Działanie | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Wyświetl metryki |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/metricsDefinitions/odczyt | Wyświetl listę definicji metryk |
> | Działanie | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/odczyt | Wyświetl listę woluminów |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/zapis | Tworzy nowe lub aktualizuje woluminy |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/volumeContainers/zapis | Tworzy nowe lub zaktualizowane kontenery woluminów (tylko seria 8000) |
> | Działanie | Microsoft.StorSimple/managers/devices/volumes/read | Wyświetl listę woluminów |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/zapis | Utwórz lub zaktualizuj urządzenia |
> | Działanie | Microsoft.StorSimple/managers/encryptionSettings/read | Wyświetla lub pobiera ustawienia szyfrowania |
> | Działanie | Microsoft. StorSimple/menedżerowie/extendedInformation/Usuń | Usuwa informacje o rozszerzonym magazynie |
> | Działanie | Microsoft. StorSimple/menedżerowie/extendedInformation/Usuń | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft. StorSimple/kierownicy/extendedInformation/odczyt | Wyświetla lub pobiera rozszerzone informacje o magazynie |
> | Działanie | Microsoft. StorSimple/kierownicy/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft. StorSimple/kierownicy/extendedInformation/Write | Utwórz lub zaktualizuj informacje o rozszerzonym magazynie |
> | Działanie | Microsoft. StorSimple/kierownicy/extendedInformation/Write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft. StorSimple/menedżerowie/funkcje/odczyt | Wyświetl listę funkcji |
> | Działanie | Microsoft.StorSimple/managers/fileservers/read | Wyświetla lub pobiera serwery plików |
> | Działanie | Microsoft.StorSimple/managers/getEncryptionKey/action | Pobierz klucz szyfrowania dla Menedżera urządzeń. |
> | Działanie | Microsoft. StorSimple/kierownicy/iscsiservers/odczyt | Wyświetla lub pobiera serwery iSCSI |
> | Działanie | Microsoft.StorSimple/managers/jobs/read | Wyświetla lub pobiera zadania |
> | Działanie | Microsoft.StorSimple/managers/listActivationKey/action | Pobiera klucz aktywacji Menedżer urządzeń StorSimple. |
> | Działanie | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Wyświetl listę publicznych kluczy szyfrowania StorSimple Menedżer urządzeń. |
> | Działanie | Microsoft.StorSimple/managers/metrics/read | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft.StorSimple/managers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrowanie z wersji klasycznej do Menedżer zasobów |
> | Działanie | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Wyświetl listę konfiguracji źródła migracji (tylko seria 8000) |
> | Działanie | Microsoft.StorSimple/managers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/kierownicy/provisionCloudAppliance/akcja | Utwórz nowe urządzenie w chmurze. |
> | Działanie | Microsoft.StorSimple/managers/read | Wyświetla lub pobiera menedżerów urządzeń |
> | Działanie | Microsoft.StorSimple/Managers/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft.StorSimple/managers/regenerateActivationKey/action | Wygeneruj ponownie klucz aktywacji dla istniejących Menedżer urządzeń StorSimple. |
> | Działanie | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Usuwa poświadczenia konta magazynu |
> | Działanie | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/storageAccountCredentials/read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Działanie | Microsoft.StorSimple/managers/storageAccountCredentials/write | Utwórz lub zaktualizuj poświadczenia konta magazynu |
> | Działanie | Microsoft. StorSimple/menedżerowie/storageDomains/Usuń | Usuwa domeny magazynu |
> | Działanie | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft.StorSimple/managers/storageDomains/read | Wyświetla lub pobiera domeny magazynu |
> | Działanie | Microsoft. StorSimple/kierownicy/storageDomains/Write | Utwórz lub zaktualizuj domeny magazynu |
> | Działanie | Microsoft. StorSimple/menedżerowie/zapis | Utwórz lub zaktualizuj menedżerów urządzeń |
> | Działanie | Microsoft. StorSimple/menedżerowie/zapis | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft.StorSimple/operations/read | Wyświetla lub pobiera operacje |
> | Działanie | Microsoft. StorSimple/Register/Action | Zarejestruj dostawcę Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.StreamAnalytics/locations/quotas/Read | Odczytaj Stream Analytics przydziału subskrypcji |
> | Działanie | Microsoft. StreamAnalytics/Operations/Read | Odczyt Stream Analytics operacji |
> | Działanie | Microsoft. StreamAnalytics/Register/Action | Zarejestruj subskrypcję za pomocą dostawcy zasobów Stream Analytics |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/Delete | Usuwanie zadania Stream Analytics |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Usuwanie funkcji zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Functions/operationresults/Read | Read operation results for Stream Analytics Job Function |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Read Stream Analytics Job Function |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Retrieve Default Definition of a Stream Analytics Job Function |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Test Stream Analytics Job Function |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Write Stream Analytics Job Function |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Delete Stream Analytics Job Input |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Read operation results for Stream Analytics Job Input |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Read Stream Analytics Job Input |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Sample Stream Analytics Job Input |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Test Stream Analytics Job Input |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Write Stream Analytics Job Input |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Read Metric Definitions |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Read operation results for Stream Analytics Job |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Delete Stream Analytics Job Output |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Read operation results for Stream Analytics Job Output |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Read Stream Analytics Job Output |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Test Stream Analytics Job Output |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Write Stream Analytics Job Output |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Read diagnostic setting. |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Write diagnostic setting. |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for streamingjobs |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for streamingjobs |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/PublishEdgePackage/action | Publish edge package for Stream Analytics Job |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/Read | Read Stream Analytics Job |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/Scale/action | Scale Stream Analytics Job |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/Start/action | Uruchamianie zadania usługi Stream Analytics |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stop Stream Analytics Job |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Delete Stream Analytics Job Transformation |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Read Stream Analytics Job Transformation |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Write Stream Analytics Job Transformation |
> | Działanie | Microsoft.StreamAnalytics/streamingjobs/Write | Write Stream Analytics Job |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Subscription/cancel/action | Cancels the Subscription |
> | Działanie | Microsoft.Subscription/CreateSubscription/action | Tworzenie subskrypcji platformy Azure |
> | Działanie | Microsoft.Subscription/register/action | Registers Subscription with Microsoft.Subscription resource provider |
> | Działanie | Microsoft.Subscription/rename/action | Renames the subscription |
> | Działanie | Microsoft.Subscription/SubscriptionDefinitions/read | Get an Azure subscription definition within a management group. |
> | Działanie | Microsoft.Subscription/SubscriptionDefinitions/write | Create an Azure subscription definition |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Support/register/action | Przeprowadza rejestrację u dostawcy zasobów pomocy technicznej |
> | Działanie | Microsoft.Support/supportTickets/read | Pobiera szczegóły biletu pomocy technicznej (w tym stan, ważność, szczegóły dotyczące kontaktu i komunikacji) lub pobiera listę biletów pomocy technicznej dla różnych subskrypcji. |
> | Działanie | Microsoft.Support/supportTickets/write | Tworzy lub aktualizuje bilet pomocy technicznej. Można utworzyć bilet pomocy technicznej dotyczący problemów technicznych, rozliczeń, limitów przydziału lub problemów związanych z zarządzaniem subskrypcją. Można także zaktualizować ważność, szczegóły dotyczące kontaktu i komunikacji dla istniejących biletów pomocy technicznej. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Deletes the access policy. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Get the properties of an access policy. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Creates a new access policy for an environment, or updates an existing access policy. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/delete | Deletes the environment. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Deletes the event source. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/eventsources/read | Get the properties of an event source. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/eventsources/write | Creates a new event source for an environment, or updates an existing event source. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/read | Get the properties of an environment. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Deletes the reference data set. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Get the properties of a reference data set. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Creates a new reference data set for an environment, or updates an existing reference data set. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/status/read | Get the status of the environment, state of its associated operations like ingress. |
> | Działanie | Microsoft.TimeSeriesInsights/environments/write | Creates a new environment, or updates an existing environment. |
> | Działanie | Microsoft.TimeSeriesInsights/register/action | Registers the subscription for the Time Series Insights resource provider and enables the creation of Time Series Insights environments. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.VisualStudio/Account/Delete | Usuń konto |
> | Działanie | Microsoft.VisualStudio/Account/Extension/Read | Read Account/Extension |
> | Działanie | Microsoft.VisualStudio/Account/Project/Read | Read Account/Project |
> | Działanie | Microsoft.VisualStudio/Account/Project/Write | Set Account/Project |
> | Działanie | Microsoft.VisualStudio/Account/Read | Read Account |
> | Działanie | Microsoft.VisualStudio/Account/Write | Set Account |
> | Działanie | Microsoft.VisualStudio/Extension/Delete | Delete Extension |
> | Działanie | Microsoft.VisualStudio/Extension/Read | Read Extension |
> | Działanie | Microsoft.VisualStudio/Extension/Write | Set Extension |
> | Działanie | Microsoft.VisualStudio/Project/Delete | Delete Project |
> | Działanie | Microsoft.VisualStudio/Project/Read | Read Project |
> | Działanie | Microsoft.VisualStudio/Project/Write | Set Project |
> | Działanie | Microsoft.VisualStudio/Register/Action | Register Azure Subscription with Microsoft.VisualStudio provider |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | microsoft.web/apimanagementaccounts/apiacls/read | Get Api Management Accounts Apiacls. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/apiacls/Delete | Usuwanie interfejsów API kont usługi API Management Apiacls. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/apiacls/odczyt | Pobierz interfejsy API kont usługi API Management Apiacls. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/apiacls/Write | Aktualizowanie interfejsów API kont usługi API Management Apiacls. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/connectionacls/odczyt | Pobierz interfejsy API kont usługi API Management Connectionacls. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/Connections/confirmconsentcode/Action | Potwierdzanie połączeń interfejsów API kont usługi Management API kodu zgody. |
> | Działanie | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacls/Delete | Usuwanie interfejsów API kont usługi API Management połączeń Connectionacls. |
> | Działanie | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacls/Read | Pobierz interfejsy API kont usługi API Management Connectionacls. |
> | Działanie | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacls/Write | Aktualizowanie interfejsów API kont usługi API Management połączenia Connectionacls. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia/usuwanie | Usuwanie połączeń interfejsów API kont usługi API Management. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/Connections/getconsentlinks/Action | Uzyskaj linki do wyrażania zgody dla połączeń interfejsów API dla kont usługi API Management. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/Connections/listconnectionkeys/Action | Wyświetlanie listy kluczy połączenia interfejsy API kont usługi Management. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/Connections/listsecrets/Action | Wyświetlanie listy Secret usługi API accounts API Connections. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia/odczyt | Pobierz połączenia interfejsów API kont usługi API Management. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia/zapis | Aktualizowanie połączeń interfejsów API kont usługi API Management. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/usuwanie | Usuń interfejsy API kont usługi API Management. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/localizeddefinitions/Delete | Usuwanie usługi API Management konta interfejsy API zlokalizowane definicje. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/localizeddefinitions/odczyt | Pobierz zlokalizowane definicje interfejsów API kont usługi API Management. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/localizeddefinitions/Write | Aktualizowanie interfejsów API kont usługi API Management zlokalizowane definicje. |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/odczyt | Pobierz interfejsy API konta zarządzania |
> | Działanie | Microsoft. Web/apimanagementaccounts/interfejsy API/zapis | Aktualizowanie interfejsów API kont usługi API Management. |
> | Działanie | microsoft.web/apimanagementaccounts/connectionacls/read | Pobierz Connectionacls kont usługi API Management. |
> | Działanie | Microsoft. Web/availablestacks/odczyt | Pobierz dostępne stosy. |
> | Działanie | Microsoft. Web/certyfikaty/usuwanie | Usuń istniejący certyfikat. |
> | Działanie | Microsoft. Web/Certificates/Read | Pobierz listę certyfikatów. |
> | Działanie | Microsoft. Web/Certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejący. |
> | Działanie | Microsoft. Web/checknameavailability/odczyt | Sprawdź, czy nazwa zasobu jest dostępna. |
> | Działanie | Microsoft. Web/classicmobileservices/odczyt | Pobierz klasyczne Mobile Services. |
> | Działanie | Microsoft. Web/connectionGateways/Delete | Usuwa bramę połączenia. |
> | Działanie | Microsoft. Web/connectionGateways/Join/Action | Sprzęga bramę połączenia. |
> | Działanie | Microsoft.Web/connectionGateways/ListStatus/Action | Wyświetla stan bramy połączenia. |
> | Działanie | Microsoft. Web/connectionGateways/Przenieś/akcja | Przenosi bramę połączenia. |
> | Działanie | Microsoft. Web/connectionGateways/odczyt | Pobierz listę bram połączeń. |
> | Działanie | Microsoft. Web/connectionGateways/Write | Tworzy lub aktualizuje bramę połączenia. |
> | Działanie | Microsoft. Web/Connections/confirmconsentcode/Action | Potwierdź kod zgody na połączenia. |
> | Działanie | Microsoft. Web/Connections/Delete | Usuwa połączenie. |
> | Działanie | Microsoft. Web/Connections/Join/Action | Sprzęga połączenie. |
> | Działanie | Microsoft. Web/Connections/listconsentlinks/Action | Wyświetl listę linków zgody dla połączeń. |
> | Działanie | Microsoft. Web/Connections/Move/Action | Przenosi połączenie. |
> | Działanie | Microsoft. Web/Connections/Read | Pobierz listę połączeń. |
> | Działanie | Microsoft. Web/Connections/Write | Tworzy lub aktualizuje połączenie. |
> | Działanie | Microsoft. Web/customApis/Delete | Usuwa niestandardowy interfejs API. |
> | Działanie | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Wyodrębnia definicję interfejsu API z WSDL. |
> | Działanie | Microsoft. Web/customApis/Join/Action | Sprzęga niestandardowy interfejs API. |
> | Działanie | Microsoft.Web/customApis/listWsdlInterfaces/Action | Wyświetla listę interfejsów WSDL dla niestandardowego interfejsu API. |
> | Działanie | Microsoft. Web/customApis/Przenieś/akcja | Przenosi niestandardowy interfejs API. |
> | Działanie | Microsoft. Web/customApis/odczyt | Pobierz listę niestandardowych interfejsów API. |
> | Działanie | Microsoft. Web/customApis/Write | Tworzy lub aktualizuje niestandardowy interfejs API. |
> | Działanie | Microsoft. Web/deletedSites/odczyt | Pobierz właściwości usuniętej aplikacji sieci Web |
> | Działanie | microsoft.web/deploymentlocations/read | Pobierz lokalizacje wdrożenia. |
> | Działanie | Microsoft. Web/georegions/Read | Pobierz listę regionów geograficznych. |
> | Działanie | Microsoft. Web/hostingenvironments/pojemności/odczyt | Uzyskaj możliwości środowiska hostingu. |
> | Działanie | Microsoft. Web/hostingEnvironments/Delete | Usuwanie App Service Environment |
> | Działanie | Microsoft. Web/hostingenvironments/detektory/odczyt | Pobierz detektory środowisk hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/Diagnostyka/odczyt | Uzyskaj diagnostykę środowisk hostingu. |
> | Działanie | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Pobierz punkty końcowe sieci wszystkich zależności przychodzących. |
> | Działanie | Microsoft. Web/hostingEnvironments/Join/Action | Sprzęga App Service Environment |
> | Działanie | Microsoft. Web/hostingenvironments/metricdefinitions/odczyt | Pobierz definicje metryk środowisk hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/multirolepools/metricdefinitions/odczyt | Pobierz definicje metryk pul wielu ról środowiska hostingu. |
> | Działanie | microsoft.web/hostingenvironments/multirolepools/metrics/read | Uzyskaj metryki pul wielu ról. |
> | Działanie | Microsoft. Web/hostingEnvironments/multiRolePools/odczyt | Pobierz właściwości puli frontonu w App Service Environment |
> | Działanie | Microsoft. Web/hostingenvironments/multirolepools/SKU/odczyt | Uzyskaj środowiska hostingu pule wieloról jednostek SKU. |
> | Działanie | Microsoft. Web/hostingenvironments/multirolepools/Usages/Read | Uzyskaj środowiska hostingu użycia pul wielorolach. |
> | Działanie | Microsoft. Web/hostingEnvironments/multiRolePools/zapis | Utwórz nową pulę frontonu w App Service Environment lub zaktualizuj istniejącą |
> | Działanie | Microsoft. Web/hostingenvironments/Operations/Read | Pobierz operacje środowiska hostingu. |
> | Działanie | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Pobierz punkty końcowe sieci wszystkich zależności wychodzących. |
> | Działanie | Microsoft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/akcja | Approve Private Endpoint Connections |
> | Działanie | Microsoft. Web/hostingEnvironments/odczyt | Pobierz właściwości App Service Environment |
> | Działanie | Microsoft. Web/hostingEnvironments/ponowny rozruch/akcja | Uruchom ponownie wszystkie maszyny w App Service Environment |
> | Działanie | Microsoft. Web/hostingenvironments/Resume/akcja | Wznów środowiska hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/dopuszczalna/odczyt | Pobierz środowiska hostingu App Service plany. |
> | Działanie | Microsoft. Web/hostingenvironments/sites/odczyt | Pobierz środowiska hostingu Web Apps. |
> | Działanie | Microsoft. Web/hostingenvironments/Suspend/akcja | Wstrzymywanie środowisk hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/Usages/Read | Uzyskaj użycie środowisk hostingu. |
> | Działanie | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Pobierz środowiska hostingu Workerpools definicje metryki. |
> | Działanie | microsoft.web/hostingenvironments/workerpools/metrics/read | Uzyskaj środowiska hostingu Workerpools metryki. |
> | Działanie | Microsoft.Web/hostingEnvironments/workerPools/Read | Pobierz właściwości puli procesów roboczych w App Service Environment |
> | Działanie | microsoft.web/hostingenvironments/workerpools/skus/read | Uzyskaj środowiska hostingu Workerpools jednostki SKU. |
> | Działanie | microsoft.web/hostingenvironments/workerpools/usages/read | Uzyskaj środowiska hostingu Workerpools użycia. |
> | Działanie | Microsoft.Web/hostingEnvironments/workerPools/Write | Utwórz nową pulę procesów roboczych w App Service Environment lub zaktualizuj istniejącą |
> | Działanie | Microsoft. Web/hostingEnvironments/Write | Utwórz nowy App Service Environment lub zaktualizuj istniejący |
> | Działanie | Microsoft. Web/ishostingenvironmentnameavailable/odczyt | Pobierz, jeśli nazwa środowiska hostingu jest dostępna. |
> | Działanie | Microsoft. Web/ishostnameavailable/odczyt | Sprawdź, czy nazwa hosta jest dostępna. |
> | Działanie | Microsoft. Web/isusernameavailable/odczyt | Sprawdź, czy nazwa użytkownika jest dostępna. |
> | Działanie | Microsoft. Web/listSitesAssignedToHostName/odczyt | Pobieranie nazw lokacji przypisanych do nazwy hosta. |
> | Działanie | Microsoft. Web/Locations/apioperations/Read | Pobieranie lokalizacji operacji interfejsu API. |
> | Działanie | microsoft.web/locations/connectiongatewayinstallations/read | Pobierz lokalizacje instalacji bramy połączeń. |
> | Działanie | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Powiadomienie o usunięciu sieci wirtualnej lub podsieci dla lokalizacji. |
> | Działanie | microsoft.web/locations/extractapidefinitionfromwsdl/action | Wyodrębnij definicję interfejsu API z WSDL dla lokalizacji. |
> | Działanie | Microsoft. Web/Locations/listwsdlinterfaces/Action | Wyświetl listę interfejsów WSDL dla lokalizacji. |
> | Działanie | Microsoft. Web/Locations/managedapis/apioperations/Read | Pobierz lokalizacje zarządzane interfejsu API. |
> | Działanie | Microsoft. Web/Locations/managedapis/Join/Action | Sprzęga zarządzany interfejs API. |
> | Działanie | Microsoft. Web/Locations/managedapis/Read | Pobierz zarządzane lokalizacje interfejsy API. |
> | Działanie | Microsoft. Web/Locations/operationResults/Read | Pobierz operacje. |
> | Działanie | Microsoft. Web/Locations/Operations/Read | Pobierz operacje. |
> | Działanie | Microsoft. Web/Operations/Read | Pobierz operacje. |
> | Działanie | Microsoft. Web/publishingusers/odczyt | Pobierz użytkowników publikacji. |
> | Działanie | Microsoft. Web/publishingusers/Write | Aktualizowanie użytkowników publikowanych. |
> | Działanie | Microsoft. Web/zalecenia/odczyt | Pobierz listę zaleceń dotyczących subskrypcji. |
> | Działanie | Microsoft. Web/Register/Action | Zarejestruj dostawcę zasobów Microsoft. Web dla subskrypcji. |
> | Działanie | Microsoft. Web/resourcehealthmetadata/odczyt | Pobierz metadane Resource Health. |
> | Działanie | Microsoft. Web/dopuszczalna/możliwości/odczyt | Pobierz możliwości planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/Delete | Usuń istniejący plan App Service |
> | Działanie | Microsoft. Web/dopuszczalna/eventGridFilters/Delete | Usuń filtr Event Grid w farmie serwerów. |
> | Działanie | Microsoft. Web/dopuszczalna/eventGridFilters/odczyt | Pobierz filtr Event Grid w farmie serwerów. |
> | Działanie | Microsoft. Web/dopuszczalna/eventGridFilters/zapis | Umieść filtr Event Grid w farmie serwerów. |
> | Działanie | microsoft.web/serverfarms/firstpartyapps/settings/delete | Usuń ustawienia App Service plany aplikacji pierwszej strony. |
> | Działanie | microsoft.web/serverfarms/firstpartyapps/settings/read | Pobierz App Service plany. |
> | Działanie | microsoft.web/serverfarms/firstpartyapps/settings/write | Aktualizacja App Service planuje ustawienia aplikacji innych firm. |
> | Działanie | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/Relay/usuwanie | Usuwanie App Service planów przestrzenie nazw połączeń hybrydowych przekaźników. |
> | Działanie | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/Relay/odczyt | Pobierz App Service plany przestrzeni nazw połączenia hybrydowego. |
> | Działanie | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/przekaźniki/witryny/odczyt | Pobierz App Service plany przestrzeń nazw połączenia hybrydowego przekazuje Web Apps. |
> | Działanie | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Pobierz limity planu połączeń hybrydowych planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/hybridconnectionrelays/odczyt | Pobierz App Service plany połączeń hybrydowych. |
> | Działanie | microsoft.web/serverfarms/metricdefinitions/read | Pobierz definicje metryk planów App Service. |
> | Działanie | microsoft.web/serverfarms/metrics/read | Pobierz metryki planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/operationresults/odczyt | Pobierz wyniki operacji planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/odczyt | Pobierz właściwości planu App Service |
> | Działanie | Microsoft. Web/dopuszczalna/restartSites/akcja | Uruchom ponownie wszystkie Web Apps w planie App Service |
> | Działanie | Microsoft. Web/dopuszczalna/sites/odczyt | Pobierz Web Apps planów App Service. |
> | Działanie | microsoft.web/serverfarms/skus/read | Pobierz jednostki SKU planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/Usages/Read | Pobierz użycia planów App Service. |
> | Działanie | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | App Service plany Virtual Network połączeń z bramą. |
> | Działanie | microsoft.web/serverfarms/virtualnetworkconnections/read | Pobierz App Service plany Virtual Network połączeń. |
> | Działanie | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Usuń App Service plany Virtual Network połączeń. |
> | Działanie | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Pobierz App Service plany Virtual Network połączeń. |
> | Działanie | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Aktualizowanie App Service planów Virtual Network połączeń. |
> | Działanie | Microsoft. Web/dopuszczalna/procesy robocze/ponowny rozruch/akcja | Uruchom ponownie App Service plany pracowników. |
> | Działanie | Microsoft. Web/dopuszczalna/Write | Utwórz nowy plan App Service lub zaktualizuj istniejący |
> | Działanie | Microsoft. Web/Sites/analyzecustomhostname/odczyt | Analizuj niestandardową nazwę hosta. |
> | Działanie | Microsoft. Web/Sites/applySlotConfig/akcja | Zastosuj konfigurację miejsca aplikacji sieci Web z miejsca docelowego do bieżącej aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/kopia zapasowa/akcja | Utwórz nową kopię zapasową aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/kopia zapasowa/odczyt | Pobierz Web Apps kopii zapasowej. |
> | Działanie | Microsoft. Web/Sites/kopia zapasowa/zapis | Aktualizacja Web Apps kopii zapasowej. |
> | Działanie | Microsoft. Web/Sites/kopie zapasowe/akcja | Wykrywa istniejącą kopię zapasową aplikacji, która może zostać przywrócona z obiektu BLOB w usłudze Azure Storage. |
> | Działanie | Microsoft. Web/Sites/kopie zapasowe/usuwanie | Usuń kopie zapasowe Web Apps. |
> | Działanie | Microsoft. Web/Sites/kopie zapasowe/lista/akcja | Utwórz listę Web Apps kopii zapasowych. |
> | Działanie | Microsoft. Web/Sites/kopie zapasowe/odczyt | Pobierz właściwości kopii zapasowej aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/kopie zapasowe/przywracanie/akcja | Przywracanie Web Apps kopii zapasowych. |
> | Działanie | Microsoft. Web/Sites/kopie zapasowe/zapis | Aktualizacja Web Apps kopii zapasowych. |
> | Działanie | Microsoft. Web/Sites/config/Delete | Usuń konfigurację Web Apps. |
> | Działanie | Microsoft. Web/Sites/config/lista/akcja | Wyświetlanie ustawień poufnych zabezpieczeń aplikacji sieci Web, takich jak poświadczenia publikowania, ustawienia aplikacji i parametry połączenia |
> | Działanie | Microsoft. Web/Sites/config/odczyt | Pobierz ustawienia konfiguracji aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/konfiguracja/migawki/odczyt | Pobierz migawki konfiguracji Web Apps. |
> | Działanie | Microsoft. Web/Sites/config/Write | Zaktualizuj ustawienia konfiguracji aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/containerlogs/akcja | Pobierz spakowane dzienniki kontenerów dla aplikacji internetowej. |
> | Działanie | Microsoft. Web/Sites/containerlogs/Pobierz/akcja | Pobierz Web Apps dzienniki kontenerów. |
> | Działanie | Microsoft. Web/Sites/continuouswebjobs/Delete | Usuwanie Web Apps ciągłych zadań w sieci Web. |
> | Działanie | Microsoft. Web/Sites/continuouswebjobs/odczyt | Uzyskaj Web Apps ciągłe zadania w sieci Web. |
> | Działanie | Microsoft. Web/Sites/continuouswebjobs/Start/akcja | Zacznij Web Apps ciągłe zadania w sieci Web. |
> | Działanie | Microsoft. Web/Sites/continuouswebjobs/akcja | Zatrzymaj Web Apps ciągłe zadania w sieci Web. |
> | Działanie | Microsoft. Web/Sites/Delete | Usuwanie istniejącej aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Deployments/Delete | Usuń wdrożenia Web Apps. |
> | Działanie | Microsoft. Web/Sites/Deployments/log/Read | Pobierz dziennik wdrożeń Web Apps. |
> | Działanie | Microsoft. Web/Sites/Deployments/Read | Pobierz Web Apps wdrożenia. |
> | Działanie | Microsoft. Web/Sites/Deployments/Write | Aktualizacje Web Apps wdrożenia. |
> | Działanie | Microsoft. Web/Sites/detektory/odczyt | Pobierz Web Apps wykrywaczy. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/analizy/wykonanie/akcja | Uruchom analizę diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/analizy/odczyt | Pobierz analizę diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/aspnetcore/odczyt | Uzyskaj Web Apps diagnostykę dla ASP.NET Core aplikacji. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/autozabliźnione/odczyt | Pobierz Web Apps autozabliźnione diagnostyki. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/wdrożenie/odczyt | Pobierz wdrożenie diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostics/Deployments/Read | Pobierz Web Apps wdrożenia diagnostyki. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/detektory/wykonaj/akcję | Uruchom narzędzie wykrywania diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/detektory/odczyt | Pobierz detektor diagnostyki Web Apps. |
> | Działanie | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Pobierz Nieudane żądania diagnostyki Web Apps na identyfikator URI. |
> | Działanie | microsoft.web/sites/diagnostics/frebanalysis/read | Web Apps Pobierz analizę FREB diagnostyki. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/loganalyzer/odczyt | Pobierz Web Apps Analizator dziennika diagnostyki. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/odczyt | Pobierz kategorie diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/runtimeavailability/odczyt | Pobierz dostępność Web Apps diagnostyki. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/servicehealth/odczyt | Pobierz Service Health Web Apps diagnostyki. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/sitecpuanalysis/odczyt | Pobierz analizę procesora CPU lokacji diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/sitecrashes/odczyt | Pobierz awarie witryny diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/sitelatency/odczyt | Pobierz opóźnienie witryny diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/sitememoryanalysis/odczyt | Pobierz analizę pamięci lokacji diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/siterestartsettingupdate/odczyt | Pobierz aktualizację ustawienia ponownego uruchomienia witryny diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/siterestartuserinitiated/odczyt | Pobierz zainicjowane przez użytkownika ponowne uruchomienie witryny diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/siteswap/odczyt | Pobierz Web Apps wymiany lokacji diagnostyki. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/ThreadCount/odczyt | Pobierz Web Apps liczbę wątków diagnostycznych. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/workeravailability/odczyt | Pobierz Web Apps diagnostyki Workeravailability. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/workerprocessrecycle/odczyt | Pobierz procedurę odtwarzania procesu roboczego diagnostyki Web Apps. |
> | Działanie | Microsoft. Web/Sites/domainownershipidentifiers/odczyt | Pobierz identyfikatory własności domeny Web Apps. |
> | Działanie | Microsoft. Web/Sites/domainownershipidentifiers/Write | Aktualizowanie identyfikatorów własności domeny Web Apps. |
> | Działanie | Microsoft. Web/Sites/eventGridFilters/Delete | Usuń filtr Event Grid w aplikacji sieci Web. |
> | Działanie | Microsoft. Web/Sites/eventGridFilters/odczyt | Pobierz filtr Event Grid w aplikacji sieci Web. |
> | Działanie | Microsoft. Web/Sites/eventGridFilters/Write | Umieść filtr Event Grid w aplikacji sieci Web. |
> | Działanie | Microsoft. Web/Sites/rozszerzenia/usuwanie | Delete Web Apps Site Extensions. |
> | Działanie | Microsoft. Web/Sites/rozszerzenia/odczyt | Get Web Apps Site Extensions. |
> | Działanie | Microsoft. Web/Sites/rozszerzenia/zapis | Update Web Apps Site Extensions. |
> | Działanie | Microsoft. Web/Sites/Functions/Action | Web Apps funkcji. |
> | Działanie | Microsoft. Web/Sites/Functions/Delete | Usuń funkcje Web Apps. |
> | Działanie | Microsoft. Web/Sites/funkcje/klucze/usuwanie | Usuń klucze funkcji. |
> | Działanie | Microsoft. Web/Sites/Functions/Keys/Write | Zaktualizuj klucze funkcji. |
> | Działanie | Microsoft. Web/Sites/Functions/ListKeys/Action | List Function keys. |
> | Działanie | microsoft.web/sites/functions/listsecrets/action | List Function secrets. |
> | Działanie | microsoft.web/sites/functions/masterkey/read | Get Web Apps Functions Masterkey. |
> | Działanie | microsoft.web/sites/functions/properties/read | Read Web Apps Functions Properties. |
> | Działanie | microsoft.web/sites/functions/properties/write | Update Web Apps Functions Properties. |
> | Działanie | microsoft.web/sites/functions/read | Get Web Apps Functions. |
> | Działanie | microsoft.web/sites/functions/token/read | Get Web Apps Functions Token. |
> | Działanie | microsoft.web/sites/functions/write | Update Web Apps Functions. |
> | Działanie | microsoft.web/sites/host/functionkeys/delete | Delete Functions Host Function keys. |
> | Działanie | microsoft.web/sites/host/functionkeys/write | Update Functions Host Function keys. |
> | Działanie | microsoft.web/sites/host/listkeys/action | List Functions Host keys. |
> | Działanie | microsoft.web/sites/host/listsyncstatus/action | List Sync Function Triggers Status. |
> | Działanie | microsoft.web/sites/host/properties/read | Read Web Apps Functions Host Properties. |
> | Działanie | microsoft.web/sites/host/sync/action | Wyzwalacze funkcji synchronizacji. |
> | Działanie | microsoft.web/sites/host/systemkeys/delete | Delete Functions Host System keys. |
> | Działanie | microsoft.web/sites/host/systemkeys/write | Update Functions Host System keys. |
> | Działanie | microsoft.web/sites/hostnamebindings/delete | Delete Web Apps Hostname Bindings. |
> | Działanie | microsoft.web/sites/hostnamebindings/read | Get Web Apps Hostname Bindings. |
> | Działanie | microsoft.web/sites/hostnamebindings/write | Update Web Apps Hostname Bindings. |
> | Działanie | microsoft.web/sites/hostruntime/functions/keys/read | Get Web Apps Hostruntime Functions Keys. |
> | Działanie | Microsoft.Web/sites/hostruntime/host/_master/read | Get Function App's master key for admin operations |
> | Działanie | Microsoft.Web/sites/hostruntime/host/action | Perform Function App runtime action like sync triggers, add functions, invoke functions, delete functions etc. |
> | Działanie | microsoft.web/sites/hostruntime/host/read | Get Web Apps Hostruntime Host. |
> | Działanie | microsoft.web/sites/hybridconnection/delete | Delete Web Apps Hybrid Connection. |
> | Działanie | microsoft.web/sites/hybridconnection/read | Get Web Apps Hybrid Connection. |
> | Działanie | microsoft.web/sites/hybridconnection/write | Update Web Apps Hybrid Connection. |
> | Działanie | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Delete Web Apps Hybrid Connection Namespaces Relays. |
> | Działanie | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | List Keys Web Apps Hybrid Connection Namespaces Relays. |
> | Działanie | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Get Web Apps Hybrid Connection Namespaces Relays. |
> | Działanie | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Update Web Apps Hybrid Connection Namespaces Relays. |
> | Działanie | microsoft.web/sites/hybridconnectionrelays/read | Get Web Apps Hybrid Connection Relays. |
> | Działanie | microsoft.web/sites/instances/deployments/delete | Delete Web Apps Instances Deployments. |
> | Działanie | microsoft.web/sites/instances/deployments/read | Get Web Apps Instances Deployments. |
> | Działanie | microsoft.web/sites/instances/extensions/log/read | Get Web Apps Instances Extensions Log. |
> | Działanie | microsoft.web/sites/instances/extensions/processes/read | Get Web Apps Instances Extensions Processes. |
> | Działanie | microsoft.web/sites/instances/extensions/read | Get Web Apps Instances Extensions. |
> | Działanie | microsoft.web/sites/instances/processes/delete | Delete Web Apps Instances Processes. |
> | Działanie | microsoft.web/sites/instances/processes/modules/read | Get Web Apps Instances Processes Modules. |
> | Działanie | microsoft.web/sites/instances/processes/read | Get Web Apps Instances Processes. |
> | Działanie | microsoft.web/sites/instances/processes/threads/read | Get Web Apps Instances Processes Threads. |
> | Działanie | microsoft.web/sites/instances/read | Get Web Apps Instances. |
> | Działanie | microsoft.web/sites/listsyncfunctiontriggerstatus/action | List Sync Function Trigger Status. |
> | Działanie | microsoft.web/sites/metricdefinitions/read | Get Web Apps Metric Definitions. |
> | Działanie | microsoft.web/sites/metrics/read | Get Web Apps Metrics. |
> | Działanie | microsoft.web/sites/metricsdefinitions/read | Get Web Apps Metrics Definitions. |
> | Działanie | microsoft.web/sites/migratemysql/action | Migrate MySql Web Apps. |
> | Działanie | microsoft.web/sites/migratemysql/read | Get Web Apps Migrate MySql. |
> | Działanie | microsoft.web/sites/networktrace/action | Network Trace Web Apps. |
> | Działanie | microsoft.web/sites/networktraces/operationresults/read | Get Web Apps Network Trace Operation Results. |
> | Działanie | microsoft.web/sites/newpassword/action | Newpassword Web Apps. |
> | Działanie | microsoft.web/sites/operationresults/read | Get Web Apps Operation Results. |
> | Działanie | microsoft.web/sites/operations/read | Get Web Apps Operations. |
> | Działanie | microsoft.web/sites/perfcounters/read | Get Web Apps Performance Counters. |
> | Działanie | microsoft.web/sites/premieraddons/delete | Delete Web Apps Premier Addons. |
> | Działanie | microsoft.web/sites/premieraddons/read | Get Web Apps Premier Addons. |
> | Działanie | microsoft.web/sites/premieraddons/write | Update Web Apps Premier Addons. |
> | Działanie | microsoft.web/sites/privateaccess/read | Get data around private site access enablement and authorized Virtual Networks that can access the site. |
> | Działanie | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Approve Private Endpoint Connections |
> | Działanie | microsoft.web/sites/processes/modules/read | Get Web Apps Processes Modules. |
> | Działanie | microsoft.web/sites/processes/read | Get Web Apps Processes. |
> | Działanie | microsoft.web/sites/processes/threads/read | Get Web Apps Processes Threads. |
> | Działanie | microsoft.web/sites/publiccertificates/delete | Delete Web Apps Public Certificates. |
> | Działanie | microsoft.web/sites/publiccertificates/read | Get Web Apps Public Certificates. |
> | Działanie | microsoft.web/sites/publiccertificates/write | Update Web Apps Public Certificates. |
> | Działanie | Microsoft.Web/sites/publish/Action | Publish a Web App |
> | Działanie | Microsoft.Web/sites/publishxml/Action | Get publishing profile xml for a Web App |
> | Działanie | microsoft.web/sites/publishxml/read | Get Web Apps Publishing XML. |
> | Działanie | Microsoft.Web/sites/Read | Get the properties of a Web App |
> | Działanie | microsoft.web/sites/recommendationhistory/read | Get Web Apps Recommendation History. |
> | Działanie | microsoft.web/sites/recommendations/disable/action | Disable Web Apps Recommendations. |
> | Działanie | Microsoft.Web/sites/recommendations/Read | Get the list of recommendations for web app. |
> | Działanie | microsoft.web/sites/recover/action | Recover Web Apps. |
> | Działanie | Microsoft.Web/sites/resetSlotConfig/Action | Reset web app configuration |
> | Działanie | microsoft.web/sites/resourcehealthmetadata/read | Get Web Apps Resource Health Metadata. |
> | Działanie | Microsoft.Web/sites/restart/Action | Restart a Web App |
> | Działanie | microsoft.web/sites/restore/read | Get Web Apps Restore. |
> | Działanie | microsoft.web/sites/restore/write | Restore Web Apps. |
> | Działanie | microsoft.web/sites/restorefrombackupblob/action | Restore Web App From Backup Blob. |
> | Działanie | microsoft.web/sites/restorefromdeletedapp/action | Restore Web Apps From Deleted App. |
> | Działanie | microsoft.web/sites/restoresnapshot/action | Restore Web Apps Snapshots. |
> | Działanie | microsoft.web/sites/siteextensions/delete | Delete Web Apps Site Extensions. |
> | Działanie | microsoft.web/sites/siteextensions/read | Get Web Apps Site Extensions. |
> | Działanie | microsoft.web/sites/siteextensions/write | Update Web Apps Site Extensions. |
> | Działanie | microsoft.web/sites/slots/analyzecustomhostname/read | Get Web Apps Slots Analyze Custom Hostname. |
> | Działanie | Microsoft.Web/sites/slots/applySlotConfig/Action | Apply web app slot configuration from target slot to the current slot. |
> | Działanie | Microsoft.Web/sites/slots/backup/Action | Create new Web App Slot backup. |
> | Działanie | microsoft.web/sites/slots/backup/read | Get Web Apps Slots Backup. |
> | Działanie | microsoft.web/sites/slots/backup/write | Update Web Apps Slots Backup. |
> | Działanie | microsoft.web/sites/slots/backups/action | Discover Web Apps Slots Backups. |
> | Działanie | microsoft.web/sites/slots/backups/delete | Delete Web Apps Slots Backups. |
> | Działanie | microsoft.web/sites/slots/backups/list/action | List Web Apps Slots Backups. |
> | Działanie | Microsoft.Web/sites/slots/backups/Read | Get the properties of a web app slots' backup |
> | Działanie | microsoft.web/sites/slots/backups/restore/action | Restore Web Apps Slots Backups. |
> | Działanie | microsoft.web/sites/slots/config/delete | Delete Web Apps Slots Config. |
> | Działanie | Microsoft.Web/sites/slots/config/list/Action | List Web App Slot's security sensitive settings, such as publishing credentials, app settings and connection strings |
> | Działanie | Microsoft.Web/sites/slots/config/Read | Get Web App Slot's configuration settings |
> | Działanie | Microsoft.Web/sites/slots/config/Write | Update Web App Slot's configuration settings |
> | Działanie | microsoft.web/sites/slots/containerlogs/action | Get Zipped Container Logs for Web App Slot. |
> | Działanie | microsoft.web/sites/slots/containerlogs/download/action | Download Web Apps Slots Container Logs. |
> | Działanie | microsoft.web/sites/slots/continuouswebjobs/delete | Delete Web Apps Slots Continuous Web Jobs. |
> | Działanie | microsoft.web/sites/slots/continuouswebjobs/read | Get Web Apps Slots Continuous Web Jobs. |
> | Działanie | microsoft.web/sites/slots/continuouswebjobs/start/action | Start Web Apps Slots Continuous Web Jobs. |
> | Działanie | microsoft.web/sites/slots/continuouswebjobs/stop/action | Stop Web Apps Slots Continuous Web Jobs. |
> | Działanie | Microsoft.Web/sites/slots/Delete | Delete an existing Web App Slot |
> | Działanie | microsoft.web/sites/slots/deployments/delete | Delete Web Apps Slots Deployments. |
> | Działanie | microsoft.web/sites/slots/deployments/log/read | Get Web Apps Slots Deployments Log. |
> | Działanie | microsoft.web/sites/slots/deployments/read | Get Web Apps Slots Deployments. |
> | Działanie | microsoft.web/sites/slots/deployments/write | Update Web Apps Slots Deployments. |
> | Działanie | microsoft.web/sites/slots/detectors/read | Get Web Apps Slots Detectors. |
> | Działanie | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Run Web Apps Slots Diagnostics Analysis. |
> | Działanie | microsoft.web/sites/slots/diagnostics/analyses/read | Get Web Apps Slots Diagnostics Analysis. |
> | Działanie | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Get Web Apps Slots Diagnostics for ASP.NET Core app. |
> | Działanie | microsoft.web/sites/slots/diagnostics/autoheal/read | Get Web Apps Slots Diagnostics Autoheal. |
> | Działanie | microsoft.web/sites/slots/diagnostics/deployment/read | Get Web Apps Slots Diagnostics Deployment. |
> | Działanie | microsoft.web/sites/slots/diagnostics/deployments/read | Get Web Apps Slots Diagnostics Deployments. |
> | Działanie | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Run Web Apps Slots Diagnostics Detector. |
> | Działanie | microsoft.web/sites/slots/diagnostics/detectors/read | Get Web Apps Slots Diagnostics Detector. |
> | Działanie | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Get Web Apps Slots Diagnostics FREB Analysis. |
> | Działanie | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Get Web Apps Slots Diagnostics Log Analyzer. |
> | Działanie | microsoft.web/sites/slots/diagnostics/read | Get Web Apps Slots Diagnostics. |
> | Działanie | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Get Web Apps Slots Diagnostics Runtime Availability. |
> | Działanie | microsoft.web/sites/slots/diagnostics/servicehealth/read | Get Web Apps Slots Diagnostics Service Health. |
> | Działanie | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Get Web Apps Slots Diagnostics Site CPU Analysis. |
> | Działanie | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Get Web Apps Slots Diagnostics Site Crashes. |
> | Działanie | microsoft.web/sites/slots/diagnostics/sitelatency/read | Get Web Apps Slots Diagnostics Site Latency. |
> | Działanie | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Get Web Apps Slots Diagnostics Site Memory Analysis. |
> | Działanie | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Get Web Apps Slots Diagnostics Site Restart Setting Update. |
> | Działanie | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Get Web Apps Slots Diagnostics Site Restart User Initiated. |
> | Działanie | microsoft.web/sites/slots/diagnostics/siteswap/read | Get Web Apps Slots Diagnostics Site Swap. |
> | Działanie | microsoft.web/sites/slots/diagnostics/threadcount/read | Get Web Apps Slots Diagnostics Thread Count. |
> | Działanie | microsoft.web/sites/slots/diagnostics/workeravailability/read | Get Web Apps Slots Diagnostics Workeravailability. |
> | Działanie | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Get Web Apps Slots Diagnostics Worker Process Recycle. |
> | Działanie | microsoft.web/sites/slots/domainownershipidentifiers/read | Get Web Apps Slots Domain Ownership Identifiers. |
> | Działanie | microsoft.web/sites/slots/functions/listsecrets/action | List Secrets Web Apps Slots Functions. |
> | Działanie | microsoft.web/sites/slots/functions/read | Get Web Apps Slots Functions. |
> | Działanie | microsoft.web/sites/slots/hostnamebindings/delete | Delete Web Apps Slots Hostname Bindings. |
> | Działanie | microsoft.web/sites/slots/hostnamebindings/read | Get Web Apps Slots Hostname Bindings. |
> | Działanie | microsoft.web/sites/slots/hostnamebindings/write | Update Web Apps Slots Hostname Bindings. |
> | Działanie | microsoft.web/sites/slots/hybridconnection/delete | Delete Web Apps Slots Hybrid Connection. |
> | Działanie | microsoft.web/sites/slots/hybridconnection/read | Get Web Apps Slots Hybrid Connection. |
> | Działanie | microsoft.web/sites/slots/hybridconnection/write | Update Web Apps Slots Hybrid Connection. |
> | Działanie | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Delete Web Apps Slots Hybrid Connection Namespaces Relays. |
> | Działanie | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Update Web Apps Slots Hybrid Connection Namespaces Relays. |
> | Działanie | microsoft.web/sites/slots/hybridconnectionrelays/read | Get Web Apps Slots Hybrid Connection Relays. |
> | Działanie | microsoft.web/sites/slots/instances/deployments/read | Get Web Apps Slots Instances Deployments. |
> | Działanie | microsoft.web/sites/slots/instances/processes/delete | Delete Web Apps Slots Instances Processes. |
> | Działanie | microsoft.web/sites/slots/instances/processes/read | Get Web Apps Slots Instances Processes. |
> | Działanie | microsoft.web/sites/slots/instances/read | Get Web Apps Slots Instances. |
> | Działanie | microsoft.web/sites/slots/metricdefinitions/read | Get Web Apps Slots Metric Definitions. |
> | Działanie | microsoft.web/sites/slots/metrics/read | Get Web Apps Slots Metrics. |
> | Działanie | microsoft.web/sites/slots/migratemysql/read | Get Web Apps Slots Migrate MySql. |
> | Działanie | microsoft.web/sites/slots/networktrace/action | Network Trace Web Apps Slots. |
> | Działanie | microsoft.web/sites/slots/networktraces/operationresults/read | Get Web Apps Slots Network Trace Operation Results. |
> | Działanie | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps Slots. |
> | Działanie | microsoft.web/sites/slots/operationresults/read | Get Web Apps Slots Operation Results. |
> | Działanie | microsoft.web/sites/slots/operations/read | Get Web Apps Slots Operations. |
> | Działanie | microsoft.web/sites/slots/perfcounters/read | Get Web Apps Slots Performance Counters. |
> | Działanie | microsoft.web/sites/slots/phplogging/read | Get Web Apps Slots Phplogging. |
> | Działanie | microsoft.web/sites/slots/premieraddons/delete | Delete Web Apps Slots Premier Addons. |
> | Działanie | microsoft.web/sites/slots/premieraddons/read | Get Web Apps Slots Premier Addons. |
> | Działanie | microsoft.web/sites/slots/premieraddons/write | Update Web Apps Slots Premier Addons. |
> | Działanie | microsoft.web/sites/slots/processes/read | Get Web Apps Slots Processes. |
> | Działanie | microsoft.web/sites/slots/publiccertificates/delete | Delete Web Apps Slots Public Certificates. |
> | Działanie | microsoft.web/sites/slots/publiccertificates/read | Get Web Apps Slots Public Certificates. |
> | Działanie | microsoft.web/sites/slots/publiccertificates/write | Create or Update Web Apps Slots Public Certificates. |
> | Działanie | Microsoft.Web/sites/slots/publish/Action | Publish a Web App Slot |
> | Działanie | Microsoft.Web/sites/slots/publishxml/Action | Get publishing profile xml for Web App Slot |
> | Działanie | Microsoft.Web/sites/slots/Read | Get the properties of a Web App deployment slot |
> | Działanie | microsoft.web/sites/slots/recover/action | Recover Web Apps Slots. |
> | Działanie | Microsoft.Web/sites/slots/resetSlotConfig/Action | Reset web app slot configuration |
> | Działanie | microsoft.web/sites/slots/resourcehealthmetadata/read | Get Web Apps Slots Resource Health Metadata. |
> | Działanie | Microsoft.Web/sites/slots/restart/Action | Restart a Web App Slot |
> | Działanie | microsoft.web/sites/slots/restore/read | Get Web Apps Slots Restore. |
> | Działanie | microsoft.web/sites/slots/restore/write | Restore Web Apps Slots. |
> | Działanie | microsoft.web/sites/slots/restorefrombackupblob/action | Restore Web Apps Slot From Backup Blob. |
> | Działanie | microsoft.web/sites/slots/restorefromdeletedapp/action | Restore Web App Slots From Deleted App. |
> | Działanie | microsoft.web/sites/slots/restoresnapshot/action | Restore Web Apps Slots Snapshots. |
> | Działanie | microsoft.web/sites/slots/siteextensions/delete | Delete Web Apps Slots Site Extensions. |
> | Działanie | microsoft.web/sites/slots/siteextensions/read | Get Web Apps Slots Site Extensions. |
> | Działanie | microsoft.web/sites/slots/siteextensions/write | Update Web Apps Slots Site Extensions. |
> | Działanie | Microsoft.Web/sites/slots/slotsdiffs/Action | Poznaj różnice między konfiguracją aplikacji sieci Web i miejsc |
> | Działanie | Microsoft.Web/sites/slots/slotsswap/Action | Wymiana miejsc wdrożenia aplikacji sieci Web |
> | Działanie | microsoft.web/sites/slots/snapshots/read | Get Web Apps Slots Snapshots. |
> | Działanie | Microsoft.Web/sites/slots/sourcecontrols/Delete | Delete Web App Slot's source control configuration settings |
> | Działanie | Microsoft.Web/sites/slots/sourcecontrols/Read | Get Web App Slot's source control configuration settings |
> | Działanie | Microsoft.Web/sites/slots/sourcecontrols/Write | Update Web App Slot's source control configuration settings |
> | Działanie | Microsoft.Web/sites/slots/start/Action | Start a Web App Slot |
> | Działanie | Microsoft.Web/sites/slots/stop/Action | Stop a Web App Slot |
> | Działanie | microsoft.web/sites/slots/sync/action | Sync Web Apps Slots. |
> | Działanie | microsoft.web/sites/slots/triggeredwebjobs/delete | Delete Web Apps Slots Triggered WebJobs. |
> | Działanie | microsoft.web/sites/slots/triggeredwebjobs/read | Get Web Apps Slots Triggered WebJobs. |
> | Działanie | microsoft.web/sites/slots/triggeredwebjobs/run/action | Run Web Apps Slots Triggered WebJobs. |
> | Działanie | microsoft.web/sites/slots/usages/read | Get Web Apps Slots Usages. |
> | Działanie | microsoft.web/sites/slots/virtualnetworkconnections/delete | Delete Web Apps Slots Virtual Network Connections. |
> | Działanie | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Update Web Apps Slots Virtual Network Connections Gateways. |
> | Działanie | microsoft.web/sites/slots/virtualnetworkconnections/read | Get Web Apps Slots Virtual Network Connections. |
> | Działanie | microsoft.web/sites/slots/virtualnetworkconnections/write | Update Web Apps Slots Virtual Network Connections. |
> | Działanie | microsoft.web/sites/slots/webjobs/read | Get Web Apps Slots WebJobs. |
> | Działanie | Microsoft. Web/Sites/szczeliny/zapis | Utwórz nowe miejsce aplikacji sieci Web lub zaktualizuj istniejące |
> | Działanie | Microsoft. Web/Sites/slotsdiffs/akcja | Poznaj różnice między konfiguracją aplikacji sieci Web i miejsc |
> | Działanie | Microsoft. Web/Sites/slotsswap/akcja | Wymiana miejsc wdrożenia aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/snapshots/Read | Pobierz migawki Web Apps. |
> | Działanie | Microsoft.Web/sites/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/sourcecontrols/odczyt | Pobierz ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Działanie | Microsoft.Web/sites/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Start/akcja | Uruchamianie aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/zatrzymywanie/akcja | Zatrzymaj aplikację internetową |
> | Działanie | Microsoft. Web/Sites/synchronizacja/akcja | Web Apps synchronizacji. |
> | Działanie | Microsoft. Web/Sites/syncfunctiontriggers/akcja | Wyzwalacze funkcji synchronizacji. |
> | Działanie | Microsoft. Web/Sites/triggeredwebjobs/Delete | Usuń Web Apps wyzwolone Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites/triggeredwebjobs/historia/odczyt | Pobierz Web Apps wyzwoloną historię zadań WebJob. |
> | Działanie | Microsoft. Web/Sites/triggeredwebjobs/odczyt | Pobierz Web Apps wyzwolone Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites/triggeredwebjobs/Run/Action | Uruchom Web Apps wyzwolone Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites//użycia/odczyt | Pobierz Web Apps użycia. |
> | Działanie | microsoft.web/sites/virtualnetworkconnections/delete | Usuń połączenia Virtual Network Web Apps. |
> | Działanie | microsoft.web/sites/virtualnetworkconnections/gateways/read | Pobierz Web Apps bram Virtual Network połączeń. |
> | Działanie | microsoft.web/sites/virtualnetworkconnections/gateways/write | Aktualizowanie bram Web Apps Virtual Network połączeń. |
> | Działanie | microsoft.web/sites/virtualnetworkconnections/read | Uzyskaj Web Apps połączenia Virtual Network. |
> | Działanie | microsoft.web/sites/virtualnetworkconnections/write | Aktualizowanie Web Apps Virtual Network połączeń. |
> | Działanie | Microsoft. Web/Sites/WebJobs/odczyt | Pobierz Web Apps Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites/Write | Utwórz nową aplikację sieci Web lub zaktualizuj istniejącą |
> | Działanie | microsoft.web/skus/read | Pobierz jednostki SKU. |
> | Działanie | microsoft.web/sourcecontrols/read | Pobierz kontrolę źródła. |
> | Działanie | Microsoft. Web/sourcecontrols/Write | Aktualizowanie kontroli źródła. |
> | Działanie | Microsoft. Web/Unregister/akcja | Wyrejestruj dostawcę zasobów Microsoft. Web dla subskrypcji. |
> | Działanie | Microsoft. Web/Validate/akcja | Legalizacj. |
> | Działanie | Microsoft. Web/verifyhostingenvironmentvnet/akcja | Sprawdź sieć wirtualną środowiska hostingu. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Monitor obciążenia został/Components/Read | Pobiera składniki dla zasobu |
> | Działanie | Microsoft. Monitor obciążenia został/componentsSummary/odczyt | Pobiera podsumowanie składników |
> | Działanie | Microsoft.WorkloadMonitor/monitorInstances/read | Pobiera wystąpienia monitorów dla zasobu |
> | Działanie | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Pobiera podsumowanie wystąpień monitora |
> | Działanie | Microsoft.WorkloadMonitor/monitors/read | Pobiera monitory dla zasobu |
> | Działanie | Microsoft.WorkloadMonitor/monitors/write | Konfigurowanie monitora dla zasobu |
> | Działanie | Microsoft.WorkloadMonitor/notificationSettings/read | Pobiera ustawienia powiadomień dla zasobu |
> | Działanie | Microsoft.WorkloadMonitor/notificationSettings/write | Konfigurowanie ustawień powiadomień dla zasobu |
> | Działanie | Microsoft.WorkloadMonitor/operations/read | Pobiera obsługiwane operacje |

## <a name="next-steps"></a>Następne kroki

- [Dopasuj dostawcę zasobów do usługi](../azure-resource-manager/azure-services-resource-providers.md)
- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
