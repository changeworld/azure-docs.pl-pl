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
ms.date: 09/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ca9b0289fbc8dd01427de38e0e7b30eec266bb12
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818592"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operacje dostawcy zasobów Azure Resource Manager

W tym artykule wymieniono operacje dostępne dla każdego dostawcy zasobów Azure Resource Manager. Te operacje mogą być używane w [rolach niestandardowych](custom-roles.md) w celu zapewnienia szczegółowej [kontroli dostępu opartej na rolach (RBAC)](overview.md) na potrzeby zasobów na platformie Azure. Ciągi operacji mają następujący format: `{Company}.{ProviderName}/{resourceType}/{action}`

Operacje dostawcy zasobów są zawsze rozwijane. Aby uzyskać najnowsze operacje, Użyj listy operacji [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) lub [AZ Provider](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. AAD/domainServices/Delete | Usuwanie usługi domeny |
> | Akcja | Microsoft. AAD/domainServices/oucontainer/usuwanie | Usuń kontener jednostki organizacyjnej |
> | Akcja | Microsoft. AAD/domainServices/oucontainer/odczyt | Odczytaj kontenery jednostek organizacyjnych |
> | Akcja | Microsoft. AAD/domainServices/oucontainer/zapis | Zapisz kontener jednostki organizacyjnej |
> | Akcja | Microsoft. AAD/domainServices/odczyt | Odczytaj usługi domenowe |
> | Akcja | Microsoft. AAD/domainServices/replicaSets/usuwanie | Usuń lokację klastra |
> | Akcja | Microsoft. AAD/domainServices/replicaSets/odczyt | Odczytaj lokację klastra |
> | Akcja | Microsoft. AAD/domainServices/replicaSets/zapis | Zapisz witrynę klastra |
> | Akcja | Microsoft. AAD/domainServices/Write | Zapisz usługę domenową |
> | Akcja | Microsoft. AAD/Locations/operationresults/Read |  |
> | Akcja | Microsoft. AAD/Operations/Read |  |
> | Akcja | Microsoft. AAD/rejestrowanie/akcja | Zarejestruj usługę domeny |
> | Akcja | Microsoft. AAD/Wyrejestruj/akcja | Wyrejestruj usługę domenową |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. aadiam/diagnosticsettings/Delete | Usuwanie ustawień diagnostycznych |
> | Akcja | Microsoft. aadiam/diagnosticsettings/odczyt | Odczytywanie ustawień diagnostycznych |
> | Akcja | Microsoft. aadiam/diagnosticsettings/Write | Zapisywanie ustawień diagnostycznych |
> | Akcja | Microsoft. aadiam/diagnosticsettingscategories/odczyt | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftaddons"></a>Microsoft. Dodatki

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Dodatki/operacje/odczyt | Pobiera obsługiwane operacje RP. |
> | Akcja | Microsoft. Dodatki/rejestrowanie/akcja | Zarejestruj określoną subskrypcję w programie Microsoft. Dodatki |
> | Akcja | Microsoft. Dodatki/supportProviders/listsupportplaninfo/akcja | Wyświetla bieżące informacje o planach pomocy technicznej dla określonej subskrypcji. |
> | Akcja | Microsoft. Dodatki/supportProviders/supportPlanTypes/usuwanie | Usuwa określony plan obsługi kanonicznej |
> | Akcja | Microsoft. Dodatki/supportProviders/supportPlanTypes/odczyt | Pobierz określony stan kanonicznego planu pomocy technicznej. |
> | Akcja | Microsoft. Dodatki/supportProviders/supportPlanTypes/Write | Dodaje określony typ planu obsługi kanonicznej. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Action | Utwórz nowy las dla dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/addomainservicemembers/Read | Pobiera wszystkie serwery dla określonej nazwy usługi. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Alerts/Read | Pobiera szczegóły alertów dla lasu, takie jak alertid, Data wygenerowania alertu, ostatnie wykrycie alertu, opis alertu, Ostatnia aktualizacja, poziom alertu, stan alertu, linki do rozwiązywania problemów z alertami itp. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/konfiguracja/odczyt | Pobiera konfigurację usługi dla lasu. Przykład — nazwa lasu, poziom funkcjonalności, główna rola FSMO nazw domen, główna rola FSMO schematu itp. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Delete | Usuwa usługę i jej serwery wraz z danymi dotyczącymi kondycji. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Dimensions/Read | Pobiera szczegóły domen i lokacji dla lasu. Przykład — stan kondycji, aktywne alerty, rozwiązane alerty, właściwości, takie jak poziom funkcjonalności domeny, Las, wzorzec infrastruktury, PDC, główny serwer RID itd.  |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Features/UserPreference/Read | Pobiera ustawienie preferencji użytkownika dla lasu.<br>Przykład — MetricCounterName, takie jak ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Ustawienia dla wykresów interfejsu użytkownika itd. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/forestsummary/Read | Pobiera podsumowanie lasu dla danego lasu, takie jak nazwa lasu, liczba domen w tym lesie, liczba lokacji i szczegółowe informacje o lokacjach itp. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/metricmetadata/Read | Pobiera listę obsługiwanych metryk dla danej usługi.<br>Na przykład blokada konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (/s) dla usługi AD FS.<br>Uwierzytelnianie NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnienia Kerberos/s, wątki ATQ i łącznie z ADDomainService.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD dla ADSync. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Metrics/Groups/Read | Dana usługa ten interfejs API pobiera informacje o metrykach.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/premiumcheck/Read | Ten interfejs API pobiera listę wszystkich dołączanych ADDomainServices dla dzierżawy w warstwie Premium. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/odczyt | Pobiera szczegóły usługi dla określonej nazwy usługi. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/replicationdetails/Read | Pobiera szczegóły replikacji dla wszystkich serwerów dla określonej nazwy usługi. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/ReplicationStatus/Read | Pobiera liczbę kontrolerów domeny i ich błędy replikacji, jeśli istnieją. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/replicationsummary/Read | Pobiera pełną listę kontrolerów domeny wraz ze szczegółami replikacji dla danego lasu. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Servicemembers/akcja | Dodaj wystąpienie serwera do usługi. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Servicemembers/Credentials/Read | Podczas rejestracji serwera ADDomainService ten interfejs API jest wywoływany w celu uzyskania poświadczeń do dołączania nowych serwerów. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Servicemembers/Delete | Usuwa serwer dla danej usługi i dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/addsservices/Write | Tworzy lub aktualizuje wystąpienie ADDomainService dla dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/konfiguracja/akcja | Aktualizuje konfigurację dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/konfiguracja/odczyt | Odczytuje konfigurację dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/konfiguracja/zapis | Tworzy konfigurację dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/Logs/Contents/Read | Pobiera zawartość instalacji agenta i dzienników rejestracji przechowywanych w obiekcie blob. |
> | Akcja | Microsoft. ADHybridHealthService/Logs/odczyt | Pobiera dzienniki instalacji i rejestracji agenta dla dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/Operations/Read | Pobiera listę operacji obsługiwanych przez system. |
> | Akcja | Microsoft. ADHybridHealthService/Register/Action | Rejestruje dostawcę zasobów ADHybrid Usługa kondycji i umożliwia utworzenie zasobu Usługa kondycji ADHybrid. |
> | Akcja | Microsoft. ADHybridHealthService/Reports/availabledeployments/Read | Pobiera listę dostępnych regionów, które są używane przez DevOps do obsługi zdarzeń klientów. |
> | Akcja | Microsoft. ADHybridHealthService/Reports/badpassword/Read | Pobiera listę nieprawidłowych prób wprowadzenia hasła dla wszystkich użytkowników w Active Directory usługa federacyjna. |
> | Akcja | Microsoft. ADHybridHealthService/Reports/badpassworduseridipfrequency/Read | Pobiera identyfikator URI sygnatury dostępu współdzielonego obiektu BLOB zawierający stan i ewentualny wynik nowo umieszczonego zadania raportu dla częstotliwości nieprawidłowej nazwy użytkownika/hasła na wartość UserId na adres IP dziennie dla danej dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/Reports/consentedtodevopstenants/Read | Pobiera listę DevOps, które wyraziły zgodę. Zwykle używany do obsługi klienta. |
> | Akcja | Microsoft. ADHybridHealthService/Reports/isdevops/Read | Pobiera wartość wskazującą, czy dzierżawca jest DevOps, czy nie. |
> | Akcja | Microsoft. ADHybridHealthService/Reports/selectdevopstenant/Read | Aktualizuje identyfikator użytkownika (objectid) dla wybranej dzierżawy Ops usługi dev. |
> | Akcja | Microsoft. ADHybridHealthService/Reports/selecteddeployment/Read | Pobiera wybrane wdrożenie dla danej dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/Reports/tenantassigneddeployment/Read | Podaną identyfikator dzierżawy Pobiera lokalizację magazynu dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/Reports/updateselecteddeployment/Read | Pobiera lokalizację geograficzną, z której będą uzyskiwane dostęp do danych. |
> | Akcja | Microsoft. ADHybridHealthService/usługi/akcja | Aktualizuje wystąpienie usługi w dzierżawie. |
> | Akcja | Microsoft. ADHybridHealthService/usługi/alerty/odczyt | Odczytuje alerty dla usługi. |
> | Akcja | Microsoft. ADHybridHealthService/usługi/alerty/odczyt | Odczytuje alerty dla usługi. |
> | Akcja | Microsoft. ADHybridHealthService/Services/checkservicefeatureavailibility/Read | Dana nazwa funkcji weryfikuje, czy usługa ma wszystkie elementy wymagane do korzystania z tej funkcji. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Delete | Usuwa wystąpienie usługi w dzierżawie. |
> | Akcja | Microsoft. ADHybridHealthService/Services/exporterrors/Read | Pobiera błędy eksportu dla danej usługi synchronizacji. |
> | Akcja | Microsoft. ADHybridHealthService/Services/exportstatus/Read | Pobiera stan eksportu dla danej usługi. |
> | Akcja | Microsoft. ADHybridHealthService/Services/opinia/opinia/odczyt | Pobiera informacje zwrotne o alertach dla danej usługi i serwera. |
> | Akcja | Microsoft. ADHybridHealthService/Services/metricmetadata/Read | Pobiera listę obsługiwanych metryk dla danej usługi.<br>Na przykład blokada konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (/s) dla usługi AD FS.<br>Uwierzytelnianie NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnienia Kerberos/s, wątki ATQ i łącznie z ADDomainService.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD dla ADSync. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Metrics/Groups/Average/Read | Przy użyciu usługi ten interfejs API pobiera średnią dla metryk dla danej usługi.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Metrics/Groups/Read | Dana usługa ten interfejs API pobiera informacje o metrykach.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Metrics/Groups/sum/Read | Ponieważ usługa ten interfejs API pobiera Zagregowany widok dla metryk dla danej usługi.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Akcja | Microsoft. ADHybridHealthService/Services/monitoringconfiguration/Write | Dodawanie lub aktualizowanie konfiguracji monitorowania dla usługi. |
> | Akcja | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/Read | Pobiera konfiguracje monitorowania dla danej usługi. |
> | Akcja | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/Write | Dodawanie lub aktualizowanie konfiguracji monitorowania dla usługi. |
> | Akcja | Microsoft. ADHybridHealthService/Services/premiumcheck/Read | Ten interfejs API pobiera listę wszystkich dołączanych usług dla dzierżawy w warstwie Premium. |
> | Akcja | Microsoft. ADHybridHealthService/usługi/odczyt | Odczytuje wystąpienia usługi w dzierżawie. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Reports/blobUris/Read | Pobiera wszystkie ryzykowne identyfikatory URI raportów IP z ostatnich 7 dni. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Reports/Details/Read | Pobiera raport dotyczący pierwszych 50 użytkowników z błędami nieprawidłowych haseł w ciągu ostatnich 7 dni |
> | Akcja | Microsoft. ADHybridHealthService/Services/Reports/generateBlobUri/Action | Generuje raport o ryzykownych adresach IP i zwraca do niego skierowany identyfikator URI. |
> | Akcja | Microsoft. ADHybridHealthService/usługi/Servicemembers/akcja | Tworzy wystąpienie serwera w usłudze. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Servicemembers/Alerts/Read | Odczytuje alerty dla serwera. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Servicemembers/Credentials/Read | Podczas rejestracji serwera ten interfejs API jest wywoływany w celu uzyskania poświadczeń do dołączania nowych serwerów. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Servicemembers/dataaktualności/odczyt | Dla danego serwera ten interfejs API pobiera listę typów danych, które są przekazywane przez serwery, oraz najnowsze czasu dla każdego przekazywania. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Servicemembers/Delete | Usuwa wystąpienie serwera w usłudze. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Servicemembers/exportstatus/Read | Pobiera szczegóły błędu eksportu synchronizacji dla danej usługi synchronizacji. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Servicemembers/Metrics/Groups/Read | Dana usługa ten interfejs API pobiera informacje o metrykach.<br>Ten interfejs API może na przykład służyć do uzyskiwania informacji związanych z: blokadami konta ekstranetu, Łączna liczba żądań zakończonych niepowodzeniem, zaległe żądania tokenów (proxy), żądania tokenów (na sekundę itp.) dla usługi ADFederation.<br>Liczba uwierzytelnień NTLM/s, pomyślne powiązania LDAP/s, czas powiązania LDAP, aktywne wątki LDAP, uwierzytelnianie Kerberos/s, wszystkie wątki ATQ itd. dla usługi ADDomain.<br>Opóźnij uruchamianie profilu, ustanowione połączenia TCP, prywatne bajty agentów, Eksportuj statystyki do usługi Azure AD w celu przeprowadzenia synchronizacji. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Servicemembers/Metrics/Read | Pobiera listę łączników i uruchamia nazwy profilów dla danej usługi i członka usługi. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Servicemembers/Read | Odczytuje wystąpienie serwera w usłudze. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Servicemembers/ServiceConfiguration/Read | Pobiera konfigurację usługi dla danej dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/Services/tenantwhitelisting/Read | Pobiera stan listy dozwolonych funkcji dla danej dzierżawy. |
> | Akcja | Microsoft. ADHybridHealthService/Services/Write | Tworzy wystąpienie usługi w dzierżawie. |
> | Akcja | Microsoft. ADHybridHealthService/Unregister/Action | Wyrejestrowuje subskrypcję dla dostawcy zasobów usługi ADHybrid Usługa kondycji. |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Advisor/konfiguracje/odczyt | Pobierz konfiguracje |
> | Akcja | Microsoft. Advisor/konfiguracje/zapis | Tworzy/aktualizuje konfigurację |
> | Akcja | Microsoft. Advisor/generateRecommendations/akcja | Generuje zalecenia |
> | Akcja | Microsoft. Advisor/generateRecommendations/Read | Pobiera stan zaleceń generowania |
> | Akcja | Microsoft. Advisor/Metadata/Read | Pobierz metadane |
> | Akcja | Microsoft. Advisor/Operations/Read | Pobiera operacje dla programu Microsoft Advisor |
> | Akcja | Microsoft. Advisor/zalecenia/dostępne/akcja | Nowe zalecenie jest dostępne w programie Microsoft Advisor |
> | Akcja | Microsoft. Advisor/zalecenia/odczyt | Odczytuje zalecenia |
> | Akcja | Microsoft. Advisor/zalecenia/tłumienie/usuwanie | Usuwa pominięcie |
> | Akcja | Microsoft. Advisor/zalecenia/tłumienie/odczyt | Pobiera pominięcia |
> | Akcja | Microsoft. Advisor/zalecenia/tłumienie/zapis | Tworzy/aktualizuje pominięcia |
> | Akcja | Microsoft. Advisor/rejestr/akcja | Rejestruje subskrypcję usługi Microsoft Advisor |
> | Akcja | Microsoft. Advisor/tłumienie/usuwanie | Usuwa pominięcie |
> | Akcja | Microsoft. Advisor/tłumienie/odczyt | Pobiera pominięcia |
> | Akcja | Microsoft. Advisor/tłumienie/zapis | Tworzy/aktualizuje pominięcia |
> | Akcja | Microsoft. Advisor/Unregister/Action | Wyrejestrowuje subskrypcję usługi Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. AlertsManagement/actionRules/Delete | Usuń regułę akcji w danej subskrypcji. |
> | Akcja | Microsoft. AlertsManagement/actionRules/odczyt | Pobierz wszystkie reguły akcji dla filtrów wejściowych. |
> | Akcja | Microsoft. AlertsManagement/actionRules/Write | Utwórz lub zaktualizuj regułę akcji w danej subskrypcji |
> | Akcja | Microsoft. AlertsManagement/Alerts/changestate/Action | Zmień stan alertu. |
> | Akcja | Microsoft. AlertsManagement/alerty/Diagnostyka/odczyt | Pobierz całą diagnostykę dla alertu |
> | Akcja | Microsoft. AlertsManagement/alerty/historia/odczyt | Pobierz historię alertu |
> | Akcja | Microsoft. AlertsManagement/Alerts/Read | Pobierz wszystkie alerty dla filtrów wejściowych. |
> | Akcja | Microsoft. AlertsManagement/alertsList/odczyt | Pobierz wszystkie alerty dla filtrów wejściowych między subskrypcjami |
> | Akcja | Microsoft. AlertsManagement/alertsMetaData/odczyt | Pobierz metadane alertów dla parametru wejściowego. |
> | Akcja | Microsoft. AlertsManagement/alertsSummary/odczyt | Pobierz podsumowanie alertów |
> | Akcja | Microsoft. AlertsManagement/alertsSummaryList/odczyt | Pobierz podsumowanie alertów w ramach subskrypcji |
> | Akcja | Microsoft. AlertsManagement/Operations/Read | Odczytuje podane operacje |
> | Akcja | Microsoft. AlertsManagement/Register/Action | Rejestruje subskrypcję zarządzania alertami firmy Microsoft |
> | Akcja | Microsoft. AlertsManagement/smartDetectorAlertRules/Delete | Usuń regułę alertu inteligentnego wykrywania w danej subskrypcji |
> | Akcja | Microsoft. AlertsManagement/smartDetectorAlertRules/odczyt | Pobieranie wszystkich reguł alertów dotyczących detektora inteligentnego dla filtrów wejściowych |
> | Akcja | Microsoft. AlertsManagement/smartDetectorAlertRules/Write | Utwórz lub zaktualizuj regułę alertu inteligentnego wykrywania w danej subskrypcji |
> | Akcja | Microsoft. AlertsManagement/smartGroups/changestate/akcja | Zmiana stanu grupy inteligentnej |
> | Akcja | Microsoft. AlertsManagement/smartGroups/History/odczyt | Pobierz historię grupy inteligentnej |
> | Akcja | Microsoft. AlertsManagement/smartGroups/odczyt | Pobierz wszystkie grupy inteligentne dla filtrów wejściowych |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. AnalysisServices/Locations/checkNameAvailability/Action | Sprawdza, czy dana nazwa Analysis Server jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft. AnalysisServices/Locations/operationresults/Read | Pobiera informacje o wyniku określonej operacji. |
> | Akcja | Microsoft. AnalysisServices/Locations/operationstatuses/Read | Pobiera informacje o stanie określonej operacji. |
> | Akcja | Microsoft. AnalysisServices/Operations/Read | Pobiera informacje o operacjach |
> | Akcja | Microsoft. AnalysisServices/Register/Action | Rejestruje dostawcę zasobów Analysis Services. |
> | Akcja | Microsoft. AnalysisServices/serwery/usuwanie | Usuwa Analysis Server. |
> | Akcja | Microsoft. AnalysisServices/serwery/listGatewayStatus/akcja | Wyświetlenie listy stan bramy skojarzonej z serwerem. |
> | Akcja | Microsoft. AnalysisServices/serwery/odczyt | Pobiera informacje o określonym Analysis Server. |
> | Akcja | Microsoft. AnalysisServices/serwery/wznowienie/akcja | Wznawia Analysis Server. |
> | Akcja | Microsoft. AnalysisServices/serwery/jednostki SKU/odczyt | Pobierz informacje o dostępnych jednostkach SKU dla serwera |
> | Akcja | Microsoft. AnalysisServices/serwery/wstrzymywanie/akcja | Wstrzymuje Analysis Server. |
> | Akcja | Microsoft. AnalysisServices/serwery/zapis | Tworzy lub aktualizuje określony Analysis Server. |
> | Akcja | Microsoft. AnalysisServices/jednostki SKU/odczyt | Pobiera informacje o jednostkach SKU |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ApiManagement/checkNameAvailability/odczyt | Sprawdza, czy podana nazwa usługi jest dostępna |
> | Akcja | Microsoft. ApiManagement/Operations/Read | Odczytuj wszystkie operacje interfejsu API dostępne dla zasobu Microsoft. ApiManagement |
> | Akcja | Microsoft. ApiManagement/Register/Action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. ApiManagement |
> | Akcja | Microsoft. ApiManagement/Reports/Read | Uzyskuj raporty zagregowane według okresów, regionu geograficznego, deweloperów, produktów, interfejsów API, operacji, subskrypcji i byRequest. |
> | Akcja | Microsoft. ApiManagement/Service/API/Delete | Usuwa określony interfejs API wystąpienia usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/API/Diagnostyka/usuwanie | Usuwa określoną diagnostykę z interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/Diagnostics/Read | Wyświetla listę wszystkich diagnostyki interfejsu API. lub pobiera szczegóły diagnostyki dla interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/Diagnostyka/zapis | Tworzy nową diagnostykę dla interfejsu API lub aktualizuje istniejący. lub aktualizuje szczegóły diagnostyki dla interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/załączniki/usuwanie | Usuwa określony komentarz z problemu. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/załączniki/odczyt | Wyświetla listę wszystkich załączników dla problemu skojarzonego z określonym interfejsem API. lub pobiera szczegóły załącznika problemu dla interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/załączniki/zapis | Tworzy nowy załącznik dla problemu w interfejsie API lub aktualizuje istniejący. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/Komentarze/usuwanie | Usuwa określony komentarz z problemu. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/Komentarze/odczyt | Wyświetla listę wszystkich komentarzy dotyczących problemu skojarzonego z określonym interfejsem API. lub pobiera szczegóły komentarza dotyczącego problemu dla interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/Komentarze/zapis | Tworzy nowy komentarz dotyczący problemu w interfejsie API lub aktualizuje istniejący. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/usuwanie | Usuwa określony problem z interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/Issues/Read | Wyświetla listę wszystkich problemów skojarzonych z określonym interfejsem API. lub pobiera szczegóły problemu dotyczącego interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/Issues/Write | Tworzy nowy problem dla interfejsu API lub aktualizuje istniejący. lub aktualizuje istniejący problem dla interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/Delete | Usuwa określoną operację w interfejsie API. |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/policies/Delete | Usuwa konfigurację zasad w operacji interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/policies/Read | Pobierz listę konfiguracji zasad na poziomie operacji interfejsu API. lub Pobierz konfigurację zasad na poziomie operacji interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/policies/Write | Tworzy lub aktualizuje konfigurację zasad dla poziomu operacji interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/Policy/Delete | Usuń konfigurację zasad na poziomie operacji |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/Policy/Read | Pobierz konfigurację zasad na poziomie operacji |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/Policy/Write | Utwórz konfigurację zasad na poziomie operacji |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/Read | Wyświetla kolekcję operacji dla określonego interfejsu API. lub pobiera szczegóły operacji interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/Tags/Delete | Odłącz tag od operacji. |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/Tags/Read | Wyświetla listę wszystkich tagów skojarzonych z operacją. lub Pobierz tag skojarzony z operacją. |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/Tags/Write | Przypisz tag do operacji. |
> | Akcja | Microsoft. ApiManagement/Service/API/Operations/Write | Tworzy nową operację w interfejsie API lub aktualizuje istniejącą. lub aktualizuje szczegóły operacji w interfejsie API określonym przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/operationsByTags/Read | Wyświetla kolekcję operacji skojarzonych ze znacznikami. |
> | Akcja | Microsoft. ApiManagement/Service/API/policies/Delete | Usuwa konfigurację zasad w interfejsie API. |
> | Akcja | Microsoft. ApiManagement/Service/API/zasady/odczyt | Pobierz konfigurację zasad na poziomie interfejsu API. lub Pobierz konfigurację zasad na poziomie interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/zasady/zapis | Tworzy lub aktualizuje konfigurację zasad dla interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/Policy/Delete | Usuń konfigurację zasad na poziomie interfejsu API |
> | Akcja | Microsoft. ApiManagement/Service/API/Policy/Read | Pobierz konfigurację zasad na poziomie interfejsu API |
> | Akcja | Microsoft. ApiManagement/Service/API/Policy/Write | Utwórz konfigurację zasad na poziomie interfejsu API |
> | Akcja | Microsoft. ApiManagement/Service/API/Products/Read | Wyświetla listę wszystkich produktów, które są częścią interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/odczyt | Wyświetla wszystkie interfejsy API wystąpienia usługi API Management. lub pobiera szczegóły interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/releases/Delete | Usuwa wszystkie wersje interfejsu API lub usuwa określoną wersję w interfejsie API. |
> | Akcja | Microsoft. ApiManagement/Service/API/releases/Read | Wyświetla wszystkie wersje interfejsu API.<br>Wersja interfejsu API jest tworzona podczas aktualnej wersji interfejsu API.<br>Wersje są również używane do wycofywania do poprzednich wersji.<br>Wyniki będą stronicowane i mogą być ograniczone przez $top i $skip parametrów.<br>lub zwraca szczegółowe informacje o wersji interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/releases/Write | Tworzy nową wersję interfejsu API. lub aktualizuje szczegóły wydania interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/Revisions/Delete | Usuwa wszystkie poprawki interfejsu API |
> | Akcja | Microsoft. ApiManagement/Service/API/Revisions/Read | Wyświetla wszystkie wersje interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/schematy/usuwanie | Usuwa konfigurację schematu w interfejsie API. |
> | Akcja | Microsoft. ApiManagement/Service/API/schematy/odczyt | Pobierz konfigurację schematu na poziomie interfejsu API. lub zapoznaj się z konfiguracją schematu na poziomie interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/schematy/zapis | Tworzy lub aktualizuje konfigurację schematu dla interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/tagDescriptions/Delete | Usuń opis tagu dla interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/tagDescriptions/Read | Wyświetla listę wszystkich opisów tagów w zakresie interfejsu API. Model podobny do struktury Swagger-tagDescription jest zdefiniowany na poziomie interfejsu API, ale tag może być przypisany do operacji lub uzyskać opis tagu w zakresie interfejsu API |
> | Akcja | Microsoft. ApiManagement/Service/API/tagDescriptions/Write | Utwórz/zaktualizuj opis tagu w zakresie interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/interfejsy API/Tagi/usuwanie | Odłącz tag od interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/interfejsy API/Tagi/odczyt | Wyświetla listę wszystkich tagów skojarzonych z interfejsem API. lub uzyskaj tag skojarzony z interfejsem API. |
> | Akcja | Microsoft. ApiManagement/Service/interfejsy API/Tagi/zapis | Przypisz tag do interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/Write | Tworzy nowy lub aktualizuje istniejący interfejs API wystąpienia usługi API Management. lub aktualizuje określony interfejs API wystąpienia usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/apisByTags/Read | Wyświetla kolekcję interfejsów API skojarzonych ze znacznikami. |
> | Akcja | Microsoft. ApiManagement/Service/apiVersionSets/Delete | Usuwa określony zestaw wersji interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/apiVersionSets/Read | Wyświetla kolekcję zestawów wersji interfejsu API w określonym wystąpieniu usługi. lub pobiera szczegóły zestawu wersji interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/apiVersionSets/wersje/odczyt | Pobierz listę jednostek wersji |
> | Akcja | Microsoft. ApiManagement/Service/apiVersionSets/Write | Tworzy lub aktualizuje zestaw wersji interfejsu API. lub aktualizuje szczegóły VersionSet interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/applynetworkconfigurationupdates/Action | Aktualizuje zasoby Microsoft. ApiManagement uruchomione w Virtual Network w celu pobrania zaktualizowanych ustawień sieci. |
> | Akcja | Microsoft. ApiManagement/Service/authorizationServers/Delete | Usuwa określone wystąpienie serwera autoryzacji. |
> | Akcja | Microsoft. ApiManagement/Service/authorizationServers/Read | Wyświetla kolekcję serwerów autoryzacji zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły serwera autoryzacji określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/authorizationServers/Write | Tworzy nowy serwer autoryzacji lub aktualizuje istniejący serwer autoryzacji. lub aktualizuje szczegóły serwera autoryzacji określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/nadkończenie/usuwanie | Usuwa określone zaplecze. |
> | Akcja | Microsoft. ApiManagement/Service/zakończyła się/odczytywać | Wyświetla kolekcję punktów końcowych w określonym wystąpieniu usługi. lub pobiera szczegóły zaplecza określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/nadkończenie/ponowne łączenie/akcja | Powiadamia serwer proxy APIM o utworzeniu nowego połączenia z zapleczem po upływie określonego limitu czasu. Jeśli nie określono limitu czasu, zostanie użyty limit czasu wynoszący 2 minuty. |
> | Akcja | Microsoft. ApiManagement/Service/nadkończenie/zapis | Tworzy lub aktualizuje zaplecze. lub aktualizuje istniejące zaplecze. |
> | Akcja | Microsoft. ApiManagement/Service/kopia zapasowa/akcja | Usługa tworzenia kopii zapasowych API Management do określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Akcja | Microsoft. ApiManagement/Service/Caches/Delete | Usuwa określoną pamięć podręczną. |
> | Akcja | Microsoft. ApiManagement/Service/Caches/Read | Wyświetla kolekcję wszystkich zewnętrznych pamięci podręcznych w określonym wystąpieniu usługi. lub pobiera szczegóły pamięci podręcznej określonej przez jej identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Caches/Write | Tworzy lub aktualizuje zewnętrzną pamięć podręczną, która ma być używana w wystąpieniu usługi API Management. lub aktualizuje szczegóły pamięci podręcznej określonej przez jej identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Certificates/Delete | Usuwa określony certyfikat. |
> | Akcja | Microsoft. ApiManagement/Service/Certificates/Read | Wyświetla kolekcję wszystkich certyfikatów w określonym wystąpieniu usługi. lub pobiera szczegóły certyfikatu określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Certificates/Write | Tworzy lub aktualizuje certyfikat używany do uwierzytelniania w zapleczu. |
> | Akcja | Microsoft. ApiManagement/Service/contentTypes/contentItems/Delete | Usuwa określony element zawartości. |
> | Akcja | Microsoft. ApiManagement/Service/contentTypes/contentItems/Read | Zwraca listę elementów zawartości lub zwraca szczegóły elementu zawartości |
> | Akcja | Microsoft. ApiManagement/Service/contentTypes/contentItems/Write | Tworzy nowy element zawartości lub aktualizuje określony element zawartości |
> | Akcja | Microsoft. ApiManagement/Service/contentTypes/Read | Zwraca listę typów zawartości |
> | Akcja | Microsoft. ApiManagement/Service/Delete | Usuń wystąpienie usługi API Management |
> | Akcja | Microsoft. ApiManagement/Service/Diagnostics/Delete | Usuwa określoną diagnostykę. |
> | Akcja | Microsoft. ApiManagement/Service/Diagnostics/Read | Wyświetla listę wszystkich diagnostyki wystąpienia usługi API Management. lub pobiera szczegóły diagnostyki określone przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Diagnostics/Write | Tworzy nową diagnostykę lub aktualizuje istniejącą. lub aktualizuje szczegóły diagnostyki określonej przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/Action | Pobiera konfigurację bramy. lub aktualizuje puls bramy. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/Delete | Usuwa określoną bramę. |
> | Akcja | Microsoft. ApiManagement/Service/Gateway//klucze/akcja | Pobiera klucze bramy. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/Read | Wyświetla kolekcję bram zarejestrowanych w wystąpieniu usługi. lub pobiera szczegóły bramy określone przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/regeneratePrimaryKey/Action | Ponownie generuje podstawowy klucz bramy invalidationg wszystkie utworzone przez niego tokeny. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/regenerateSecondaryKey/Action | Ponownie generuje pomocniczy klucz bramy invalidationg wszystkie utworzone przez niego tokeny. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/token/Action | Pobiera token autoryzacji dostępu współdzielonego dla bramy. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/Write | Tworzy lub aktualizuje bramę do użycia w wystąpieniu usługi API Management. lub aktualizuje szczegóły bramy określone przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/getssotoken/Action | Pobiera token rejestracji jednokrotnej, którego można użyć do zalogowania się do portalu usługi API Management starszej wersji jako administrator |
> | Akcja | Microsoft. ApiManagement/Service/Groups/Delete | Usuwa konkretną grupę wystąpienia usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/Groups/Read | Wyświetla kolekcję grup zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły grupy określonej przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Groups/users/Delete | Usuń istniejącego użytkownika z istniejącej grupy. |
> | Akcja | Microsoft. ApiManagement/Service/Groups/users/Read | Wyświetla kolekcję jednostek użytkownika skojarzonych z grupą. |
> | Akcja | Microsoft. ApiManagement/Service/Groups/users/Write | Dodaj istniejącego użytkownika do istniejącej grupy |
> | Akcja | Microsoft. ApiManagement/Service/Groups/Write | Tworzy lub aktualizuje grupę. lub aktualizuje Szczegóły grupy określonej przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/skojarzeni/Delete | Usuwa określoną konfigurację dostawcy tożsamości. |
> | Akcja | Microsoft. ApiManagement/Service/skojarzeni/Read | Wyświetla kolekcję dostawców tożsamości skonfigurowanych w określonym wystąpieniu usługi. lub pobiera szczegóły konfiguracji dostawcy tożsamości skonfigurowanego w określonym wystąpieniu usługi. |
> | Akcja | Microsoft. ApiManagement/Service/skojarzeni/Write | Tworzy lub aktualizuje konfigurację IdentityProvider. lub aktualizuje istniejącą konfigurację IdentityProvider. |
> | Akcja | Microsoft. ApiManagement/Service/Issues/Read | Wyświetla kolekcję problemów w określonym wystąpieniu usługi. lub pobiera API Management Szczegóły problemu |
> | Akcja | Microsoft. ApiManagement/Service/Locations/NetworkStatus/Read | Pobiera stan dostępu do sieci dla zasobów, od których zależy usługa w danej lokalizacji. |
> | Akcja | Microsoft. ApiManagement/Service/rejestratory/usuwanie | Usuwa określony rejestrator. |
> | Akcja | Microsoft. ApiManagement/Service/rejestratory/odczyt | Wyświetla kolekcję rejestratorów w określonym wystąpieniu usługi. lub pobiera szczegóły rejestratora określony przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/rejestratory/zapis | Tworzy lub aktualizuje rejestrator. lub aktualizuje istniejący rejestrator. |
> | Akcja | Microsoft. ApiManagement/Service/managedeployments/Action | Zmień jednostkę SKU/jednostki, Dodaj/Usuń wdrożenia regionalne usługi API Management |
> | Akcja | Microsoft. ApiManagement/Service/NetworkStatus/Read | Pobiera stan dostępu do sieci dla zasobów, od których zależy usługa. |
> | Akcja | Microsoft. ApiManagement/Service/Notifications/Action | Wysyła powiadomienie do określonego użytkownika |
> | Akcja | Microsoft. ApiManagement/Service/Notifications/Read | Wyświetla kolekcję właściwości zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły powiadomienia określone przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Delete | Usuwa wiadomość e-mail z listy powiadomień. |
> | Akcja | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Read | Pobiera listę wiadomości E-mail dotyczących adresatów powiadomień subskrybowanych w ramach powiadomienia. |
> | Akcja | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Write | Dodaje adres E-mail do listy adresatów powiadomienia. |
> | Akcja | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Delete | Usuwa użytkownika API Management z listy powiadomień. |
> | Akcja | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Read | Pobiera listę użytkowników adresatów powiadomień, którzy zasubskrybują powiadomienie. |
> | Akcja | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Write | Dodaje API Management użytkownika do listy adresatów powiadomienia. |
> | Akcja | Microsoft. ApiManagement/Service/Notifications/Write | Utwórz lub zaktualizuj powiadomienia wydawcy API Management. |
> | Akcja | Microsoft. ApiManagement/Service/openidConnectProviders/Delete | Usuwa określonego dostawcę połączenia OpenID Connect dla wystąpienia usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/openidConnectProviders/Read | Listy wszystkich dostawców połączeń OpenID Connect. lub pobiera określonego dostawcę połączenia OpenID Connect. |
> | Akcja | Microsoft. ApiManagement/Service/openidConnectProviders/Write | Tworzy lub aktualizuje dostawcę połączenia OpenID Connect. lub aktualizuje określonego dostawcę połączenia OpenID Connect. |
> | Akcja | Microsoft. ApiManagement/Service/operationresults/Read | Pobiera bieżący stan długotrwałej operacji |
> | Akcja | Microsoft. ApiManagement/Service/policies/Delete | Usuwa globalną konfigurację zasad usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/policies/Read | Wyświetla wszystkie globalne definicje zasad usługi API Management. lub uzyskać definicję zasad globalnych usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/policies/Write | Tworzy lub aktualizuje globalną konfigurację zasad usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/Policy/Delete | Usuń konfigurację zasad na poziomie dzierżawy |
> | Akcja | Microsoft. ApiManagement/Service/Policy/Read | Pobierz konfigurację zasad na poziomie dzierżawy |
> | Akcja | Microsoft. ApiManagement/Service/Policy/Write | Utwórz konfigurację zasad na poziomie dzierżawy |
> | Akcja | Microsoft. ApiManagement/Service/policyDescriptions/Read | Wyświetla listę wszystkich opisów zasad. |
> | Akcja | Microsoft. ApiManagement/Service/policySnippets/Read | Wyświetla wszystkie fragmenty kodu zasad. |
> | Akcja | Microsoft. ApiManagement/Service/portalsettings/Read | Uzyskaj ustawienia logowania dla portalu lub uzyskaj ustawienia rejestracji w portalu lub uzyskaj ustawienia delegowania dla portalu. |
> | Akcja | Microsoft. ApiManagement/Service/portalsettings/Write | Zaktualizuj ustawienia logowania. lub Utwórz lub zaktualizuj ustawienia logowania. lub zaktualizuj ustawienia rejestracji lub zaktualizuj ustawienia rejestracji lub zaktualizuj ustawienia delegowania. lub Utwórz lub zaktualizuj ustawienia delegowania. |
> | Akcja | Microsoft. ApiManagement/Service/Products/API/Delete | Usuwa określony interfejs API z określonego produktu. |
> | Akcja | Microsoft. ApiManagement/Service/Products/API/odczyt | Wyświetla kolekcję interfejsów API skojarzonych z produktem. |
> | Akcja | Microsoft. ApiManagement/Service/Products/API/Write | Dodaje interfejs API do określonego produktu. |
> | Akcja | Microsoft. ApiManagement/Service/Products/Delete | Usuń produkt. |
> | Akcja | Microsoft. ApiManagement/Service/Products/Groups/Delete | Usuwa skojarzenie między określoną grupą i produktem. |
> | Akcja | Microsoft. ApiManagement/Service/Products/Groups/Read | Wyświetla listę grup deweloperów skojarzonych z określonym produktem. |
> | Akcja | Microsoft. ApiManagement/Service/Products/Groups/Write | Dodaje skojarzenie między określoną grupą deweloperów i określonym produktem. |
> | Akcja | Microsoft. ApiManagement/Service/Products/zasady/usuwanie | Usuwa konfigurację zasad w produkcie. |
> | Akcja | Microsoft. ApiManagement/Service/Products/zasady/odczyt | Pobierz konfigurację zasad na poziomie produktu. lub Pobierz konfigurację zasad na poziomie produktu. |
> | Akcja | Microsoft. ApiManagement/Service/Products/zasady/zapis | Tworzy lub aktualizuje konfigurację zasad dla produktu. |
> | Akcja | Microsoft. ApiManagement/Service/Products/Policy/Delete | Usuń konfigurację zasad na poziomie produktu |
> | Akcja | Microsoft. ApiManagement/Service/Products/Policy/odczyt | Pobierz konfigurację zasad na poziomie produktu |
> | Akcja | Microsoft. ApiManagement/Service/Products/Policy/Write | Utwórz konfigurację zasad na poziomie produktu |
> | Akcja | Microsoft. ApiManagement/Service/Products/Read | Wyświetla kolekcję produktów w określonym wystąpieniu usługi. lub pobiera szczegóły produktu określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Products/subscriptions/Read | Zawiera listę kolekcji subskrypcji dla określonego produktu. |
> | Akcja | Microsoft. ApiManagement/usługa/produkty/Tagi/Usuń | Odłącz tag od produktu. |
> | Akcja | Microsoft. ApiManagement/Service/Products/Tagi/odczyt | Wyświetla listę wszystkich tagów skojarzonych z produktem. lub Pobierz tag skojarzony z produktem. |
> | Akcja | Microsoft. ApiManagement/usługa/produkty/Tagi/zapis | Przypisz tag do produktu. |
> | Akcja | Microsoft. ApiManagement/Service/Products/Write | Tworzy lub aktualizuje produkt. lub zaktualizuj istniejące informacje o produkcie. |
> | Akcja | Microsoft. ApiManagement/Service/productsByTags/Read | Wyświetla kolekcję produktów skojarzonych ze znacznikami. |
> | Akcja | Microsoft. ApiManagement/Service/Properties/Delete | Usuwa określoną właściwość z wystąpienia usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/Properties/Read | Wyświetla kolekcję właściwości zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły właściwości określonej przez jej identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Properties/Write | Tworzy lub aktualizuje właściwość. lub aktualizuje konkretną właściwość. |
> | Akcja | Microsoft. ApiManagement/Service/limit przydziału/okresy/odczyt | Pobierz wartość licznika przydziału dla okresu |
> | Akcja | Microsoft. ApiManagement/Service/limit przydziału/okresy/zapis | Ustaw bieżącą wartość licznika przydziału |
> | Akcja | Microsoft. ApiManagement/Service/przydziały/odczyt | Pobierz wartości przydziału |
> | Akcja | Microsoft. ApiManagement/Service/przydziały/zapis | Ustaw bieżącą wartość licznika przydziału |
> | Akcja | Microsoft. ApiManagement/Service/Read | Odczytaj metadane dla wystąpienia usługi API Management |
> | Akcja | Microsoft. ApiManagement/Service/Regions/Read | Wyświetla listę wszystkich regionów świadczenia usługi Azure, w których istnieje usługa. |
> | Akcja | Microsoft. ApiManagement/Service/Reports/Read | Pobierz raport zagregowany według okresów lub Pobierz raport zagregowany według regionu geograficznego lub Pobierz raport zagregowany przez deweloperów.<br>lub uzyskać raport zagregowany według produktów.<br>lub uzyskać raport zagregowany przez interfejsy API lub uzyskać raport zagregowany według operacji lub uzyskać raport zagregowany według subskrypcji.<br>lub Pobierz żądania danych raportowania |
> | Akcja | Microsoft. ApiManagement/Service/Restore/Action | Przywracanie usługi API Management z określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Akcja | Microsoft. ApiManagement/Service/subscriptions/Delete | Usuwa określoną subskrypcję. |
> | Akcja | Microsoft. ApiManagement/Service/subscriptions/Read | Wyświetla wszystkie subskrypcje wystąpienia usługi API Management. lub pobiera określoną jednostkę subskrypcji. |
> | Akcja | Microsoft. ApiManagement/Service/subscriptions/regeneratePrimaryKey/Action | Ponownie generuje klucz podstawowy istniejącej subskrypcji wystąpienia usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/subscriptions/regenerateSecondaryKey/Action | Generuje ponownie klucz pomocniczy istniejącej subskrypcji wystąpienia usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/subscriptions/Write | Tworzy lub aktualizuje subskrypcję określonego użytkownika dla określonego produktu. lub aktualizuje szczegóły subskrypcji określonej przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/tagResources/Read | Wyświetla kolekcję zasobów skojarzonych ze znacznikami. |
> | Akcja | Microsoft. ApiManagement/Service/Tagi/Usuń | Usuwa konkretny tag wystąpienia usługi API Management. |
> | Akcja | Microsoft. ApiManagement/Service/Tags/odczyt | Wyświetla kolekcję tagów zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły tagu określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Tags/Write | Tworzy tag. lub aktualizuje szczegóły tagu określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/templates/Delete | Resetuj szablon wiadomości e-mail domyślnego API Management |
> | Akcja | Microsoft. ApiManagement/Service/templates/Read | Pobiera wszystkie szablony wiadomości e-mail lub pobiera API Management szczegóły szablonu wiadomości e-mail |
> | Akcja | Microsoft. ApiManagement/Service/templates/Write | Utwórz lub zaktualizuj szablon wiadomości e-mail API Management lub API Management szablon wiadomości e-mail |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/usuwanie | Usuń konfigurację zasad dla dzierżawy |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/wdrożenie/akcja | Uruchamia zadanie wdrażania, aby zastosować zmiany z określonej gałęzi git do konfiguracji w bazie danych. |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/operationResults/odczyt | Pobierz listę wyników operacji lub Pobierz wynik określonej operacji |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/odczyt | Pobierz definicję zasad globalnych usługi API Management. lub Uzyskaj szczegółowe informacje o dostępie dzierżawy |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/regeneratePrimaryKey/akcja | Wygeneruj ponownie podstawowy klucz dostępu |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/regenerateSecondaryKey/akcja | Ponowne generowanie pomocniczego klucza dostępu |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/Zapisz/akcja | Tworzy potwierdzeń z migawką konfiguracji do określonej gałęzi w repozytorium |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/syncState/odczyt | Pobierz stan ostatniej synchronizacji git |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/weryfikacja/akcja | Sprawdza poprawność zmian z określonej gałęzi git |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/zapis | Ustaw konfigurację zasad dla dzierżawy lub Zaktualizuj szczegóły informacji o dostępie dzierżawy |
> | Akcja | Microsoft. ApiManagement/Service/updatecertificate/Action | Przekaż certyfikat SSL dla usługi API Management |
> | Akcja | Microsoft. ApiManagement/Service/updatehostname/Action | Instalowanie, aktualizowanie lub usuwanie niestandardowych nazw domen dla usługi API Management |
> | Akcja | Microsoft. ApiManagement/Service/users/Action | Rejestrowanie nowego użytkownika |
> | Akcja | Microsoft. ApiManagement/Service/users/CONFIRMS/Send/Action | Wysyła potwierdzenie |
> | Akcja | Microsoft. ApiManagement/Service/users/Delete | Usuwa określonego użytkownika. |
> | Akcja | Microsoft. ApiManagement/Service/users/generateSsoUrl/Action | Pobiera adres URL przekierowania zawierający token uwierzytelniania do podpisywania danego użytkownika w portalu dla deweloperów. |
> | Akcja | Microsoft. ApiManagement/Service/users/Groups/Read | Wyświetla listę wszystkich grup użytkowników. |
> | Akcja | Microsoft. ApiManagement/Service/users/tożsamość/odczyt | Lista wszystkich tożsamości użytkowników. |
> | Akcja | Microsoft. ApiManagement/Service/users/klucze/odczyt | Pobierz klucze skojarzone z użytkownikiem |
> | Akcja | Microsoft. ApiManagement/Service/users/odczyt | Wyświetla kolekcję zarejestrowanych użytkowników w określonym wystąpieniu usługi. lub pobiera szczegóły użytkownika określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/users/subscriptions/Read | Zawiera listę kolekcji subskrypcji określonego użytkownika. |
> | Akcja | Microsoft. ApiManagement/Service/users/token/Action | Pobiera token autoryzacji dostępu współdzielonego dla użytkownika. |
> | Akcja | Microsoft. ApiManagement/Service/users/Write | Tworzy lub aktualizuje użytkownika. lub aktualizuje szczegóły użytkownika określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Write | Utwórz nowe wystąpienie usługi API Management Service |
> | Akcja | Microsoft. ApiManagement/Unregister/Action | Wyrejestrowywanie subskrypcji dla dostawcy zasobów Microsoft. ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft. autoryzacja

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Authorization/classicAdministrators/Delete | Usuwa administratora z subskrypcji. |
> | Akcja | Microsoft. Authorization/classicAdministrators/operationstatuses/odczyt | Pobiera Stany operacji administratora subskrypcji. |
> | Akcja | Microsoft. Authorization/classicAdministrators/Read | Odczytuje administratorów dla subskrypcji. |
> | Akcja | Microsoft. Authorization/classicAdministrators/Write | Dodaj lub zmodyfikuj administratora do subskrypcji. |
> | Akcja | Microsoft. Authorization/denyAssignments/Delete | Usuń przypisanie odmowy w określonym zakresie. |
> | Akcja | Microsoft. Authorization/denyAssignments/Read | Pobierz informacje o przypisaniu Odmów. |
> | Akcja | Microsoft. Authorization/denyAssignments/Write | Utwórz przypisanie odmowy w określonym zakresie. |
> | Akcja | Microsoft. Authorization/elevateAccess/akcja | Przyznaje administratorowi dostępu użytkownika wywołującego w zakresie dzierżawy |
> | Akcja | Microsoft. Authorization/Locks/Delete | Usuń blokady w określonym zakresie. |
> | Akcja | Microsoft. Authorization/Locks/Read | Pobiera blokady w określonym zakresie. |
> | Akcja | Microsoft. Authorization/Locks/Write | Dodaj blokady w podanym zakresie. |
> | Akcja | Microsoft. Authorization/Operations/Read | Pobiera listę operacji |
> | Akcja | Microsoft. Autoryzacja/uprawnienia/Odczyt | Wyświetla wszystkie uprawnienia, które obiekt wywołujący ma w danym zakresie. |
> | Akcja | Microsoft. Autoryzacja/zasady/Inspekcja/akcja | Działanie podejmowane w wyniku oceny Azure Policy z efektem "inspekcja" |
> | Akcja | Microsoft. authorization/policies/auditIfNotExists/Action | Działanie podejmowane w wyniku oceny Azure Policy z efektem "auditIfNotExists" |
> | Akcja | Microsoft. Autoryzacja/zasady/odmowa/akcja | Działanie podejmowane w wyniku oceny Azure Policy z efektem "odmowa" |
> | Akcja | Microsoft. authorization/policies/deployIfNotExists/Action | Działanie podejmowane w wyniku oceny Azure Policy z efektem "deployIfNotExists" |
> | Akcja | Microsoft. Authorization/policyAssignments/Delete | Usuń przypisanie zasad w określonym zakresie. |
> | Akcja | Microsoft. Authorization/policyAssignments/Read | Pobierz informacje o przypisaniu zasad. |
> | Akcja | Microsoft. Authorization/policyAssignments/Write | Utwórz przypisanie zasad w określonym zakresie. |
> | Akcja | Microsoft. Authorization/policyDefinitions/Delete | Usuń definicję zasad. |
> | Akcja | Microsoft. Authorization/policyDefinitions/Read | Pobierz informacje o definicji zasad. |
> | Akcja | Microsoft. Authorization/policyDefinitions/Write | Utwórz niestandardową definicję zasad. |
> | Akcja | Microsoft. Authorization/policySetDefinitions/Delete | Usuń definicję zestawu zasad. |
> | Akcja | Microsoft. Authorization/policySetDefinitions/Read | Pobierz informacje o definicji zestawu zasad. |
> | Akcja | Microsoft. Authorization/policySetDefinitions/Write | Utwórz niestandardową definicję zestawu zasad. |
> | Akcja | Microsoft. Authorization/providerOperations/Read | Pobierz operacje dla wszystkich dostawców zasobów, których można użyć w definicjach ról. |
> | Akcja | Microsoft. Authorization/roleAssignments/Delete | Usuń przypisanie roli w określonym zakresie. |
> | Akcja | Microsoft. Authorization/roleAssignments/Read | Pobierz informacje o przypisaniu roli. |
> | Akcja | Microsoft.Authorization/roleAssignments/write | Utwórz przypisanie roli w określonym zakresie. |
> | Akcja | Microsoft. Authorization/roleDefinitions/Delete | Usuń określoną definicję roli niestandardowej. |
> | Akcja | Microsoft. Authorization/roleDefinitions/Read | Pobierz informacje o definicji roli. |
> | Akcja | Microsoft. Authorization/roleDefinitions/Write | Utwórz lub zaktualizuj niestandardową definicję roli z określonymi uprawnieniami i możliwymi do przypisania zakresami. |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Automation/automationAccounts/agentRegistrationInformation/odczyt | Odczytaj Informacje rejestracyjne Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/agentRegistrationInformation/regenerateKey/Action | Zapisuje żądanie ponownego wygenerowania kluczy Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/certyfikaty/usuwanie | Usuwa zasób certyfikatu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Certificates/GetCount/Action | Odczytuje liczbę certyfikatów |
> | Akcja | Microsoft. Automation/automationAccounts/Certificates/Read | Pobiera zasób certyfikatu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Certificates/Write | Tworzy lub aktualizuje zasób certyfikatu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/compilationjobs/odczyt | Odczytuje kompilację Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/compilationjobs/odczyt | Odczytuje kompilację Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/compilationjobs/Write | Zapisuje kompilację Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/compilationjobs/Write | Zapisuje kompilację Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/konfiguracje/Content/Read | Odczytuje zawartość nośnika konfiguracji |
> | Akcja | Microsoft. Automation/automationAccounts/konfiguracje/usuwanie | Usuwa zawartość Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/konfiguracje/GetCount/Action | Odczytuje liczbę zawartości Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/konfiguracje/odczyt | Pobiera zawartość Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/konfiguracje/zapis | Zapisuje zawartość Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/Connections/Delete | Usuwa zasób połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Connections/akcja | Odczytuje liczbę połączeń |
> | Akcja | Microsoft. Automation/automationAccounts/Connections/Read | Pobiera zasób połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Connections/Write | Tworzy lub aktualizuje zasób połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/connectionTypes/Delete | Usuwa zasób typu połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/connectionTypes/odczyt | Pobiera zasób typu połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/connectionTypes/Write | Tworzy zasób typu połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/poświadczenia/usuwanie | Usuwa zasób poświadczenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Credentials/GetCount/Action | Odczytuje liczbę poświadczeń |
> | Akcja | Microsoft. Automation/automationAccounts/poświadczenia/odczyt | Pobiera zasób poświadczenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/poświadczenia/zapis | Tworzy lub aktualizuje zasób poświadczenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Delete | Usuwa konto Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Delete | Usuwa zasoby hybrydowego procesu roboczego elementu Runbook |
> | Akcja | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/odczyt | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/Read | Pobiera zadanie Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/Resume/Action | Wznawia zadanie Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/runbookContent/Action | Pobiera zawartość elementu Runbook Azure Automation w czasie wykonywania zadania |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/akcja | Kończy zadanie Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Pobiera strumień zadania Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Pobiera strumień zadania Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/Suspend/Action | Wstrzymuje zadanie Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/Write | Tworzy zadanie Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/jobSchedules/Delete | Usuwa harmonogram zadań Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/jobSchedules/odczyt | Pobiera harmonogram zadań Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/jobSchedules/Write | Tworzy harmonogram zadań Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/linkedWorkspace/odczyt | Pobiera obszar roboczy połączony z kontem usługi Automation |
> | Akcja | Microsoft. Automation/automationAccounts/listKeys/akcja | Odczytuje klucze dla konta usługi Automation |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/działania/odczyt | Pobiera działania Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/usuwanie | Usuwa moduł Azure Automation PowerShell |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/GetCount/Action | Pobiera liczbę modułów programu PowerShell w ramach konta usługi Automation |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/odczyt | Pobiera moduł Azure Automation PowerShell |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/zapis | Tworzy lub aktualizuje moduł Azure Automation PowerShell |
> | Akcja | Microsoft. Automation/automationAccounts/nodeConfigurations/Delete | Usuwa konfigurację węzła Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodeConfigurations/rawContent/Action | Odczytuje zawartość konfiguracji węzła Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodeConfigurations/odczyt | Odczytuje konfigurację węzła Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodeConfigurations/Write | Zapisuje konfigurację węzła Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodecounts/odczyt | Odczytuje podsumowanie liczby węzłów dla określonego typu |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Delete | Usuwa Azure Automation węzły DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Read | Odczytuje Azure Automation węzłów DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Reports/Content/Read | Odczytuje zawartość raportu Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Reports/Read | Odczytuje Azure Automation raporty DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Write | Tworzy lub aktualizuje Azure Automation węzłów DSC |
> | Akcja | Microsoft. Automation/automationAccounts/objectDataTypes/Fields/Read | Pobiera Azure Automation TypeFields |
> | Akcja | Microsoft. Automation/automationAccounts/python2Packages/Delete | Usuwa pakiet Azure Automation Python 2 |
> | Akcja | Microsoft. Automation/automationAccounts/python2Packages/odczyt | Pobiera pakiet Azure Automation Python 2 |
> | Akcja | Microsoft. Automation/automationAccounts/python2Packages/Write | Tworzy lub aktualizuje pakiet Azure Automation Python 2 |
> | Akcja | Microsoft. Automation/automationAccounts/python3Packages/Delete | Usuwa pakiet Azure Automation Python 3 |
> | Akcja | Microsoft. Automation/automationAccounts/python3Packages/odczyt | Pobiera pakiet Azure Automation Python 3 |
> | Akcja | Microsoft. Automation/automationAccounts/python3Packages/Write | Tworzy lub aktualizuje pakiet Azure Automation Python 3 |
> | Akcja | Microsoft. Automation/automationAccounts/Read | Pobiera konto Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Content/Read | Pobiera zawartość elementu Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Delete | Usuwa element Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Draft/Content/Write | Tworzy zawartość wersji roboczej elementu Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Draft/operationResults/Read | Pobiera wyniki operacji wersji roboczej elementu Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Draft/Read | Pobiera wersję roboczą elementu Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/Read | Pobiera zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/Resume/Action | Wznawia zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/testJob/Stop/Action | Kończy zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/Suspend/Action | Wstrzymuje zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/testJob/Write | Tworzy zadanie testowe wersji roboczej elementu Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/undoEdit/Action | Cofnij edycję wersji roboczej elementu Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/GetCount/Action | Pobiera liczbę elementów Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Publish/Action | Publikuje wersję roboczą elementu Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Read | Pobiera element Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Write | Tworzy lub aktualizuje element Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/harmonogramy/usuwanie | Usuwa zasób harmonogramu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Schedules/GetCount/Action | Pobiera liczbę harmonogramów Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Schedules/Read | Pobiera zasób harmonogramu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Schedules/Write | Tworzy lub aktualizuje zasób harmonogramu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/odczyt | Pobiera Azure Automation uruchomienie komputera z konfiguracją aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/odczyt | Pobiera Azure Automation uruchomienie konfiguracji aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Delete | Usuwa Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Delete | Usuwa Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/odczyt | Pobiera Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/odczyt | Pobiera Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Tworzy lub aktualizuje Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Tworzy lub aktualizuje Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/Statystyka/odczyt | Pobiera statystyki Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/updateDeploymentMachineRuns/odczyt | Pobierz maszynę wdrożenia aktualizacji Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/updateManagementPatchJob/odczyt | Pobiera zadanie aktualizacji Azure Automation Update Management |
> | Akcja | Microsoft. Automation/automationAccounts/Usages/Read | Pobiera Azure Automation użycie |
> | Akcja | Microsoft. Automation/automationAccounts/zmienne/usuwanie | Usuwa zasób zmiennej Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/zmienne/odczyt | Odczytuje Azure Automation zasób zmiennej |
> | Akcja | Microsoft. Automation/automationAccounts/zmienne/zapis | Tworzy lub aktualizuje zasób zmiennej Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/obserwatorzy/usuwanie | Usuwanie zadania obserwatora Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/obserwatorzy/odczyt | Pobiera Azure Automation zadanie obserwatora |
> | Akcja | Microsoft. Automation/automationAccounts/obserwatorzy/uruchamianie/akcja | Uruchamianie zadania obserwatora Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/obserwatorzy/akcja | Zatrzymywanie zadania obserwatora Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/zegarki/strumienie/odczyt | Pobiera strumień zadania obserwatora Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/WATCHS/watcherActions/Delete | Usuwanie akcji zadania obserwatora Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/WATCHS/watcherActions/Read | Pobiera Azure Automation akcje zadania obserwatora |
> | Akcja | Microsoft. Automation/automationAccounts/WATCHS/watcherActions/Write | Tworzenie akcji zadania obserwatora Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/obserwatorzy/zapis | Tworzy zadanie obserwatora Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/webhooks/Action | Generuje identyfikator URI dla Azure Automation elementu webhook |
> | Akcja | Microsoft. Automation/automationAccounts/webhooks/Delete | Usuwa element webhook Azure Automation  |
> | Akcja | Microsoft. Automation/automationAccounts/webhooks/Read | Odczytuje Azure Automation elementu webhook |
> | Akcja | Microsoft. Automation/automationAccounts/webhooks/Write | Tworzy lub aktualizuje Azure Automation elementu webhook |
> | Akcja | Microsoft. Automation/automationAccounts/Write | Tworzy lub aktualizuje konto Azure Automation |
> | Akcja | Microsoft. Automation/Operations/Read | Pobiera dostępne operacje dla Azure Automation zasobów |
> | Akcja | Microsoft. Automation/rejestrowanie/akcja | Rejestruje subskrypcję w usłudze Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft. usługi azureactivedirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. usługi azureactivedirectory/b2cDirectories/Delete | Usuń zasób katalogu B2C |
> | Akcja | Microsoft. usługi azureactivedirectory/b2cDirectories/odczyt | Wyświetl zasób katalogu B2C |
> | Akcja | Microsoft. usługi azureactivedirectory/b2cDirectories/Write | Utwórz lub zaktualizuj zasób katalogu B2C |
> | Akcja | Microsoft. usługi azureactivedirectory/b2ctenants/odczyt | Wyświetla wszystkie dzierżawy B2C, w których użytkownik jest członkiem |
> | Akcja | Microsoft. usługi azureactivedirectory/Operations/Read | Przeczytaj wszystkie operacje interfejsu API dostępne dla dostawcy zasobów Microsoft. usługi azureactivedirectory |
> | Akcja | Microsoft. usługi azureactivedirectory/Register/Action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. usługi azureactivedirectory |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. AzureStack/Operations/Read | Pobiera właściwości operacji dostawcy zasobów |
> | Akcja | Microsoft. AzureStack/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. AzureStack |
> | Akcja | Microsoft. AzureStack/registrations/customerSubscriptions/Delete | Usuwa subskrypcję klienta Azure Stack |
> | Akcja | Microsoft. AzureStack/registrations/customerSubscriptions/Read | Pobiera właściwości subskrypcji klienta Azure Stack |
> | Akcja | Microsoft. AzureStack/registrations/customerSubscriptions/Write | Tworzy lub aktualizuje subskrypcję klienta Azure Stack |
> | Akcja | Microsoft. AzureStack/rejestracje/usuwanie | Usuwa rejestrację Azure Stack |
> | Akcja | Microsoft.AzureStack/registrations/getActivationKey/action | Pobiera najnowszy klucz aktywacji Azure Stack |
> | Akcja | Microsoft. AzureStack/rejestracje/produkty/listDetails/akcja | Pobiera rozszerzone szczegóły dotyczące produktu Azure Stack Marketplace |
> | Akcja | Microsoft. AzureStack/rejestracje/produkty/odczyt | Pobiera właściwości produktu Azure Stack Marketplace |
> | Akcja | Microsoft. AzureStack/rejestracje/produkty/uploadProductLog/akcja | Rejestrowanie stanu i sygnatury czasowej operacji produktu Azure Stack Marketplace |
> | Akcja | Microsoft. AzureStack/rejestracje/odczyt | Pobiera właściwości rejestracji Azure Stack |
> | Akcja | Microsoft. AzureStack/rejestracje/zapis | Tworzy lub aktualizuje rejestrację Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Batch/batchAccounts/Applications/Delete | Usuwa aplikację |
> | Akcja | Microsoft. Batch/batchAccounts/Applications/Read | Wyświetla listę aplikacji lub pobiera właściwości aplikacji |
> | Akcja | Microsoft. Batch/batchAccounts/Applications/Versions/Activate/Action | Aktywuje pakiet aplikacji |
> | Akcja | Microsoft. Batch/batchAccounts/Applications/Versions/Delete | Usuwa pakiet aplikacji |
> | Akcja | Microsoft. Batch/batchAccounts/Applications/Versions/Read | Pobiera właściwości pakietu aplikacji |
> | Akcja | Microsoft. Batch/batchAccounts/Applications/Versions/Write | Tworzy nowy pakiet aplikacji lub aktualizuje istniejący pakiet aplikacji |
> | Akcja | Microsoft. Batch/batchAccounts/Applications/Write | Tworzy nową aplikację lub aktualizuje istniejącą aplikację |
> | Akcja | Microsoft. Batch/batchAccounts/certificateOperationResults/odczyt | Pobiera wyniki długotrwałej operacji certyfikatu na koncie wsadowym |
> | Akcja | Microsoft. Batch/batchAccounts/Certificates/cancelDelete/Action | Anuluje nieudane usunięcie certyfikatu na koncie wsadowym |
> | Akcja | Microsoft. Batch/batchAccounts/Certificates/Delete | Usuwa certyfikat z konta usługi Batch |
> | Akcja | Microsoft. Batch/batchAccounts/Certificates/Read | Wyświetla listę certyfikatów na koncie wsadowym lub pobiera właściwości certyfikatu |
> | Akcja | Microsoft. Batch/batchAccounts/Certificates/Write | Tworzy nowy certyfikat na koncie wsadowym lub aktualizuje istniejący certyfikat |
> | Akcja | Microsoft. Batch/batchAccounts/Delete | Usuwa konto wsadowe |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Delete | Usuwa zadanie z konta usługi Batch |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Read | Wyświetla listę zadań na koncie wsadowym lub pobiera właściwości zadania |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Write | Tworzy nowe zadanie na koncie wsadowym lub aktualizuje istniejące zadanie |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Delete | Usuwa harmonogram zadań z konta usługi Batch |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/odczyt | Wyświetla listę harmonogramów zadań na koncie wsadowym lub pobiera właściwości harmonogramu zadań |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Write | Tworzy nowy harmonogram zadań na koncie wsadowym lub aktualizuje istniejący harmonogram zadań |
> | Akcja | Microsoft. Batch/batchAccounts/ListKeys/Action | Wyświetla klucze dostępu dla konta usługi Batch |
> | Akcja | Microsoft. Batch/batchAccounts/operationResults/odczyt | Pobiera wyniki długotrwałej operacji konta partii |
> | Akcja | Microsoft. Batch/batchAccounts/poolOperationResults/odczyt | Pobiera wyniki długotrwałej operacji puli na koncie wsadowym |
> | Akcja | Microsoft. Batch/batchAccounts/pule/usuwanie | Usuwa pulę z konta usługi Batch |
> | Akcja | Microsoft. Batch/batchAccounts/pule/disableAutoscale/akcja | Wyłącza automatyczne skalowanie puli kont usługi Batch |
> | Akcja | Microsoft. Batch/batchAccounts/pule/odczyt | Wyświetla listę pul na koncie wsadowym lub pobiera właściwości puli |
> | Akcja | Microsoft. Batch/batchAccounts/pule/stopResize/akcja | Kończy trwającą operację zmiany rozmiaru w puli kont wsadowych |
> | Akcja | Microsoft. Batch/batchAccounts/pule/zapis | Tworzy nową pulę na koncie wsadowym lub aktualizuje istniejącą pulę |
> | Akcja | Microsoft. Batch/batchAccounts/Read | Wyświetla listę kont wsadowych lub pobiera właściwości konta w usłudze Batch |
> | Akcja | Microsoft. Batch/batchAccounts/regeneratekeys/Action | Generuje ponownie klucze dostępu dla konta usługi Batch |
> | Akcja | Microsoft. Batch/batchAccounts/syncAutoStorageKeys/Action | Synchronizuje klucze dostępu dla konta usługi autostorage skonfigurowanego dla konta usługi Batch |
> | Akcja | Microsoft. Batch/batchAccounts/Write | Tworzy nowe konto wsadowe lub aktualizuje istniejące konto w usłudze Batch |
> | Akcja | Microsoft. Batch/Locations/accountOperationResults/Read | Pobiera wyniki długotrwałej operacji konta partii |
> | Akcja | Microsoft. Batch/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft. Batch/lokalizacje/przydziały/odczyt | Pobiera przydziały usługi Batch dla określonej subskrypcji w określonym regionie platformy Azure |
> | Akcja | Microsoft. Batch/Operations/Read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. Batch |
> | Akcja | Microsoft. Batch/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi Batch i umożliwia tworzenie kont usługi Batch |
> | Akcja | Microsoft. Batch/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Batch uniemożliwiającą tworzenie kont usługi Batch |

## <a name="microsoftbilling"></a>Microsoft. rozliczenia

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. rozliczenia/billingAccounts/umowy/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingPermissions/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/billingPermissions/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/klienci/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/faktury/arkusza cen/Pobierz/akcja |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/billingPermissions/Read |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/Przenieś/akcja |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/transfer/akcja |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/akcja |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/produkty/Przenieś/akcja |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/produkty/transfer/akcja |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/produkty/validateMoveEligibility/akcja |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/invoiceSections/zapis |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/arkusza cen/pobieranie/akcja |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/zapis |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingProfiles/zapis |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/billingSubscriptions/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/klienci/billingPermissions/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/klienci/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/działy/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/enrollmentAccounts/billingPermissions/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/enrollmentAccounts/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/enrollmentDepartments/billingPermissions/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/akcja |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/produkty/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/odczyt |  |
> | Akcja | Microsoft. rozliczenia/billingAccounts/zapis |  |
> | Akcja | Microsoft. rozliczenia/działy/odczyt |  |
> | Akcja | Microsoft. rozliczenia/rejestr/akcja |  |
> | Akcja | Microsoft. rozliczenia/validateAddress/akcja |  |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. BingMaps/mapApis/Delete | Operacja usuwania |
> | Akcja | Microsoft. BingMaps/mapApis/listSecrets/akcja | Wystaw wpisy tajne |
> | Akcja | Microsoft. BingMaps/mapApis/listSingleSignOnToken/akcja | Odczytaj Token autoryzacji logowania jednokrotnego dla zasobu |
> | Akcja | Microsoft. BingMaps/mapApis/odczyt | Operacja odczytu |
> | Akcja | Microsoft. BingMaps/mapApis/regenerateKey/akcja | Generuje ponownie klucz |
> | Akcja | Microsoft. BingMaps/mapApis/Write | Operacja zapisu |
> | Akcja | Microsoft. BingMaps/Operations/Read | Opis operacji. |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/Delete | Usuwa istniejącego członka łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/listApiKeys/akcja | Pobiera lub Wyświetla istniejące klucze interfejsu API elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/odczyt | Pobiera lub Wyświetla istniejące elementy członkowskie łańcucha bloków. |
> | Akcja dataaction | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Connect/Action | Nawiązuje połączenie z węzłem transakcji elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Delete | Usuwa istniejący węzeł transakcji elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/listApiKeys/Action | Pobiera lub Wyświetla istniejące klucze interfejsu API węzła transakcji elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Read | Pobiera lub Wyświetla istniejące węzły transakcji składowych łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Write | Tworzy lub aktualizuje węzeł transakcji elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/Write | Tworzy lub aktualizuje element członkowski łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/Locations/blockchainMemberOperationResults/Read | Pobiera wyniki operacji elementów członkowskich łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa zasobu jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft. łańcucha bloków/Operations/Read | Wyświetl listę wszystkich operacji w dostawcy zasobów programu Microsoft łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/Register/Action | Rejestruje subskrypcję dostawcy zasobów łańcucha bloków. |

## <a name="microsoftblueprint"></a>Microsoft. plan

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. plan/blueprintAssignments/assignmentOperations/odczyt | Odczytaj wszelkie artefakty strategii |
> | Akcja | Microsoft. plan/blueprintAssignments/usuwanie | Usuń wszelkie artefakty strategii |
> | Akcja | Microsoft. plan/blueprintAssignments/odczyt | Odczytaj wszelkie artefakty strategii |
> | Akcja | Microsoft. plan/blueprintAssignments/whoisblueprint/akcja | Pobierz identyfikator obiektu podmiotu zabezpieczeń usługi Azure Plans. |
> | Akcja | Microsoft. plan/blueprintAssignments/zapis | Utwórz lub zaktualizuj wszelkie artefakty strategii |
> | Akcja | Microsoft. plan/plany/artefakty/usuwanie | Usuń wszelkie artefakty strategii |
> | Akcja | Microsoft. plan/plany/artefakty/odczyt | Odczytaj wszelkie artefakty strategii |
> | Akcja | Microsoft. plan/plany/artefakty/zapis | Utwórz lub zaktualizuj wszelkie artefakty strategii |
> | Akcja | Microsoft. plan/plany/usuwanie | Usuń wszystkie plany |
> | Akcja | Microsoft. plan/plany/odczyt | Przeczytaj wszelkie plany |
> | Akcja | Microsoft. plan/plany/wersje/artefakty/odczyt | Odczytaj wszelkie artefakty strategii |
> | Akcja | Microsoft. plan/plany/wersje/usuwanie | Usuń wszystkie plany |
> | Akcja | Microsoft. plan/plany/wersje/odczyt | Przeczytaj wszelkie plany |
> | Akcja | Microsoft. plan/plany/wersje/zapis | Utwórz lub zaktualizuj wszelkie plany |
> | Akcja | Microsoft. plan/plany/zapis | Utwórz lub zaktualizuj wszelkie plany |
> | Akcja | Microsoft. plan/rejestr/akcja | Rejestruje dostawcę zasobów usługi Azure Plans |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. BotService/botServices/Channels/Delete | Usuwanie usługi bot |
> | Akcja | Microsoft. BotService/botServices/Channels/Read | Odczytaj usługę bot |
> | Akcja | Microsoft. BotService/botServices/Channels/Write | Napisz usługę bot |
> | Akcja | Microsoft. BotService/botServices/Connections/Delete | Usuwanie usługi bot |
> | Akcja | Microsoft. BotService/botServices/Connections/Read | Odczytaj usługę bot |
> | Akcja | Microsoft. BotService/botServices/Connections/Write | Napisz usługę bot |
> | Akcja | Microsoft. BotService/botServices/Delete | Usuwanie usługi bot |
> | Akcja | Microsoft. BotService/botServices/odczyt | Odczytaj usługę bot |
> | Akcja | Microsoft. BotService/botServices/Write | Napisz usługę bot |
> | Akcja | Microsoft. BotService/Locations/operationresults/Read | Odczytywanie stanu operacji asynchronicznej |
> | Akcja | Microsoft. BotService/Operations/Read | Odczytaj operacje dla wszystkich typów zasobów |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. cache/checknameavailability/akcja | Sprawdza, czy nazwa jest dostępna do użycia z nowym Redis Cache |
> | Akcja | Microsoft. cache/Locations/operationresults/Read | Pobiera wynik długotrwałej operacji, dla której nagłówek "Location" został wcześniej zwrócony do klienta |
> | Akcja | Microsoft. cache/Operations/Read | Wyświetla listę operacji obsługiwanych przez dostawcę "Microsoft. cache". |
> | Akcja | Microsoft. cache/Redis/Delete | Usuń cały Redis Cache |
> | Akcja | Microsoft. cache/Redis/eksport/akcja | Eksportuj dane Redis do przedprawionych obiektów blob magazynu w określonym formacie |
> | Akcja | Microsoft. cache/Redis/firewallRules/Delete | Usuwanie reguł zapory adresów IP Redis Cache |
> | Akcja | Microsoft. cache/Redis/firewallRules/odczyt | Pobieranie reguł zapory adresów IP Redis Cache |
> | Akcja | Microsoft. cache/Redis/firewallRules/zapis | Edytowanie reguł zapory adresów IP Redis Cache |
> | Akcja | Microsoft. cache/Redis/forceReboot/akcja | Wymuś ponowne uruchomienie wystąpienia pamięci podręcznej, potencjalnie z utratą danych. |
> | Akcja | Microsoft. cache/Redis/import/akcja | Importuj dane określonego formatu z wielu obiektów BLOB do Redis |
> | Akcja | Microsoft. cache/Redis/LinkedServers/Delete | Usuwanie połączonego serwera z Redis Cache |
> | Akcja | Microsoft. cache/Redis/LinkedServers/odczyt | Pobierz połączone serwery skojarzone z pamięcią podręczną Redis. |
> | Akcja | Microsoft. cache/Redis/LinkedServers/zapis | Dodawanie połączonego serwera do Redis Cache |
> | Akcja | Microsoft. cache/Redis/listKeys/akcja | Wyświetlanie wartości Redis Cache kluczy dostępu w portalu zarządzania |
> | Akcja | Microsoft. cache/Redis/listUpgradeNotifications/odczyt | Wyświetl listę najnowszych powiadomień dotyczących uaktualnienia dla dzierżawy pamięci podręcznej. |
> | Akcja | Microsoft. cache/Redis/metricDefinitions/odczyt | Pobiera dostępne metryki dla Redis Cache |
> | Akcja | Microsoft. cache/Redis/patchSchedules/Delete | Usuwanie harmonogramu poprawek Redis Cache |
> | Akcja | Microsoft. cache/Redis/patchSchedules/odczyt | Pobiera harmonogram stosowania poprawek Redis Cache |
> | Akcja | Microsoft. cache/Redis/patchSchedules/zapis | Modyfikowanie harmonogramu poprawek Redis Cache |
> | Akcja | Microsoft. cache/Redis/odczyt | Wyświetlanie ustawień i konfiguracji Redis Cache w portalu zarządzania |
> | Akcja | Microsoft. cache/Redis/regenerateKey/akcja | Zmiana wartości Redis Cache kluczy dostępu w portalu zarządzania |
> | Akcja | Microsoft. cache/Redis/Start/akcja | Uruchom wystąpienie pamięci podręcznej. |
> | Akcja | Microsoft. cache/Redis/Stop/akcja | Zatrzymaj wystąpienie pamięci podręcznej. |
> | Akcja | Microsoft. cache/Redis/Write | Modyfikowanie ustawień i konfiguracji Redis Cache w portalu zarządzania |
> | Akcja | Microsoft. cache/Register/Action | Rejestruje dostawcę zasobów "Microsoft. cache" z subskrypcją |
> | Akcja | Microsoft. cache/Unregister/akcja | Wyrejestrowuje dostawcę zasobów "Microsoft. cache" z subskrypcją |

## <a name="microsoftcapacity"></a>Microsoft. Pojemność

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Pojemność/appliedreservations/odczyt | Odczytuj wszystkie rezerwacje |
> | Akcja | Microsoft. Pojemność/calculateexchange/akcja | Oblicza kwotę i cenę wymiany nowych zakupów i zwraca błędy zasad. |
> | Akcja | Microsoft. Pojemność/calculateprice/akcja | Oblicz dowolną cenę rezerwacji |
> | Akcja | Microsoft. Pojemność/wykazy/odczyt | Odczytaj wykaz rezerwacji |
> | Akcja | Microsoft. Pojemność/checkoffers/akcja | Sprawdź wszystkie oferty subskrypcji |
> | Akcja | Microsoft. Pojemność/checkscopes/akcja | Sprawdź dowolną subskrypcję |
> | Akcja | Microsoft. Pojemność/commercialreservationorders/odczyt | Pobieranie zamówień rezerwacji utworzonych w dowolnej dzierżawie |
> | Akcja | Microsoft. Pojemność/wymiana/akcja | Wymiana dowolnej rezerwacji |
> | Akcja | Microsoft. Pojemność/operacje/odczyt | Odczytaj dowolną operację |
> | Akcja | Microsoft. Pojemność/rejestr/akcja | Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie zasobów pojemności. |
> | Akcja | Microsoft. Pojemność/reservationorders/akcja | Aktualizowanie dowolnej rezerwacji |
> | Akcja | Microsoft. Pojemność/reservationorders/availablescopes/akcja | Znajdowanie dowolnego dostępnego zakresu |
> | Akcja | Microsoft. Pojemność/reservationorders/calculaterefund/akcja | Oblicza kwotę zwrotu i cenę nowego zakupu oraz zwraca błędy zasad. |
> | Akcja | Microsoft. Pojemność/reservationorders/usuwanie | Usuń dowolne zastrzeżenie |
> | Akcja | Microsoft. Pojemność/reservationorders/scalanie/akcja | Scalanie wszelkich rezerwacji |
> | Akcja | Microsoft. Pojemność/reservationorders/odczyt | Odczytuj wszystkie rezerwacje |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/akcja | Aktualizowanie dowolnej rezerwacji |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/availablescopes/akcja | Znajdowanie dowolnego dostępnego zakresu |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/usuwanie | Usuń dowolne zastrzeżenie |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/odczyt | Odczytuj wszystkie rezerwacje |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/poprawki/odczyt | Odczytuj wszystkie rezerwacje |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/zapis | Utwórz dowolną rezerwację |
> | Akcja | Microsoft. Pojemność/reservationorders/powrót/akcja | Zwróć wszelkie rezerwacje |
> | Akcja | Microsoft. Pojemność/reservationorders/Split/akcja | Podziel wszystkie rezerwacje |
> | Akcja | Microsoft. Pojemność/reservationorders/swap/akcja | Zamień wszystkie rezerwacje |
> | Akcja | Microsoft. Pojemność/reservationorders/zapis | Utwórz dowolną rezerwację |
> | Akcja | Microsoft. Pojemność/dzierżawcy/rejestr/akcja | Rejestracja dowolnej dzierżawy |
> | Akcja | Microsoft. Pojemność/Wyrejestrowanie/akcja | Wyrejestrowywanie dzierżawy |
> | Akcja | Microsoft. Pojemność/validatereservationorder/akcja | Weryfikowanie wszelkich rezerwacji |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. CDN/CheckNameAvailability/akcja |  |
> | Akcja | Microsoft. CDN/CheckResourceUsage/akcja |  |
> | Akcja | Microsoft. CDN/edgenodes/Delete |  |
> | Akcja | Microsoft. CDN/edgenodes/odczyt |  |
> | Akcja | Microsoft. CDN/edgenodes/Write |  |
> | Akcja | Microsoft. CDN/operationresults/Delete |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/CheckResourceUsage/Action |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/Delete |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/CheckResourceUsage/Action |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Delete |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/Action |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/Action |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Read |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Write |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/Delete |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/obciążenie/akcja |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Delete |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Read |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Write |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/przeczyszczanie/akcja |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/Read |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/Start/akcja |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/Stop/akcja |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/ValidateCustomDomain/Action |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/zapis |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/GenerateSsoUri/Action |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/GetSupportedOptimizationTypes/Action |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/odczyt |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/zapis |  |
> | Akcja | Microsoft. CDN/operationresults/odczyt |  |
> | Akcja | Microsoft. CDN/operationresults/Write |  |
> | Akcja | Microsoft. CDN/Operations/Read |  |
> | Akcja | Microsoft. CDN/profile/CheckResourceUsage/akcja |  |
> | Akcja | Microsoft. CDN/profile/usuwanie |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/CheckResourceUsage/akcja |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/customdomains/Delete |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/customdomains/DisableCustomHttps/Action |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/customdomains/EnableCustomHttps/Action |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/customdomains/odczyt |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/customdomains/Write |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/usuwanie |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/obciążenie/akcja |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/pochodzenia/usuwanie |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/źródła/odczyt |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/źródła/zapis |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/Przeczyść/akcja |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/odczyt |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/uruchomienie/akcja |  |
> | Akcja | Microsoft. CDN/profile/Endpoints/akcja |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/ValidateCustomDomain/akcja |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/zapis |  |
> | Akcja | Microsoft. CDN/profile/GenerateSsoUri/akcja |  |
> | Akcja | Microsoft. CDN/profile/GetSupportedOptimizationTypes/akcja |  |
> | Akcja | Microsoft. CDN/profile/odczyt |  |
> | Akcja | Microsoft. CDN/profile/zapis |  |
> | Akcja | Microsoft. CDN/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi CDN i umożliwia tworzenie profilów sieci CDN. |
> | Akcja | Microsoft. CDN/ValidateProbe/akcja |  |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/Certificates/Delete | Usuń istniejący certyfikat |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/Certificates/Read | Pobieranie listy certyfikatów |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/Certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejący |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/Delete | Usuń istniejący AppServiceCertificate |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/odczyt | Pobierz listę CertificateOrders |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/Reissue/akcja | Wydaj ponownie istniejące zamówienia certyfikatu |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/Renew/akcja | Odnów istniejący zamówienia certyfikatu |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/resendEmail/akcja | Wyślij ponownie wiadomość e-mail z certyfikatem |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/resendRequestEmails/akcja | Wyślij ponownie wiadomości e-mail z żądaniem na inny adres e-mail |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/resendRequestEmails/akcja | Pobierz pieczęć witryny dla wystawionego Certyfikat usługi App Service |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/retrieveCertificateActions/akcja | Pobieranie listy akcji certyfikatów |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/retrieveEmailHistory/akcja | Pobierz historię poczty e-mail certyfikatu |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/verifyDomainOwnership/akcja | Weryfikuj własność domeny |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/Write | Dodaj nowy zamówienia certyfikatu lub zaktualizuj istniejący |
> | Akcja | Microsoft. CertificateRegistration/Operations/Read | Wyświetl listę wszystkich operacji z rejestracji certyfikatu usługi App Service |
> | Akcja | Microsoft. CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Zainicjuj obsługę główną usługi dla jednostki aplikacji usługi |
> | Akcja | Microsoft. CertificateRegistration/Register/Action | Zarejestruj dostawcę zasobów Microsoft Certificates dla subskrypcji |
> | Akcja | Microsoft. CertificateRegistration/validateCertificateRegistrationInformation/Action | Weryfikowanie obiektu zakupu certyfikatu bez jego przesyłania |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ClassicCompute/możliwości/odczyt | Pokazuje możliwości |
> | Akcja | Microsoft. ClassicCompute/checkDomainNameAvailability/Action | Sprawdza dostępność danej nazwy domeny. |
> | Akcja | Microsoft. ClassicCompute/checkDomainNameAvailability/odczyt | Pobiera dostępność danej nazwy domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Active/Write | Ustawia nazwę aktywnej domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/availabilitySets/Read | Pokaż zestaw dostępności dla zasobu. |
> | Akcja | Microsoft. ClassicCompute/domainNames/możliwości/odczyt | Pokazuje możliwości nazw domen |
> | Akcja | Microsoft. ClassicCompute/domainNames/Delete | Usuń nazwy domen dla zasobów. |
> | Akcja | Microsoft. ClassicCompute/domainNames/deploymentslots/Read | Pokazuje miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/deploymentslots/role/odczyt | Pobierz rolę w miejscu wdrożenia nazwy domeny |
> | Akcja | Microsoft. ClassicCompute/domainNames/deploymentslots/role/roleinstances/Read | Pobierz wystąpienie roli dla roli w miejscu wdrożenia nazwy domeny |
> | Akcja | Microsoft. ClassicCompute/domainNames/deploymentslots/State/Read | Pobierz stan miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/deploymentslots/State/Write | Dodaj stan miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/deploymentslots/upgradedomain/Read | Pobierz domenę uaktualnienia dla miejsca wdrożenia w nazwie domeny |
> | Akcja | Microsoft. ClassicCompute/domainNames/deploymentslots/upgradedomain/Write | Aktualizuj domenę uaktualnienia dla miejsca wdrożenia w nazwie domeny |
> | Akcja | Microsoft. ClassicCompute/domainNames/deploymentslots/Write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft. ClassicCompute/domainNames/rozszerzenia/usuwanie | Usuń rozszerzenia nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Extensions/operationStatuses/Read | Odczytuje stan operacji dla rozszerzeń nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Extensions/odczyt | Zwraca rozszerzenia nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Extensions/Write | Dodaj rozszerzenia nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Delete | Usuń nowe wewnętrzne równoważenie obciążenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/Read | Odczytuje stan operacji dla wewnętrznych modułów równoważenia obciążenia nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Read | Pobiera wewnętrzne moduły równoważenia obciążenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Write | Tworzy nowe wewnętrzne równoważenie obciążenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/Read | Odczytuje stan operacji dla zestawów punktów końcowych z równoważeniem obciążenia nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Read | Pobierz zestawy punktów końcowych ze zrównoważonym obciążeniem. |
> | Akcja | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Write | Dodaj zestaw punktów końcowych ze zrównoważonym obciążeniem. |
> | Akcja | Microsoft. ClassicCompute/domainNames/operationstatuses/Read | Pobierz stan operacji dla nazwy domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/operationStatuses/Read | Odczytuje stan operacji dla rozszerzeń nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/odczyt | Zwróć nazwy domen dla zasobów. |
> | Akcja | Microsoft. ClassicCompute/domainNames/servicecertificates/Delete | Usuń używane certyfikaty usługi. |
> | Akcja | Microsoft. ClassicCompute/domainNames/servicecertificates/operationStatuses/Read | Odczytuje stan operacji dla certyfikatów usługi nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/servicecertificates/Read | Zwraca używane certyfikaty usługi. |
> | Akcja | Microsoft. ClassicCompute/domainNames/servicecertificates/Write | Dodaj lub zmodyfikuj używane certyfikaty usługi. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/abortMigration/Action | Przerywa migrację miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/commitMigration/Action | Zatwierdza migrację miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/Delete | Usuwa określone miejsce wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/operationStatuses/Read | Odczytuje stan operacji dla gniazd nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/prepareMigration/Action | Przygotowuje migrację miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/Read | Pokazuje miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/extensionReferences/Delete | Usuń odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/extensionReferences/operationStatuses/Read | Odczytuje stan operacji dla odwołań rozszerzenia ról gniazd nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/extensionReferences/Read | Zwraca odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/extensionReferences/Write | Dodaj lub zmodyfikuj odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/metricdefinitions/Read | Pobierz definicję metryki roli dla nazwy domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/Metrics/Read | Pobierz metrykę roli dla nazwy domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/operationstatuses/Read | Pobierz stan operacji dla roli gniazda nazwy domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/Read | Uzyskaj rolę dla miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/downloadremotedesktopconnectionfile/Action | Pobiera plik połączenia pulpitu zdalnego dla wystąpienia roli w roli gniazda nazwy domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/operationStatuses/Read | Pobiera stan operacji dla wystąpienia roli w ramach roli gniazda nazwy domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/Read | Pobierz wystąpienie roli. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/Rebuild/Action | Odbudowuje wystąpienie roli. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/Reimage/Action | Odtwarza z obrazu wystąpienie roli. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/roleInstances/restart/Action | Uruchamia ponownie wystąpienia roli. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/SKU/odczyt | Pobierz jednostkę SKU roli dla miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/role/Write | Dodaj rolę dla miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/Start/akcja | Uruchamia miejsce wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/State/Start/Write | Zmienia stan miejsca wdrożenia na zatrzymane. |
> | Akcja | Microsoft. ClassicCompute/domainNames/szczeliny/stan/Zatrzymaj/Zapisz | Zmienia stan miejsca wdrożenia na rozpoczęte. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/akcja/Stop | Wstrzymuje miejsce wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/upgradeDomain/Write | Przeprowadź uaktualnienie domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/validateMigration/Action | Sprawdza poprawność migracji miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/Write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft. ClassicCompute/domainNames/swap/Action | Zamienia miejsce przejściowe na miejsce produkcyjne. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Write | Dodaj lub zmodyfikuj nazwy domen dla zasobów. |
> | Akcja | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Przenieś wszystkie zasoby klasyczne do innej subskrypcji. |
> | Akcja | Microsoft. ClassicCompute/operatingSystemFamilies/odczyt | Wyświetla listę rodzin systemów operacyjnych gościa dostępnych w Microsoft Azure, a także listę wersji systemu operacyjnego dostępnych dla każdej rodziny. |
> | Akcja | Microsoft. ClassicCompute/operatingSystems/odczyt | Wyświetla listę wersji systemu operacyjnego gościa, które są obecnie dostępne w Microsoft Azure. |
> | Akcja | Microsoft. ClassicCompute/Operations/Read | Pobiera listę operacji. |
> | Akcja | Microsoft. ClassicCompute/operationStatuses/odczyt | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft. ClassicCompute/przydziały/odczyt | Pobieranie przydziału dla subskrypcji. |
> | Akcja | Microsoft. ClassicCompute/Register/Action | Zarejestruj się w ramach obliczeń klasycznych |
> | Akcja | Microsoft. ClassicCompute/resourceTypes/SKU/odczyt | Pobiera listę jednostek SKU dla obsługiwanych typów zasobów. |
> | Akcja | Microsoft. ClassicCompute/validateSubscriptionMoveAvailability/Action | Sprawdź dostępność subskrypcji dla klasycznej operacji przenoszenia. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/Read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszynami wirtualnymi. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Read | Pobiera sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Write | Dodaje sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Pobiera możliwe operacje asynchroniczne |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/attachDisk/akcja | Dołącza dysk danych do maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/przechwytywanie/akcja | Przechwyć maszynę wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Delete | Usuwa maszyny wirtualne. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/detachDisk/akcja | Odłącza dysk danych z maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/diagnosticsettings/Read | Pobierz ustawienia diagnostyki maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/disks/Read | Pobiera listę dysków z danymi |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/akcja | Pobiera plik RDP dla maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Extensions/operationStatuses/Read | Odczytuje stan operacji dla rozszerzeń maszyn wirtualnych. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Extensions/odczyt | Pobiera rozszerzenie maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Extensions/Write | Umieszcza rozszerzenie maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/metricdefinitions/Read | Pobierz definicję metryki maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Metrics/odczyt | Pobiera metryki. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/Read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszynami wirtualnymi. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Read | Pobiera sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Write | Dodaje sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/operationStatuses/Read | Odczytuje stan operacji dla maszyn wirtualnych. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/performMaintenance/akcja | Wykonuje konserwację na maszynie wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/odczyt | Pobiera listę maszyn wirtualnych. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/redeploy/akcja | Ponownie wdraża maszynę wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/restart/akcja | Uruchamia ponownie maszyny wirtualne. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Shutdown/akcja | Zamknij maszynę wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Start/akcja | Uruchom maszynę wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Stop/akcja | Wyłącza maszynę wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Write | Dodaj lub zmodyfikuj maszyny wirtualne. |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/operationstatuses/Read | Pobierz stan operacji połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/Komunikacja równorzędna/usuwanie | Usuń komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/Peers/operationstatuses/Read | Pobierz stan operacji komunikacji równorzędnej połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/Peers/Read | Pobierz komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/Peers/Write | Dodaj komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/odczyt | Pobierz połączenia krzyżowe w usłudze Express Route. |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Dodaj połączenia krzyżowe dla trasy Express Route. |
> | Akcja | Microsoft. ClassicNetwork/gatewaySupportedDevices/odczyt | Pobiera listę obsługiwanych urządzeń. |
> | Akcja | Microsoft. ClassicNetwork/networkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft. ClassicNetwork/networkSecurityGroups/operationStatuses/Read | Odczytuje stan operacji dla sieciowej grupy zabezpieczeń. |
> | Akcja | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienia diagnostyczne sieciowych grup zabezpieczeń |
> | Akcja | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienia diagnostyczne grup zabezpieczeń sieci. Ta operacja jest uzupełniana przez dostawcę zasobów usługi Insights. |
> | Akcja | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera zdarzenia dla sieciowej grupy zabezpieczeń |
> | Akcja | Microsoft. ClassicNetwork/networkSecurityGroups/odczyt | Pobiera sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Delete | Usuwa regułę zabezpieczeń. |
> | Akcja | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/Read | Odczytuje stan operacji dla reguł zabezpieczeń sieciowych grup zabezpieczeń. |
> | Akcja | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Read | Pobiera regułę zabezpieczeń. |
> | Akcja | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Write | Dodaje lub aktualizuje regułę zabezpieczeń. |
> | Akcja | Microsoft. ClassicNetwork/networkSecurityGroups/Write | Dodaje nową sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft. ClassicNetwork/Operations/Read | Pobierz operacje dotyczące sieci klasycznej. |
> | Akcja | Microsoft. ClassicNetwork/przydziały/odczyt | Pobieranie przydziału dla subskrypcji. |
> | Akcja | Microsoft. ClassicNetwork/Register/Action | Zarejestruj w sieci klasycznej |
> | Akcja | Microsoft. ClassicNetwork/reservedIps/Delete | Usuń zastrzeżony adres IP. |
> | Akcja | Microsoft. ClassicNetwork/reservedIps/Join/Action | Dołącz zastrzeżony adres IP |
> | Akcja | Microsoft. ClassicNetwork/reservedIps/link/akcja | Połącz zastrzeżony adres IP |
> | Akcja | Microsoft. ClassicNetwork/reservedIps/operationStatuses/Read | Odczytuje stan operacji dla zarezerwowanych adresów IP. |
> | Akcja | Microsoft. ClassicNetwork/reservedIps/odczyt | Pobiera zastrzeżone adresy IP |
> | Akcja | Microsoft. ClassicNetwork/reservedIps/Write | Dodaj nowy zastrzeżony adres IP |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/akcja | Przerywa migrację Virtual Network |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/możliwości/odczyt | Pokazuje możliwości |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/akcja | Sprawdza dostępność danego adresu IP w sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/akcja | Zatwierdza migrację Virtual Network |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Delete | Usuwa sieć wirtualną. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRevokedCertificates/Delete | Cofa odwołanie certyfikatu klienta. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRevokedCertificates/Read | Odczytaj odwołane certyfikaty klienta. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRevokedCertificates/Write | Odwołuje certyfikat klienta. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Delete | Usuwa certyfikat klienta bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Download/Action | Pobiera certyfikat według odcisku palca. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/listPackage/Action | Wyświetla pakiet certyfikatu bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Read | Znajdź certyfikaty główne klienta. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Write | Przekazuje nowy certyfikat główny klienta. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Connect/Action | Nawiązuje połączenie z lokacją usługi Site Gateway. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Disconnect/Action | Rozłącza połączenie między lokacją a bramą lokacji. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Read | Pobiera listę połączeń. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/test/akcja | Testuje połączenie bramy lokacja-lokacja. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Delete | Usuwa bramę sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/downloadDeviceConfigurationScript/Action | Pobiera skrypt konfiguracji urządzenia. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/downloadDiagnostics/Action | Pobiera diagnostykę bramy. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/listCircuitServiceKey/Action | Pobiera klucz usługi obwodu. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/listPackage/Action | Wyświetla pakiet bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/operationStatuses/Read | Odczytuje stan operacji dla bram sieci wirtualnych. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Packages/Read | Pobiera pakiet bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Read | Pobiera bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/startDiagnostics/Action | Uruchamia diagnostykę dla bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/stopDiagnostics/Action | Wyłącza diagnostykę bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Brama/zapis | Dodaje bramę sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Join/Action | Przyłącza do sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/operationStatuses/Read | Odczytuje stan operacji dla sieci wirtualnych. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/peer/Action | Tworzy równorzędną sieć wirtualną z inną siecią wirtualną. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/akcja | Przygotowuje migrację Virtual Network |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/odczyt | Pobierz sieć wirtualną. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Delete | Usuwa zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Read | Pobiera zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Write | Dodaje lub aktualizuje zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Subnets/associatedNetworkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Subnets/associatedNetworkSecurityGroups/operationStatuses/Read | Odczytuje stan operacji dla grupy zabezpieczeń sieci skojarzonej z podsiecią sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Subnets/associatedNetworkSecurityGroups/Read | Pobiera sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Subnets/associatedNetworkSecurityGroups/Write | Dodaje sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/akcja | Sprawdza poprawność migracji Virtual Network |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/virtualNetworkPeerings/Read | Pobiera komunikację równorzędną sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Write | Dodaj nową sieć wirtualną. |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ClassicStorage/możliwości/odczyt | Pokazuje możliwości |
> | Akcja | Microsoft. ClassicStorage/checkStorageAccountAvailability/Action | Sprawdza dostępność konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/checkStorageAccountAvailability/odczyt | Pobierz dostępność konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/disks/Read | Zwraca dysk konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/images/operationstatuses/Read | Pobiera stan operacji obrazu. |
> | Akcja | Microsoft. ClassicStorage/images/Read | Zwraca obraz. |
> | Akcja | Microsoft. ClassicStorage/Operations/Read | Pobiera operacje magazynu klasycznego |
> | Akcja | Microsoft. ClassicStorage/osImages/odczyt | Zwraca obraz systemu operacyjnego. |
> | Akcja | Microsoft. ClassicStorage/osPlatformImages/odczyt | Pobiera obraz platformy systemu operacyjnego. |
> | Akcja | Microsoft. ClassicStorage/publicImages/odczyt | Pobiera publiczny obraz maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicStorage/przydziały/odczyt | Pobieranie przydziału dla subskrypcji. |
> | Akcja | Microsoft. ClassicStorage/Register/Action | Zarejestruj do magazynu klasycznego |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/abortMigration/akcja | Przerywa migrację konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/commitMigration/akcja | Zatwierdza migrację konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Delete | Usuń konto magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/disks/Delete | Usuwa dany dysk konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/disks/operationStatuses/Read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/disks/Read | Zwraca dysk konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/disks/Write | Dodaje dysk konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/images/Delete | Usuwa dany obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/images/operationstatuses/Read | Zwraca stan operacji obrazu konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/images/Read | Zwraca obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/listKeys/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/operationStatuses/Read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/osImages/Delete | Usuwa dany obraz systemu operacyjnego konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/osImages/Read | Zwraca obraz systemu operacyjnego konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/osImages/Write | Dodaje dany obraz systemu operacyjnego konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/prepareMigration/akcja | Przygotowuje migrację konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/odczyt | Zwróć konto magazynu z danym kontem. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/regenerateKey/akcja | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Services/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Services/Metrics/Read | Pobiera metryki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Services/odczyt | Pobierz dostępne usługi. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/validateMigration/akcja | Sprawdza poprawność migracji konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/vmImages/Delete | Usuwa dany obraz maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/vmImages/operationstatuses/Read | Pobiera stan operacji obrazu maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/vmImages/Read | Zwraca obraz maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/vmImages/Write | Dodaje dany obraz maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Write | Dodaje nowe konto magazynu. |
> | Akcja | Microsoft. ClassicStorage/vmImages/odczyt | Wyświetla listę obrazów maszyn wirtualnych. |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/automatyczne sugerowanie/wyszukiwanie/akcja | Ta operacja zawiera sugestie dotyczące danego zapytania lub zapytania częściowego. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/analiza/akcja | Ta operacja wyodrębnia bogaty zestaw funkcji wizualnych na podstawie zawartości obrazu.  |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/areaofinterest/Action | Ta operacja zwraca pole ograniczenia wokół najważniejszego obszaru obrazu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/opisywanie/akcja | Ta operacja generuje opis obrazu w języku czytelnym dla ludzi z pełnymi zdaniami.<br> Opis jest oparty na kolekcji tagów zawartości, które są również zwracane przez operację.<br>Dla każdego obrazu można wygenerować więcej niż jeden opis.<br> Opisy są uporządkowane według ich wyniku ufności.<br>Wszystkie opisy są w języku angielskim. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/Detection/Action | Ta operacja służy do wykrywania obiektów na określonym obrazie.  |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/generatethumbnail/Action | Ta operacja generuje obraz miniatury z szerokością i wysokością określoną przez użytkownika.<br> Domyślnie usługa analizuje obraz, identyfikuje region zainteresowania (zwrotny) i generuje inteligentne współrzędne przycinania na podstawie zwrotu z inwestycji.<br> Inteligentne przycinanie pomaga w przypadku określenia współczynnika proporcji, który różni się od obrazu wejściowego |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/models/analiza/akcja | Ta operacja rozpoznaje zawartość w obrazie, stosując model specyficzny dla domeny.<br> Listę modeli specyficznych dla domeny, które są obsługiwane przez interfejs API przetwarzania obrazów, można pobrać przy użyciu żądania GET/models.<br> Obecnie interfejs API udostępnia następujące modele specyficzne dla domeny: osobistości, dzielnice. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/models/Read | Ta operacja zwraca listę modeli specyficznych dla domeny, które są obsługiwane przez interfejs API przetwarzania obrazów.  Obecnie interfejs API obsługuje następujące modele specyficzne dla domeny: aparat rozpoznawania osobistości, punkt rozpoznawania punktu orientacyjnego. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/OCR/Action | Optyczne rozpoznawanie znaków (OCR) wykrywa tekst w obrazie i wyodrębnia rozpoznane znaki do strumienia znaków do użycia maszynowego.    |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/recognizetext/Action | Użyj tego interfejsu, aby uzyskać wynik operacji Rozpoznawanie tekstu. Gdy używasz interfejsu Rozpoznawanie tekstu, odpowiedź zawiera pole o nazwie "Operation-Location". Pole "operacja — lokalizacja" zawiera adres URL, którego należy użyć dla operacji pobierania Rozpoznawanie tekstu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/tag/Action | Ta operacja generuje listę słów lub tagów, które są istotne dla zawartości dostarczonego obrazu.<br>Interfejs API przetwarzania obrazów może zwracać Tagi na podstawie obiektów, żywych, scenerii lub akcji znalezionych w obrazach.<br>W przeciwieństwie do kategorii, Tagi nie są zorganizowane zgodnie z hierarchicznym systemem klasyfikacji, ale odpowiadają zawartości obrazu.<br>Tagi mogą zawierać wskazówki, aby uniknąć niejednoznaczności lub zapewnienia kontekstu, na przykład tag "Cello" może towarzyszyć wskazówki "instrument muzyczny".<br>Wszystkie Tagi są w języku angielskim. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/textoperations/Read | Ten interfejs jest używany do pobierania wyniku operacji tekstu. Adres URL tego interfejsu powinien zostać pobrany z pola <b>"Lokalizacja operacji"</b> zwróconego z interfejsu rozpoznawanie tekstu. |
> | Akcja | Microsoft. CognitiveServices/accounts/Delete | Usuwa konta interfejsu API |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/EntitySearch/Search/Action | Pobierz jednostki i umieści wyniki dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/Detect/Action | Wykrywaj ludzkie twarze na obrazie, zwracają prostokąty twarzy i opcjonalnie z faceIds, dzielnicami i atrybutami. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/facelists/Delete | Usuń określoną listę czołową. Obrazy powiązanej części na liście czołowej zostaną również usunięte. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/facelists/persistedfaces/Delete | Usuń miarę z listy czołowej według określonych faceListId i persisitedFaceId. Obraz powiązanej ze stroną czołową zostanie również usunięty. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/facelists/persistedfaces/Write | Dodaj twarzy do określonej listy twarzy, do 1 000 twarze. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/facelists/Read | Pobierz faceListId, nazwę, userData i twarze listy twarzy na liście twarzy. Lista czołowa lists "faceListId, Name i userData". |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/facelists/Write | Utwórz pustą listę czołową z faceListId określonymi przez użytkownika, nazwą i opcjonalnym postawce userData. Do 64 list czołowych są dozwolone informacje o aktualizacji listy czołowej, w tym nazwa i userData. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/findsimilars/Action | Podaną faceId twarzy zapytania, aby wyszukiwać podobne buźki z tablicy faceId, listy twarzy lub dużej listy twarzy. faceId |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/Group/Action | Podziel powierzchnie kandydatów na grupy w oparciu o podobieństwo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/Identyfikuj/Action | Identyfikacja 1-do-wielu, aby znaleźć najbliższe dopasowania konkretnej osoby, której dotyczy zapytanie, od grupy osób lub dużej osoby. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largefacelists/Delete | Usuń określoną dużą listę czołową. Powiązane obrazy ze stroną ze znaczną stroną są również usuwane. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largefacelists/persistedfaces/Delete | Usuń miarę z dużej listy czołowej według określonych largeFaceListId i persisitedFaceId. Obraz powiązanej ze stroną czołową zostanie również usunięty. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largefacelists/persistedfaces/Read | Pobierz utrwaloną twarzy na dużą listę twarzy przez largeFaceListId i persistedFaceId. Lista twarzy persistedFaceId i userData na określonej dużej liście |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largefacelists/persistedfaces/Write | Dodaj twarz do określonej dużej listy twarzy, do 1 000 000 twarzy. Zaktualizuj pole userData określonego kroju na dużą listę czołową przez jego persistedFaceId. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largefacelists/Read | Pobierz largeFaceListId, imię i nazwisko, userData dla dużej listy czołowej. Wyświetlanie listy dużych elementów z listą kroju "informacje o largeFaceListId, nazwie i userData. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largefacelists/pociąg/akcja | Prześlij zadanie szkoleniowe o dużym rozmiarze. Szkolenie to kluczowy krok, którego może używać tylko przeszkolonej dużej listy czołowej. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largefacelists/Training/Read | Aby sprawdzić stan szkolenia z dużą ilością, zakończono lub nadal trwa. Szkolenie LargeFaceList jest operacją asynchroniczną |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largefacelists/Write | Utwórz pustą listę dużej kroju z largeFaceListId określonymi przez użytkownika, nazwą i opcjonalnym postawce userData. Aktualizowanie informacji o dużej liście czołowej, w tym nazwa i userData. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Delete | Usuń istniejącą grupę dużej osoby z określonym personGroupId. Dane utrwalone w tej dużej grupie osób zostaną usunięte. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Persons/Action | Utwórz nową osobę w określonej grupie dużej osoby. Aby dodać miarę do tej osoby, zadzwoń |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Persons/Delete | Usuń istniejącą osobę z grupy dużej osoby. Wszystkie przechowywane dane osoby i obrazy do wprowadzania danych zostaną usunięte. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Persons/persistedfaces/Delete | Usuń miarę z osoby w grupie dużej osoby. Dane dotyczące kroju i obrazu związane z tym wpisem na tej samej rachunku zostaną również usunięte. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Persons/persistedfaces/Read | Pobierz informacje o głowie osoby. Głowa utrwalonej osoby jest określana przez jej largePersonGroupId, personId i persistedFaceId. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Persons/persistedfaces/Write | Dodaj obraz przedstawiający do osoby do grupy dużych osób na potrzeby identyfikacji lub weryfikacji. Aby rozwiązać ten obraz, należy zaktualizować pole userData twarzy dla osoby. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Persons/Read | Pobierz imię i nazwisko osoby, a utrwalony faceIds reprezentujący zarejestrowanego obrazu twarzy osoby. Wyświetl listę wszystkich informacji o osobach w określonej grupie dużych osób, w tym personId, Name, userData i persistedFaceIds. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Persons/Write | Nazwa aktualizacji lub userData osoby. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Read | Pobierz informacje o dużej grupie osób, w tym jej nazwę i userData. Ten interfejs API zwraca listę informacji o dużej grupie osób, których wszystkie istniejące grupy osób mają largePesonGroupId, nazwę i userData. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/pociąg/akcja | Prześlij zadanie szkoleniowe dotyczące dużej grupy osób. Szkolenie to kluczowy krok, który może być używany tylko przez przeszkolonej grupy osób. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Training/Read | Aby sprawdzić, czy stan szkolenia dla dużej grupy osób zakończył się lub nadal trwa. Szkolenie LargePersonGroup jest operacją asynchroniczną |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/largepersongroups/Write | Utwórz nową grupę dużych osób z largePersonGroupId określonymi przez użytkownika, nazwą i opcjonalnym postawce userData. Zaktualizuj nazwę istniejącej grupy osób o dużej osobie. Właściwości są przechowywane bez zmian, jeśli nie znajdują się w treści żądania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Delete | Usuń istniejącą grupę osób z określonym personGroupId. Utrwalone dane w tej grupie osób zostaną usunięte. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Persons/Action | Utwórz nową osobę w określonej grupie osób. Aby dodać miarę do tej osoby, zadzwoń |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Persons/Delete | Usuń istniejącą osobę z grupy osób. Wszystkie przechowywane dane osoby i obrazy do wprowadzania danych zostaną usunięte. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Persons/persistedfaces/Delete | Usuń miarę z osoby w grupie osób. Dane dotyczące kroju i obrazu związane z tym wpisem na tej samej rachunku zostaną również usunięte. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Persons/persistedfaces/Read | Pobierz informacje o głowie osoby. Głowa utrwalonej osoby jest określana przez jej personGroupId, personId i persistedFaceId. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Persons/persistedfaces/Write | Dodawanie obrazu do osoby do grupy osób w celu identyfikacji lub weryfikacji. Aby obsłużyć obraz wielu aktualizacji pola userData w trwałej twarzy dla osoby. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Persons/Read | Pobierz imię i nazwisko osoby, a utrwalony faceIds reprezentujący zarejestrowanego obrazu twarzy osoby. Wyświetl listę wszystkich informacji o osobach w określonej grupie osób, w tym personId, Name, userData i persistedFaceIds zarejestrowanych. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Persons/Write | Nazwa aktualizacji lub userData osoby. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Read | Pobierz nazwę grupy osób i userData. Aby uzyskać informacje o osobach w ramach tej grupy osób, użyj list pesonGroupId, Name i userData grup osób. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/pociąg/akcja | Prześlij zadanie szkoleniowe dotyczące grupy osób. Szkolenie to kluczowy krok, który może być używany tylko przez przeszkolonej grupy osób. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Training/Read | Aby sprawdzić, czy stan szkolenia grupy osób zakończył się lub nadal trwa. Uczenie użytkownika jest wyzwalane przez operację asynchroniczną |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/grup osób wynosi/Write | Utwórz nową grupę osób o określonej personGroupId, nazwie i podanym przez użytkownika postawce. Zaktualizuj nazwę istniejącej grupy osób i userData. Właściwości są przechowywane bez zmian, jeśli nie znajdują się w treści żądania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/Front/verify/Action | Sprawdź, czy dwie twarze należą do tej samej osoby, czy jedna z nich należy do osoby. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImageSearch/szczegóły/akcja | Zwraca szczegółowe informacje o obrazie, takie jak strony sieci Web, które zawierają obraz. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImageSearch/Search/Action | Pobierz odpowiednie obrazy dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImageSearch/trendy/akcja | Pobierz obecnie popularne obrazy. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/Action | Tworzy sesję czytnika immersyjny |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/InkRecognizer/Rozpoznaj/akcja | Dane zestawu pociągnięć analizują zawartość i generują listę rozpoznanych jednostek, w tym rozpoznany tekst. |
> | Akcja | Microsoft. CognitiveServices/accounts/listKeys/Action | Klucze list |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/LUIS/przewidywania/akcja | Pobiera opublikowaną prognozę punktów końcowych dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/categorysearch/Action | Zwraca wiadomości dla podanej kategorii. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/Search/Action | Uzyskaj artykuły z wiadomościami istotnymi dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/Trendingtopics/Action | Poznaj tematy trendów identyfikowane przez usługę Bing. Są to te same tematy widoczne na transparencie u dołu strony głównej usługi Bing. |
> | Akcja | Microsoft. CognitiveServices/accounts/Read | Odczytuje konta interfejsu API. |
> | Akcja | Microsoft. CognitiveServices/accounts/regenerateKey/Action | Wygeneruj ponownie klucz |
> | Akcja | Microsoft. CognitiveServices/accounts/SKU/Read | Odczytuje dostępne jednostki SKU dla istniejącego zasobu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/sprawdzanie pisowni/sprawdzanie pisowni/akcja | Pobierz wynik zapytania sprawdzania pisowni za pomocą GET lub POST. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/textanalytics/Entities/akcja | Interfejs API zwraca listę znanych jednostek i ogólnych nazwanych jednostek (\"osoby\", \"lokalizację\", \"organizacji\" itp.) w danym dokumencie. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/textanalytics/frazy kluczowe/akcja | Interfejs API zwraca listę ciągów wskazujących najważniejsze punkty w tekście wejściowym. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/textanalytics/Języki/akcja | Interfejs API zwraca wykryty język i wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 oznaczają 100% pewności, że określony język jest poprawny. Obsługiwanych jest 120 języków. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/textanalytics/tonacji/Action | Interfejs API zwraca wynik liczbowy z zakresu od 0 do 1.<br>Wyniki zbliżone do 1 oznaczają pozytywne tonacji, podczas gdy wyniki zbliżone do 0 oznaczają ujemną tonacji.<br>Wynik 0,5 wskazuje brak tonacji (np.<br>Instrukcja factoid). |
> | Akcja | Microsoft. CognitiveServices/accounts/Usages/Read | Pobierz użycie przydziału dla istniejącego zasobu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/szczegóły/akcja | Uzyskaj wgląd w dane wideo, takie jak powiązane wideo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/Search/Action | Pobierz wideo odpowiednie dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/trendy/akcja | Pobierz obecnie trendy wideo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VisualSearch/Search/Action | Zwraca listę tagów odnoszących się do podanego obrazu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/WebSearch/Search/Action | Pobierz wyniki dla danego zapytania z Internetu, obrazu, wiadomości & wideo. |
> | Akcja | Microsoft. CognitiveServices/accounts/Write | Zapisuje konta interfejsu API. |
> | Akcja | Microsoft. CognitiveServices/checkDomainAvailability/Action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Akcja | Microsoft. CognitiveServices/Locations/checkSkuAvailability/Action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Akcja | Microsoft. CognitiveServices/Locations/checkSkuAvailability/Action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Akcja | Microsoft. CognitiveServices/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadomienie z firmy Microsoft. Network usuwającej VirtualNetworks lub podsieci. |
> | Akcja | Microsoft. CognitiveServices/Operations/Read | Wyświetl wszystkie dostępne operacje |
> | Akcja | Microsoft. CognitiveServices/Register/Action | Rejestruje subskrypcję dla Cognitive Services |
> | Akcja | Microsoft. CognitiveServices/Register/Action | Rejestruje subskrypcję dla Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Commerce/RateCard/odczyt | Zwraca dane oferty, metadane zasobów/licznika i stawki dla danej subskrypcji. |
> | Akcja | Microsoft. Commerce/UsageAggregates/odczyt | Pobiera użycie Microsoft Azure przez subskrypcję. Wynik zawiera zagregowane dane użycia, informacje dotyczące subskrypcji i zasobów, w określonym przedziale czasu. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. COMPUTE/availabilitySets/Delete | Usuwa zestaw dostępności |
> | Akcja | Microsoft. COMPUTE/availabilitySets/Read | Pobierz właściwości zestawu dostępności |
> | Akcja | Microsoft. COMPUTE/availabilitySets/vmSizes/odczyt | Wyświetl dostępne rozmiary dla tworzenia lub aktualizowania maszyny wirtualnej w zestawie dostępności |
> | Akcja | Microsoft. COMPUTE/availabilitySets/Write | Tworzy nowy zestaw dostępności lub aktualizuje istniejący |
> | Akcja | Microsoft. COMPUTE/diskEncryptionSets/Delete | Usuń zestaw szyfrowania dysków |
> | Akcja | Microsoft. COMPUTE/diskEncryptionSets/Read | Pobierz właściwości zestawu szyfrowania dysków |
> | Akcja | Microsoft. COMPUTE/diskEncryptionSets/Write | Utwórz nowy zestaw szyfrowania dysków lub zaktualizuj istniejący |
> | Akcja | Microsoft. COMPUTE/disks/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dysku na potrzeby usługi dostęp do obiektów BLOB |
> | Akcja | Microsoft. COMPUTE/disks/Delete | Usuwa dysk |
> | Akcja | Microsoft. COMPUTE/disks/endGetAccess/Action | Odwołaj identyfikator URI sygnatury dostępu współdzielonego dysku |
> | Akcja | Microsoft. COMPUTE/disks/Read | Pobieranie właściwości dysku |
> | Akcja | Microsoft. COMPUTE/disks/Write | Tworzy nowy dysk lub aktualizuje istniejący |
> | Akcja | Microsoft. COMPUTE/Galerie/Delete | Usuwa galerię |
> | Akcja | Microsoft. COMPUTE/Galerie/obrazy/usuwanie | Usuwa obraz galerii |
> | Akcja | Microsoft. COMPUTE/Galerie/images/Read | Pobiera właściwości obrazu galerii |
> | Akcja | Microsoft. COMPUTE/Galerie/obrazy/wersje/usuwanie | Usuwa wersję obrazu galerii |
> | Akcja | Microsoft. COMPUTE/Galerie/obrazy/wersje/odczyt | Pobiera właściwości wersji obrazu galerii |
> | Akcja | Microsoft. COMPUTE/Galerie/obrazy/wersje/zapis | Tworzy nową wersję obrazu galerii lub aktualizuje istniejącą |
> | Akcja | Microsoft. COMPUTE/Galerie/images/Write | Tworzy nowy obraz galerii lub aktualizuje istniejący |
> | Akcja | Microsoft. COMPUTE/Galerie/odczyt | Pobiera właściwości galerii |
> | Akcja | Microsoft. COMPUTE/Galerie/Write | Tworzy nową galerię lub aktualizuje istniejącą. |
> | Akcja | Microsoft. COMPUTE/hostGroups/Delete | Usuwa grupę hostów |
> | Akcja | Microsoft. COMPUTE/hostGroups/hosts/Delete | Usuwa hosta |
> | Akcja | Microsoft. COMPUTE/hostGroups/hosty/odczyt | Pobierz właściwości hosta |
> | Akcja | Microsoft. COMPUTE/hostGroups/hosts/Write | Tworzy nowy host lub aktualizuje istniejącego hosta |
> | Akcja | Microsoft. COMPUTE/hostGroups/Read | Pobierz właściwości grupy hostów |
> | Akcja | Microsoft. COMPUTE/hostGroups/Write | Tworzy nową grupę hostów lub aktualizuje istniejącą grupę hostów |
> | Akcja | Microsoft. COMPUTE/images/Delete | Usuwa obraz |
> | Akcja | Microsoft. COMPUTE/images/Read | Pobierz właściwości obrazu |
> | Akcja | Microsoft. COMPUTE/images/Write | Tworzy nowy obraz lub aktualizuje istniejący |
> | Akcja | Microsoft. COMPUTE/Locations/capsOperations/Read | Pobiera stan operacji asynchronicznej Cap |
> | Akcja | Microsoft. COMPUTE/Locations/diskOperations/Read | Pobiera stan operacji na dysku asynchronicznym |
> | Akcja | Microsoft. COMPUTE/Locations/logAnalytics/getRequestRateByInterval/Action | Utwórz dzienniki, aby wyświetlić łączną liczbę żądań według przedziałów czasu, aby ułatwić diagnostykę. |
> | Akcja | Microsoft. COMPUTE/Locations/logAnalytics/getThrottledRequests/Action | Utwórz dzienniki, aby wyświetlić zagregowane żądania ograniczane pogrupowane według resourceName, OperationName lub stosowanych zasad ograniczania. |
> | Akcja | Microsoft. COMPUTE/Locations/Operations/Read | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/offers/Read | Pobierz właściwości oferty obrazu platformy |
> | Akcja | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/offers/SKU/Read | Pobierz właściwości jednostki SKU obrazu platformy |
> | Akcja | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/offers/SKU/wersje/odczyt | Pobierz właściwości wersji obrazu platformy |
> | Akcja | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/Types/Read | Pobierz właściwości typu VMExtension |
> | Akcja | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/Types/wersje/odczyt | Pobierz właściwości wersji VMExtension |
> | Akcja | Microsoft. COMPUTE/Locations/Wydawcas/Read | Pobierz właściwości wydawcy |
> | Akcja | Microsoft. COMPUTE/Locations/runCommands/Read | Wyświetla dostępne polecenia uruchamiania w lokalizacji |
> | Akcja | Microsoft. COMPUTE/lokalizacje/użycia/odczyt | Pobiera limity usługi i bieżące ilości użycia dla zasobów obliczeniowych subskrypcji w lokalizacji |
> | Akcja | Microsoft. COMPUTE/Locations/vmSizes/Read | Wyświetla dostępne rozmiary maszyn wirtualnych w lokalizacji |
> | Akcja | Microsoft. COMPUTE/Operations/Read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. COMPUTE |
> | Akcja | Microsoft. COMPUTE/proximityPlacementGroups/Delete | Usuwa grupę umieszczania zbliżeniowego |
> | Akcja | Microsoft. COMPUTE/proximityPlacementGroups/Read | Pobierz właściwości grupy położenia zbliżeniowe |
> | Akcja | Microsoft. COMPUTE/proximityPlacementGroups/Write | Tworzy nową grupę umieszczania w sąsiedztwie lub aktualizuje istniejącą |
> | Akcja | Microsoft. COMPUTE/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. COMPUTE |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/Delete | Usuwa kolekcję punktów przywracania i zawiera punkty przywracania |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/Read | Pobierz właściwości kolekcji punktów przywracania |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/restorePoints/Delete | Usuwa punkt przywracania |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/restorePoints/odczyt | Pobierz właściwości punktu przywracania |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/restorePoints/retrieveSasUris/Action | Pobierz właściwości punktu przywracania wraz z identyfikatorami URI SAS obiektów BLOB |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/restorePoints/Write | Tworzy nowy punkt przywracania |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/Write | Tworzy nową kolekcję punktów przywracania lub aktualizuje istniejącą. |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/Delete | Usuwa element sharedvmimage |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/Read | Pobierz właściwości elementu element sharedvmimage |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/wersje/usuwanie | Usuń element sharedvmimageversion |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/wersje/odczyt | Pobierz właściwości elementu element sharedvmimageversion |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/Versions/Replikacja/Akcja | Replikowanie element sharedvmimageversion do regionów docelowych |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/wersje/zapis | Utwórz nowy element sharedvmimageversion lub zaktualizuj istniejący |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/Write | Tworzy nowy element sharedvmimage lub aktualizuje istniejący |
> | Akcja | Microsoft. COMPUTE/SKU/odczyt | Pobiera listę jednostek SKU Microsoft. COMPUTE dostępnych dla Twojej subskrypcji |
> | Akcja | Microsoft. COMPUTE/snapshots/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dla migawki do obiektu BLOB |
> | Akcja | Microsoft. COMPUTE/snapshots/Delete | Usuń migawkę |
> | Akcja | Microsoft. COMPUTE/snapshots/endGetAccess/Action | Odwołaj identyfikator URI sygnatury dostępu współdzielonego dla migawki |
> | Akcja | Microsoft. COMPUTE/snapshots/Read | Pobierz właściwości migawki |
> | Akcja | Microsoft. COMPUTE/snapshots/Write | Utwórz nową migawkę lub zaktualizuj istniejącą |
> | Akcja | Microsoft. COMPUTE/Unregister/Action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. COMPUTE |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Capture/Action | Przechwytuje maszynę wirtualną przez kopiowanie wirtualnych dysków twardych i generuje szablon, którego można użyć do tworzenia podobnych maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachines/convertToManagedDisks/akcja | Konwertuje dyski oparte na obiektach Blob maszyny wirtualnej na dyski zarządzane |
> | Akcja | Microsoft. COMPUTE/virtualMachines/deallocate/Action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Delete | Usuwa maszynę wirtualną |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Extensions/Delete | Usuwa rozszerzenie maszyny wirtualnej |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Extensions/Read | Pobierz właściwości rozszerzenia maszyny wirtualnej |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Extensions/Write | Tworzy nowe rozszerzenie maszyny wirtualnej lub aktualizuje istniejące |
> | Akcja | Microsoft. COMPUTE/virtualMachines/generalize/akcja | Ustawia stan maszyny wirtualnej na uogólnione i przygotowuje maszynę wirtualną do przechwycenia |
> | Akcja | Microsoft. COMPUTE/virtualMachines/instanceView/odczyt | Pobiera szczegółowy stan środowiska uruchomieniowego maszyny wirtualnej i jej zasobów |
> | Akcja dataaction | Microsoft. COMPUTE/virtualMachines/login/akcja | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | Akcja dataaction | Microsoft. COMPUTE/virtualMachines/loginAsAdmin/akcja | Logowanie się do maszyny wirtualnej przy użyciu uprawnień administratora systemu Windows lub użytkownika root w systemie Linux |
> | Akcja | Microsoft. COMPUTE/virtualMachines/performMaintenance/akcja | Wykonuje operację konserwacji na maszynie wirtualnej. |
> | Akcja | Microsoft. COMPUTE/virtualMachines/wyłączenie/akcja | Wyłącza maszynę wirtualną. Pamiętaj, że opłaty za maszynę wirtualną będą kontynuowane. |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Read | Pobierz właściwości maszyny wirtualnej |
> | Akcja | Microsoft. COMPUTE/virtualMachines/redeploy/Action | Ponownie wdraża maszynę wirtualną |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Reimage/Action | Odtwarza z obrazu maszynę wirtualną, która korzysta z dysku różnicowego. |
> | Akcja | Microsoft. COMPUTE/virtualMachines/restart/akcja | Uruchamia ponownie maszynę wirtualną |
> | Akcja | Microsoft. COMPUTE/virtualMachines/runCommand/akcja | Wykonuje wstępnie zdefiniowany skrypt na maszynie wirtualnej |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Start/akcja | Uruchamia maszynę wirtualną |
> | Akcja | Microsoft. COMPUTE/virtualMachines/vmSizes/odczyt | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Write | Tworzy nową maszynę wirtualną lub aktualizuje istniejącą maszynę wirtualną |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/deallocate/Action | Wyłącza i zwalnia zasoby obliczeniowe dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Delete | Usuwa zestaw skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Delete/Action | Usuwa wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Delete | Usuwa rozszerzenie zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Read | Pobiera właściwości rozszerzenia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write | Tworzy nowe rozszerzenie zestawu skalowania maszyn wirtualnych lub aktualizuje istniejące |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/akcja | Ręcznie przechodzenie do domeny aktualizacji platformy zestawu skalowania maszyn wirtualnych usługi Service Fabric w celu zakończenia oczekującej aktualizacji, która jest zablokowana |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/instanceView/odczyt | Pobiera widok wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/manualUpgrade/akcja | Ręcznie aktualizuje wystąpienia do najnowszego modelu zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/networkInterfaces/odczyt | Pobierz właściwości wszystkich interfejsów sieciowych zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/osRollingUpgrade/akcja | Uruchamia uaktualnienie stopniowe, aby przenieść wszystkie wystąpienia zestawu skalowania maszyn wirtualnych do najnowszej dostępnej wersji systemu operacyjnego obrazu platformy. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/osUpgradeHistory/odczyt | Pobiera historię uaktualnień systemu operacyjnego dla zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/performMaintenance/akcja | Przeprowadza planowaną konserwację na wystąpieniach zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/wyłączenie/akcja | Wyłącza wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/adresów publicipaddress/odczyt | Pobierz właściwości wszystkich publicznych adresów IP zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Read | Pobierz właściwości zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/redeploy/Action | Wdróż ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Reimage/Action | Odtwarza z obrazu wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/reimageAll/akcja | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/restart/akcja | Uruchamia ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/rollingUpgrades/Cancel/Action | Anuluje stopniowe uaktualnianie zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/rollingUpgrades/odczyt | Pobierz najnowszy stan stopniowego uaktualniania dla zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Skala/akcja | Sprawdź, czy istniejący zestaw skalowania maszyn wirtualnych może skalować w poziomie/skalować do określonej liczby wystąpień |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/SKU/odczyt | Wyświetla prawidłowe jednostki SKU dla istniejącego zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Start/akcja | Uruchamia wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/unallocate/Action | Wyłącza i zwalnia zasoby obliczeniowe dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Delete | Usuń konkretną maszynę wirtualną w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/instanceView/Read | Pobiera widok wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/Ipconfiguration/adresów publicipaddress/Read | Pobierz właściwości publicznego adresu IP utworzonego przy użyciu zestawu skalowania maszyn wirtualnych. Zestaw skalowania maszyn wirtualnych może utworzyć maksymalnie jeden publiczny adres IP na element ipconfiguration (prywatny adres IP) |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/Ipconfiguration/odczyt | Pobierz właściwości jednej lub wszystkich konfiguracji protokołu IP interfejsu sieciowego utworzonego przy użyciu zestawu skalowania maszyn wirtualnych. Konfiguracje adresów IP reprezentują prywatne adresy IP |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/Read | Pobierz właściwości jednego lub wszystkich interfejsów sieciowych maszyny wirtualnej utworzonej przy użyciu zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/performMaintenance/Action | Przeprowadza planowaną konserwację na wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/wyłączenie/Action | Wyłącza wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/odczyt | Pobiera właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/redeploy/Action | Ponownie wdraża wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Reimage/Action | Odtwarza z obrazu wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/reimageAll/Action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) dla wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/restart/akcja | Uruchamia ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/UruchomPolecenie/Action | Wykonuje wstępnie zdefiniowany skrypt w wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Start/akcja | Uruchamia wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Write | Aktualizuje właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/vmSizes/odczyt | Wyświetl dostępne rozmiary dla tworzenia lub aktualizowania maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Write | Tworzy nowy zestaw skalowania maszyn wirtualnych lub aktualizuje istniejący |

## <a name="microsoftconsumption"></a>Microsoft. zużycie

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. zużycie/salda/odczyt | Wyświetl podsumowanie użycia dla okresu rozliczeniowego dla grupy zarządzania. |
> | Akcja | Microsoft. zużycie/budżety/usuwanie | Usuń budżety przez subskrypcję lub grupę zarządzania. |
> | Akcja | Microsoft. zużycie/budżety/odczyt | Wystaw budżety według subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft. zużycie/budżety/zapis | Tworzy i aktualizuje budżety za pomocą subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft. zużycie/opłaty/odczyt | Wystaw opłaty |
> | Akcja | Microsoft. zużycie/kredyty/odczyt | Wystaw kredyty |
> | Akcja | Microsoft. użycie/zdarzenia/odczyt | Wyświetl listę zdarzeń |
> | Akcja | Microsoft. użycie/prognozy/odczyt | Prognozowanie list |
> | Akcja | Microsoft. zużycie/partie/odczyt | Lista partii |
> | Akcja | Microsoft. zużycie/Marketplace/odczyt | Wyświetl listę szczegółów użycia zasobów portalu Marketplace dla zakresu subskrypcji EA i WebDirect. |
> | Akcja | Microsoft. zużycie/operationresults/odczyt | Operationresults listy |
> | Akcja | Microsoft. użycie/operacje/odczyt | Wyświetl listę wszystkich obsługiwanych operacji przez dostawcę zasobów Microsoft. zużycie. |
> | Akcja | Microsoft. zużycie/operationstatus/odczyt | Operationstatus listy |
> | Akcja | Microsoft. zużycie/pricesheets/odczyt | Wyświetl listę danych Pricesheets dla subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft. użycie/rejestrowanie/akcja | Zarejestruj się w celu użycia RP |
> | Akcja | Microsoft. zużycie/reservationDetails/odczyt | Wyświetl listę szczegółów użycia dla wystąpień zarezerwowanych według kolejności rezerwacji lub grup zarządzania. Dane szczegółowe są na poziomie każdego wystąpienia dziennie. |
> | Akcja | Microsoft. zużycie/reservationRecommendations/odczyt | Wyświetl listę pojedynczych lub udostępnionych zaleceń dla wystąpień zarezerwowanych dla subskrypcji. |
> | Akcja | Microsoft. zużycie/reservationSummaries/odczyt | Wyświetl listę podsumowań użycia dla wystąpień zarezerwowanych według kolejności rezerwacji lub grup zarządzania. Dane podsumowujące są na poziomie co miesiąc lub codziennie. |
> | Akcja | Microsoft. zużycie/reservationTransactions/odczyt | Wyświetl listę historii transakcji dla wystąpień zarezerwowanych według grup zarządzania. |
> | Akcja | Microsoft. użycie/Tagi/odczyt | Lista tagów dla umów EA i Subscriptions. |
> | Akcja | Microsoft. użycie/dzierżawy/odczyt | Wyświetlanie listy dzierżawców |
> | Akcja | Microsoft. użycie/dzierżawcy/rejestr/akcja | Zarejestruj akcję dla zakresu Microsoft. zużycie przez dzierżawcę. |
> | Akcja | Microsoft. zużycie/warunki/odczyt | Wyświetl listę warunków subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft. zużycie/usageDetails/odczyt | Wyświetl listę szczegółów użycia zakresu dla subskrypcji EA i WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ContainerInstance/containerGroups/Containers/exec/akcja | Exec do określonego kontenera. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/Containers/Logs/odczyt | Pobierz dzienniki dla określonego kontenera. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/Delete | Usuń określoną grupę kontenerów. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Pobierz wynik operacji asynchronicznej |
> | Akcja | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla grupy kontenerów. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla grupy kontenerów. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla grupy kontenerów. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/odczyt | Pobierz wszystkie grupy kontenerów. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/restart/akcja | Uruchamia ponownie określoną grupę kontenerów. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/Start/akcja | Uruchamia określoną grupę kontenerów. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/Stop/akcja | Kończy określoną grupę kontenerów. Zasoby obliczeniowe zostaną cofnięte i rozliczenia zostaną zatrzymane. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/Write | Utwórz lub zaktualizuj określoną grupę kontenerów. |
> | Akcja | Microsoft. ContainerInstance/Locations/cachedImages/Read | Pobiera buforowane obrazy dla subskrypcji w regionie. |
> | Akcja | Microsoft. ContainerInstance/lokalizacje/możliwości/odczyt | Uzyskaj możliwości dla regionu. |
> | Akcja | Microsoft. ContainerInstance/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadamia Microsoft. ContainerInstance, że sieć wirtualna lub podsieć jest usuwana. |
> | Akcja | Microsoft. ContainerInstance/Locations/Operations/Read | Wyświetl listę operacji usługi wystąpienia kontenera platformy Azure. |
> | Akcja | Microsoft. ContainerInstance/lokalizacje/użycia/odczyt | Pobierz użycie dla określonego regionu. |
> | Akcja | Microsoft. ContainerInstance/Operations/Read | Wyświetl listę operacji usługi wystąpienia kontenera platformy Azure. |
> | Akcja | Microsoft. ContainerInstance/Register/Action | Rejestruje subskrypcję dostawcy zasobów wystąpienia kontenera i umożliwia tworzenie grup kontenerów. |
> | Akcja | Microsoft. ContainerInstance/serviceassociationlinks/Delete | Usuń link skojarzenia usługi utworzony przez dostawcę zasobów wystąpienia kontenera platformy Azure w podsieci. |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ContainerRegistry/checkNameAvailability/odczyt | Sprawdza, czy nazwa rejestru kontenerów jest dostępna do użycia. |
> | Akcja | Microsoft. ContainerRegistry/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadamia Microsoft. ContainerRegistry, że sieć wirtualna lub podsieć jest usuwana |
> | Akcja | Microsoft. ContainerRegistry/Locations/operationResults/Read | Pobiera wynik operacji asynchronicznej |
> | Akcja | Microsoft. ContainerRegistry/Operations/Read | Wyświetla wszystkie dostępne Azure Container Registry operacje interfejsu API REST |
> | Akcja | Microsoft. ContainerRegistry/Register/Action | Rejestruje subskrypcję dostawcy zasobów rejestru kontenerów i umożliwia tworzenie rejestrów kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/artefakty/usuwanie | Usuń artefakt w rejestrze kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/kompilacje/akcja | Anuluje istniejącą kompilację. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/kompilacje/getLogLink/akcja | Pobiera link umożliwiający pobranie dzienników kompilacji. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/kompilacje/odczyty | Pobiera właściwości określonej kompilacji lub wyświetla wszystkie kompilacje dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/kompilacje/zapisywanie | Aktualizuje kompilację dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/buildTasks/usuwanie | Usuwa zadanie kompilacji z rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/buildTasks/listSourceRepositoryProperties/akcja | Wyświetla listę właściwości kontroli źródła dla zadania kompilacji. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/buildTasks/odczyt | Pobiera właściwości określonego zadania kompilacji lub wyświetla wszystkie zadania kompilacji dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/buildTasks/kroki/Usuwanie | Usuwa krok kompilacji z zadania kompilacji. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/buildTasks/etapy/listBuildArguments/akcja | Wyświetla listę argumentów kompilacji dla kroku kompilacji, w tym argumentów tajnych. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/buildTasks/kroki/odczyt | Pobiera właściwości określonego kroku kompilacji lub wyświetla listę wszystkich kroków kompilacji dla określonego zadania kompilacji. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/buildTasks/kroki/zapis | Tworzy lub aktualizuje krok kompilacji dla zadania kompilacji o określonych parametrach. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/buildTasks/zapis | Tworzy lub aktualizuje zadanie kompilacji dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/usuwanie | Usuwa rejestr kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/eventGridFilters/usuwanie | Usuwa filtr usługi Event Grid z rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/eventGridFilters/odczyt | Pobiera właściwości określonego filtru usługi Event Grid lub wyświetla listę wszystkich filtrów usługi Event Grid dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/eventGridFilters/zapis | Tworzy lub aktualizuje filtr usługi Event Grid dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/getBuildSourceUploadUrl/akcja | Pobiera lokalizację przekazywania dla użytkownika, aby mógł przekazać źródło. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/importImage/akcja | Zaimportuj obraz do rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/listBuildSourceUploadUrl/akcja | Pobierz lokalizację adresu URL przekazywania źródła dla rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/listCredentials/akcja | Wyświetla listę poświadczeń logowania dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/listPolicies/odczyt | Wyświetla listę zasad dla określonego rejestru kontenerów |
> | Akcja | Microsoft. ContainerRegistry/rejestry/listUsages/odczyt | Wyświetla listę użycia przydziału dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/kancelarie/metadane/odczyt | Pobiera metadane określonego repozytorium dla rejestru kontenerów |
> | Akcja | Microsoft. ContainerRegistry/kancelarie/metadane/zapis | Aktualizuje metadane repozytorium dla rejestru kontenerów |
> | Akcja | Microsoft. ContainerRegistry/rejestry/operationStatuses/odczyt | Pobiera stan operacji asynchronicznej rejestru |
> | Akcja | Microsoft. ContainerRegistry/rejestry/ściąganie/odczyt | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/wypychanie/zapis | Wypchnij lub Zapisz obrazy do rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/quarantineRead/odczyt | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
> | Akcja | Microsoft. ContainerRegistry/rejestry/quarantineWrite/zapis | Zapisywanie/modyfikowanie stanu kwarantanny obrazów z kwarantanny |
> | Akcja | Microsoft. ContainerRegistry/rejestry/queueBuild/akcja | Tworzy nową kompilację opartą na parametrach żądania i Dodaj ją do kolejki kompilacji. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/odczyty | Pobiera właściwości określonego rejestru kontenerów lub wyświetla listę wszystkich rejestrów kontenerów w ramach określonej grupy zasobów lub subskrypcji. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/regenerateCredential/akcja | Generuje ponownie jedno z poświadczeń logowania dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/replikacje/usuwanie | Usuwa replikację z rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/replikacje/operationStatuses/odczyt | Pobiera stan operacji asynchronicznej replikacji |
> | Akcja | Microsoft. ContainerRegistry/rejestry/replikacje/odczyt | Pobiera właściwości określonej replikacji lub wyświetla wszystkie replikacje dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/replikacje/zapis | Tworzy lub aktualizuje replikację dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/uruchomienia/anulowanie/akcja | Anuluj istniejące uruchomienie. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/uruchomienia/listLogSasUrl/akcja | Pobiera adres URL sygnatury dostępu współdzielonego dziennika dla uruchomienia. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/uruchomienia/odczyt | Pobiera właściwości przebiegu dotyczącego przebiegu rejestru lub listy kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/uruchomienia/zapis | Aktualizuje przebieg. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/scheduleRun/akcja | Zaplanuj przebieg dla rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/rejestrowanie/zapisywanie | Wypychanie/Ściąganie metadanych zaufania zawartości dla rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/zadania/usuwanie | Usuwa zadanie dla rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/zadania/listDetails/akcja | Wyświetl listę wszystkich szczegółów zadania dla rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/zadania/odczyt | Pobiera zadanie dla rejestru kontenerów lub wyświetla listę wszystkich zadań. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/zadania/zapis | Tworzy lub aktualizuje zadanie dla rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/updatePolicies/zapis | Aktualizuje zasady dla określonego rejestru kontenerów |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/usuwanie | Usuwa element webhook z rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/getCallbackConfig/akcja | Pobiera konfigurację identyfikatora URI usługi i nagłówków niestandardowych dla elementu webhook. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/listEvents/akcja | Wyświetla listę ostatnich zdarzeń dla określonego elementu webhook. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/operationStatuses/odczyt | Pobiera stan operacji asynchronicznej elementu webhook |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/polecenie ping/akcja | Wyzwala zdarzenie ping do wysłania do elementu webhook. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/odczyt | Pobiera właściwości określonego elementu webhook lub wyświetla listę wszystkich elementów webhook dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/zapis | Tworzy lub aktualizuje element webhook dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/zapis | Tworzy lub aktualizuje rejestr kontenerów o określonych parametrach. |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ContainerService/containerServices/Delete | Usuwa usługę kontenera |
> | Akcja | Microsoft. ContainerService/containerServices/odczyt | Pobierz usługę kontenera |
> | Akcja | Microsoft. ContainerService/containerServices/Write | Tworzy nową usługę kontenera lub aktualizuje istniejącą |
> | Akcja | Microsoft. ContainerService/Locations/operationresults/Read | Pobiera stan wyniku operacji asynchronicznej |
> | Akcja | Microsoft. ContainerService/Locations/Operations/Read | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft. ContainerService/Locations/orchestrators/Read | Wyświetla listę obsługiwanych programów Orchestrator |
> | Akcja | Microsoft. ContainerService/managedClusters/accessProfiles/listCredential/Action | Uzyskiwanie zarządzanego profilu dostępu do klastra według nazwy roli przy użyciu poświadczeń listy |
> | Akcja | Microsoft. ContainerService/managedClusters/accessProfiles/Read | Uzyskaj zarządzany Profil dostępu do klastra według nazwy roli |
> | Akcja | Microsoft. ContainerService/managedClusters/agentPools/Delete | Usuwa pulę agentów |
> | Akcja | Microsoft. ContainerService/managedClusters/agentPools/Read | Pobiera pulę agentów |
> | Akcja | Microsoft. ContainerService/managedClusters/agentPools/upgradeProfiles/Read | Pobiera profil uaktualnienia puli agentów |
> | Akcja | Microsoft. ContainerService/managedClusters/agentPools/Write | Tworzy nową pulę agentów lub aktualizuje istniejącą. |
> | Akcja | Microsoft. ContainerService/managedClusters/Delete | Usuwa zarządzany klaster |
> | Akcja | Microsoft. ContainerService/managedClusters/detektory/odczyt | Pobierz detektor zarządzanego klastra |
> | Akcja | Microsoft. ContainerService/managedClusters/diagnosticsState/Read | Pobiera stan diagnostyki klastra |
> | Akcja | Microsoft. ContainerService/managedClusters/listClusterAdminCredential/akcja | Wyświetlanie clusterAdmin poświadczenia zarządzanego klastra |
> | Akcja | Microsoft. ContainerService/managedClusters/listClusterUserCredential/akcja | Wyświetlanie clusterUser poświadczenia zarządzanego klastra |
> | Akcja | Microsoft. ContainerService/managedClusters/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. ContainerService/managedClusters/odczyt | Pobierz zarządzany klaster |
> | Akcja | Microsoft. ContainerService/managedClusters/resetAADProfile/akcja | Resetowanie profilu usługi AAD w zarządzanym klastrze |
> | Akcja | Microsoft. ContainerService/managedClusters/resetServicePrincipalProfile/akcja | Resetowanie profilu jednostki usługi zarządzanego klastra |
> | Akcja | Microsoft. ContainerService/managedClusters/upgradeProfiles/Read | Pobiera profil uaktualnienia klastra |
> | Akcja | Microsoft. ContainerService/managedClusters/Write | Tworzy nowy zarządzany klaster lub aktualizuje istniejący. |
> | Akcja | Microsoft. ContainerService/openShiftClusters/Delete | Usuń otwarty klaster przesunięcia |
> | Akcja | Microsoft. ContainerService/openShiftClusters/odczyt | Pobierz otwarty klaster przesunięcia |
> | Akcja | Microsoft. ContainerService/openShiftClusters/Write | Tworzy nowy otwarty klaster przesunięcia lub aktualizuje istniejący |
> | Akcja | Microsoft. ContainerService/openShiftManagedClusters/Delete | Usuń otwarty klaster z zarządzaną przesunięciami |
> | Akcja | Microsoft. ContainerService/openShiftManagedClusters/odczyt | Pobierz klaster zarządzany za otwartym przesunięciami |
> | Akcja | Microsoft. ContainerService/openShiftManagedClusters/Write | Tworzy nowy klaster zarządzany przez przesunięcia lub aktualizuje istniejący |
> | Akcja | Microsoft. ContainerService/Operations/Read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. ContainerService |
> | Akcja | Microsoft. ContainerService/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. ContainerService |
> | Akcja | Microsoft. ContainerService/Unregister/Action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ContentModerator/aplikacje/usuwanie | Operacja usuwania |
> | Akcja | Microsoft. ContentModerator/Applications/listSecrets/Action | Wyświetl wpisy tajne |
> | Akcja | Microsoft. ContentModerator/Applications/listSingleSignOnToken/Action | Odczytaj tokeny logowania jednokrotnego |
> | Akcja | Microsoft. ContentModerator/Applications/Read | Operacja odczytu |
> | Akcja | Microsoft. ContentModerator/Applications/Write | Operacja zapisu |
> | Akcja | Microsoft. ContentModerator/Applications/Write | Operacja zapisu |
> | Akcja | Microsoft. ContentModerator/listCommunicationPreference/Action | Wyświetl preferencję komunikacji |
> | Akcja | Microsoft. ContentModerator/Operations/Read | operacje odczytu |
> | Akcja | Microsoft. ContentModerator/updateCommunicationPreference/Action | Aktualizuj preferencję komunikacji |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. CostManagement/cloudConnectors/Delete | Usuń określony cloudConnector. |
> | Akcja | Microsoft. CostManagement/cloudConnectors/odczyt | Wyświetl listę cloudConnectors dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft. CostManagement/cloudConnectors/Write | Utwórz lub zaktualizuj określony cloudConnector. |
> | Akcja | Microsoft. CostManagement/Dimensions/Read | Wyświetl listę wszystkich obsługiwanych wymiarów według zakresu. |
> | Akcja | Microsoft. CostManagement/eksporty/akcja | Uruchom określony eksport. |
> | Akcja | Microsoft. CostManagement/exports/Delete | Usuń określony eksport. |
> | Akcja | Microsoft. CostManagement/exports/Read | Wyświetl listę eksportów według zakresu. |
> | Akcja | Microsoft. CostManagement/exports/Run/Action | Uruchom eksporty. |
> | Akcja | Microsoft. CostManagement/exports/Write | Utwórz lub zaktualizuj określony eksport. |
> | Akcja | Microsoft. CostManagement/externalBillingAccounts/externalSubscriptions/Read | Wyświetl listę externalSubscriptions w externalBillingAccount dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft. CostManagement/externalBillingAccounts/odczyt | Wyświetl listę externalBillingAccounts dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft. CostManagement/externalSubscriptions/odczyt | Wyświetl listę externalSubscriptions dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft. CostManagement/externalSubscriptions/Write | Aktualizuj skojarzoną grupę zarządzania externalSubscription |
> | Akcja | Microsoft. CostManagement/kwerenda/akcja | Wykonywanie zapytań o dane użycia według zakresu. |
> | Akcja | Microsoft. CostManagement/Query/Read | Wykonywanie zapytań o dane użycia według zakresu. |
> | Akcja | Microsoft. CostManagement/Register/Action | Zarejestruj akcję dla zakresu Microsoft. CostManagement przez subskrypcję. |
> | Akcja | Microsoft. CostManagement/raporty/akcja | Zaplanuj raporty dotyczące danych użycia według zakresu. |
> | Akcja | Microsoft. CostManagement/Reports/Read | Zaplanuj raporty dotyczące danych użycia według zakresu. |
> | Akcja | Microsoft. CostManagement/dzierżawcy/rejestr/akcja | Zarejestruj akcję dla zakresu Microsoft. CostManagement przez dzierżawcę. |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DataBox/Jobs/bookShipmentPickUp/akcja | Umożliwia zaksięgowanie pobrania dla wysyłek zwrotnych. |
> | Akcja | Microsoft. DataBox/Jobs/Cancel/Action | Anuluje zamówienie w toku. |
> | Akcja | Microsoft. DataBox/Jobs/Delete | Usuwanie zamówień |
> | Akcja | Microsoft. DataBox/Jobs/listCredentials/akcja | Wyświetla listę nieszyfrowanych poświadczeń związanych z kolejnością. |
> | Akcja | Microsoft. DataBox/Jobs/Read | Wyświetl lub Pobierz zamówienia |
> | Akcja | Microsoft. DataBox/Jobs/Write | Utwórz lub zaktualizuj zamówienia |
> | Akcja | Microsoft. DataBox/Locations/availableSkus/Action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | Akcja | Microsoft. DataBox/Locations/availableSkus/Read | Wyświetl lub uzyskaj dostępne jednostki SKU |
> | Akcja | Microsoft. DataBox/Locations/operationResults/Read | Wyświetl lub Pobierz wyniki operacji |
> | Akcja | Microsoft. DataBox/Locations/regionConfiguration/Action | Ta metoda zwraca konfiguracje dla regionu. |
> | Akcja | Microsoft. DataBox/Locations/validateAddress/Action | Sprawdza poprawność adresu wysyłkowego i udostępnia alternatywne adresy, jeśli istnieją. |
> | Akcja | Microsoft. DataBox/Locations/validateInputs/Action | Ta metoda wykonuje wszystkie typy walidacji. |
> | Akcja | Microsoft. DataBox/Operations/Read | Wyświetl lub Pobierz operacje |
> | Akcja | Microsoft. DataBox/Register/Action | Zarejestruj dostawcę Microsoft. DATAbox |
> | Akcja | Microsoft. DataBox/subscriptions/resourceGroups/moveResources/Action | Ta metoda służy do przenoszenia zasobu. |
> | Akcja | Microsoft. DataBox/subscriptions/resourceGroups/validateMoveResources/Action | Ta metoda sprawdza, czy przenoszenie zasobów jest dozwolone. |
> | Akcja | Microsoft. DataBox/Unregister/Action | Wyrejestruj dostawcę Microsoft. DATAbox |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Alerts/Read | Wyświetla lub pobiera alerty |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Alerts/Read | Wyświetla lub pobiera alerty |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Delete | Usuwa harmonogramy przepustowości |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Wyświetla lub pobiera harmonogramy przepustowości |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Wyświetla lub pobiera harmonogramy przepustowości |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Write | Tworzy lub aktualizuje harmonogramy przepustowości |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Delete | Usuwa urządzenia Data Box Edge |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/akcja | Pobierz aktualizacje w urządzeniu |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/akcja | Pobiera rozszerzone informacje o zasobach |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/installUpdates/akcja | Zainstaluj aktualizacje na urządzeniu |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Jobs/odczyt | Wyświetla lub pobiera zadania |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/networkSettings/Read | Wyświetla lub pobiera ustawienia sieci urządzenia |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/nodes/Read | Wyświetla lub pobiera węzły |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationsStatus/Read | Wyświetla lub Pobiera stan operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Delete | Usuwa zamówienia |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Wyświetla lub pobiera zamówienia |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Wyświetla lub pobiera zamówienia |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Write | Tworzy lub aktualizuje zamówienia |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/odczyt | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/odczyt | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/odczyt | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/usuwanie | Usuwa role |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/odczyt | Wyświetla lub pobiera role |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/odczyt | Wyświetla lub pobiera role |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/Write | Tworzy lub aktualizuje role |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/akcja | Skanuj w poszukiwaniu aktualizacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/Update/Action | Aktualizowanie ustawień zabezpieczeń |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/udziały/usuwanie | Usuwa udziały |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/Refresh/Action | Odświeżanie metadanych udziału przy użyciu danych z chmury |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/udziały/zapis | Tworzy lub aktualizuje udziały |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Delete | Usuwa poświadczenia konta magazynu |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Write | Tworzy lub aktualizuje poświadczenia konta magazynu |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/Delete | Usuwa wyzwalacze |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/Read | Wyświetla lub pobiera wyzwalacze |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/Read | Wyświetla lub pobiera wyzwalacze |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/Write | Tworzy lub aktualizuje wyzwalacze |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/updateSummary/Read | Wyświetla lub pobiera podsumowanie aktualizacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/akcja | Przekaż certyfikat do rejestracji urządzenia |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/usuwanie | Usuwa użytkowników udziału |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/odczyt | Wyświetla lub pobiera użytkowników udziału |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/odczyt | Wyświetla lub pobiera użytkowników udziału |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/zapis | Tworzy lub aktualizuje udział użytkowników |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Tworzy lub aktualizuje urządzenia Data Box Edge |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Tworzy lub aktualizuje urządzenia Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft. datacegły

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. datacegły/lokalizacje/getNetworkPolicies/akcja | Pobierz zasady dotyczące zastosowania sieci dla podsieci na podstawie lokalizacji używanej przez dostawcy NRP |
> | Akcja | Microsoft. datacegły/rejestr/akcja | Zarejestruj się w kostkach. |
> | Akcja | Microsoft. datacegły/obszary robocze/usuwanie | Usuwa obszar roboczy datakosteks. |
> | Akcja | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Ustawia dostępne ustawienia diagnostyczne dla obszaru roboczego datakosteks |
> | Akcja | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne definicje dzienników dla obszaru roboczego datakosteks |
> | Akcja | Microsoft. datacegły/obszary robocze/odczyt | Pobiera listę obszarów roboczych elementów datakosteks. |
> | Akcja | Microsoft. datacegły/obszary robocze/zapis | Tworzy obszar roboczy datakosteks. |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. datacatalog/wykazy/usuwanie | Usuń zasób wykazów dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft. datacatalog/wykazy/odczyt | Zasób wykazów odczytu dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft. datacatalog/wykazy/zapis | Zasób katalogu zapisu dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft. datacatalog/checkNameAvailability/Read | Sprawdź dostępność nazwy wykazu dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft. datacatalog/datacatalogs/Delete | Usuń zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft. datacatalog/datacatalogs/Read | Odczytaj zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft. datacatalog/datacatalogs/Write | Zapisz zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft. datacatalog/Operations/Read | Odczytuje wszystkie dostępne operacje w Data Catalog dostawcy zasobów. |
> | Akcja | Microsoft. datacatalog/Register/Action | Zarejestruj subskrypcję dla dostawcy zasobów Data Catalog |
> | Akcja | Microsoft. datacatalog/Unregister/Action | Wyrejestrowywanie subskrypcji dla dostawcy zasobów Data Catalog |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DataFactory/checkazuredatafactorynameavailability/Read | Sprawdza, czy nazwa Data Factory jest dostępna do użycia. |
> | Akcja | Microsoft. DataFactory/datafactors/activitywindows/Read | Odczytuje okna działania w Data Factory z określonymi parametrami. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Activities/activitywindows/Read | Odczytuje okna aktywności dla działania potoku z określonymi parametrami. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/activitywindows/Read | Odczytuje okna działań dla potoku z określonymi parametrami. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Delete | Usuwa każdy potok. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Pause/Action | Wstrzymuje wszystkie potoki. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Read | Odczytuje wszystkie potoki. |
> | Akcja | Microsoft. DataFactory/datafactors///Resume/akcja | Wznawia wszystkie potoki. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Update/Action | Aktualizuje każdy potok. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Write | Tworzy lub aktualizuje każdy potok. |
> | Akcja | Microsoft. DataFactory/datafactors/dataactivitywindowss/Read | Odczytuje okna działań dla zestawu danych z określonymi parametrami. |
> | Akcja | Microsoft. DataFactory/datafabrykas/datafactors/Delete | Usuwa dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/datafactors/datadatasets/Read | Odczytuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/datafactors/dataslicerunss/Read | Odczytuje wycinek danych uruchomienia dla danego zestawu danych z danym czasem rozpoczęcia. |
> | Akcja | Microsoft. DataFactory/datafactors/datadatasets/odczytane | Pobiera wycinki danych w danym okresie. |
> | Akcja | Microsoft. DataFactory/datafactorers/zestawy danych/wycinki/zapis | Zaktualizuj stan wycinka danych. |
> | Akcja | Microsoft. DataFactory/datafactors/DataSets/Write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/datafactors/Delete | Usuwa Data Factory. |
> | Akcja | Microsoft. DataFactory/datafactors/Gateways/ConnectionInfo/Action | Odczytuje informacje o połączeniu dla każdej bramy. |
> | Akcja | Microsoft. DataFactory/datafactors/Gateways/Delete | Usuwa wszystkie bramy. |
> | Akcja | Microsoft. DataFactory/datafactors/Gateways/listauthkeys/Action | Wyświetla listę kluczy uwierzytelniania dla każdej bramy. |
> | Akcja | Microsoft. DataFactory/datafactors/Gateways/Read | Odczytuje każdą bramę. |
> | Akcja | Microsoft. DataFactory/datafactors/Gateways/regenerateauthkey/Action | Generuje ponownie klucze uwierzytelniania dla każdej bramy. |
> | Akcja | Microsoft. DataFactory/datafabrykas/Gateways/Write | Tworzy lub aktualizuje bramę. |
> | Akcja | Microsoft. DataFactory/datafactors/linkedServices/Delete | Usuwa wszystkie połączone usługi. |
> | Akcja | Microsoft. DataFactory/datafactors/linkedServices/Read | Odczytuje wszystkie połączone usługi. |
> | Akcja | Microsoft. DataFactory/datafactors/linkedServices/Write | Tworzy lub aktualizuje każdą połączoną usługę. |
> | Akcja | Microsoft. DataFactory/datafactors/Read | Odczytuje Data Factory. |
> | Akcja | Microsoft. DataFactory/datafactors/LogInfo/Read | Odczytuje identyfikator URI sygnatury dostępu współdzielonego do kontenera obiektów BLOB zawierającego dzienniki. |
> | Akcja | Microsoft. DataFactory/datafactors/Tables/Delete | Usuwa dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/datafactors/Tables/Read | Odczytuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/datafactors/Tables/Write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/datafactors/Write | Tworzy lub aktualizuje Data Factory. |
> | Akcja | Microsoft. DataFactory/Factors/cancelpipelinerun/Action | Anuluje uruchomienie potoku określone przez identyfikator uruchomienia. |
> | Akcja | Microsoft. DataFactory/Factors/cancelSandboxPipelineRun/Action | Anuluje uruchomienie debugowania dla potoku. |
> | Akcja | Microsoft. DataFactory/Factors/createdataflowdebugsession/Action | Tworzy sesję debugowania przepływu danych. |
> | Akcja | Microsoft. DataFactory/Factors/dataflows/Delete | Usuwa przepływ danych. |
> | Akcja | Microsoft. DataFactory/Factors/dataflows/Read | Odczytuje przepływ danych. |
> | Akcja | Microsoft. datafabryka/fabryki/dataflows/Write | Utwórz lub zaktualizuj przepływ danych |
> | Akcja | Microsoft. datafabryka/fabryki/zestawy danych/usuwanie | Usuwa dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/Factors/DataSets/odczyt | Odczytuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/Factors/DataSets/Write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/Factors/debugpipelineruns/Cancel/Action | Anuluje uruchomienie debugowania dla potoku. |
> | Akcja | Microsoft. DataFactory/Factors/Delete | Usuwa Data Factory. |
> | Akcja | Microsoft. DataFactory/Factors/deletedataflowdebugsession/Action | Usuwa sesję debugowania przepływu danych. |
> | Akcja | Microsoft. DataFactory/Factors/getDataPlaneAccess/Action | Uzyskuje dostęp do usługi dataplaning ADF. |
> | Akcja | Microsoft. DataFactory/Factors/getDataPlaneAccess/Read | Odczytuje dostęp do usługi dataplaning ADF. |
> | Akcja | Microsoft. DataFactory/Factors/getFeatureValue/Action | Wartość funkcji kontroli ekspozycji dla określonej lokalizacji. |
> | Akcja | Microsoft. DataFactory/Factors/getFeatureValue/Read | Odczytuje wartość funkcji kontroli zagrożeń dla określonej lokalizacji. |
> | Akcja | Microsoft. DataFactory/Factors/getGitHubAccessToken/Action | Pobiera token dostępu usługi GitHub. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Delete | Usuwa wszystkie Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/GetConnectionInfo/Read | Odczytuje Integration Runtime informacji o połączeniu. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/getObjectMetadata/Action | Pobierz metadane usług SSIS Integration Runtime dla określonego Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/GetStatus/Read | Odczytuje Integration Runtime stanu. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/linkedIntegrationRuntime/Action | Utwórz połączone odwołanie Integration Runtime w określonym Integration Runtime udostępnionym. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/listauthkeys/Read | Wyświetla listę kluczy uwierzytelniania dla każdej Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/monitoringdata/Read | Pobiera dane monitorowania dla dowolnych Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Delete | Usuwa węzeł dla podanej Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/nodes/ipAddress/Action | Zwraca adres IP dla określonego węzła Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Read | Odczytuje węzeł dla określonego Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Write | Aktualizuje węzeł Integration Runtime samoobsługowego. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Read | Odczytuje wszystkie Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/refreshObjectMetadata/Action | Odśwież Integration Runtime metadanych dla określonego Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/regenerateauthkey/Action | Generuje ponownie klucze uwierzytelniania dla podanej Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/removelinks/Action | Usuwa połączone Integration Runtime odwołania z określonego Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Start/Action | Uruchamia dowolne Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Stop/Action | Powoduje zatrzymanie wszelkich Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/synccredentials/Action | Synchronizuje poświadczenia dla określonego Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/upgrade/Action | Uaktualnia określony Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Write | Tworzy lub aktualizuje wszystkie Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/linkedServices/Delete | Usuwa połączoną usługę. |
> | Akcja | Microsoft. DataFactory/Factors/linkedServices/Read | Odczytuje połączoną usługę. |
> | Akcja | Microsoft. DataFactory/Factors/linkedServices/Write | Utwórz lub zaktualizuj połączoną usługę |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/activityruns/Read | Odczytuje uruchomienia działania dla określonego identyfikatora uruchomienia potoku. |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/Cancel/Action | Anuluje uruchomienie potoku określone przez identyfikator uruchomienia. |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/queryactivityruns/Action | Wysyła zapytanie do przebiegów działania dla określonego identyfikatora uruchomienia potoku. |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/queryactivityruns/Read | Odczytuje wynik przebiegu działania zapytania dla określonego identyfikatora uruchomienia potoku. |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/Read | Odczytuje uruchomienia potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/createrun/akcja | Tworzy przebieg dla potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/usuwanie | Usuwa potok. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/pipelineruns/activityruns/postęp/odczyt | Pobiera postęp uruchomienia działania. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/pipelineruns/odczyt | Odczytuje uruchomienie potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/odczyt | Odczytuje potok. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/piaskownica/akcja | Tworzy środowisko uruchamiania debugowania dla potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/piaskownica/Utwórz/akcję | Tworzy środowisko uruchamiania debugowania dla potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/piaskownica/Uruchom/akcja | Tworzy przebieg debugowania dla potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/zapis | Utwórz lub zaktualizuj potok |
> | Akcja | Microsoft. DataFactory/Factors/querydebugpipelineruns/Action | Wysyła zapytanie do przebiegów potoku debugowania. |
> | Akcja | Microsoft. DataFactory/Factors/querypipelineruns/Action | Wykonuje zapytanie o uruchomienia potoku. |
> | Akcja | Microsoft. DataFactory/Factors/querypipelineruns/Read | Odczytuje wynik uruchomienia potoku zapytania. |
> | Akcja | Microsoft. DataFactory/Factors/querytriggerruns/Action | Wysyła zapytanie do uruchomienia wyzwalacza. |
> | Akcja | Microsoft. DataFactory/Factors/querytriggerruns/Read | Odczytuje wynik uruchomienia wyzwalacza. |
> | Akcja | Microsoft. DataFactory/Factors/Read | Odczytuje Data Factory. |
> | Akcja | Microsoft. DataFactory/Factors/sandboxpipelineruns/sandboxActivityRuns/Read | Pobiera informacje o uruchamianiu debugowania dla działania. |
> | Akcja | Microsoft. DataFactory/Factors/startdataflowdebugsession/Action | Uruchamia sesję debugowania przepływu danych. |
> | Akcja | Microsoft. DataFactory/Factors/submitDataFlowForPreview/Action | Prześlij przepływ danych, aby uzyskać podgląd danych przy użyciu sesji debugowania. |
> | Akcja | Microsoft. DataFactory/Factors/triggerruns/Read | Odczytuje uruchomienia wyzwalacza. |
> | Akcja | Microsoft. DataFactory/fabryki/wyzwalacze/usuwanie | Usuwa każdy wyzwalacz. |
> | Akcja | Microsoft. DataFactory/fabryki/wyzwalacze/odczyt | Odczytuje dowolny wyzwalacz. |
> | Akcja | Microsoft. DataFactory/Factors/Triggers/Start/Action | Uruchamia dowolny wyzwalacz. |
> | Akcja | Microsoft. DataFactory/Factors/Triggers/akcja | Powoduje zatrzymanie dowolnego wyzwalacza. |
> | Akcja | Microsoft. DataFactory/Factors/Triggers/triggerruns/Read | Odczytuje uruchomienia wyzwalacza. |
> | Akcja | Microsoft. DataFactory/fabryki/wyzwalacze/zapis | Tworzy lub aktualizuje dowolny wyzwalacz. |
> | Akcja | Microsoft. DataFactory/Factors/Write | Utwórz lub zaktualizuj Data Factory |
> | Akcja | Microsoft. DataFactory/Locations/configureFactoryRepo/Action | Konfiguruje repozytorium dla fabryki. |
> | Akcja | Microsoft. DataFactory/Locations/getFeatureValue/Action | Wartość funkcji kontroli ekspozycji dla określonej lokalizacji. |
> | Akcja | Microsoft. DataFactory/Locations/getFeatureValue/Read | Odczytuje wartość funkcji kontroli zagrożeń dla określonej lokalizacji. |
> | Akcja | Microsoft. DataFactory/Operations/Read | Odczytuje wszystkie operacje w programie Microsoft Data Factory Provider. |
> | Akcja | Microsoft. DataFactory/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów Data Factory. |
> | Akcja | Microsoft. DataFactory/Unregister/Action | Wyrejestrowuje subskrypcję dla dostawcy zasobów Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/computePolicies/Delete | Usuń zasady obliczeń. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/computePolicies/Read | Uzyskaj informacje na temat zasad obliczeniowych. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/computePolicies/Write | Utwórz lub zaktualizuj zasady obliczeniowe. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Odłączanie konta kontach datalakestore z konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Read | Uzyskaj informacje o połączonym koncie kontach datalakestore konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Utwórz lub zaktualizuj połączone konto kontach datalakestore konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/Delete | Usuń konto DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/firewallRules/Read | Uzyskaj informacje na temat reguły zapory. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/operationResults/Read | Pobierz wynik operacji konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/Read | Pobierz informacje o istniejącym koncie DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/Action | Wyświetl listę tokenów sygnatury dostępu współdzielonego dla kontenerów magazynu połączonego konta magazynu konta usługi DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Containers/Read | Pobierz kontenery połączonego konta magazynu dla konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Delete | Odłączanie konta magazynu od konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Read | Uzyskaj informacje o połączonym koncie magazynu konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Write | Utwórz lub zaktualizuj połączone konto magazynu dla konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/TakeOwnership/Action | Udziel uprawnień do anulowania zadań przesłanych przez innych użytkowników. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/transferAnalyticsUnits/Action | Przenoszenie SystemMaxAnalyticsUnits między kontami DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Delete | Usuń regułę sieci wirtualnej. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Read | Uzyskaj informacje na temat reguły sieci wirtualnej. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Akcja | Microsoft. DataLakeAnalytics/accounts/Write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/lokalizacje/możliwości/odczyt | Uzyskaj informacje o możliwościach subskrypcji dotyczące korzystania z DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/Locations/checkNameAvailability/Action | Sprawdź dostępność nazwy konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/Locations/operationResults/Read | Pobierz wynik operacji konta DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/lokalizacje/użycia/odczyt | Uzyskaj informacje dotyczące użycia przydziału dla subskrypcji dotyczącej korzystania z DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/Operations/Read | Pobierz dostępne operacje DataLakeAnalytics. |
> | Akcja | Microsoft. DataLakeAnalytics/Register/Action | Zarejestruj subskrypcję usługi DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft. kontach datalakestore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. kontach datalakestore/accounts/Delete | Usuń konto kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/accounts/enableKeyVault/Action | Włącz Magazyn kluczy dla konta kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/accounts/eventGridFilters/Delete | Usuń filtr EventGrid. |
> | Akcja | Microsoft. kontach datalakestore/accounts/eventGridFilters/Read | Pobierz filtr EventGrid. |
> | Akcja | Microsoft. kontach datalakestore/accounts/eventGridFilters/Write | Utwórz lub zaktualizuj filtr EventGrid. |
> | Akcja | Microsoft. kontach datalakestore/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | Akcja | Microsoft. kontach datalakestore/accounts/firewallRules/Read | Uzyskaj informacje na temat reguły zapory. |
> | Akcja | Microsoft. kontach datalakestore/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | Akcja | Microsoft. kontach datalakestore/accounts/operationResults/Read | Pobierz wynik operacji konta kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/accounts/Read | Pobierz informacje o istniejącym koncie kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/accounts/shares/Delete | Usuń udział. |
> | Akcja | Microsoft. kontach datalakestore/accounts/shares/Read | Pobierz informacje o udziale. |
> | Akcja | Microsoft. kontach datalakestore/accounts/shares/Write | Utwórz lub zaktualizuj udział. |
> | Akcja | Microsoft.DataLakeStore/accounts/Superuser/action | Przyznaj administratora na Data Lake Store po udzieleniu z firmą Microsoft. Authorization/roleAssignments/Write. |
> | Akcja | Microsoft. kontach datalakestore/accounts/trustedIdProviders/Delete | Usuń zaufanego dostawcę tożsamości. |
> | Akcja | Microsoft. kontach datalakestore/accounts/trustedIdProviders/Read | Uzyskaj informacje na temat zaufanego dostawcy tożsamości. |
> | Akcja | Microsoft. kontach datalakestore/accounts/trustedIdProviders/Write | Utwórz lub zaktualizuj zaufanego dostawcę tożsamości. |
> | Akcja | Microsoft. kontach datalakestore/accounts/virtualNetworkRules/Delete | Usuń regułę sieci wirtualnej. |
> | Akcja | Microsoft. kontach datalakestore/accounts/virtualNetworkRules/Read | Uzyskaj informacje na temat reguły sieci wirtualnej. |
> | Akcja | Microsoft. kontach datalakestore/accounts/virtualNetworkRules/Write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Akcja | Microsoft. kontach datalakestore/accounts/Write | Utwórz lub zaktualizuj konto kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/lokalizacje/możliwości/odczyt | Uzyskaj informacje o możliwościach subskrypcji dotyczące korzystania z kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/Locations/checkNameAvailability/Action | Sprawdź dostępność nazwy konta kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/Locations/operationResults/Read | Pobierz wynik operacji konta kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/lokalizacje/użycia/odczyt | Uzyskaj informacje dotyczące użycia przydziału dla subskrypcji dotyczącej korzystania z kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/Operations/Read | Pobierz dostępne operacje kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/Register/Action | Zarejestruj subskrypcję usługi kontach datalakestore. |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. datamigration/Locations/operationResults/Read | Pobieranie stanu długotrwałej operacji związanej z zaakceptowaną odpowiedzią 202 |
> | Akcja | Microsoft. datamigration/Locations/operationStatuses/Read | Pobieranie stanu długotrwałej operacji związanej z zaakceptowaną odpowiedzią 202 |
> | Akcja | Microsoft. datamigration/Register/Action | Rejestruje subskrypcję za pomocą dostawcy Azure Database Migration Service |
> | Akcja | Microsoft. datamigration/Services/addworker/Action | Dodaje proces roboczy DMS do dostępny procesów roboczych usługi |
> | Akcja | Microsoft. datamigration/Services/checkStatus/Action | Sprawdź, czy usługa jest wdrożona i uruchomiona |
> | Akcja | Microsoft. datamigration/Services/configureWorker/Action | Konfiguruje proces roboczy DMS dla procesów roboczych dostępny usługi |
> | Akcja | Microsoft. datamigration/Services/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. datamigration/Services/projects/accessArtifacts/Action | Generowanie adresu URL, którego można użyć do pobierania lub UMIESZCZAnia artefaktów projektu |
> | Akcja | Microsoft. datamigration/Services/projects/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. datamigration/Services/projects/Files/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. datamigration/Services/projects/Files/Read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft. datamigration/Services/projects/Files/Read/Action | Uzyskaj adres URL, za pomocą którego można odczytać zawartość pliku |
> | Akcja | Microsoft. datamigration/Services/projects/Files/readWrite/Action | Uzyskaj adres URL, za pomocą którego można odczytywać lub zapisywać zawartość pliku |
> | Akcja | Microsoft. datamigration/Services/projects/Files/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Akcja | Microsoft. datamigration/Services/projects/Read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft. datamigration/Services/projects/Tasks/Cancel/Action | Anuluj zadanie, jeśli jest aktualnie uruchomione |
> | Akcja | Microsoft. datamigration/Services/projects/Tasks/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. datamigration/Services/projects/Tasks/Read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft. datamigration/Services/projects/Tasks/Write | Uruchom zadania Azure Database Migration Service zadania |
> | Akcja | Microsoft. datamigration/Services/projects/Write | Uruchom zadania Azure Database Migration Service zadania |
> | Akcja | Microsoft. datamigration/Services/Read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft. datamigration/Services/removeWorker/Action | Usuwa proces roboczy DMS do dostępny procesów roboczych usługi |
> | Akcja | Microsoft. datamigration/Services/servicetasks/Cancel/Action | Anuluj zadanie, jeśli jest aktualnie uruchomione |
> | Akcja | Microsoft. datamigration/Services/servicetasks/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. datamigration/Services/servicetasks/Read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft. datamigration/Services/servicetasks/Write | Uruchom zadania Azure Database Migration Service zadania |
> | Akcja | Microsoft. datamigration/Services/Slots/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. datamigration/Services/Slots/Read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft. datamigration/Services/Slots/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Akcja | Microsoft. datamigration/Services/Start/akcja | Uruchom usługę DMS, aby umożliwić jej ponowne przetworzenie migracji |
> | Akcja | Microsoft. datamigration/Services/Stop/akcja | Zatrzymaj usługę DMS, aby zminimalizować jej koszt |
> | Akcja | Microsoft. datamigration/Services/updateAgentConfig/Action | Aktualizuje konfigurację agenta DMS przy użyciu podanych wartości. |
> | Akcja | Microsoft. datamigration/Services/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Akcja | Microsoft. datamigration/SKU/Read | Pobierz listę jednostek SKU obsługiwanych przez zasoby DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DBforMariaDB/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Akcja | Microsoft. DBforMariaDB/Locations/azureAsyncOperation/Read | Zwróć wyniki operacji serwera MariaDB |
> | Akcja | Microsoft. DBforMariaDB/Locations/operationResults/Read | Zwróć wyniki operacji serwera MariaDB opartego na źródle zasobów |
> | Akcja | Microsoft. DBforMariaDB/Locations/operationResults/Read | Zwróć wyniki operacji serwera MariaDB |
> | Akcja | Microsoft. DBforMariaDB/Locations/performanceTiers/Read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesAzureAsyncOperation/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesOperationResults/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft. DBforMariaDB/Operations/Read | Zwróć listę operacji MariaDB. |
> | Akcja | Microsoft. DBforMariaDB/performanceTiers/odczyt | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft. DBforMariaDB/Register/Action | Zarejestruj dostawcę zasobów MariaDB |
> | Akcja | Microsoft. DBforMariaDB/serwery/Administratorzy/usuwanie | Usuwa istniejącego administratora serwera MariaDB. |
> | Akcja | Microsoft. DBforMariaDB/serwery/Administratorzy/odczyt | Pobiera listę administratorów serwera MariaDB. |
> | Akcja | Microsoft. DBforMariaDB/serwery/Administratorzy/zapis | Tworzy lub aktualizuje administratora serwera MariaDB o określonych parametrach. |
> | Akcja | Microsoft. DBforMariaDB/serwery/doradcy/createRecommendedActionSession/akcja | Utwórz nową sesję akcji rekomendacji |
> | Akcja | Microsoft. DBforMariaDB/serwery/doradcy/odczyt | Zwróć listę doradców |
> | Akcja | Microsoft. DBforMariaDB/serwery/doradcy/odczyt | Zwracanie klasyfikatora |
> | Akcja | Microsoft. DBforMariaDB/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft. DBforMariaDB/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft. DBforMariaDB/Servers/Advisors/recommendedActions/Read | Zwróć zalecaną akcję |
> | Akcja | Microsoft. DBforMariaDB/serwery/konfiguracje/odczyt | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft. DBforMariaDB/serwery/konfiguracje/zapis | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft. DBforMariaDB/serwery/bazy danych/usuwanie | Usuwa istniejącą bazę danych MariaDB. |
> | Akcja | Microsoft. DBforMariaDB/serwery/bazy danych/Odczyt | Zwróć listę baz danych MariaDB lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft. DBforMariaDB/serwery/bazy danych/zapis | Tworzy bazę danych MariaDB z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Akcja | Microsoft. DBforMariaDB/serwery/usuwanie | Usuwa istniejący serwer. |
> | Akcja | Microsoft. DBforMariaDB/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft. DBforMariaDB/serwery/firewallRules/odczyt | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Akcja | Microsoft. DBforMariaDB/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft. DBforMariaDB/serwery/logFiles/odczyt | Zwróć listę plików dziennika MariaDB. |
> | Akcja | Microsoft. DBforMariaDB/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie disagnostic dla zasobu |
> | Akcja | Microsoft. DBforMariaDB/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. DBforMariaDB/serwery/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla serwerów MariaDB |
> | Akcja | Microsoft. DBforMariaDB/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft. DBforMariaDB/serwery/queryTexts/akcja | Zwróć teksty dla listy zapytań |
> | Akcja | Microsoft. DBforMariaDB/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Akcja | Microsoft. DBforMariaDB/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft. DBforMariaDB/serwery/recoverableServers/odczyt | Zwróć informacje o odzyskiwanym serwerze MariaDB |
> | Akcja | Microsoft. DBforMariaDB/serwery/repliki/odczyt | Pobierz repliki serwera MariaDB |
> | Akcja | Microsoft. DBforMariaDB/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Akcja | Microsoft. DBforMariaDB/serwery/securityAlertPolicies/odczyt | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft. DBforMariaDB/serwery/securityAlertPolicies/zapis | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft. DBforMariaDB/serwery/topQueryStatistics/odczyt | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Akcja | Microsoft. DBforMariaDB/serwery/topQueryStatistics/odczyt | Zwróć statystykę zapytania |
> | Akcja | Microsoft. DBforMariaDB/serwery/updateConfigurations/akcja | Zaktualizuj konfiguracje dla określonego serwera |
> | Akcja | Microsoft. DBforMariaDB/serwery/virtualNetworkRules/usuwanie | Usuwa istniejącą regułę Virtual Network |
> | Akcja | Microsoft. DBforMariaDB/serwery/virtualNetworkRules/odczyt | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft. DBforMariaDB/serwery/virtualNetworkRules/zapis | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft. DBforMariaDB/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania dla wystąpienia |
> | Akcja | Microsoft. DBforMariaDB/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania |
> | Akcja | Microsoft. DBforMariaDB/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DBforMySQL/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Akcja | Microsoft. DBforMySQL/Locations/azureAsyncOperation/Read | Zwróć wyniki operacji serwera MySQL |
> | Akcja | Microsoft. DBforMySQL/Locations/operationResults/Read | Zwróć wyniki operacji serwera MySQL na podstawie zasobów |
> | Akcja | Microsoft. DBforMySQL/Locations/operationResults/Read | Zwróć wyniki operacji serwera MySQL |
> | Akcja | Microsoft. DBforMySQL/Locations/performanceTiers/Read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft. DBforMySQL/Locations/securityAlertPoliciesAzureAsyncOperation/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft. DBforMySQL/Locations/securityAlertPoliciesOperationResults/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft. DBforMySQL/Operations/Read | Zwróć listę operacji programu MySQL. |
> | Akcja | Microsoft. DBforMySQL/performanceTiers/odczyt | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft. DBforMySQL/Register/Action | Zarejestruj dostawcę zasobów MySQL |
> | Akcja | Microsoft. DBforMySQL/serwery/Administratorzy/usuwanie | Usuwa istniejącego administratora serwera MySQL. |
> | Akcja | Microsoft. DBforMySQL/serwery/Administratorzy/odczyt | Pobiera listę administratorów serwera MySQL. |
> | Akcja | Microsoft. DBforMySQL/serwery/Administratorzy/zapis | Tworzy lub aktualizuje administratora serwera MySQL o określonych parametrach. |
> | Akcja | Microsoft. DBforMySQL/serwery/doradcy/createRecommendedActionSession/akcja | Utwórz nową sesję akcji rekomendacji |
> | Akcja | Microsoft. DBforMySQL/serwery/doradcy/odczyt | Zwróć listę doradców |
> | Akcja | Microsoft. DBforMySQL/serwery/doradcy/odczyt | Zwracanie klasyfikatora |
> | Akcja | Microsoft. DBforMySQL/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft. DBforMySQL/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft. DBforMySQL/Servers/Advisors/recommendedActions/Read | Zwróć zalecaną akcję |
> | Akcja | Microsoft. DBforMySQL/serwery/konfiguracje/odczyt | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft. DBforMySQL/serwery/konfiguracje/zapis | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft. DBforMySQL/serwery/bazy danych/usuwanie | Usuwa istniejącą bazę danych MySQL. |
> | Akcja | Microsoft. DBforMySQL/serwery/bazy danych/Odczyt | Zwróć listę baz danych MySQL lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft. DBforMySQL/serwery/bazy danych/zapis | Tworzy bazę danych MySQL z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Akcja | Microsoft. DBforMySQL/serwery/usuwanie | Usuwa istniejący serwer. |
> | Akcja | Microsoft. DBforMySQL/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft. DBforMySQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Akcja | Microsoft. DBforMySQL/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft. DBforMySQL/serwery/logFiles/odczyt | Zwróć listę plików dziennika PostgreSQL. |
> | Akcja | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie disagnostic dla zasobu |
> | Akcja | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla serwerów MySQL |
> | Akcja | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft. DBforMySQL/serwery/queryTexts/akcja | Zwróć teksty dla listy zapytań |
> | Akcja | Microsoft. DBforMySQL/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Akcja | Microsoft. DBforMySQL/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft. DBforMySQL/serwery/recoverableServers/odczyt | Zwróć informacje o odzyskiwanym serwerze MySQL |
> | Akcja | Microsoft. DBforMySQL/serwery/repliki/odczyt | Uzyskaj odczyt replik serwera MySQL |
> | Akcja | Microsoft. DBforMySQL/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Akcja | Microsoft. DBforMySQL/serwery/securityAlertPolicies/odczyt | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft. DBforMySQL/serwery/securityAlertPolicies/zapis | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft. DBforMySQL/serwery/topQueryStatistics/odczyt | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Akcja | Microsoft. DBforMySQL/serwery/topQueryStatistics/odczyt | Zwróć statystykę zapytania |
> | Akcja | Microsoft. DBforMySQL/serwery/updateConfigurations/akcja | Zaktualizuj konfiguracje dla określonego serwera |
> | Akcja | Microsoft. DBforMySQL/serwery/virtualNetworkRules/usuwanie | Usuwa istniejącą regułę Virtual Network |
> | Akcja | Microsoft. DBforMySQL/serwery/virtualNetworkRules/odczyt | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft. DBforMySQL/serwery/virtualNetworkRules/zapis | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft. DBforMySQL/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania dla wystąpienia |
> | Akcja | Microsoft. DBforMySQL/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania |
> | Akcja | Microsoft. DBforMySQL/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DBforPostgreSQL/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/azureAsyncOperation/Read | Zwróć wyniki operacji serwera PostgreSQL |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/operationResults/Read | Zwróć wyniki operacji serwera PostgreSQL opartego na źródle zasobów |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/operationResults/Read | Zwróć wyniki operacji serwera PostgreSQL |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/performanceTiers/Read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesAzureAsyncOperation/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesOperationResults/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft. DBforPostgreSQL/Operations/Read | Zwróć listę operacji PostgreSQL. |
> | Akcja | Microsoft. DBforPostgreSQL/performanceTiers/odczyt | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft. DBforPostgreSQL/Register/Action | Zarejestruj dostawcę zasobów PostgreSQL |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/Administratorzy/usuwanie | Usuwa istniejącego administratora serwera PostgreSQL. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/Administratorzy/odczyt | Pobiera listę administratorów serwera PostgreSQL. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/Administratorzy/zapis | Tworzy lub aktualizuje administratora serwera PostgreSQL o określonych parametrach. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/doradcy/odczyt | Zwróć listę doradców |
> | Akcja | Microsoft. DBforPostgreSQL/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/doradcy/recommendedActionSessions/akcja | Wykonaj zalecenia |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/konfiguracje/odczyt | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/konfiguracje/zapis | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/bazy danych/usuwanie | Usuwa istniejącą bazę danych PostgreSQL. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/bazy danych/Odczyt | Zwróć listę baz danych PostgreSQL lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/bazy danych/zapis | Tworzy bazę danych PostgreSQL z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/usuwanie | Usuwa istniejący serwer. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/logFiles/odczyt | Zwróć listę plików dziennika PostgreSQL. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/usuwanie | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/odczyt | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Validate/Action | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/zapis | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/usuwanie | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/odczyt | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/zapis | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateLinkResources/odczyt | Pobierz zasoby linku prywatnego dla odpowiedniego serwera PostgreSQL |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie disagnostic dla zasobu |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/queryTexts/odczyt | Zwróć teksty dla listy zapytań |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/recoverableServers/odczyt | Zwróć informacje o odzyskiwanym serwerze PostgreSQL |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/repliki/odczyt | Pobierz repliki serwera PostgreSQL |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/securityAlertPolicies/odczyt | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/securityAlertPolicies/zapis | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/topQueryStatistics/odczyt | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/updateConfigurations/akcja | Zaktualizuj konfiguracje dla określonego serwera |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/virtualNetworkRules/usuwanie | Usuwa istniejącą regułę Virtual Network |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/virtualNetworkRules/odczyt | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/virtualNetworkRules/zapis | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania dla wystąpienia |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/konfiguracje/odczyt | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/Configurations/Write | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/Delete | Usuwa istniejący serwer. |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Delete | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Read | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Write | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie disagnostic dla zasobu |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/updateConfigurations/akcja | Zaktualizuj konfiguracje dla określonego serwera |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/Write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdevices"></a>Microsoft. urządzenia

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Devices/Account/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/Account/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/Account/logDefinitions/Read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Akcja | Microsoft. Devices/Account/metricDefinitions/Read | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft. Devices/checkNameAvailability/akcja | Sprawdź, czy nazwa IotHub jest dostępna |
> | Akcja | Microsoft. Devices/checkNameAvailability/akcja | Sprawdź, czy nazwa IotHub jest dostępna |
> | Akcja | Microsoft. Devices/checkProvisioningServiceNameAvailability/akcja | Sprawdź, czy nazwa usługi aprowizacji jest dostępna |
> | Akcja | Microsoft. Devices/checkProvisioningServiceNameAvailability/akcja | Sprawdź, czy nazwa usługi aprowizacji jest dostępna |
> | Akcja | Microsoft. Devices/digitalTwins/Delete | Usuń istniejące konto bliźniaczych reprezentacji Digital i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. Devices/digitalTwins/operationresults/odczyt | Pobieranie stanu operacji przy użyciu konta bliźniaczych reprezentacji Digital |
> | Akcja | Microsoft. Devices/digitalTwins/odczyt | Pobiera listę kont bliźniaczych reprezentacji cyfrowych skojarzonych z subskrypcją |
> | Akcja | Microsoft. Devices/digitalTwins/SKU/odczyt | Pobierz listę prawidłowych jednostek SKU dla kont Digital bliźniaczych reprezentacji |
> | Akcja | Microsoft. Devices/digitalTwins/Write | Utwórz nowe konto bliźniaczych reprezentacji z cyframi |
> | Akcja | Microsoft. Devices/ElasticPools/diagnosticSettings/odczyt | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/ElasticPools/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Delete | Usuwanie zasobu dzierżawy IotHub |
> | Akcja | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Usuń grupę odbiorców EventHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Utwórz grupę odbiorców EventHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/exportDevices/Action | Eksportuj urządzenia |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/get\field/Read | Pobiera zasób statystyk dzierżawy IotHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/importDevices/Action | Importuj urządzenia |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/iotHubKeys/ListKeys/Action | Pobiera klucz dzierżawy IotHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Jobs/Read | Pobierz szczegóły zadań przesłane w danym IotHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/listKeys/Action | Pobiera klucze dzierżawy IotHub |
> | Akcja | Microsoft. Devices/ElasticPools/IotHubTenants/logDefinitions/Read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Akcja | Microsoft. Devices/ElasticPools/IotHubTenants/metricDefinitions/Read | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Pobierz metryki przydziału |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/odczyt | Pobiera zasób dzierżawy IotHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Routing/trasy/$testall/Action | Przetestuj komunikat dla wszystkich istniejących tras |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Routing/trasy/$testnew/Action | Przetestuj komunikat w odniesieniu do podanej trasy testowej |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla IotHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Write | Utwórz lub zaktualizuj zasób dzierżawy IotHub |
> | Akcja | Microsoft. Devices/ElasticPools/metricDefinitions/odczyt | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft. Devices/iotHubs/Certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft. Devices/iotHubs/Certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft. Devices/iotHubs/Certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft. Devices/iotHubs/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Akcja | Microsoft. Devices/iotHubs/Certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Akcja | Microsoft. Devices/iotHubs/Delete | Usuń zasób IotHub |
> | Akcja | Microsoft. Devices/IotHubs/diagnosticSettings/odczyt | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/IotHubs/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/iotHubs/eventGridFilters/Delete | Usuwa filtr Event Grid |
> | Akcja | Microsoft. Devices/iotHubs/eventGridFilters/odczyt | Pobiera Filtr Event Grid |
> | Akcja | Microsoft. Devices/iotHubs/eventGridFilters/Write | Utwórz nowy lub zaktualizuj istniejący filtr Event Grid |
> | Akcja | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Usuń grupę odbiorców EventHub |
> | Akcja | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Akcja | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Utwórz grupę odbiorców EventHub |
> | Akcja | Microsoft. Devices/iotHubs/exportDevices/akcja | Eksportuj urządzenia |
> | Akcja | Microsoft. Devices/iotHubs/importDevices/akcja | Importuj urządzenia |
> | Akcja | Microsoft. Devices/iotHubs/iotHubKeys/ListKeys/Action | Pobierz klucz IotHub dla podaną nazwę |
> | Akcja | Microsoft. Devices/iotHubs/iotHubStats/odczyt | Pobierz statystyki IotHub |
> | Akcja | Microsoft. Devices/iotHubs/Jobs/Read | Pobierz szczegóły zadań przesłane w danym IotHub |
> | Akcja | Microsoft. Devices/iotHubs/ListKeys/akcja | Pobierz wszystkie klucze IotHub |
> | Akcja | Microsoft. Devices/IotHubs/logDefinitions/odczyt | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Akcja | Microsoft. Devices/IotHubs/metricDefinitions/odczyt | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft. Devices/iotHubs/operationresults/odczyt | Pobierz wynik operacji (przestarzały interfejs API) |
> | Akcja | Microsoft. Devices/iotHubs/quotaMetrics/odczyt | Pobierz metryki przydziału |
> | Akcja | Microsoft. Devices/iotHubs/odczyt | Pobiera zasoby IotHub |
> | Akcja | Microsoft. Devices/iotHubs/Routing/$testall/Action | Przetestuj komunikat dla wszystkich istniejących tras |
> | Akcja | Microsoft. Devices/iotHubs/Routing/$testnew/Action | Przetestuj komunikat w odniesieniu do podanej trasy testowej |
> | Akcja | Microsoft. Devices/iotHubs/routingEndpointsHealth/odczyt | Pobiera kondycję wszystkich punktów końcowych routingu dla IotHub |
> | Akcja | Microsoft. Devices/iotHubs/SKU/odczyt | Pobieranie prawidłowych jednostek SKU IotHub |
> | Akcja | Microsoft. Devices/iotHubs/Write | Utwórz lub zaktualizuj zasób IotHub |
> | Akcja | Microsoft. Devices/Locations/operationresults/Read | Pobierz wynik operacji na podstawie lokalizacji |
> | Akcja | Microsoft. Devices/operationresults/odczyt | Pobierz wynik operacji |
> | Akcja | Microsoft. Devices/Operations/Read | Pobierz wszystkie operacje ResourceProvider |
> | Akcja | Microsoft. Devices/provisioningServices/Certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft. Devices/provisioningServices/Certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft. Devices/provisioningServices/Certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft. Devices/provisioningServices/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Akcja | Microsoft. Devices/provisioningServices/Certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Akcja | Microsoft. Devices/provisioningServices/Delete | Usuń zasób IotDps |
> | Akcja | Microsoft. Devices/provisioningServices/diagnosticSettings/odczyt | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/provisioningServices/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/provisioningServices/Keys/ListKeys/akcja | Pobierz klucze IotDps dla nazwy klucza |
> | Akcja | Microsoft. Devices/provisioningServices/ListKeys/akcja | Pobierz wszystkie klucze IotDps |
> | Akcja | Microsoft. Devices/provisioningServices/logDefinitions/odczyt | Pobiera dostępne definicje dzienników dla usługi aprowizacji |
> | Akcja | Microsoft. Devices/provisioningServices/metricDefinitions/odczyt | Pobiera dostępne metryki dla usługi aprowizacji |
> | Akcja | Microsoft. Devices/provisioningServices/operationresults/odczyt | Pobierz wynik operacji DPS |
> | Akcja | Microsoft. Devices/provisioningServices/odczyt | Pobierz zasób IotDps |
> | Akcja | Microsoft. Devices/provisioningServices/SKU/odczyt | Pobieranie prawidłowych jednostek SKU IotDps |
> | Akcja | Microsoft. Devices/provisioningServices/Write | Utwórz zasób IotDps |
> | Akcja | Microsoft. Devices/Register/Action | Zarejestruj subskrypcję dostawcy zasobów IotHub i Włącz tworzenie zasobów IotHub |
> | Akcja | Microsoft. urządzenia/użycia/odczyt | Pobierz szczegóły użycia subskrypcji dla tego dostawcy. |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DevSpaces/controllers/Delete | Usuwanie kontrolera Azure Dev Spaces i usług dataplaning |
> | Akcja | Microsoft. DevSpaces/controllers/listConnectionDetails/Action | Wyświetl szczegóły połączenia dla infrastruktury kontrolera Azure Dev Spaces |
> | Akcja | Microsoft. DevSpaces/controllers/Read | Odczytaj właściwości kontrolera Azure Dev Spaces |
> | Akcja | Microsoft. DevSpaces/controllers/Write | Utwórz lub zaktualizuj właściwości kontrolera Azure Dev Spaces |
> | Akcja | Microsoft. DevSpaces/Locations/checkContainerHostMapping/Action | Sprawdź istniejące mapowanie kontrolera dla hosta kontenera |
> | Akcja | Microsoft. DevSpaces/Locations/operationresults/Read | Stan odczytu operacji asynchronicznej |
> | Akcja | Microsoft. DevSpaces/Register/Action | Rejestrowanie dostawcy zasobów Microsoft dev Spaces z subskrypcją |

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. wspólny/labCenters/Delete | Usuń centra laboratoryjne. |
> | Akcja | Microsoft. wspólny/labCenters/odczyt | Odczytywanie centrów laboratoryjnych. |
> | Akcja | Microsoft. wspólny/labCenters/Write | Dodawanie lub modyfikowanie centrów laboratoryjnych. |
> | Akcja | Microsoft. wspólny/Labs/artifactSources/armTemplates/Read | Odczytywanie szablonów usługi Azure Resource Manager. |
> | Akcja | Microsoft. wspólny/Labs/artifactSources/artefakty/GenerateArmTemplate/akcja | Generuje szablon ARM dla danego artefaktu, przekazuje wymagane pliki na konto magazynu i sprawdza poprawność wygenerowanego artefaktu. |
> | Akcja | Microsoft. wspólny/Labs/artifactSources/artefakty/odczyt | Odczytaj artefakty. |
> | Akcja | Microsoft. wspólny/Labs/artifactSources/usuwanie | Usuń źródła artefaktów. |
> | Akcja | Microsoft. wspólny/Labs/artifactSources/odczyt | Odczytaj źródła artefaktów. |
> | Akcja | Microsoft. wspólny/Labs/artifactSources/zapis | Dodaj lub zmodyfikuj źródła artefaktów. |
> | Akcja | Microsoft. wspólny/Labs/ClaimAnyVm/akcja | Zarezerwuj losową maszynę wirtualną z dodaną odpowiedzialnością w laboratorium. |
> | Akcja | Microsoft. wspólny/Labs/koszty/odczyt | Zapoznaj się z kosztami. |
> | Akcja | Microsoft. wspólny/Labs/koszty/zapis | Dodawanie lub modyfikowanie kosztów. |
> | Akcja | Microsoft. wspólny/Labs/środowisko/akcja | Twórz maszyny wirtualne w laboratorium. |
> | Akcja | Microsoft. wspólny/Labs/customImages/usuwanie | Usuń obrazy niestandardowe. |
> | Akcja | Microsoft. wspólny/Labs/customImages/odczyt | Odczytaj obrazy niestandardowe. |
> | Akcja | Microsoft. wspólny/Labs/customImages/zapis | Dodawanie lub modyfikowanie obrazów niestandardowych. |
> | Akcja | Microsoft. wspólny/Labs/usuwanie | Usuwanie laboratoriów. |
> | Akcja | Microsoft. wspólny/Labs/EnsureCurrentUserProfile/akcja | Upewnij się, że bieżący użytkownik ma prawidłowy profil w laboratorium. |
> | Akcja | Microsoft. wspólny/Labs/ExportResourceUsage/akcja | Eksportuje użycie zasobów laboratorium do konta magazynu |
> | Akcja | Microsoft. wspólny/Labs/formuły/usuwanie | Usuń formuły. |
> | Akcja | Microsoft. wspólny/Labs/formuły/odczyt | Odczytaj formuły. |
> | Akcja | Microsoft. wspólny/Labs/formuły/zapis | Dodaj lub zmodyfikuj formuły. |
> | Akcja | Microsoft. wspólny/Labs/galleryImages/odczyt | Odczytuj obrazy galerii. |
> | Akcja | Microsoft. wspólny/Labs/GenerateUploadUri/akcja | Generowanie identyfikatora URI na potrzeby przekazywania niestandardowych obrazów dysków do laboratorium. |
> | Akcja | Microsoft. wspólny/Labs/ImportVirtualMachine/akcja | Zaimportuj maszynę wirtualną do innego laboratorium. |
> | Akcja | Microsoft. wspólny/Labs/ListVhds/akcja | Wyświetlanie listy obrazów dysków dostępnych do tworzenia obrazów niestandardowych. |
> | Akcja | Microsoft. wspólny/Labs/notificationChannels/usuwanie | Usuwanie kanałów powiadomień. |
> | Akcja | Microsoft. wspólny/Labs/notificationChannels/powiadamianie/akcja | Wyślij powiadomienie do podanego kanału. |
> | Akcja | Microsoft. wspólny/Labs/notificationChannels/odczyt | Odczytaj kanały powiadomień. |
> | Akcja | Microsoft. wspólny/Labs/notificationChannels/zapis | Dodawanie lub modyfikowanie kanałów powiadomień. |
> | Akcja | Microsoft. wspólny/Labs/policySets/EvaluatePolicies/Action | Szacuje zasady laboratorium. |
> | Akcja | Microsoft. wspólny/Labs/policySets/zasady/usuwanie | Usuń zasady. |
> | Akcja | Microsoft. wspólny/Labs/policySets/zasady/odczyt | Odczytaj zasady. |
> | Akcja | Microsoft. wspólny/Labs/policySets/zasady/zapis | Dodawanie lub modyfikowanie zasad. |
> | Akcja | Microsoft. wspólny/Labs/policySets/odczyt | Odczytaj zestawy zasad. |
> | Akcja | Microsoft. wspólny/Labs/odczyt | Odczytywanie laboratoriów. |
> | Akcja | Microsoft. wspólny/Labs/harmonogramy/usuwanie | Usuwanie harmonogramów. |
> | Akcja | Microsoft. wspólny/Labs/harmonogramy/wykonywanie/akcja | Wykonaj harmonogram. |
> | Akcja | Microsoft. wspólny/Labs/Schedules/ListApplicable/Action | Wyświetla wszystkie odpowiednie harmonogramy |
> | Akcja | Microsoft. wspólny/Labs/harmonogramy/odczyt | Odczytaj harmonogramy. |
> | Akcja | Microsoft. wspólny/Labs/harmonogramy/zapis | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft. wspólny/Labs/servicerunners/Delete | Usuwanie modułu uruchamiającego usługi. |
> | Akcja | Microsoft. wspólny/Labs/servicerunners/Read | Odczytywanie modułu uruchamiającego usługi. |
> | Akcja | Microsoft. wspólny/Labs/servicerunners/Write | Dodawanie lub modyfikowanie modułu uruchamiającego usługi. |
> | Akcja | Microsoft. wspólny/Labs/sharedGalleries/usuwanie | Usuń udostępnione galerie. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/read | Odczytuj udostępnione galerie. |
> | Akcja | Microsoft. wspólny/Labs/sharedGalleries/sharedImages/Delete | Usuń obrazy udostępnione. |
> | Akcja | Microsoft. wspólny/Labs/sharedGalleries/sharedImages/Read | Odczytuj obrazy udostępnione. |
> | Akcja | Microsoft. wspólny/Labs/sharedGalleries/sharedImages/Write | Dodaj lub Modyfikuj obrazy udostępnione. |
> | Akcja | Microsoft. wspólny/Labs/sharedGalleries/zapis | Dodaj lub zmodyfikuj udostępnione galerie. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/usuwanie | Usuń profile użytkowników. |
> | Akcja | Microsoft. wspólny/Labs/users/disks/Attach/Action | Dołącz i Utwórz dzierżawę dysku dla maszyny wirtualnej. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/dyski/usuwanie | Usuń dyski. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/dyski/odłączenie/akcja | Odłącz i Przerwij dzierżawę dysku dołączonego do maszyny wirtualnej. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/dyski/odczyt | Odczytywanie dysków. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/dyski/zapis | Dodawanie lub modyfikowanie dysków. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/środowiska/usuwanie | Usuń środowiska. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/środowiska/odczyt | Odczytuj środowiska. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/środowiska/zapis | Dodaj lub zmodyfikuj środowiska. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/odczyt | Odczytaj profile użytkowników. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/wpisy tajne/usuwanie | Usuń wpisy tajne. |
> | Akcja | Microsoft. wspólny/Labs/users/wpisy tajne/odczyt | Odczytaj wpisy tajne. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/wpisy tajne/zapis | Dodaj lub zmodyfikuj wpisy tajne. |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/Delete | Usuwanie sieci szkieletowej usług. |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/ListApplicableSchedules/Action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/Read | Odczytaj sieci szkieletowe usług. |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Execute/Action | Wykonaj harmonogram. |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Read | Odczytaj harmonogramy. |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/servicefabrics/Start/akcja | Uruchom sieć szkieletową usług. |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/Stop/Action | Zatrzymaj sieć szkieletową usług |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/Write | Dodawanie lub modyfikowanie sieci szkieletowej usług. |
> | Akcja | Microsoft. wspólny/Labs/użytkownicy/zapis | Dodaj lub zmodyfikuj profile użytkowników. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/AddDataDisk/Action | Dołącz nowy lub istniejący dysk danych do maszyny wirtualnej. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/ApplyArtifacts/Action | Zastosuj artefakty do maszyny wirtualnej. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/roszczeń/akcja | Przejmowanie na własność istniejącej maszyny wirtualnej |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/usuwanie | Usuwanie maszyn wirtualnych. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/DetachDataDisk/Action | Odłącz określony dysk od maszyny wirtualnej. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/GetRdpFileContents/Action | Pobiera ciąg, który reprezentuje zawartość pliku RDP dla maszyny wirtualnej |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/ListApplicableSchedules/Action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/odczyt | Odczytaj maszyny wirtualne. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/ponowna instalacja/akcja | Ponowne wdrażanie maszyny wirtualnej |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/Zmień rozmiar/akcję | Zmień rozmiar maszyny wirtualnej. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/restart/akcja | Uruchom ponownie maszynę wirtualną. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/Schedules/Delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/Schedules/Execute/Action | Wykonaj harmonogram. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/Schedules/Read | Odczytaj harmonogramy. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/Schedules/Write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/Start/akcja | Uruchom maszynę wirtualną. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/Stop/akcja | Zatrzymaj maszynę wirtualną |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/TransferDisks/Action | Przenosi wszystkie dyski z danymi dołączone do maszyny wirtualnej, które mają należeć do bieżącego użytkownika. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/unclaim/Action | Zwolnij własność istniejącej maszyny wirtualnej |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/zapis | Dodaj lub zmodyfikuj maszyny wirtualne. |
> | Akcja | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Delete | Usuń bastionhosts. |
> | Akcja | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Read | Odczytaj bastionhosts. |
> | Akcja | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Write | Dodaj lub zmodyfikuj bastionhosts. |
> | Akcja | Microsoft. wspólny/Labs/virtualNetworks/usuwanie | Usuń sieci wirtualne. |
> | Akcja | Microsoft. wspólny/Labs/virtualNetworks/odczyt | Odczytaj sieci wirtualne. |
> | Akcja | Microsoft. wspólny/Labs/virtualNetworks/zapis | Dodaj lub zmodyfikuj sieci wirtualne. |
> | Akcja | Microsoft. wspólny/Labs/vmPools/usuwanie | Usuń pule maszyn wirtualnych. |
> | Akcja | Microsoft. wspólny/Labs/vmPools/odczyt | Odczytaj pule maszyn wirtualnych. |
> | Akcja | Microsoft. wspólny/Labs/vmPools/zapis | Dodaj lub zmodyfikuj pule maszyn wirtualnych. |
> | Akcja | Microsoft. wspólny/Labs/zapis | Dodawanie lub modyfikowanie laboratoriów. |
> | Akcja | Microsoft. wspólny/Locations/Operations/Read | Operacje odczytu. |
> | Akcja | Microsoft. wspólny/Register/Action | Rejestruje subskrypcję |
> | Akcja | Microsoft. wspólny/harmonogramy/usuwanie | Usuwanie harmonogramów. |
> | Akcja | Microsoft. wspólny/harmonogramy/wykonywanie/akcja | Wykonaj harmonogram. |
> | Akcja | Microsoft. wspólny/Schedules/Read | Odczytaj harmonogramy. |
> | Akcja | Microsoft. wspólny/harmonogramy/Przekieruj/akcję | Aktualizuje identyfikator zasobu docelowego harmonogramu. |
> | Akcja | Microsoft. wspólny/Schedules/Write | Dodawanie lub modyfikowanie harmonogramów. |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DocumentDB/databaseAccountNames/odczyt | Sprawdza dostępność nazw. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Delete | Usuwanie kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Read | Odczytaj kolekcję lub Wyświetl listę wszystkich kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Settings/Read | Odczytaj przepływność kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Settings/Write | Aktualizowanie przepływności kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Write | Utwórz lub zaktualizuj kolekcję. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Delete | Usuń kontener. Dotyczy tylko typów interfejsu API: "SQL". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Read | Odczytaj kontener lub Wyświetl listę wszystkich kontenerów. Dotyczy tylko typów interfejsu API: "SQL". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Settings/Read | Odczytaj przepływność kontenera. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Settings/Write | Zaktualizuj przepływność kontenera. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Write | Utwórz lub zaktualizuj kontener. Dotyczy tylko typów interfejsu API: "SQL". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Delete | Usuń bazę danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/bazy danych/wykresy/usuwanie | Usuwanie grafu. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/graphs/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/bazy danych/wykresy/odczyt | Odczytaj wykres lub Wyświetl wszystkie wykresy. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/graphs/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/graphs/Settings/Read | Odczytaj przepływność grafu. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/wykresy/ustawienia/zapis | Aktualizowanie przepływności grafu. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/bazy danych/wykresy/zapis | Utwórz lub zaktualizuj Graf. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Read | Odczytaj bazę danych lub listę wszystkich baz danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Settings/Read | Odczytaj przepływność bazy danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Settings/Write | Aktualizowanie przepływności bazy danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Write | Tworzenie bazy danych Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/usuwanie | Usuń przestrzeń kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Spaces/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Spaces/Read | Odczytaj miejsce na dysku lub Wyświetl wszystkie spacje. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/dataspaces/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/miejsca/ustawienia/odczyt | Odczytaj przepustowość przestrzeni kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/ustawienia/zapis | Aktualizowanie przepływności przestrzeni kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/tabele/usuwanie | Usuń tabelę. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Spaces/tabless/Tables/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/tabele/odczyt | Odczytaj tabelę lub listę wszystkich tabel. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Spaces/Tables/Tables/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Spaces/Tables/Tables/Settings/Read | Odczytaj tabelę przepływność. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Spaces/Tables/Tables/Settings/Write | Aktualizowanie przepływności tabeli. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/tabele/zapis | Utwórz lub zaktualizuj tabelę. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/miejsca do zapisu/zapis | Utwórz miejsce na dysku. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/usuwanie | Usuń tabelę. Dotyczy tylko typów interfejsu API: "Table". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Tables/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Table". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/odczyt | Odczytaj tabelę lub listę wszystkich tabel. Dotyczy tylko typów interfejsu API: "Table". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/ustawienia/operationResults/odczyt | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/ustawienia/odczyt | Odczytaj tabelę przepływność. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/ustawienia/zapis | Aktualizowanie przepływności tabeli. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/zapis | Utwórz lub zaktualizuj tabelę. Dotyczy tylko typów interfejsu API: "Table". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/kopia zapasowa/akcja | Prześlij żądanie skonfigurowania kopii zapasowej |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/changeResourceGroup/akcja | Zmień grupę zasobów konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Databases/Collections/metricDefinitions/Read | Odczytuje definicje metryk kolekcji. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Databases/Collections/Metrics/Read | Odczytuje metryki kolekcji. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Databases/Collections/partitionKeyRangeId/Metrics/Read | Odczytaj metryki na poziomie klucza partycji konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/bazy danych/kolekcje/partycje/metryki/odczyt | Odczytaj metryki na poziomie partycji konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Databases/Collections/Partitions/Read | Odczytywanie partycji konta bazy danych w kolekcji |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/bazy danych/kolekcje/partycje/użycia/odczyt | Odczytaj użycie poziomów partycji konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/bazy danych/kolekcje/użycia/odczyt | Odczytuje użycie kolekcji. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Databases/metricDefinitions/Read | Odczytuje definicje metryk bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Databases/Metrics/Read | Odczytuje metryki bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/bazy danych/użycia/odczyt | Odczytuje użycie bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Delete | Usuwa konta bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/failoverPriorityChange/akcja | Zmień priorytety trybu failover regionów konta bazy danych. Służy do wykonywania ręcznej operacji przełączania do trybu failover |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/akcja | Pobieranie zasad tworzenia kopii zapasowej konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/akcja | Pobieranie parametrów połączenia dla konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/listKeys/akcja | Lista kluczy konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/metricDefinitions/Read | Odczytuje definicje metryk konta bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Metrics/odczyt | Odczytuje metryki konta bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/offlineRegion/akcja | Offline w regionie konta bazy danych.  |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/onlineRegion/akcja | Online region konta bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/operationResults/Read | Stan odczytu operacji asynchronicznej |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/percentyl/metryki/odczyt | Metryki opóźnienia odczytu |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/percentyl/odczyt | Odczytaj percentyle opóźnień replikacji |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/percentyl/sourceRegion/targetRegion/Metrics/Read | Odczytaj metryki opóźnienia dla określonego źródła i regionu docelowego |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/percentyl/targetRegion/Metrics/Read | Odczytaj metryki opóźnienia dla określonego regionu docelowego |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Delete | Usuń serwer proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Read | Odczytaj prywatny serwer proxy połączenia dla konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Validate/akcja | Weryfikowanie serwera proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Write | Utwórz lub zaktualizuj serwer proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/odczyt | Odczytuje konto bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/readonlykeys/akcja | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Read | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/regenerateKey/akcja | Obróć klucze konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/region/bazy danych/kolekcje/metryki/odczyt | Odczytuje metryki kolekcji regionalnej. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/region/Databases/Collections/partitionKeyRangeId/Metrics/Read | Odczytaj metryki na poziomie klucza partycji konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/region/bazy danych/kolekcje/partycje/metryki/odczyt | Odczytaj metryki na poziomie partycji konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/region/bazy danych/kolekcje/partycje/odczyt | Odczytywanie partycji konta bazy danych regionalnych w kolekcji |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/region/metryki/odczyt | Odczytuje metryki konta regionu i bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Prześlij żądanie przywrócenia |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Usages/Read | Odczytuje użycie konta bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Write | Zaktualizuj konta bazy danych. |
> | Akcja | Microsoft. DocumentDB/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadamia Microsoft. DocumentDB, że VirtualNetwork lub podsieć jest usuwana |
> | Akcja | Microsoft. DocumentDB/Locations/deleteVirtualNetworkOrSubnets/operationResults/Read | Odczytaj stan operacji asynchronicznej deleteVirtualNetworkOrSubnets |
> | Akcja | Microsoft. DocumentDB/Locations/operationsStatus/Read | Odczytuje stan operacji asynchronicznych |
> | Akcja | Microsoft. DocumentDB/operationResults/odczyt | Stan odczytu operacji asynchronicznej |
> | Akcja | Microsoft. DocumentDB/Operations/Read | Operacje odczytu dostępne dla programu Microsoft DocumentDBe  |
> | Akcja | Microsoft. DocumentDB/Register/Action |  Zarejestruj dostawcę zasobów Microsoft DocumentDB dla subskrypcji |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DomainRegistration/checkDomainAvailability/Action | Sprawdź, czy domena jest dostępna do zakupu |
> | Akcja | Microsoft. DomainRegistration/domeny/usuwanie | Usuń istniejącą domenę. |
> | Akcja | Microsoft. DomainRegistration/domens/domainownershipidentifiers/Delete | Usuwanie identyfikatora własności |
> | Akcja | Microsoft. DomainRegistration/domens/domainownershipidentifiers/Read | Identyfikatory własności listy |
> | Akcja | Microsoft. DomainRegistration/domens/domainownershipidentifiers/Read | Uzyskaj identyfikator własności |
> | Akcja | Microsoft. DomainRegistration/domens/domainownershipidentifiers/Write | Utwórz lub zaktualizuj identyfikator |
> | Akcja | Microsoft. DomainRegistration/domens/operationresults/Read | Pobierz operację domeny |
> | Akcja | Microsoft. DomainRegistration/domeny/odczyt | Pobierz listę domen |
> | Akcja | Microsoft. DomainRegistration/domeny/odczyt | Pobierz domenę |
> | Akcja | Microsoft. DomainRegistration/domeny/Odnów/akcja | Odnów istniejącą domenę. |
> | Akcja | Microsoft. DomainRegistration/domeny/zapis | Dodaj nową domenę lub zaktualizuj istniejącą |
> | Akcja | Microsoft. DomainRegistration/generateSsoRequest/Action | Wygeneruj żądanie zalogowania się do centrum sterowania domeną. |
> | Akcja | Microsoft. DomainRegistration/listDomainRecommendations/Action | Pobierz zalecenia dotyczące domeny listy na podstawie słów kluczowych |
> | Akcja | Microsoft. DomainRegistration/Operations/Read | Wyświetl listę wszystkich operacji z rejestracji domeny usługi App Service |
> | Akcja | Microsoft. DomainRegistration/Register/Action | Zarejestruj dostawcę zasobów domen firmy Microsoft dla subskrypcji |
> | Akcja | Microsoft. DomainRegistration/topLevelDomains/listAgreements/akcja | Lista akcja umowy |
> | Akcja | Microsoft. DomainRegistration/topLevelDomains/odczyt | Pobierz domeny Toplevel |
> | Akcja | Microsoft. DomainRegistration/topLevelDomains/odczyt | Pobierz domenę Toplevel |
> | Akcja | Microsoft. DomainRegistration/validateDomainRegistrationInformation/Action | Sprawdzanie poprawności obiektu zakupu domeny bez jego przesyłania |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. EventGrid/domeny/usuwanie | Usuwanie domeny |
> | Akcja | Microsoft. EventGrid/domeny/listKeys/akcja | Wyświetl klucze dla domeny |
> | Akcja | Microsoft. EventGrid/domeny/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla domen |
> | Akcja | Microsoft. EventGrid/domeny/odczyt | Odczytaj domenę |
> | Akcja | Microsoft. EventGrid/domeny/regenerateKey/akcja | Wygeneruj ponownie klucz dla domeny |
> | Akcja | Microsoft. EventGrid/domen/tematów/tematy/Usuń | Usuwanie tematu domeny |
> | Akcja | Microsoft. EventGrid/domeny/tematy/Odczytaj | Odczytaj temat domeny |
> | Akcja | Microsoft. EventGrid/domen/tematy/Zapisz | Utwórz lub zaktualizuj temat domeny |
> | Akcja | Microsoft. EventGrid/domeny/zapis | Utwórz lub zaktualizuj domenę |
> | Akcja | Microsoft. EventGrid/eventSubscriptions/Delete | Usuń eventSubscription |
> | Akcja | Microsoft. EventGrid/eventSubscriptions/getFullUrl/akcja | Pobierz pełny adres URL dla subskrypcji zdarzeń |
> | Akcja | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Akcja | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Akcja | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla eventSubscriptions |
> | Akcja | Microsoft. EventGrid/eventSubscriptions/odczyt | Odczytaj eventSubscription |
> | Akcja | Microsoft. EventGrid/eventSubscriptions/Write | Utwórz lub zaktualizuj eventSubscription |
> | Akcja | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla tematów |
> | Akcja | Microsoft. EventGrid/extensionTopics/odczyt | Odczytaj extensionTopic. |
> | Akcja | Microsoft. EventGrid/Locations/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | Akcja | Microsoft. EventGrid/Locations/operationResults/Read | Przeczytaj wynik operacji regionalnej |
> | Akcja | Microsoft. EventGrid/Locations/operationsStatus/Read | Odczytywanie stanu operacji regionalnej |
> | Akcja | Microsoft. EventGrid/Locations/topictypes/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | Akcja | Microsoft. EventGrid/operationResults/odczyt | Odczytaj wynik operacji |
> | Akcja | Microsoft. EventGrid/Operations/Read | Utwórz listę operacji EventGrid. |
> | Akcja | Microsoft. EventGrid/operationsStatus/odczyt | Odczytywanie stanu operacji |
> | Akcja | Microsoft. EventGrid/Register/Action | Rejestruje subskrypcję dostawcy zasobów EventGrid. |
> | Akcja | Microsoft. EventGrid/tematy/Usuń | Usuwanie tematu |
> | Akcja | Microsoft. EventGrid/tematy/listKeys/akcja | Lista kluczy tematu |
> | Akcja | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla tematów |
> | Akcja | Microsoft. EventGrid/tematy/odczyt | Zapoznaj się z tematem |
> | Akcja | Microsoft. EventGrid/tematy/regenerateKey/akcja | Wygeneruj ponownie klucz dla tematu |
> | Akcja | Microsoft. EventGrid/tematy/zapis | Utwórz lub zaktualizuj temat |
> | Akcja | Microsoft. EventGrid/topictypes/eventSubscriptions/Read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | Akcja | Microsoft. EventGrid/topictypes/eventtypes/Read | Odczytaj zdarzenia typu obsługiwane przez element temattype |
> | Akcja | Microsoft. EventGrid/topictypes/odczyt | Odczytaj element tematu |
> | Akcja | Microsoft. EventGrid/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy zasobów EventGrid. |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. EventHub/availableClusterRegions/odczyt | Operacja odczytu, aby wyświetlić listę dostępnych wstępnie inicjowanych klastrów według regionów platformy Azure. |
> | Akcja | Microsoft. EventHub/checkNameAvailability/akcja | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft. EventHub/checkNamespaceAvailability/akcja | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Akcja | Microsoft. EventHub/klastry/usuwanie | Usuwa istniejący zasób klastra. |
> | Akcja | Microsoft. EventHub/klastry/przestrzenie nazw/odczyt | Wyświetl listę identyfikatorów ARM obszaru nazw dla przestrzeni nazw w klastrze. |
> | Akcja | Microsoft. EventHub/klastry/operationresults/odczyt | Pobierz stan asynchronicznej operacji klastra. |
> | Akcja | Microsoft. EventHub/klastry/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk klastra |
> | Akcja | Microsoft. EventHub/klastry/odczyt | Pobiera opis zasobu klastra |
> | Akcja | Microsoft. EventHub/klastry/zapis | Tworzy lub modyfikuje istniejący zasób klastra. |
> | Akcja | Microsoft. EventHub/lokalizacje/deleteVirtualNetworkOrSubnets/akcja | Usuwa reguły sieci wirtualnej w dostawcy zasobów EventHub dla określonej sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/reguł autoryzacji/ListKeys/Action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/reguł autoryzacji/odczyt | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/breakPairing/Action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/disasterrecoveryconfigs/checkNameAvailability/Action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/Delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/tryb failover/akcja | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/Read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/Write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Action | Operacja aktualizowania centrum EventHub. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji EventHub |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do centrum EventHub |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/odczyt |  Pobierz listę reguł autoryzacji EventHub |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Write | Utwórz reguły autoryzacji EventHub i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventHubs/consumergroups/Delete | Operacja usuwania zasobu w ramach odbiorcy |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventHubs/consumergroups/odczyt | Pobierz listę opisów zasobów dla odbiorców |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventHubs/consumergroups/Write | Utwórz lub zaktualizuj właściwości odbiorcy. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/Delete | Operacja usuwania zasobu EventHub |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/Read | Pobierz listę opisów zasobów EventHub |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/Write | Utwórz lub zaktualizuj właściwości EventHub. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Delete | Usuń zasób filtru IP |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Read | Pobierz zasób filtru IP |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Write | Utwórz zasób filtru IP |
> | Akcja dataaction | Microsoft. EventHub/przestrzenie nazw/komunikaty/odbieranie/akcja | Odbieranie komunikatów |
> | Akcja dataaction | Microsoft. EventHub/przestrzenie nazw/komunikaty/wysyłanie/akcja | Wysyłanie komunikatów |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/networkruleset/Delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/networkruleset/Read | Pobiera zasób NetworkRuleSet |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/networkruleset/Write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/networkrulesets/Delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/networkrulesets/Read | Pobiera zasób NetworkRuleSet |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/networkrulesets/Write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/operationresults/Read | Pobierz stan operacji przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobierz listę opisów zasobów dzienników przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/removeAcsNamepsace/Action | Usuń przestrzeń nazw ACS |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/virtualNetworkRules/Delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/virtualNetworkRules/Read | Pobiera zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/virtualNetworkRules/Write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Akcja | Microsoft. EventHub/Operations/Read | Pobierz operacje |
> | Akcja | Microsoft. EventHub/rejestr/akcja | Rejestruje subskrypcję dostawcy zasobów centrum zdarzeń i umożliwia tworzenie zasobów EventHub |
> | Akcja | Microsoft. EventHub/SKU/odczyt | Pobierz listę opisów zasobów jednostki SKU |
> | Akcja | Microsoft. EventHub/SKU/regiony/odczyt | Pobierz listę opisów zasobów SkuRegions |
> | Akcja | Microsoft. EventHub/Wyrejestruj/akcja | Rejestruje dostawcę zasobów centrum zdarzeń |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Features/Features/Read | Pobiera funkcje subskrypcji. |
> | Akcja | Microsoft. Features/Operations/Read | Pobiera listę operacji. |
> | Akcja | Microsoft. Features/Providers/Features/Read | Pobiera funkcję subskrypcji dla danego dostawcy zasobów. |
> | Akcja | Microsoft. Features/Providers/Features/Register/Action | Rejestruje funkcję subskrypcji dla danego dostawcy zasobów. |
> | Akcja | Microsoft. Features/Providers/Features/Unregister/Action | Wyrejestrowuje funkcję subskrypcji w ramach danego dostawcy zasobów. |
> | Akcja | Microsoft. Features/Register/Action | Rejestruje funkcję subskrypcji. |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. GuestConfiguration/guestConfigurationAssignments/Delete | Usuń przypisanie konfiguracji gościa. |
> | Akcja | Microsoft. GuestConfiguration/guestConfigurationAssignments/odczyt | Pobierz przypisanie konfiguracji gościa. |
> | Akcja | Microsoft. GuestConfiguration/guestConfigurationAssignments/Reports/Read | Pobierz raport dotyczący przypisywania konfiguracji gościa. |
> | Akcja | Microsoft. GuestConfiguration/guestConfigurationAssignments/Write | Utwórz nowe przypisanie konfiguracji gościa. |
> | Akcja | Microsoft. GuestConfiguration/Operations/Read | Pobiera operacje dla dostawcy zasobów Microsoft. GuestConfiguration |
> | Akcja | Microsoft. GuestConfiguration/Register/Action | Rejestruje subskrypcję dostawcy zasobów Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. HDInsight/Klastry/aplikacje/usuwanie | Usuwanie aplikacji dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/aplikacje/odczyt | Pobierz aplikację dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/aplikacje/zapis | Utwórz lub zaktualizuj aplikację dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/changerdpsetting/akcja | Zmiana ustawienia protokołu RDP dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/konfiguracje/akcja | Aktualizowanie konfiguracji klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/konfiguracje/akcja | Pobierz konfiguracje klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/konfiguracje/odczyt | Pobierz konfiguracje klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/usuwanie | Usuwanie klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/rozszerzenia/usuwanie | Usuwanie rozszerzenia klastra dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/rozszerzenia/odczyt | Pobierz rozszerzenie klastra dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/rozszerzenia/zapis | Utwórz rozszerzenie klastra dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/getGatewaySettings/akcja | Pobierz ustawienia bramy dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla klastra usługi HDInsight zasobów |
> | Akcja | Microsoft. HDInsight/Klastry/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla klastra usługi HDInsight zasobów |
> | Akcja | Microsoft. HDInsight/Klastry/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/odczyt | Pobierz szczegóły dotyczące klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/role/zmiana/akcja | Zmiana rozmiaru klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/updateGatewaySettings/akcja | Aktualizowanie ustawień bramy dla klastra usługi HDInsight |
> | Akcja | Microsoft. HDInsight/Klastry/zapis | Utwórz lub zaktualizuj klaster usługi HDInsight |
> | Akcja | Microsoft. HDInsight/lokalizacje/możliwości/odczyt | Uzyskaj możliwości subskrypcji |
> | Akcja | Microsoft. HDInsight/lokalizacje/checkNameAvailability/odczyt | Sprawdź dostępność nazwy |
> | Akcja | Microsoft. HDInsight/rejestr/akcja | Zarejestruj dostawcę zasobów usługi HDInsight dla subskrypcji |
> | Akcja | Microsoft. HDInsight/Wyrejestruj/akcja | Wyrejestruj dostawcę zasobów usługi HDInsight dla subskrypcji |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ImportExport/Jobs/Delete | Usuwa istniejące zadanie. |
> | Akcja | Microsoft. ImportExport/Jobs/listBitLockerKeys/akcja | Pobiera klucze funkcji BitLocker dla określonego zadania. |
> | Akcja | Microsoft. ImportExport/Jobs/Read | Pobiera właściwości określonego zadania lub zwraca listę zadań. |
> | Akcja | Microsoft. ImportExport/Jobs/Write | Tworzy zadanie z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego zadania. |
> | Akcja | Microsoft. ImportExport/lokalizacje/odczyt | Pobiera właściwości dla określonej lokalizacji lub zwraca listę lokalizacji. |
> | Akcja | Microsoft. ImportExport/Operations/Read | Pobiera operacje obsługiwane przez dostawcę zasobów. |
> | Akcja | Microsoft. ImportExport/Register/Action | Rejestruje subskrypcję dostawcy zasobów importu/eksportu i umożliwia tworzenie zadań importu/eksportu. |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Insights/ActionGroups/Delete | Usuwanie grupy akcji |
> | Akcja | Microsoft. Insights/ActionGroups/Read | Odczytaj grupę akcji |
> | Akcja | Microsoft. Insights/ActionGroups/Write | Utwórz lub Zaktualizuj grupę akcji |
> | Akcja | Microsoft. Insights/ActivityLogAlerts/aktywowany/akcja | Uaktywniono alert dziennika aktywności |
> | Akcja | Microsoft. Insights/ActivityLogAlerts/Delete | Usuwanie alertu dziennika aktywności |
> | Akcja | Microsoft. Insights/ActivityLogAlerts/Read | Odczytaj alert dotyczący dziennika aktywności |
> | Akcja | Microsoft. Insights/ActivityLogAlerts/Write | Utwórz lub zaktualizuj alert dotyczący dziennika aktywności |
> | Akcja | Microsoft. Insights/AlertRules/aktywowany/akcja | Uaktywniono alert dotyczący metryki klasycznej |
> | Akcja | Microsoft. Insights/AlertRules/Delete | Usuwanie alertu dotyczącego klasycznej metryki |
> | Akcja | Microsoft. Insights/AlertRules/incydenty/odczyt | Odczytaj zdarzenie klasycznego alertu dotyczącego metryki |
> | Akcja | Microsoft. Insights/AlertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | Akcja | Microsoft. Insights/AlertRules/rozwiązany/akcja | Rozwiązano alert dotyczący metryki klasycznej |
> | Akcja | Microsoft. Insights/AlertRules/z ograniczeniami/akcją | Ograniczona reguła alertu dotyczącego metryki klasycznej |
> | Akcja | Microsoft. Insights/AlertRules/Write | Utwórz lub zaktualizuj klasyczny alert dotyczący metryki |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Delete | Usuń ustawienie automatycznego skalowania |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/logDefinitions/Read | Odczytaj definicje dzienników |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Read | Odczytaj ustawienie automatycznego skalowania |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Scaledown/Action | Inicjowano skalowanie w poziomie automatycznego skalowania |
> | Akcja | Microsoft. Insights/AutoscaleSettings/ScaledownResult/Action | Ukończono Skalowanie automatyczne w dół |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Scaleup/Action | Zainicjowanie skalowania automatycznego skalowania w górę |
> | Akcja | Microsoft. Insights/AutoscaleSettings/ScaleupResult/Action | Ukończenie skalowania automatycznego skalowania w górę |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Write | Utwórz lub zaktualizuj ustawienie automatycznego skalowania |
> | Akcja | Microsoft. Insights/Baseline/Read | Odczytaj linię bazową metryki (wersja zapoznawcza) |
> | Akcja | Microsoft. Insights/CalculateBaseline/Read | Oblicz linię bazową dla wartości metryk (wersja zapoznawcza) |
> | Akcja | Microsoft. Insights/Components/AnalyticsItems/Delete | Usuwanie elementu analizy Application Insights |
> | Akcja | Microsoft. Insights/Components/AnalyticsItems/Read | Odczytywanie elementu analizy Application Insights |
> | Akcja | Microsoft. Insights/Components/AnalyticsItems/Write | Pisanie elementu analizy Application Insights |
> | Akcja | Microsoft. Insights/Components/AnalyticsTables/Action | Akcja tabeli Application Insights Analytics |
> | Akcja | Microsoft. Insights/Components/AnalyticsTables/Delete | Usuwanie schematu tabeli analizy Application Insights |
> | Akcja | Microsoft. Insights/Components/AnalyticsTables/Read | Odczytywanie schematu tabeli analizy Application Insights |
> | Akcja | Microsoft. Insights/Components/AnalyticsTables/Write | Pisanie schematu tabeli analizy Application Insights |
> | Akcja | Microsoft. Insights/Components/Annotations/Delete | Usuwanie adnotacji Application Insights |
> | Akcja | Microsoft. Insights/Components/Annotations/Read | Odczytywanie adnotacji Application Insights |
> | Akcja | Microsoft. Insights/Components/Annotations/Write | Pisanie adnotacji Application Insights |
> | Akcja | Microsoft. Insights/Components/API/Read | Odczytywanie interfejsu API danych Application Insights składników |
> | Akcja | Microsoft. Insights/Components/ApiKeys/Action | Generowanie klucza interfejsu API Application Insights |
> | Akcja | Microsoft. Insights/Components/ApiKeys/Delete | Usuwanie klucza interfejsu API Application Insights |
> | Akcja | Microsoft. Insights/Components/ApiKeys/Read | Odczytywanie klucza interfejsu API Application Insights |
> | Akcja | Microsoft. Insights/Components/BillingPlanForComponent/Read | Odczytywanie planu rozliczeniowego dla składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/CurrentBillingFeatures/Read | Odczytywanie bieżących funkcji rozliczeń dla składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/CurrentBillingFeatures/Write | Pisanie bieżących funkcji rozliczeń dla składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/DailyCapReached/Action | Osiągnięto dzienny limit dla składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/DailyCapWarningThresholdReached/Action | Osiągnięto próg ostrzeżenia dziennego limitu dla składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/DefaultWorkItemConfig/Read | Odczytywanie Application Insights domyślnej konfiguracji integracji ALM |
> | Akcja | Microsoft. Insights/Components/Delete | Usuwanie konfiguracji składnika usługi Application Insights |
> | Akcja | Microsoft. Insights/Components/Events/Read | Pobierz dzienniki z Application Insights przy użyciu formatu zapytania OData |
> | Akcja | Microsoft. Insights/Components/ExportConfiguration/Action | Akcja eksportowania ustawień Application Insights |
> | Akcja | Microsoft. Insights/Components/ExportConfiguration/Delete | Usuwanie ustawień eksportu Application Insights |
> | Akcja | Microsoft. Insights/Components/ExportConfiguration/Read | Odczytywanie ustawień eksportu Application Insights |
> | Akcja | Microsoft. Insights/Components/ExportConfiguration/Write | Zapisywanie Application Insights ustawienia eksportu |
> | Akcja | Microsoft. Insights/Components/ExtendQueries/Read | Odczytywanie rozszerzonych wyników zapytania składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/Ulubione/Usuń | Usuwanie Application Insights ulubionych |
> | Akcja | Microsoft. Insights/Components/Ulubione/Odczytaj | Odczytywanie Application Insights ulubionych |
> | Akcja | Microsoft. Insights/Components/Ulubione/zapisuj | Pisanie Application Insights ulubionych |
> | Akcja | Microsoft. Insights/Components/FeatureCapabilities/Read | Odczytywanie możliwości funkcji składników Application Insights |
> | Akcja | Microsoft. Insights/Components/GetAvailableBillingFeatures/Read | Odczytywanie dostępnych funkcji rozliczeń Application Insights składników |
> | Akcja | Microsoft. Insights/Components/GetToken/Read | Odczytywanie tokenu składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/MetricDefinitions/Read | Odczytywanie definicji metryk składników Application Insights |
> | Akcja | Microsoft. Insights/Components/Metrics/Read | Odczytywanie metryk Application Insights składników |
> | Akcja | Microsoft. Insights/Components/Move/Action | Przenoszenie składnika Application Insights do innej grupy zasobów lub subskrypcji |
> | Akcja | Microsoft. Insights/Components/MyAnalyticsItems/Delete | Usuwanie Application Insights osobistego elementu analizy |
> | Akcja | Microsoft. Insights/Components/MyAnalyticsItems/Read | Odczytywanie Application Insights osobistego elementu analizy |
> | Akcja | Microsoft. Insights/Components/MyAnalyticsItems/Write | Pisanie Application Insights osobistego elementu analizy |
> | Akcja | Microsoft. Insights/Components/webulubionych/odczyt | Odczytywanie Application Insights osobistego elementu ulubionego |
> | Akcja | Microsoft. Insights/Components/Operations/Read | Pobieranie stanu długotrwałych operacji w Application Insights |
> | Akcja | Microsoft. Insights/Components/PricingPlans/Read | Odczytywanie planu cenowego składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/PricingPlans/Write | Pisanie planu cenowego składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/ProactiveDetectionConfigs/Read | Odczytywanie konfiguracji wykrywania proaktywnego Application Insights |
> | Akcja | Microsoft. Insights/Components/ProactiveDetectionConfigs/Write | Pisanie Application Insights konfiguracji wykrywania proaktywnego |
> | Akcja | Microsoft. Insights/Components/Providers/Microsoft. Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft. Insights/Components/Przeczyść/akcja | Przeczyszczanie danych z Application Insights |
> | Akcja | Microsoft. Insights/Components/Query/Read | Uruchamianie zapytań względem dzienników Application Insights |
> | Akcja | Microsoft. Insights/Components/QuotaStatus/Read | Odczytywanie stanu przydziału składników Application Insights |
> | Akcja | Microsoft. Insights/Components/Read | Odczytywanie konfiguracji składnika usługi Application Insights |
> | Akcja | Microsoft. Insights/Components/SyntheticMonitorLocations/Read | Odczytywanie Application Insights lokalizacji WebTest |
> | Akcja | Microsoft. Insights/Components/webtests/Read | Odczytywanie konfiguracji WebTest |
> | Akcja | Microsoft. Insights/Components/WorkItemConfigs/Delete | Usuwanie Application Insights ALM Integration Configuration |
> | Akcja | Microsoft. Insights/Components/WorkItemConfigs/Read | Odczytywanie konfiguracji integracji Application Insights ALM |
> | Akcja | Microsoft. Insights/Components/WorkItemConfigs/Write | Pisanie Application Insights ALM Integration Configuration |
> | Akcja | Microsoft. Insights/Components/Write | Zapisywanie w konfiguracji składnika usługi Application Insights |
> | Akcja | Microsoft. Insights/DiagnosticSettings/Delete | Usuń ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft. Insights/DiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft. Insights/DiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft. Insights/EventCategories/Read | Odczytaj dostępne kategorie zdarzeń dziennika aktywności |
> | Akcja | Microsoft. Insights/eventtypes/digestevents/Read | Odczytaj podsumowanie typu zdarzenia zarządzania |
> | Akcja | Microsoft. Insights/eventtypes/Values/odczyty | Odczytaj zdarzenia dziennika aktywności |
> | Akcja | Microsoft. Insights/ExtendedDiagnosticSettings/Delete | Usuń ustawienie diagnostyczne dziennika przepływu sieci |
> | Akcja | Microsoft. Insights/ExtendedDiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne dziennika przepływu sieci |
> | Akcja | Microsoft. Insights/ExtendedDiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne dziennika przepływu sieci |
> | Akcja | Microsoft. Insights/ListMigrationDate/Action | Pobierz datę migracji subskrypcji |
> | Akcja | Microsoft. Insights/ListMigrationDate/Read | Pobierz datę migracji subskrypcji |
> | Akcja | Microsoft. Insights/LogDefinitions/Read | Odczytaj definicje dzienników |
> | Akcja | Microsoft. Insights/LogProfiles/Delete | Usuwanie profilu dziennika aktywności |
> | Akcja | Microsoft. Insights/LogProfiles/Read | Odczytaj profil dziennika aktywności |
> | Akcja | Microsoft. Insights/LogProfiles/Write | Utwórz lub zaktualizuj profil dziennika aktywności |
> | Akcja | Microsoft. Insights/Logs/ADAssessmentRecommendation/Read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/ADReplicationResult/Read | Odczytaj dane z tabeli ADReplicationResult |
> | Akcja | Microsoft. Insights/Logs/ADSecurityAssessmentRecommendation/Read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/alerty/odczyt | Odczytaj dane z tabeli alertów |
> | Akcja | Microsoft. Insights/Logs/AlertHistory/Read | Odczytaj dane z tabeli AlertHistory |
> | Akcja | Microsoft. Insights/Logs/ApplicationInsights/Read | Odczytaj dane z tabeli ApplicationInsights |
> | Akcja | Microsoft. Insights/Logs/Azure/odczyt | Odczytywanie danych z tabeli platformy Azure |
> | Akcja | Microsoft. Insights/Logs/AzureMetrics/Read | Odczytaj dane z tabeli AzureMetrics |
> | Akcja | Microsoft. Insights/Logs/BoundPort/Read | Odczytaj dane z tabeli BoundPort |
> | Akcja | Microsoft. Insights/Logs/CommonSecurityLog/Read | Odczytaj dane z tabeli CommonSecurityLog |
> | Akcja | Microsoft. Insights/dzienniki/komputery/odczyt | Odczytywanie danych z tabeli komputerów |
> | Akcja | Microsoft. Insights/Logs/Zmianakonfiguracji/Read | Odczytaj dane z tabeli Zmianakonfiguracji |
> | Akcja | Microsoft. Insights/Logs/ConfigurationData/Read | Odczytaj dane z tabeli ConfigurationData |
> | Akcja | Microsoft. Insights/Logs/ContainerImageInventory/Read | Odczytaj dane z tabeli ContainerImageInventory |
> | Akcja | Microsoft. Insights/Logs/ContainerInventory/Read | Odczytaj dane z tabeli ContainerInventory |
> | Akcja | Microsoft. Insights/Logs/ContainerLog/Read | Odczytaj dane z tabeli ContainerLog |
> | Akcja | Microsoft. Insights/Logs/ContainerServiceLog/Read | Odczytaj dane z tabeli ContainerServiceLog |
> | Akcja | Microsoft. Insights/Logs/DeviceAppCrash/Read | Odczytaj dane z tabeli DeviceAppCrash |
> | Akcja | Microsoft. Insights/Logs/DeviceAppLaunch/Read | Odczytaj dane z tabeli DeviceAppLaunch |
> | Akcja | Microsoft. Insights/Logs/DeviceCalendar/Read | Odczytaj dane z tabeli DeviceCalendar |
> | Akcja | Microsoft. Insights/Logs/DeviceCleanup/Read | Odczytaj dane z tabeli DeviceCleanup |
> | Akcja | Microsoft. Insights/Logs/DeviceConnectSession/Read | Odczytaj dane z tabeli DeviceConnectSession |
> | Akcja | Microsoft. Insights/Logs/DeviceEtw/Read | Odczytaj dane z tabeli DeviceEtw |
> | Akcja | Microsoft. Insights/Logs/DeviceHardwareHealth/Read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | Akcja | Microsoft. Insights/Logs/zameldowaniem/Read | Odczytaj dane z tabeli zameldowaniem |
> | Akcja | Microsoft. Insights/Logs/DeviceHeartbeat/Read | Odczytaj dane z tabeli DeviceHeartbeat |
> | Akcja | Microsoft. Insights/Logs/DeviceSkypeHeartbeat/Read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | Akcja | Microsoft. Insights/Logs/DeviceSkypeSignIn/Read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | Akcja | Microsoft. Insights/Logs/DeviceSleepState/Read | Odczytaj dane z tabeli DeviceSleepState |
> | Akcja | Microsoft. Insights/Logs/DHAppFailure/Read | Odczytaj dane z tabeli DHAppFailure |
> | Akcja | Microsoft. Insights/Logs/DHAppReliability/Read | Odczytaj dane z tabeli DHAppReliability |
> | Akcja | Microsoft. Insights/Logs/DHDriverReliability/Read | Odczytaj dane z tabeli DHDriverReliability |
> | Akcja | Microsoft. Insights/Logs/DHLogonFailures/Read | Odczytaj dane z tabeli DHLogonFailures |
> | Akcja | Microsoft. Insights/Logs/DHLogonMetrics/Read | Odczytaj dane z tabeli DHLogonMetrics |
> | Akcja | Microsoft. Insights/Logs/DHOSCrashData/Read | Odczytaj dane z tabeli DHOSCrashData |
> | Akcja | Microsoft. Insights/Logs/DHOSReliability/Read | Odczytaj dane z tabeli DHOSReliability |
> | Akcja | Microsoft. Insights/Logs/DHWipAppLearning/Read | Odczytaj dane z tabeli DHWipAppLearning |
> | Akcja | Microsoft. Insights/Logs/DnsEvents/Read | Odczytaj dane z tabeli DnsEvents |
> | Akcja | Microsoft. Insights/Logs/DnsInventory/Read | Odczytaj dane z tabeli DnsInventory |
> | Akcja | Microsoft. Insights/Logs/ETWEvent/Read | Odczytaj dane z tabeli ETWEvent |
> | Akcja | Microsoft. Insights/Logs/Event/Read | Odczytaj dane z tabeli zdarzeń |
> | Akcja | Microsoft. Insights/Logs/ExchangeAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | Akcja | Microsoft. Insights/dzienniki/puls/odczyt | Odczytaj dane z tabeli pulsu |
> | Akcja | Microsoft. Insights/Logs/IISAssessmentRecommendation/Read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/InboundConnection/Read | Odczytaj dane z tabeli InboundConnection |
> | Akcja | Microsoft. Insights/Logs/KubeNodeInventory/Read | Odczytaj dane z tabeli KubeNodeInventory |
> | Akcja | Microsoft. Insights/Logs/KubePodInventory/Read | Odczytaj dane z tabeli KubePodInventory |
> | Akcja | Microsoft. Insights/Logs/LinuxAuditLog/Read | Odczytaj dane z tabeli LinuxAuditLog |
> | Akcja | Microsoft. Insights/Logs/MAApplication/Read | Odczytaj dane z tabeli MAApplication |
> | Akcja | Microsoft. Insights/Logs/MAApplicationHealth/Read | Odczytaj dane z tabeli MAApplicationHealth |
> | Akcja | Microsoft. Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | Akcja | Microsoft. Insights/Logs/MAApplicationHealthIssues/Read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | Akcja | Microsoft. Insights/Logs/MAApplicationInstance/Read | Odczytaj dane z tabeli MAApplicationInstance |
> | Akcja | Microsoft. Insights/Logs/MAApplicationInstanceReadiness/Read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | Akcja | Microsoft. Insights/Logs/MAApplicationReadiness/Read | Odczytaj dane z tabeli MAApplicationReadiness |
> | Akcja | Microsoft. Insights/Logs/MADeploymentPlan/Read | Odczytaj dane z tabeli MADeploymentPlan |
> | Akcja | Microsoft. Insights/Logs/MADevice/Read | Odczytaj dane z tabeli MADevice |
> | Akcja | Microsoft. Insights/Logs/MADevicePnPHealth/Read | Odczytaj dane z tabeli MADevicePnPHealth |
> | Akcja | Microsoft. Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | Akcja | Microsoft. Insights/Logs/MADevicePnPHealthIssues/Read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | Akcja | Microsoft. Insights/Logs/MADeviceReadiness/Read | Odczytaj dane z tabeli MADeviceReadiness |
> | Akcja | Microsoft. Insights/Logs/MADriverInstanceReadiness/Read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | Akcja | Microsoft. Insights/Logs/MADriverReadiness/Read | Odczytaj dane z tabeli MADriverReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddin/Read | Odczytaj dane z tabeli MAOfficeAddin |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddinHealth/Read | Odczytaj dane z tabeli MAOfficeAddinHealth |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddinHealthIssues/Read | Odczytaj dane z tabeli MAOfficeAddinHealthIssues |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddinInstance/Read | Odczytaj dane z tabeli MAOfficeAddinInstance |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinInstanceReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddinReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeApp/Read | Odczytaj dane z tabeli MAOfficeApp |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAppHealth/Read | Odczytaj dane z tabeli MAOfficeAppHealth |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAppInstance/Read | Odczytaj dane z tabeli MAOfficeAppInstance |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAppReadiness/Read | Odczytaj dane z tabeli MAOfficeAppReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeBuildInfo/Read | Odczytaj dane z tabeli MAOfficeBuildInfo |
> | Akcja | Microsoft. Insights/Logs/MAOfficeCurrencyAssessment/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessment |
> | Akcja | Microsoft. Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft. Insights/Logs/MAOfficeDeploymentStatus/Read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroHealth/Read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroHealthIssues/Read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroIssueReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroSummary/Read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | Akcja | Microsoft. Insights/Logs/MAOfficeSuite/Read | Odczytaj dane z tabeli MAOfficeSuite |
> | Akcja | Microsoft. Insights/Logs/MAOfficeSuiteInstance/Read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | Akcja | Microsoft. Insights/Logs/MAProposedPilotDevices/Read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | Akcja | Microsoft. Insights/Logs/MAWindowsBuildInfo/Read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | Akcja | Microsoft. Insights/Logs/MAWindowsCurrencyAssessment/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | Akcja | Microsoft. Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft. Insights/Logs/MAWindowsDeploymentStatus/Read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | Akcja | Microsoft. Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | Akcja | Microsoft. Insights/Logs/NetworkMonitoring/Read | Odczytaj dane z tabeli NetworkMonitoring |
> | Akcja | Microsoft. Insights/Logs/dzienniki/Office/odczyt | Odczytaj dane z tabeli pakietu Office |
> | Akcja | Microsoft. Insights/Logs/dzienników/operacji/odczytu | Odczytaj dane z tabeli operacji |
> | Akcja | Microsoft. Insights/Logs/OutboundConnection/Read | Odczytaj dane z tabeli OutboundConnection |
> | Akcja | Microsoft. Insights/Logs/perf/Read | Odczytaj dane z tabeli wydajności |
> | Akcja | Microsoft. Insights/Logs/ProtectionStatus/Read | Odczytaj dane z tabeli ProtectionStatus |
> | Akcja | Microsoft. Insights/Logs/odczyt | Odczytywanie danych ze wszystkich dzienników |
> | Akcja | Microsoft. Insights/Logs/ReservedAzureCommonFields/Read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | Akcja | Microsoft. Insights/Logs/ReservedCommonFields/Read | Odczytaj dane z tabeli ReservedCommonFields |
> | Akcja | Microsoft. Insights/Logs/SCCMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SCOMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SecurityAlert/Read | Odczytaj dane z tabeli SecurityAlert |
> | Akcja | Microsoft. Insights/Logs/SecurityBaseline/Read | Odczytaj dane z tabeli SecurityBaseline |
> | Akcja | Microsoft. Insights/Logs/SecurityBaselineSummary/Read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | Akcja | Microsoft. Insights/Logs/SecurityDetection/Read | Odczytaj dane z tabeli SecurityDetection |
> | Akcja | Microsoft. Insights/Logs/SecurityEvent/Read | Odczytaj dane z tabeli SecurityEvent |
> | Akcja | Microsoft. Insights/Logs/ServiceFabricOperationalEvent/Read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | Akcja | Microsoft. Insights/Logs/ServiceFabricReliableActorEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | Akcja | Microsoft. Insights/Logs/ServiceFabricReliableServiceEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | Akcja | Microsoft. Insights/Logs/SfBAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SPAssessmentRecommendation/Read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SQLAssessmentRecommendation/Read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SQLQueryPerformance/Read | Odczytaj dane z tabeli SQLQueryPerformance |
> | Akcja | Microsoft. Insights/Logs/dziennik systemowy/odczyt | Odczytaj dane z tabeli dziennika systemowego |
> | Akcja | Microsoft. Insights/Logs/SysmonEvent/Read | Odczytaj dane z tabeli SysmonEvent |
> | Akcja | Microsoft. Insights/dzienniki/tabele. Custom/Read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | Akcja | Microsoft. Insights/Logs/UAApp/Read | Odczytaj dane z tabeli UAApp |
> | Akcja | Microsoft. Insights/Logs/UAComputer/Read | Odczytaj dane z tabeli UAComputer |
> | Akcja | Microsoft. Insights/Logs/UAComputerRank/Read | Odczytaj dane z tabeli UAComputerRank |
> | Akcja | Microsoft. Insights/Logs/UADriver/Read | Odczytaj dane z tabeli UADriver |
> | Akcja | Microsoft. Insights/Logs/UADriverProblemCodes/Read | Odczytaj dane z tabeli UADriverProblemCodes |
> | Akcja | Microsoft. Insights/Logs/UAFeedback/Read | Odczytaj dane z tabeli UAFeedback |
> | Akcja | Microsoft. Insights/Logs/UAHardwareSecurity/Read | Odczytaj dane z tabeli UAHardwareSecurity |
> | Akcja | Microsoft. Insights/Logs/UAIESiteDiscovery/Read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | Akcja | Microsoft. Insights/Logs/UAOfficeAddIn/Read | Odczytaj dane z tabeli UAOfficeAddIn |
> | Akcja | Microsoft. Insights/Logs/UAProposedActionPlan/Read | Odczytaj dane z tabeli UAProposedActionPlan |
> | Akcja | Microsoft. Insights/Logs/UASysReqIssue/Read | Odczytaj dane z tabeli UASysReqIssue |
> | Akcja | Microsoft. Insights/Logs/UAUpgradedComputer/Read | Odczytaj dane z tabeli UAUpgradedComputer |
> | Akcja | Microsoft. Insights/Logs/Update/Read | Odczytaj dane z tabeli aktualizacji |
> | Akcja | Microsoft. Insights/Logs/UpdateRunProgress/Read | Odczytaj dane z tabeli UpdateRunProgress |
> | Akcja | Microsoft. Insights/Logs/UpdateSummary/Read | Odczytaj dane z tabeli UpdateSummary |
> | Akcja | Microsoft. Insights/Logs/dzienniki/użycie/odczyt | Odczytywanie danych z tabeli użycia |
> | Akcja | Microsoft. Insights/Logs/W3CIISLog/Read | Odczytaj dane z tabeli W3CIISLog |
> | Akcja | Microsoft. Insights/Logs/WaaSDeploymentStatus/Read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | Akcja | Microsoft. Insights/Logs/WaaSInsiderStatus/Read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | Akcja | Microsoft. Insights/Logs/WaaSUpdateStatus/Read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | Akcja | Microsoft. Insights/Logs/WDAVStatus/Read | Odczytaj dane z tabeli WDAVStatus |
> | Akcja | Microsoft. Insights/Logs/WDAVThreat/Read | Odczytaj dane z tabeli WDAVThreat |
> | Akcja | Microsoft. Insights/Logs/WindowsClientAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/WindowsFirewall/Read | Odczytaj dane z tabeli WindowsFirewall |
> | Akcja | Microsoft. Insights/Logs/WindowsServerAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/typowe/Read | Odczytaj dane z tabeli typowe |
> | Akcja | Microsoft. Insights/Logs/WUDOAggregatedStatus/Read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | Akcja | Microsoft. Insights/Logs/WUDOStatus/Read | Odczytaj dane z tabeli WUDOStatus |
> | Akcja | Microsoft. Insights/MetricAlerts/Delete | Usuwanie alertu metryki |
> | Akcja | Microsoft. Insights/MetricAlerts/Read | Odczytaj alert dotyczący metryki |
> | Akcja | Microsoft. Insights/MetricAlerts/status/Read | Odczytaj stan alertu metryki |
> | Akcja | Microsoft. Insights/MetricAlerts/Write | Utwórz lub zaktualizuj alert dotyczący metryki |
> | Akcja | Microsoft. Insights/MetricBaselines/Read | Odczytaj linie bazowe metryki |
> | Akcja | Microsoft. Insights/MetricDefinitions/Microsoft. Insights/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft. Insights/MetricDefinitions/Providers/Microsoft. Insights/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft. Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft. Insights/Metricnamespaces/Read | Odczytaj przestrzenie nazw metryk |
> | Akcja | Microsoft. Insights/Metrics/Action | Akcja metryki |
> | Akcja | Microsoft. Insights/Metrics/Microsoft. Insights/Read | Odczytaj metryki |
> | Akcja | Microsoft. Insights/Metrics/Providers/Metrics/Read | Odczytaj metryki |
> | Akcja | Microsoft. Insights/Metrics/Read | Odczytaj metryki |
> | Akcja dataaction | Microsoft. Insights/Metrics/Write | Metryki zapisu |
> | Akcja | Microsoft. Insights/MigrateToNewpricingModel/Action | Migruj subskrypcję do nowego modelu cen |
> | Akcja | Microsoft. Insights/Operations/Read | Operacje odczytu |
> | Akcja | Microsoft. Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Akcja | Microsoft. Insights/RollbackToLegacyPricingModel/Action | Wycofywanie subskrypcji do starszego modelu cen |
> | Akcja | Microsoft. Insights/ScheduledQueryRules/Delete | Usuwanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft. Insights/ScheduledQueryRules/Read | Odczytywanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft. Insights/ScheduledQueryRules/Write | Pisanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft. Insights/dzierżawcy/rejestr/akcja | Inicjuje dostawcę usługi Microsoft Insights |
> | Akcja | Microsoft. Insights/Unregister/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Akcja | Microsoft. Insights/webtests/Delete | Usuwanie konfiguracji WebTest |
> | Akcja | Microsoft. Insights/webtests/GetToken/Read | Odczytywanie tokenu WebTest |
> | Akcja | Microsoft. Insights/webtests/MetricDefinitions/Read | Odczytywanie definicji metryk WebTest |
> | Akcja | Microsoft. Insights/webtests/Metrics/Read | Odczytywanie metryk WebTest |
> | Akcja | Microsoft. Insights/webtests/Read | Odczytywanie konfiguracji WebTest |
> | Akcja | Microsoft. Insights/webtests/Write | Zapisywanie w konfiguracji WebTest |
> | Akcja | Microsoft. Insights/skoroszyty/usuwanie | Usuwanie skoroszytu |
> | Akcja | Microsoft. Insights/skoroszyty/przeczytane | Odczytaj skoroszyt |
> | Akcja | Microsoft. Insights/skoroszyty/zapis | Utwórz lub zaktualizuj skoroszyt |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Intune/diagnosticsettings/Delete | Usuwanie ustawień diagnostycznych |
> | Akcja | Microsoft. Intune/diagnosticsettings/odczyt | Odczytywanie ustawień diagnostycznych |
> | Akcja | Microsoft. Intune/diagnosticsettings/Write | Zapisywanie ustawień diagnostycznych |
> | Akcja | Microsoft. Intune/diagnosticsettingscategories/odczyt | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. IoTCentral/appTemplates/Action | Pobiera wszystkie dostępne szablony aplikacji na platformie Azure IoT Central |
> | Akcja | Microsoft. IoTCentral/checkNameAvailability/Action | Sprawdza, czy nazwa aplikacji IoT Central jest dostępna |
> | Akcja | Microsoft. IoTCentral/checkSubdomainAvailability/Action | Sprawdza, czy IoT Central poddomena aplikacji jest dostępna |
> | Akcja | Microsoft. IoTCentral/IoTApps/Delete | Usuwa IoT Central aplikacje |
> | Akcja | Microsoft. IoTCentral/IoTApps/odczyt | Pobiera pojedynczą aplikację IoT Central |
> | Akcja | Microsoft. IoTCentral/IoTApps/Write | Tworzy lub aktualizuje aplikacje IoT Central |
> | Akcja | Microsoft. IoTCentral/Operations/Read | Pobiera wszystkie dostępne operacje dotyczące IoT Central aplikacji |
> | Akcja | Microsoft. IoTCentral/Register/Action | Zarejestruj subskrypcję dostawcy zasobów usługi Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. IoTSpaces/Graph/Delete | Usuwa zasób programu Microsoft. IoTSpaces Graph |
> | Akcja | Microsoft. IoTSpaces/Graph/odczyt | Pobiera zasoby programu Microsoft. IoTSpaces Graph |
> | Akcja | Microsoft. IoTSpaces/Graph/Write | Utwórz zasób programu Microsoft. IoTSpaces Graph |
> | Akcja | Microsoft. IoTSpaces/Register/Action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. IoTSpaces Graph, aby umożliwić tworzenie zasobów |

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft./Magazyn kluczy/checkNameAvailability/odczyt | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i czy nie jest używana |
> | Akcja | Microsoft./Magazyn kluczy/deletedVaults/odczyt | Wyświetl właściwości usuniętych nietrwałych magazynów kluczy |
> | Akcja | Microsoft./Magazyn kluczy/hsmPools/usuwanie | Usuwanie puli modułów HSM |
> | Akcja | Microsoft. kluczy/hsmPools/joinVault/Action | Dołącz Magazyn kluczy do puli modułów HSM |
> | Akcja | Microsoft./Magazyn kluczy/hsmPools/odczyt | Wyświetlanie właściwości puli modułów HSM |
> | Akcja | Microsoft./Magazyn kluczy/hsmPools/zapis | Utwórz nową pulę modułów HSM, aby zaktualizować właściwości istniejącej puli modułów HSM |
> | Akcja | Microsoft./Magazyn kluczy/lokalizacji/deletedVaults/przeczyszczanie/akcja | Przeczyść usunięty nietrwale Magazyn kluczy |
> | Akcja | Microsoft./Magazyn kluczy/lokalizacji/deletedVaults/odczyt | Wyświetl właściwości usuniętego nietrwałego magazynu kluczy |
> | Akcja | Microsoft./Magazyn kluczy/lokalizacje/deleteVirtualNetworkOrSubnets/akcja | Powiadamia Microsoft. kluczy, że sieć wirtualna lub podsieć jest usuwana |
> | Akcja | Microsoft./Magazyn kluczy/lokalizacji/operationResults/odczyt | Sprawdź wynik długotrwałej operacji uruchamiania |
> | Akcja | Microsoft./Magazyn kluczy/operacje/odczyt | Wyświetla listę operacji dostępnych w ramach dostawcy zasobów Microsoft. |
> | Akcja | Microsoft.//Rejestr/Rejestracja/akcja | Rejestruje subskrypcję |
> | Akcja | Microsoft.//Unregister/akcja | Wyrejestrowuje subskrypcję |
> | Akcja | Microsoft./magazyny kluczy/magazynu/accessPolicies/zapis | Aktualizowanie istniejących zasad dostępu przez scalanie lub zastępowanie lub Dodawanie nowych zasad dostępu do magazynu. |
> | Akcja | Microsoft./magazyny/magazynu/usuwanie | Usuń magazyn kluczy |
> | Akcja | Microsoft./magazyny/magazynu/wdrożenie/akcja | Zapewnia dostęp do wpisów tajnych w magazynie kluczy podczas wdrażania zasobów platformy Azure |
> | Akcja | Microsoft./magazyny/magazynu/eventGridFilters/usuwanie | Powiadamia Microsoft. EventGrid, że jest usuwana subskrypcja usługi dla Key Vault |
> | Akcja | Microsoft./magazyny/magazynu/eventGridFilters/odczyt | Powiadamia Microsoft. EventGrid, że jest wyświetlana subskrypcja usługi dla Key Vault |
> | Akcja | Microsoft./magazyny kluczy/magazynu/eventGridFilters/zapis | Powiadamia Microsoft. kluczy, że jest tworzona nowa subskrypcja EventGrid dla Key Vault |
> | Akcja | Microsoft./magazyny kluczy/magazynu/odczyt | Wyświetlanie właściwości magazynu kluczy |
> | Akcja | Microsoft./magazyny/magazynu/wpisy tajne/odczyty | Wyświetl właściwości wpisu tajnego, ale nie jego wartość. |
> | Akcja | Microsoft./magazyny/magazynu/wpisy tajne/zapis | Utwórz nowy wpis tajny lub zaktualizuj wartość istniejącego klucza tajnego. |
> | Akcja | Microsoft./magazyny kluczy/magazynu/zapis | Utwórz nowy magazyn kluczy lub zaktualizuj właściwości istniejącego magazynu kluczy |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Kusto/klastry/aktywacja/akcja | Uruchamia klaster. |
> | Akcja | Microsoft. Kusto/klastry/AttachedDatabaseConfigurations/usuwanie | Usuwa dołączoną resourceCopy konfiguracji bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/AttachedDatabaseConfigurations/odczyt | Odczytuje załączoną konfigurację bazy danych resourceCopy. |
> | Akcja | Microsoft. Kusto/klastry/AttachedDatabaseConfigurations/zapis | Zapisuje dołączoną resourceCopy konfiguracji bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/CheckNameAvailability/akcja | Sprawdza dostępność nazwy klastra. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/addprincipals/Action | Dodaje podmioty zabezpieczeń bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/CheckNameAvailability/akcja | Sprawdza dostępność nazwy dla danego typu. |
> | Akcja | Microsoft. Kusto/klastry/Databases/dataconnections/Delete | Usuwa resourceCopy połączeń danych. |
> | Akcja | Microsoft. Kusto/klastry/Databases/dataconnections/Read | Odczytuje resourceCopy połączeń danych. |
> | Akcja | Microsoft. Kusto/klastry/Databases/dataconnections/Write | Zapisuje resourceCopy połączeń danych. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/DataConnectionValidation/akcja | Sprawdza poprawność połączenia danych bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/usuwanie | Usuwa resourceCopy bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/EventHubConnections/usuwanie | Usuwa resourceCopy połączeń centrum zdarzeń. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/EventHubConnections/odczyt | Odczytuje resourceCopy połączeń centrum zdarzeń. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/EventHubConnections/zapis | Zapisuje resourceCopy połączeń centrum zdarzeń. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/EventHubConnectionValidation/akcja | Sprawdza poprawność połączenia centrum zdarzeń bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/ListPrincipals/akcja | Wyświetla listę podmiotów zabezpieczeń bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/Odczyt | Odczytuje resourceCopy bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/RemovePrincipals/akcja | Usuwa podmioty zabezpieczeń bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/zapis | Zapisuje resourceCopy bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/dezaktywowanie/akcja | Zamyka klaster. |
> | Akcja | Microsoft. Kusto/klastry/usuwanie | Usuwa klaster resourceCopy. |
> | Akcja | Microsoft. Kusto/klastry/odczyt | Odczytuje resourceCopy klastra. |
> | Akcja | Microsoft. Kusto/klastry/jednostki SKU/odczyt | Odczytuje jednostkę SKU klastra resourceCopy. |
> | Akcja | Microsoft. Kusto/klastry/uruchamianie/akcja | Uruchamia klaster. |
> | Akcja | Microsoft. Kusto/klastry/zatrzymanie/akcja | Zamyka klaster. |
> | Akcja | Microsoft. Kusto/klastry/zapis | Zapisuje resourceCopy klastra. |
> | Akcja | Microsoft. Kusto/DetachFollowerDatabases/Action | Odłącza bazy danych programu. |
> | Akcja | Microsoft. Kusto/ListFollowerDatabases/Action | Wyświetla listę baz danych programu. |
> | Akcja | Microsoft. Kusto/Locations/CheckNameAvailability/Action | Sprawdza dostępność nazwy resourceCopy. |
> | Akcja | Microsoft. Kusto/Locations/operationresults/Read | Odczytuje operacje resourceCopys |
> | Akcja | Microsoft. Kusto/Operations/Read | Odczytuje operacje resourceCopys |
> | Akcja | Microsoft. Kusto/Register/Action | Akcja rejestracji subskrypcji |
> | Akcja | Microsoft. Kusto/Register/Action | Rejestruje subskrypcję u dostawcy zasobów Kusto. |
> | Akcja | Microsoft. Kusto/jednostki SKU/odczyt | Odczytuje resourceCopy jednostki SKU. |
> | Akcja | Microsoft. Kusto/Unregister/Action | Wyrejestrowuje subskrypcję do dostawcy zasobów Kusto. |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. LabServices/labAccounts/CreateLab/akcja | Utwórz laboratorium na koncie laboratorium. |
> | Akcja | Microsoft. LabServices/labAccounts/Delete | Usuń konta laboratorium. |
> | Akcja | Microsoft. LabServices/labAccounts/galleryImages/Delete | Usuń obrazy galerii. |
> | Akcja | Microsoft. LabServices/labAccounts/galleryImages/Read | Odczytuj obrazy galerii. |
> | Akcja | Microsoft. LabServices/labAccounts/galleryImages/Write | Dodawanie lub modyfikowanie obrazów galerii. |
> | Akcja | Microsoft. LabServices/labAccounts/GetRegionalAvailability/akcja | Uzyskaj informacje o dostępności regionalnej dla każdej kategorii rozmiaru skonfigurowanej w ramach konta laboratorium |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/ADDUSERS/Action | Dodawanie użytkowników do laboratorium |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/usuwanie | Usuwanie laboratoriów. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Delete | Usuń ustawienie środowiska. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Delete | Usuń środowiska. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Read | Odczytuj środowiska. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/ResetPassword/Action | Resetuje hasło użytkownika w środowisku |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Start/Action | Uruchamia środowisko przez uruchomienie wszystkich zasobów w środowisku. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/akcja | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Write | Dodaj lub zmodyfikuj środowiska. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Publish/Action | Rezerwy/rezerwowe zasoby wymagane dla ustawienia środowiska na podstawie bieżącego stanu ustawienia laboratorium/środowisko. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Read | Odczytaj ustawienie środowiska. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ResetPassword/Action | Resetuje hasło na maszynie wirtualnej szablonu. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/SaveImage/Action | Zapisuje bieżący obraz szablonu do udostępnionej galerii na koncie laboratorium |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/harmonogramy/usuwanie | Usuwanie harmonogramów. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Schedules/Read | Odczytaj harmonogramy. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Schedules/Write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Start/akcja | Uruchamia szablon przez uruchomienie wszystkich zasobów w szablonie. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/akcja | Zatrzymuje szablon przez zatrzymanie wszystkich zasobów w szablonie. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Write | Dodaj lub zmodyfikuj ustawienie środowiska. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/odczyt | Odczytywanie laboratoriów. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/SendEmail/Action | Wyślij wiadomość e-mail z linkiem rejestracji do laboratorium |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/użytkownicy/usuwanie | Usuń użytkowników. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/użytkownicy/odczyt | Odczytuj użytkowników. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/użytkownicy/zapis | Dodawanie lub modyfikowanie użytkowników. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/zapis | Dodawanie lub modyfikowanie laboratoriów. |
> | Akcja | Microsoft. LabServices/labAccounts/odczyt | Odczytuj konta laboratorium. |
> | Akcja | Microsoft. LabServices/labAccounts/sharedGalleries/Delete | Usuń sharedgalleries. |
> | Akcja | Microsoft. LabServices/labAccounts/sharedGalleries/Read | Odczytaj sharedgalleries. |
> | Akcja | Microsoft. LabServices/labAccounts/sharedGalleries/Write | Dodaj lub zmodyfikuj sharedgalleries. |
> | Akcja | Microsoft. LabServices/labAccounts/sharedImages/Delete | Usuń Sharedimages. |
> | Akcja | Microsoft. LabServices/labAccounts/sharedImages/Read | Odczytaj Sharedimages. |
> | Akcja | Microsoft. LabServices/labAccounts/sharedImages/Write | Dodaj lub zmodyfikuj Sharedimages. |
> | Akcja | Microsoft. LabServices/labAccounts/Write | Dodawanie lub modyfikowanie kont laboratorium. |
> | Akcja | Microsoft. LabServices/Locations/Operations/Read | Operacje odczytu. |
> | Akcja | Microsoft. LabServices/Register/Action | Rejestruje subskrypcję |
> | Akcja | Microsoft. LabServices/users/GetOperationBatchStatus/akcja | Pobierz stan operacji wsadowej |
> | Akcja | Microsoft. LabServices/users/zwróciło/akcja | Pobiera stan długotrwałej operacji |
> | Akcja | Microsoft. LabServices/users/GetPersonalPreferences/akcja | Uzyskaj osobiste preferencje dla użytkownika |
> | Akcja | Microsoft. LabServices/users/ListAllEnvironments/akcja | Wyświetl listę wszystkich środowisk dla użytkownika |
> | Akcja | Microsoft. LabServices/users/rejestr/akcja | Rejestrowanie użytkownika w zarządzanym laboratorium |
> | Akcja | Microsoft. LabServices/users/ResetPassword/akcja | Resetuje hasło użytkownika w środowisku |
> | Akcja | Microsoft. LabServices/users/StartEnvironment/akcja | Uruchamia środowisko przez uruchomienie wszystkich zasobów w środowisku. |
> | Akcja | Microsoft. LabServices/users/StopEnvironment/akcja | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Logic/integrationAccounts/Agreement/Delete | Usuwa umowę na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Agreements/listContentCallbackUrl/Action | Pobiera adres URL wywołania zwrotnego dla zawartości umowy na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Agreement/Read | Odczytuje umowę na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Agreements/Write | Tworzy lub aktualizuje umowę na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/zestawy/usuwanie | Usuwa zestaw na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/zestawy/listContentCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla zawartości zestawu na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/zestawy/odczyt | Odczytuje zestaw na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/zestawy/zapis | Tworzy lub aktualizuje zestaw na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/batchConfigurations/Delete | Usuwa konfigurację partii na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/batchConfigurations/odczyt | Odczytuje konfigurację partii na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/batchConfigurations/Write | Tworzy lub aktualizuje konfigurację partii na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Certificates/Delete | Usuwa certyfikat na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Certificates/Read | Odczytuje certyfikat na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Certificates/Write | Tworzy lub aktualizuje certyfikat na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Delete | Usuwa konto integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Join/Action | Umożliwia dołączenie do konta integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/listCallbackUrl/Action | Pobiera adres URL wywołania zwrotnego dla konta integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/listKeyVaultKeys/Action | Pobiera klucze w magazynie kluczy. |
> | Akcja | Microsoft. Logic/integrationAccounts/logTrackingEvents/Action | Rejestruje zdarzenia śledzenia na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Maps/Delete | Usuwa mapowanie na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Maps/listContentCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla zawartości mapy na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Maps/odczyt | Odczytuje mapę na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Maps/Write | Tworzy lub aktualizuje mapę na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Partners/Delete | Usuwa partnera na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Partners/listContentCallbackUrl/Action | Pobiera adres URL wywołania zwrotnego dla zawartości partnera na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Partners/Read | Odczytuje partnera na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Partners/Write | Tworzy lub aktualizuje partnera na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Providers/Microsoft. Insights/logDefinitions/Read | Odczytuje definicje dzienników konta integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Read | Odczytuje konto integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/regenerateAccessKey/Action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft. Logic/integrationAccounts/schematy/usuwanie | Usuwa schemat na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/schematów/listContentCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla zawartości schematu na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/schematy/odczyt | Odczytuje schemat na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/schematy/zapis | Tworzy lub aktualizuje schemat na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Sessions/Delete | Usuwa sesję na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Sessions/Read | Odczytuje konfigurację partii na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Sessions/Write | Tworzy lub aktualizuje sesję na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Write | Tworzy lub aktualizuje konto integracji. |
> | Akcja | Microsoft. Logic/integrationServiceEnvironments/Delete | Usuwa środowisko usługi integracji. |
> | Akcja | Microsoft. Logic/integrationServiceEnvironments/Join/Action | Sprzęga środowisko usługi integracji. |
> | Akcja | Microsoft. Logic/integrationServiceEnvironments/managedApis/apiOperations/Read | Odczytuje operację zarządzanego interfejsu API środowiska usługi integracji. |
> | Akcja | Microsoft. Logic/integrationServiceEnvironments/managedApis/odczyt | Odczytuje zarządzany interfejs API środowiska usługi integracji. |
> | Akcja | Microsoft. Logic/integrationServiceEnvironments/operationStatuses/odczyt | Odczytuje stan operacji środowiska usługi integracji. |
> | Akcja | Microsoft. Logic/integrationServiceEnvironments/Providers/Microsoft. Insights/metricDefinitions/Read | Odczytuje definicje metryk środowiska usługi integracji. |
> | Akcja | Microsoft. Logic/integrationServiceEnvironments/Read | Odczytuje środowisko usługi integracji. |
> | Akcja | Microsoft. Logic/integrationServiceEnvironments/Write | Tworzy lub aktualizuje środowisko usługi integracji. |
> | Akcja | Microsoft. Logic/Locations/Workflows/recommendOperationGroups/Action | Pobiera grupy operacji zalecane dla przepływu pracy. |
> | Akcja | Microsoft. Logic/lokalizacje/przepływy pracy/Walidacja/akcja | Sprawdza poprawność przepływu pracy. |
> | Akcja | Microsoft. Logic/Operations/Read | Pobiera operację. |
> | Akcja | Microsoft. Logic/Register/Action | Rejestruje dostawcę zasobów Microsoft. Logic dla danej subskrypcji. |
> | Akcja | Microsoft. Logic/Workflows/accessKeys/Delete | Usuwa klucz dostępu. |
> | Akcja | Microsoft. Logic/Workflows/accessKeys/list/Action | Wyświetla wpisy tajne klucza dostępu. |
> | Akcja | Microsoft. Logic/Workflows/accessKeys/Read | Odczytuje klucz dostępu. |
> | Akcja | Microsoft. Logic/Workflows/accessKeys/Regenerate/Action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft. Logic/Workflows/accessKeys/Write | Tworzy lub aktualizuje klucz dostępu. |
> | Akcja | Microsoft. Logic/przepływy pracy/usuwanie | Usuwa przepływ pracy. |
> | Akcja | Microsoft. Logic/Workflows/Disable/Action | Wyłącza przepływ pracy. |
> | Akcja | Microsoft. Logic/Workflows/Enable/Action | Włącza przepływ pracy. |
> | Akcja | Microsoft. logiki/przepływy pracy/listCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla przepływu pracy. |
> | Akcja | Microsoft. logiki/przepływy pracy/listSwagger/akcja | Pobiera definicje struktury Swagger przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/przenoszenie/akcja | Przenosi przepływ pracy z istniejącego identyfikatora subskrypcji, grupy zasobów i/lub nazwy do innego identyfikatora subskrypcji, grupy zasobów i/lub nazwy. |
> | Akcja | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/diagnosticSettings/Read | Odczytuje ustawienia diagnostyki przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyki przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/logDefinitions/Read | Odczytuje definicje dziennika przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/metricDefinitions/Read | Odczytuje definicje metryk przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/Read | Odczytuje przepływ pracy. |
> | Akcja | Microsoft. logiki/przepływy pracy/regenerateAccessKey/akcja | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft. Logic/Workflows/Run/Action | Uruchamia przebieg przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/listExpressionTraces/akcja | Pobiera ślady wyrażenia akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/odczyt | Odczytuje akcję przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia/listExpressionTraces/akcja | Pobiera ślady wyrażenia powtórzenia akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia/odczyt | Odczytuje powtórzenie akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia/requestHistories/odczyt | Odczytuje historię żądań akcji powtórzenia przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/uruchomienia/Actions/requestHistories/Read | Odczytuje historię żądań akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/uruchomienia/Actions/scoperepetitions/Read | Odczytuje powtórzenie zakresu akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/uruchomienia/Anuluj/akcja | Anuluje uruchomienie przepływu pracy. |
> | Akcja | Microsoft. logiki/przepływy pracy/uruchomienia/Usuń | Usuwa przebieg przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/uruchomienia/Operations/Read | Odczytuje stan operacji przebiegu przepływu pracy. |
> | Akcja | Microsoft. logiki/przepływy pracy/uruchomienia/odczyt | Odczytuje przebieg przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/wstrzymywanie/akcja | Wstrzymuje przepływ pracy. |
> | Akcja | Microsoft. Logic/przepływy/wyzwalacze/historie/odczyt | Odczytuje historie wyzwalacza. |
> | Akcja | Microsoft. Logic/przepływy/wyzwalacze/historie/ponowne przesyłanie/akcja | Przesyła ponownie wyzwalacz przepływu pracy. |
> | Akcja | Microsoft. logiki/przepływy pracy/wyzwalacze/listCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Akcja | Microsoft. Logic/Workflows/Triggers/Read | Odczytuje wyzwalacz. |
> | Akcja | Microsoft. Logic/przepływy pracy/wyzwalacze/Reset/akcja | Resetuje wyzwalacz. |
> | Akcja | Microsoft. Logic/Workflows/Triggers/Run/Action | Wykonuje wyzwalacz. |
> | Akcja | Microsoft. Logic/Workflows/Triggers/setstate/Action | Ustawia stan wyzwalacza. |
> | Akcja | Microsoft. Logic/Workflows/Validate/Action | Sprawdza poprawność przepływu pracy. |
> | Akcja | Microsoft. logiki/przepływy pracy/wersje/odczyt | Odczytuje wersję przepływu pracy. |
> | Akcja | Microsoft. logiki/przepływy pracy/wersje/wyzwalacze/listCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Akcja | Microsoft. Logic/przepływy pracy/zapis | Tworzy lub aktualizuje przepływ pracy. |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/Move/Action | Przenieś dowolne Machine Learning skojarzenie planu zobowiązania |
> | Akcja | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/Read | Odczytaj wszystkie Machine Learning skojarzenie planu zobowiązania |
> | Akcja | Microsoft. MachineLearning/commitmentPlans/Delete | Usuń dowolny Machine Learning plan zobowiązania |
> | Akcja | Microsoft. MachineLearning/commitmentPlans/Join/Action | Dołącz dowolny plan zobowiązania Machine Learning |
> | Akcja | Microsoft. MachineLearning/commitmentPlans/odczyt | Przeczytaj dowolny plan zobowiązania Machine Learning |
> | Akcja | Microsoft. MachineLearning/commitmentPlans/Write | Utwórz lub zaktualizuj dowolny plan zobowiązania Machine Learning |
> | Akcja | Microsoft. MachineLearning/Locations/operationresults/Read | Pobierz wynik operacji Machine Learning |
> | Akcja | Microsoft. MachineLearning/Locations/operationsstatus/Read | Pobierz stan trwającej Machine Learning operacji |
> | Akcja | Microsoft. MachineLearning/Operations/Read | Pobierz operacje Machine Learning |
> | Akcja | Microsoft. MachineLearning/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi sieci Web Machine Learning i umożliwia tworzenie usług sieci Web. |
> | Akcja | Microsoft. MachineLearning/jednostki SKU/odczyt | Pobierz jednostki SKU planu zobowiązania Machine Learning |
> | Akcja | Microsoft. MachineLearning/WebServices/Action | Tworzenie regionalnych właściwości usługi sieci Web dla obsługiwanych regionów |
> | Akcja | Microsoft. MachineLearning/WebServices/Delete | Usuń wszystkie Machine Learning usługę sieci Web |
> | Akcja | Microsoft. MachineLearning/WebServices/ListKeys/Read | Pobierz klucze do usługi sieci Web Machine Learning |
> | Akcja | Microsoft. MachineLearning/WebServices/Read | Odczytaj dowolny Machine Learning usługę sieci Web |
> | Akcja | Microsoft. MachineLearning/WebServices/Write | Utwórz lub zaktualizuj dowolną Machine Learning usługę sieci Web |
> | Akcja | Microsoft. MachineLearning/obszary robocze/usuwanie | Usuń wszystkie Obszar roboczy usługi Machine Learning |
> | Akcja | Microsoft. MachineLearning/obszary robocze/listworkspacekeys/akcja | Lista kluczy dla Obszar roboczy usługi Machine Learning |
> | Akcja | Microsoft. MachineLearning/obszary robocze/odczyt | Odczytaj wszystkie Obszar roboczy usługi Machine Learning |
> | Akcja | Microsoft. MachineLearning/obszary robocze/resyncstoragekeys/akcja | Ponowne synchronizowanie kluczy konta magazynu skonfigurowanego dla Obszar roboczy usługi Machine Learning |
> | Akcja | Microsoft. MachineLearning/obszary robocze/zapis | Utwórz lub zaktualizuj dowolne Obszar roboczy usługi Machine Learning |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. MachineLearningServices/Locations/computeoperationsstatus/Read | Pobiera stan określonej operacji obliczeniowej |
> | Akcja | Microsoft. MachineLearningServices/lokalizacje/użycia/odczyt | Raport użycia dla zasobów obliczeniowych AML w ramach subskrypcji |
> | Akcja | Microsoft. MachineLearningServices/Locations/vmsizes/Read | Pobierz obsługiwane rozmiary maszyn wirtualnych |
> | Akcja | Microsoft. MachineLearningServices/Locations/workspaceOperationsStatus/Read | Pobiera stan określonej operacji obszaru roboczego |
> | Akcja | Microsoft. MachineLearningServices/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów Machine Learning Services |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/obliczenia/usuwanie | Usuwa zasoby obliczeniowe w Machine Learning Services obszarach roboczych |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/obliczenia/listKeys/akcja | Wyświetlanie wpisów tajnych dla zasobów obliczeniowych w obszarze roboczym Machine Learning Services |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/obliczenia/listNodes/akcja | Wyświetlanie listy węzłów dla zasobu obliczeniowego w obszarze roboczym Machine Learning Services |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/obliczenia/odczyt | Pobiera zasoby obliczeniowe w Machine Learning Services obszarach roboczych |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/obliczenia/zapis | Tworzy lub aktualizuje zasoby obliczeniowe w obszarze roboczym Machine Learning Services |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/usuwanie | Usuwa obszary robocze Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/usuwanie | Usuwa eksperymenty w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/odczyt | Pobiera eksperymenty w Machine Learning Servicesych obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/zapis | Tworzy lub aktualizuje eksperymenty w Machine Learning Services obszarach roboczych |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/listKeys/akcja | Wyświetlanie wpisów tajnych dla obszaru roboczego Machine Learning Services |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/odczyt | Pobiera Machine Learning Services obszary robocze |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/zapis | Tworzy lub aktualizuje Machine Learning Services obszary robocze |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ManagedIdentity/tożsamości/odczyt | Pobiera istniejącą tożsamość przypisaną do systemu |
> | Akcja | Microsoft. ManagedIdentity/Register/Action | Rejestruje subskrypcję dostawcy zasobów tożsamości zarządzanej |
> | Akcja | Microsoft. ManagedIdentity/Resourceidentity/Assign/Action | Akcja RBAC do przypisywania istniejącej przypisanej użytkownikowi tożsamości do zasobu |
> | Akcja | Microsoft. ManagedIdentity/Resourceidentity/Delete | Usuwa istniejącą tożsamość przypisanego użytkownika |
> | Akcja | Microsoft. ManagedIdentity/Resourceidentity/odczyt | Pobiera istniejącą tożsamość przypisanego użytkownika |
> | Akcja | Microsoft. ManagedIdentity/Resourceidentity/Write | Tworzy nową tożsamość przypisanego użytkownika lub aktualizuje Tagi skojarzone z istniejącą tożsamością przypisanego użytkownika |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ManagedServices/marketplaceRegistrationDefinitions/odczyt | Pobiera listę definicji rejestracji usług zarządzanych. |
> | Akcja | Microsoft. ManagedServices/Operations/Read | Pobiera listę operacji usług zarządzanych. |
> | Akcja | Microsoft. ManagedServices/operationStatuses/odczyt | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft. ManagedServices/Register/Action | Zarejestruj się w usłudze Managed Services. |
> | Akcja | Microsoft. ManagedServices/registrationAssignments/Delete | Usuwa przypisanie rejestracji usług zarządzanych. |
> | Akcja | Microsoft. ManagedServices/registrationAssignments/odczyt | Pobiera listę przypisań rejestracji usług zarządzanych. |
> | Akcja | Microsoft. ManagedServices/registrationAssignments/Write | Dodaj lub zmodyfikuj przypisanie rejestracji usług zarządzanych. |
> | Akcja | Microsoft. ManagedServices/registrationDefinitions/Delete | Usuwa definicję rejestracji usług zarządzanych. |
> | Akcja | Microsoft. ManagedServices/registrationDefinitions/odczyt | Pobiera listę definicji rejestracji usług zarządzanych. |
> | Akcja | Microsoft. ManagedServices/registrationDefinitions/Write | Dodaj lub zmodyfikuj definicję rejestracji usług zarządzanych. |
> | Akcja | Microsoft. ManagedServices/Unregister/Action | Wyrejestruj z usług zarządzanych. |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Management/checkNameAvailability/akcja | Sprawdza, czy określona nazwa grupy zarządzania jest prawidłowa i unikatowa. |
> | Akcja | Microsoft. Management/getentities/akcja | Wyświetl listę wszystkich jednostek (Grupy zarządzania, subskrypcje itp.) dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft. Management/managementGroups/Delete | Usuń grupę zarządzania. |
> | Akcja | Microsoft. Management/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft. Management/managementGroups/subskrypcje/usuwanie | Anuluj skojarzenie subskrypcji z grupą zarządzania. |
> | Akcja | Microsoft. Management/managementGroups/subskrypcje/zapis | Kojarzy istniejącą subskrypcję z grupą zarządzania. |
> | Akcja | Microsoft. Management/managementGroups/Write | Utwórz lub Zaktualizuj grupę zarządzania. |
> | Akcja | Microsoft. Management/Register/Action | Zarejestruj określoną subskrypcję w usłudze Microsoft. Management |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja dataaction | Microsoft. Maps/konta/dane/odczyt | Przyznaje dostęp do odczytu do konta Maps. |
> | Akcja | Microsoft. Maps/accounts/Delete | Usuń konto Maps. |
> | Akcja | Microsoft. Maps/accounts/eventGridFilters/Delete | Usuń filtr Event Grid |
> | Akcja | Microsoft. Maps/accounts/eventGridFilters/Read | Pobierz filtr Event Grid |
> | Akcja | Microsoft. Maps/accounts/eventGridFilters/Write | Utwórz lub zaktualizuj filtr Event Grid |
> | Akcja | Microsoft. Maps/accounts/listKeys/Action | Lista kluczy konta map |
> | Akcja | Microsoft. Maps/konta/odczyt | Pobierz konto Maps. |
> | Akcja | Microsoft. Maps/accounts/regenerateKey/Action | Generuj nowy klucz podstawowy lub pomocniczy konta mapy |
> | Akcja | Microsoft. Maps/accounts/Write | Utwórz lub zaktualizuj konto Maps. |
> | Akcja | Microsoft. Maps/Operations/Read | Odczytaj operacje dostawcy |
> | Akcja | Microsoft. Maps/Zarejestruj/akcja | Rejestrowanie dostawcy |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Marketplace/offerTypes/wydawcy/oferty/plany/umowy/odczyt | Zwraca umowę. |
> | Akcja | Microsoft. Marketplace/offerTypes/wydawcy/oferty/plany/umowy/zapis | Akceptuje umowę ze znakiem. |
> | Akcja | Microsoft. Marketplace/offerTypes/Wydawca/oferty/plany/konfiguracje/importImage/akcja | Importuje obraz do ACR użytkownika końcowego. |
> | Akcja | Microsoft. Marketplace/offerTypes/wydawcy/oferty/plany/konfiguracje/odczyt | Zwraca konfigurację. |
> | Akcja | Microsoft. Marketplace/offerTypes/wydawcy/oferty/plany/konfiguracje/zapis | Zapisuje konfigurację. |
> | Akcja | Microsoft. Marketplace/rejestr/akcja | Rejestruje dostawcę zasobów Microsoft. Marketplace w ramach subskrypcji. |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. MarketplaceApps/ClassicDevServices/Delete | Wykonuje operację usuwania dla zasobu klasycznej usługi deweloperskiej. |
> | Akcja | Microsoft. MarketplaceApps/ClassicDevServices/listSecrets/akcja | Pobiera klasyczne klucze zarządzania zasobami usług deweloperskich. |
> | Akcja | Microsoft. MarketplaceApps/ClassicDevServices/listSingleSignOnToken/akcja | Pobiera adres URL logowania jednokrotnego dla klasycznej usługi deweloperskiej. |
> | Akcja | Microsoft. MarketplaceApps/ClassicDevServices/odczyt | Wykonuje operację pobierania na klasycznej usłudze deweloperskiej. |
> | Akcja | Microsoft. MarketplaceApps/ClassicDevServices/regenerateKey/akcja | Generuje klucze zarządzania zasobami klasycznej usługi deweloperskiej. |
> | Akcja | Microsoft. MarketplaceApps/Operations/Read | Przeczytaj operacje dla wszystkich typów zasobów. |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. MarketplaceOrdering/umowy/oferty/plany/anulowanie/akcja | Anulowanie umowy dotyczącej danego elementu portalu Marketplace |
> | Akcja | Microsoft. MarketplaceOrdering/umowy/oferty/plany/odczyt | Zwróć umowę dla danego elementu portalu Marketplace |
> | Akcja | Microsoft. MarketplaceOrdering/umowy/oferty/plany/Podpisz/akcję | Podpisz umowę dotyczącą danego elementu portalu Marketplace |
> | Akcja | Microsoft. MarketplaceOrdering/umowy/odczyt | Zwróć wszystkie umowy w ramach danej subskrypcji |
> | Akcja | Microsoft. MarketplaceOrdering/offertypes/Wydawca/oferty/plany/umowy/odczyt | Pobierz umowę dotyczącą danego elementu maszyny wirtualnej portalu Marketplace |
> | Akcja | Microsoft. MarketplaceOrdering/offertypes/Wydawca/oferty/plany/umowy/zapis | Podpisz lub Anuluj umowę dotyczącą danego elementu maszyny wirtualnej portalu Marketplace |
> | Akcja | Microsoft. MarketplaceOrdering/Operations/Read | Wyświetl listę wszystkich możliwych operacji w interfejsie API |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Media/checknameavailability/Action | Sprawdza, czy nazwa konta Media Services jest dostępna |
> | Akcja | Microsoft. Media/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa konta Media Services jest dostępna |
> | Akcja | Microsoft. Media/MediaServices/accountfilters/Delete | Usuń filtr konta |
> | Akcja | Microsoft. Media/MediaServices/accountfilters/Read | Odczytaj dowolny filtr konta |
> | Akcja | Microsoft. Media/MediaServices/accountfilters/Write | Utwórz lub zaktualizuj dowolny filtr konta |
> | Akcja | Microsoft. Media/MediaServices/Assets/assetfilters/Delete | Usuń dowolny filtr zasobów |
> | Akcja | Microsoft. Media/MediaServices/Assets/assetfilters/Read | Odczytaj dowolny filtr zasobów |
> | Akcja | Microsoft. Media/MediaServices/Assets/assetfilters/Write | Utwórz lub zaktualizuj dowolny filtr zasobów |
> | Akcja | Microsoft. Media/MediaServices/Assets/Delete | Usuń dowolny element zawartości |
> | Akcja | Microsoft. Media/MediaServices/Assets/getEncryptionKey/Action | Pobierz klucz szyfrowania zasobu |
> | Akcja | Microsoft. Media/MediaServices/Assets/listContainerSas/Action | Wyświetl listę adresów URL SAS kontenera zasobów |
> | Akcja | Microsoft. Media/MediaServices/Assets/listStreamingLocators/Action | Wyświetl listę lokalizatorów przesyłania strumieniowego dla elementu zawartości |
> | Akcja | Microsoft. Media/MediaServices/Assets/Read | Odczytaj dowolny zasób |
> | Akcja | Microsoft. Media/MediaServices/Assets/Write | Utwórz lub zaktualizuj dowolny element zawartości |
> | Akcja | Microsoft. Media/MediaServices/contentKeyPolicies/Delete | Usuń wszystkie zasady kluczy zawartości |
> | Akcja | Microsoft. Media/MediaServices/contentKeyPolicies/getPolicyPropertiesWithSecrets/Action | Pobieranie właściwości zasad przy użyciu wpisów tajnych |
> | Akcja | Microsoft. Media/MediaServices/contentKeyPolicies/Read | Odczytaj wszystkie zasady dotyczące klucza zawartości |
> | Akcja | Microsoft. Media/MediaServices/contentKeyPolicies/Write | Tworzenie lub aktualizowanie zasad klucza zawartości |
> | Akcja | Microsoft. Media/MediaServices/Delete | Usuń dowolne konto Media Services |
> | Akcja | Microsoft. Media/MediaServices/eventGridFilters/Delete | Usuń dowolny filtr Event Grid |
> | Akcja | Microsoft. Media/MediaServices/eventGridFilters/Read | Odczytaj dowolny filtr Event Grid |
> | Akcja | Microsoft. Media/MediaServices/eventGridFilters/Write | Utwórz lub zaktualizuj dowolny filtr Event Grid |
> | Akcja | Microsoft. Media/MediaServices/liveEventOperations/Read | Odczytaj wszystkie operacje na żywo dotyczące zdarzeń |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/Delete | Usuń dowolne wydarzenie na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Delete | Usuń wszystkie dane wyjściowe na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Read | Odczytaj wszystkie dane wyjściowe na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Write | Utwórz lub zaktualizuj wszystkie dane wyjściowe na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/Read | Odczytaj dowolne wydarzenie na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/Reset/Action | Zresetuj wszystkie operacje na żywo dotyczące zdarzeń |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/Start/akcja | Rozpocznij wszystkie operacje na żywo dotyczące zdarzeń |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/akcja | Zatrzymaj wszystkie operacje na żywo dotyczące zdarzeń |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/Write | Utwórz lub zaktualizuj dowolne wydarzenie na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveOutputOperations/Read | Odczytaj wszystkie operacje wyjścia na żywo |
> | Akcja | Microsoft. Media/MediaServices/Read | Odczytaj dowolne konto Media Services |
> | Akcja | Microsoft. Media/MediaServices/streamingEndpointOperations/Read | Odczytaj wszystkie operacje punktu końcowego przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingEndpoints/Delete | Usuń wszystkie punkty końcowe przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingEndpoints/Read | Odczytaj dowolny punkt końcowy przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingEndpoints/Skala/akcja | Skaluj wszystkie operacje punktu końcowego przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingEndpoints/Start/akcja | Uruchom wszystkie operacje punktu końcowego przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingEndpoints/akcja | Zatrzymaj wszystkie operacje punktu końcowego przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingEndpoints/Write | Utwórz lub zaktualizuj dowolny punkt końcowy przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingLocators/Delete | Usuń wszystkie lokalizatory przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingLocators/listContentKeys/Action | Wyświetl listę kluczy zawartości |
> | Akcja | Microsoft. Media/MediaServices/streamingLocators/listPaths/Action | Wyświetl ścieżki |
> | Akcja | Microsoft. Media/MediaServices/streamingLocators/Read | Odczytaj każdy lokalizator przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingLocators/Write | Utwórz lub zaktualizuj lokalizator przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingPolicies/Delete | Usuń wszystkie zasady przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingPolicies/Read | Odczytaj wszystkie zasady przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/streamingPolicies/Write | Tworzenie lub aktualizowanie wszelkich zasad przesyłania strumieniowego |
> | Akcja | Microsoft. Media/MediaServices/syncStorageKeys/Action | Synchronizuj klucze magazynu dla dołączonego konta usługi Azure Storage |
> | Akcja | Microsoft. Media/MediaServices/transformacje/usuwanie | Usuń dowolne przekształcenie |
> | Akcja | Microsoft. Media/MediaServices/transformacje/zadania/cancelJob/akcja | Anuluj zadanie |
> | Akcja | Microsoft. Media/MediaServices/transformacje/zadania/usuwanie | Usuń dowolne zadanie |
> | Akcja | Microsoft. Media/MediaServices/transformacje/zadania/odczyt | Odczytaj dowolne zadanie |
> | Akcja | Microsoft. Media/MediaServices/transformacje/zadania/zapis | Utwórz lub zaktualizuj dowolne zadanie |
> | Akcja | Microsoft. Media/MediaServices/transformacje/odczyt | Odczytaj dowolne przekształcenia |
> | Akcja | Microsoft. Media/MediaServices/transformacje/zapis | Utwórz lub zaktualizuj dowolne przekształcenie |
> | Akcja | Microsoft. Media/MediaServices/Write | Utwórz lub zaktualizuj dowolne konto Media Services |
> | Akcja | Microsoft. Media/Operations/Read | Pobierz dostępne operacje |
> | Akcja | Microsoft. Media/Register/Action | Rejestruje subskrypcję dostawcy zasobów Media Services i umożliwia tworzenie kont Media Services |
> | Akcja | Microsoft. Media/Unregister/Action | Wyrejestrowuje subskrypcję dla dostawcy zasobów Media Services |

## <a name="microsoftmigrate"></a>Microsoft. Migrowanie

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/assessmentOptions/odczyt | Pobiera opcje oceny, które są dostępne w danej lokalizacji |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/oceny/odczyt | Wyświetla listę ocen w projekcie |
> | Akcja | Microsoft. Migruj/assessmentprojects/Usuń | Usuwa projekt oceny |
> | Akcja | Microsoft. migruje/assessmentprojects/Groups/Assessments/assessedmachines/Read | Pobierz właściwości ocenianego komputera |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/grupy/oceny/usuwanie | Usuwa ocenę |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/grupy/oceny/downloadurl/akcja | Pobiera adres URL raportu oceny |
> | Akcja | Microsoft. Migruj/assessmentprojects/grupy/oceny/odczyt | Pobiera właściwości oceny |
> | Akcja | Microsoft. Migruj/assessmentprojects/grupy/oceny/zapis | Tworzy nową ocenę lub aktualizuje istniejącą ocenę |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/grupy/usuwanie | Usuwa grupę |
> | Akcja | Microsoft. migrowana/assessmentprojects/Groups/Read | Pobierz właściwości grupy |
> | Akcja | Microsoft. zmigrować/assessmentprojects/Groups/updateMachines/Action | Aktualizowanie grupy przez dodawanie lub usuwanie maszyn |
> | Akcja | Microsoft. migruje/assessmentprojects/Groups/Write | Tworzy nową grupę lub aktualizuje istniejącą grupę |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/hypervcollectors/usuwanie | Usuwa moduł zbierający HyperV |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/hypervcollectors/odczyt | Pobiera właściwości modułu zbierającego HyperV |
> | Akcja | Microsoft. Migruj/assessmentprojects/hypervcollectors/Write | Tworzy nowy moduł zbierający HyperV lub aktualizuje istniejący moduł zbierający funkcji Hyper-v |
> | Akcja | Microsoft. migruje/assessmentprojects/Machines/Read | Pobiera właściwości maszyny |
> | Akcja | Microsoft. migrowana/assessmentprojects/Read | Pobiera właściwości projektu oceny |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/vmwarecollectors/usuwanie | Usuwa moduł zbierający VMware |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/vmwarecollectors/odczyt | Pobiera właściwości modułu zbierającego VMware |
> | Akcja | Microsoft. Migruj/assessmentprojects/vmwarecollectors/Write | Tworzy nowy moduł zbierający VMware lub aktualizuje istniejący moduł zbierający VMware |
> | Akcja | Microsoft. Migruj/assessmentprojects/Write | Tworzy nowy projekt oceny lub aktualizuje istniejący projekt oceny |
> | Akcja | Microsoft. migrowana/Locations/assessmentOptions/Read | Pobiera opcje oceny, które są dostępne w danej lokalizacji |
> | Akcja | Microsoft. migrowana/Locations/checknameavailability/Action | Sprawdza dostępność nazwy zasobu dla danej subskrypcji w danej lokalizacji |
> | Akcja | Microsoft. Migrowanie/migrateprojects/DatabaseInstances/odczyt | Pobiera właściwości wystąpienia bazy danych |
> | Akcja | Microsoft. migruje/migrateprojects/Databases/Read | Pobiera właściwości bazy danych |
> | Akcja | Microsoft. Migruj/migrateprojects/Usuń | Usuwa projekt migracji |
> | Akcja | Microsoft. migruje/migrateprojects/Machines/Read | Pobiera właściwości maszyny |
> | Akcja | Microsoft. Migrowanie/migrateprojects/MigrateEvents/usuwanie | Usuwa zdarzenie migracji |
> | Akcja | Microsoft. Migrowanie/migrateprojects/MigrateEvents/odczyt | Pobiera właściwości zdarzenia migracji. |
> | Akcja | Microsoft. migrowana/migrateprojects/Read | Pobiera właściwości migracji projektu |
> | Akcja | Microsoft. migrowana/migrateprojects/RefreshSummary/akcja | Odświeża podsumowanie migracji projektu |
> | Akcja | Microsoft. migrowana/migrateprojects/registerTool/akcja | Rejestruje narzędzie do projektu migracji |
> | Akcja | Microsoft. migruje/migrateprojects/Solutions/cleanupData/Action | Wyczyść dane rozwiązania migracji projektu |
> | Akcja | Microsoft. migruje/migrateprojects/Solutions/Delete | Usuwa rozwiązanie migracji projektu |
> | Akcja | Microsoft. zmigrować/migrateprojects/Solutions/GetConfig/Action | Pobiera konfigurację rozwiązania migracji projektu |
> | Akcja | Microsoft. migruje/migrateprojects/Solutions/Read | Pobiera właściwości rozwiązania Migrowanie projektu |
> | Akcja | Microsoft. migruje/migrateprojects/Solutions/Write | Tworzy nowe rozwiązanie migracji projektu lub aktualizuje istniejące rozwiązanie migracji projektu |
> | Akcja | Microsoft. Migruj/migrateprojects/Write | Tworzy nowy projekt migracji lub aktualizuje istniejący projekt migracji |
> | Akcja | Microsoft. migrowana/Operations/Read | Wyświetla listę operacji dostępnych w witrynie Microsoft. Migrowanie dostawcy zasobów |
> | Akcja | Microsoft. Migracja/projekty/oceny/odczyt | Wyświetla listę ocen w projekcie |
> | Akcja | Microsoft. Migracja/projekty/usuwanie | Usuwa projekt |
> | Akcja | Microsoft. Migrowanie/projekty/grupy/oceny/assessedmachines/odczyt | Pobierz właściwości ocenianego komputera |
> | Akcja | Microsoft. Migrowanie/projekty/grupy/oceny/usuwanie | Usuwa ocenę |
> | Akcja | Microsoft. Migracja/projekty/grupy/oceny/downloadurl/akcja | Pobiera adres URL raportu oceny |
> | Akcja | Microsoft. Migracja/projekty/grupy/oceny/odczyt | Pobiera właściwości oceny |
> | Akcja | Microsoft. Migrowanie/projekty/grupy/oceny/zapis | Tworzy nową ocenę lub aktualizuje istniejącą ocenę |
> | Akcja | Microsoft. Migracja/projekty/grupy/usuwanie | Usuwa grupę |
> | Akcja | Microsoft. Migrowanie/projekty/grupy/odczyt | Pobierz właściwości grupy |
> | Akcja | Microsoft. Migrowanie/projekty/grupy/zapis | Tworzy nową grupę lub aktualizuje istniejącą grupę |
> | Akcja | Microsoft. Migruj/projekty/klucze/akcja | Pobiera klucze wspólne dla projektu |
> | Akcja | Microsoft. Migrowanie/projekty/maszyny/odczyt | Pobiera właściwości maszyny |
> | Akcja | Microsoft. Migracja/projekty/odczyt | Pobiera właściwości projektu |
> | Akcja | Microsoft. Migracja/projekty/zapis | Tworzy nowy projekt lub aktualizuje istniejący projekt |
> | Akcja | Microsoft. Migrowanie/rejestrowanie/akcja | Rejestruje subskrypcję za pomocą programu Microsoft. Migrowanie dostawcy zasobów |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. MixedReality/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów rzeczywistości mieszanej. |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Utwórz kotwice przestrzenne |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Usuń kotwice przestrzenne |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/odnajdywanie/odczyt | Odkryj kotwice w pobliżu |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/właściwości/odczyt | Pobierz właściwości kotwic przestrzennych |
> | Akcja | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla Microsoft. MixedReality/spatialAnchorsAccounts |
> | Akcja | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla Microsoft. MixedReality/spatialAnchorsAccounts |
> | Akcja | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla elementu Microsoft. MixedReality/spatialAnchorsAccounts |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Aktualizuj właściwości kotwic przestrzennych |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. NetApp/Locations/checkfilepathavailability/Action | Sprawdź, czy ścieżka pliku jest dostępna |
> | Akcja | Microsoft. NetApp/Locations/checknameavailability/Action | Sprawdź, czy nazwa zasobu jest dostępna |
> | Akcja | Microsoft. NetApp/Locations/operationresults/Read | Odczytuje zasób wyniku operacji. |
> | Akcja | Microsoft. NetApp/lokalizacje/odczyt | Odczytuje zasób sprawdzania dostępności. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/Delete | Usuwa zasób puli. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/Read | Odczytuje zasób puli. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/Delete | Usuwa zasób woluminu. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/mountTargets/Read | Odczytuje zasób docelowy instalacji. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/Read | Odczytuje zasób woluminu. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy/migawki/usuwanie | Usuwa zasób migawki. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy/migawki/odczyt | Odczytuje zasób migawki. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy/migawki/zapis | Zapisuje zasób migawki. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/Write | Zapisuje zasób woluminu. |
> | Akcja | Microsoft. NetApp/netAppAccounts/capacityPools/Write | Zapisuje zasób puli. |
> | Akcja | Microsoft. NetApp/netAppAccounts/Delete | Usuwa zasób konta. |
> | Akcja | Microsoft. NetApp/netAppAccounts/odczyt | Odczytuje zasób konta. |
> | Akcja | Microsoft. NetApp/netAppAccounts/Write | Zapisuje zasób konta. |
> | Akcja | Microsoft. NetApp/Operations/Read | Odczytuje zasoby operacji. |
> | Akcja | Microsoft. NetApp/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. NetApp |
> | Akcja | Microsoft. NetApp/Unregister/Action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. NetApp |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Network/applicationGatewayAvailableRequestHeaders/odczyt | Pobierz Application Gateway dostępne nagłówki żądania |
> | Akcja | Microsoft. Network/applicationGatewayAvailableResponseHeaders/odczyt | Pobierz Application Gateway dostępny nagłówek odpowiedzi |
> | Akcja | Microsoft. Network/applicationGatewayAvailableServerVariables/odczyt | Pobierz Application Gateway dostępne zmienne serwera |
> | Akcja | Microsoft. Network/applicationGatewayAvailableSslOptions/predefinedPolicies/odczyt | Application Gateway wstępnie zdefiniowanych zasad SSL |
> | Akcja | Microsoft. Network/applicationGatewayAvailableSslOptions/odczyt | Application Gateway dostępne opcje protokołu SSL |
> | Akcja | Microsoft. Network/applicationGatewayAvailableWafRuleSets/odczyt | Pobiera Application Gateway dostępne zestawy reguł WAF |
> | Akcja | Microsoft. Network/applicationGateways/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza bramy aplikacji. Brak alertów. |
> | Akcja | Microsoft. Network/applicationGateways/backendhealth/akcja | Pobiera kondycję zaplecza bramy aplikacji |
> | Akcja | Microsoft. Network/applicationGateways/Delete | Usuwa bramę aplikacji |
> | Akcja | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/akcja | Pobiera kondycję zaplecza bramy aplikacji na żądanie dla danego ustawienia http i puli zaplecza |
> | Akcja | Microsoft. Network/applicationGateways/odczyt | Pobiera bramę aplikacji |
> | Akcja | Microsoft. Network/applicationGateways/Start/akcja | Uruchamia bramę aplikacji |
> | Akcja | Microsoft. Network/applicationGateways/Stop/akcja | Kończy działanie bramy aplikacji |
> | Akcja | Microsoft. Network/applicationGateways/Write | Tworzy bramę aplikacji lub aktualizuje bramę aplikacji |
> | Akcja | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Delete | Usuwa zasady WAF Application Gateway |
> | Akcja | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/odczyt | Pobiera zasady WAF Application Gateway |
> | Akcja | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Write | Application Gateway tworzy zasady WAFymi lub aktualizuje zasady Application Gateway WAF |
> | Akcja | Microsoft. Network/applicationSecurityGroups/Delete | Usuwa grupę zabezpieczeń aplikacji |
> | Akcja | Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/akcja | Przyłącza konfigurację IP do grup zabezpieczeń aplikacji. Brak alertów. |
> | Akcja | Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/akcja | Przyłączanie reguły zabezpieczeń do grup zabezpieczeń aplikacji. Brak alertów. |
> | Akcja | Microsoft. Network/applicationSecurityGroups/listIpConfigurations/akcja | Wyświetla konfiguracje protokołu IP w ApplicationSecurityGroup |
> | Akcja | Microsoft. Network/applicationSecurityGroups/odczyt | Pobiera identyfikator grupy zabezpieczeń aplikacji. |
> | Akcja | Microsoft. Network/applicationSecurityGroups/Write | Tworzy grupę zabezpieczeń aplikacji lub aktualizuje istniejącą grupę zabezpieczeń aplikacji. |
> | Akcja | Microsoft. Network/azureFirewallFqdnTags/odczyt | Pobiera Tagi FQDN zapory platformy Azure |
> | Akcja | Microsoft. Network/azurefirewalls/Delete | Usuwanie zapory platformy Azure |
> | Akcja | Microsoft. Network/azurefirewalls/odczyt | Uzyskiwanie zapory platformy Azure |
> | Akcja | Microsoft. Network/azurefirewalls/Write | Tworzy lub aktualizuje zaporę platformy Azure |
> | Akcja | Microsoft. Network/bastionHosts/Delete | Usuwa hosta bastionu |
> | Akcja | Microsoft. Network/bastionHosts/odczyt | Pobiera hosta bastionu |
> | Akcja | Microsoft. Network/bastionHosts/Write | Tworzenie lub aktualizowanie hosta bastionu |
> | Akcja | Microsoft. Network/bgpServiceCommunities/odczyt | Pobierz społeczności usługi BGP |
> | Akcja | Microsoft. Network/checkFrontDoorNameAvailability/akcja | Sprawdza, czy nazwa drzwi czołowych jest dostępna |
> | Akcja | Microsoft. Network/checkTrafficManagerNameAvailability/akcja | Sprawdza dostępność Traffic Manager względnej nazwy DNS. |
> | Akcja | Microsoft. Network/Connections/Delete | Usuwa VirtualNetworkGatewayConnection |
> | Akcja | Microsoft. Network/Connections/Read | Pobiera VirtualNetworkGatewayConnection |
> | Akcja | Microsoft. Network/Connections/odwoływanie/akcja | Oznacza stan połączenia usługi Express Route jako odwołane |
> | Akcja | Microsoft. Network/Connections/SharedKey/Action | Pobierz VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft. Network/Connections/sharedKey/Read | Pobiera VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft. Network/Connections/sharedKey/Write | Tworzy lub aktualizuje istniejące VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft. Network/Connections/vpndeviceconfigurationscript/Action | Pobiera konfigurację urządzenia sieci VPN VirtualNetworkGatewayConnection |
> | Akcja | Microsoft. Network/Connections/Write | Tworzy lub aktualizuje istniejący VirtualNetworkGatewayConnection |
> | Akcja | Microsoft. Network/ddosCustomPolicies/Delete | Usuwa zasady niestandardowe DDoS |
> | Akcja | Microsoft. Network/ddosCustomPolicies/odczyt | Pobiera DDoS dostosowaną definicję zasad |
> | Akcja | Microsoft. Network/ddosCustomPolicies/Write | Tworzy dostosowane zasady DDoS lub aktualizuje istniejące dostosowane zasady DDoS |
> | Akcja | Microsoft. Network/ddosProtectionPlans/Delete | Usuwa plan DDoS Protection |
> | Akcja | Microsoft. Network/ddosProtectionPlans/Join/Action | Dołącza do planu DDoS Protection. Brak alertów. |
> | Akcja | Microsoft. Network/ddosProtectionPlans/odczyt | Pobiera plan DDoS Protection |
> | Akcja | Microsoft. Network/ddosProtectionPlans/Write | Tworzy plan DDoS Protection lub aktualizuje plan DDoS Protection  |
> | Akcja | Microsoft. Network/dnsoperationresults/odczyt | Pobiera wyniki operacji DNS |
> | Akcja | Microsoft. Network/dnsoperationstatuses/odczyt | Pobiera stan operacji DNS  |
> | Akcja | Microsoft. Network/dnszones/A/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "A" ze strefy DNS. |
> | Akcja | Microsoft. Network/dnszones/A/Odczyt | Pobierz zestaw rekordów typu "A" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/A/Write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/dnszones/AAAA/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "AAAA" ze strefy DNS. |
> | Akcja | Microsoft. Network/dnszones/AAAA/odczyt | Pobierz zestaw rekordów typu "AAAA" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/AAAA/Write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/dnszones/All/Read | Pobiera zestawy rekordów DNS dla różnych typów |
> | Akcja | Microsoft. Network/dnszones/CAA/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "CAA" ze strefy DNS. |
> | Akcja | Microsoft. Network/dnszones/CAA/odczyt | Pobierz zestaw rekordów typu "CAA" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/CAA/Write | Utwórz lub zaktualizuj zestaw rekordów typu "CAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/dnszones/CNAME/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "CNAME" ze strefy DNS. |
> | Akcja | Microsoft. Network/dnszones/CNAME/odczyt | Pobierz zestaw rekordów typu "CNAME" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/CNAME/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/dnszones/Delete | Usuń strefę DNS w formacie JSON. Właściwości strefy obejmują Tagi, ETag, numberOfRecordSets i maxNumberOfRecordSets. |
> | Akcja | Microsoft. Network/dnszones/MX/Delete | Usuń zestaw rekordów o danej nazwie i typie "MX" ze strefy DNS. |
> | Akcja | Microsoft. Network/dnszones/MX/odczyt | Pobierz zestaw rekordów typu "MX" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/MX/Write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/dnszones/NS/Delete | Usuwa zestaw rekordów DNS typu NS |
> | Akcja | Microsoft. Network/dnszones/NS/odczyt | Pobiera zestaw rekordów DNS typu NS |
> | Akcja | Microsoft. Network/dnszones/NS/zapis | Tworzy lub aktualizuje zestaw rekordów DNS typu NS |
> | Akcja | Microsoft. Network/dnszones/PTR/usuwanie | Usuń zestaw rekordów o danej nazwie i typie "PTR" ze strefy DNS. |
> | Akcja | Microsoft. Network/dnszones/PTR/odczyt | Pobierz zestaw rekordów typu "PTR" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/PTR/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/dnszones/odczyt | Pobierz strefę DNS w formacie JSON. Właściwości strefy obejmują Tagi, ETag, numberOfRecordSets i maxNumberOfRecordSets. Należy zauważyć, że to polecenie nie pobiera zestawów rekordów zawartych w strefie. |
> | Akcja | Microsoft. Network/dnszones/zestawy rekordów/odczyt | Pobiera zestawy rekordów DNS dla różnych typów |
> | Akcja | Microsoft. Network/dnszones/SOA/Read | Pobiera zestaw rekordów DNS typu SOA |
> | Akcja | Microsoft. Network/dnszones/SOA/Write | Tworzy lub aktualizuje zestaw rekordów DNS typu SOA |
> | Akcja | Microsoft. Network/dnszones/SRV/Delete | Usuń zestaw rekordów o danej nazwie i typie "SRV" ze strefy DNS. |
> | Akcja | Microsoft. Network/dnszones/SRV/Read | Pobierz zestaw rekordów typu "SRV" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/SRV/Write | Utwórz lub zaktualizuj zestaw rekordów typu SRV |
> | Akcja | Microsoft. Network/dnszones/TXT/Delete | Usuń zestaw rekordów o danej nazwie i typie "TXT" ze strefy DNS. |
> | Akcja | Microsoft. Network/dnszones/TXT/odczyt | Pobierz zestaw rekordów typu "TXT" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/TXT/Write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/dnszones/Write | Utwórz lub zaktualizuj strefę DNS w grupie zasobów.  Służy do aktualizowania tagów w zasobie strefy DNS. Należy pamiętać, że tego polecenia nie można użyć do tworzenia lub aktualizowania zestawów rekordów w strefie. |
> | Akcja | Microsoft. Network/expressRouteCircuits/autoryzacje/usuwanie | Usuwa autoryzację ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/authorizations/Read | Pobiera autoryzację ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/autoryzacje/zapis | Tworzy lub aktualizuje istniejącą autoryzację ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Delete | Usuwa ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Join/Action | Sprzęga obwód trasy Express. Brak alertów. |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/arpTables/Read | Pobiera ExpressRoute komunikacji równorzędnej ArpTable |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/Connections/Delete | Usuwa połączenie ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/Connections/Read | Pobiera połączenie ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/Connections/Write | Tworzy lub aktualizuje istniejący zasób połączenia usługi ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna/usuwanie | Usuwa komunikację równorzędną ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/peerConnections/Read | Pobiera połączenie obwodu trasy usługi peer Express |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/Read | Pobiera komunikację równorzędną ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/routeTables/Read | Pobiera trasę komunikacji równorzędnej ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/routeTablesSummary/Read | Pobiera podsumowanie tras komunikacji równorzędnej ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/\field/Read | Pobiera stat ExpressRoute komunikacji równorzędnej |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/Write | Tworzy lub aktualizuje istniejącą komunikację równorzędną ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/odczyt | Pobierz ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Statystyka/odczyt | Pobiera ExpressRoute stat |
> | Akcja | Microsoft. Network/expressRouteCircuits/Write | Tworzy lub aktualizuje istniejący ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Join/Action | Sprzęga połączenie krzyżowe usługi Express Route. Brak alertów. |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Peers/arpTables/Read | Pobiera tabelę ARP komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Komunikacja równorzędna/usuwanie | Usuwa komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Peers/Read | Pobiera komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Peers/routeTables/Read | Pobiera tabelę tras komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Peers/routeTableSummary/Read | Pobiera podsumowanie tabeli tras komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Peers/Write | Tworzy komunikację równorzędną połączenia krzyżowego usługi Express Route lub aktualizuje istniejącą komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/odczyt | Pobierz połączenie krzyżowe usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteGateways/expressRouteConnections/Delete | Usuwa połączenie usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteGateways/expressRouteConnections/odczyt | Pobiera połączenie usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteGateways/expressRouteConnections/Write | Tworzy połączenie usługi Express Route lub aktualizuje istniejące połączenie usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteGateways/Join/Action | Umożliwia dołączenie do bramy Express Route Gateway. Brak alertów. |
> | Akcja | Microsoft. Network/expressRouteGateways/odczyt | Pobierz bramę Express Route Gateway |
> | Akcja | Microsoft. Network/expressRoutePorts/Delete | Usuwa ExpressRoutePorts |
> | Akcja | Microsoft. Network/expressRoutePorts/Join/Action | Sprzęga porty routingu Express. Brak alertów. |
> | Akcja | Microsoft. Network/expressRoutePorts/Links/Read | Pobiera ExpressRouteLink |
> | Akcja | Microsoft. Network/expressRoutePorts/odczyt | Pobiera ExpressRoutePorts |
> | Akcja | Microsoft. Network/expressRoutePorts/Write | Tworzy lub aktualizuje ExpressRoutePorts |
> | Akcja | Microsoft. Network/expressRoutePortsLocations/odczyt | Pobierz lokalizacje portów Express Route |
> | Akcja | Microsoft. Network/expressRouteServiceProviders/odczyt | Pobiera dostawców Route Service ekspresowych |
> | Akcja | Microsoft. Network/firewallPolicies/Delete | Usuwa zasady zapory |
> | Akcja | Microsoft. Network/firewallPolicies/Join/Action | Przyłącza zasady zapory. Brak alertów. |
> | Akcja | Microsoft. Network/firewallPolicies/odczyt | Pobiera zasady zapory |
> | Akcja | Microsoft. Network/firewallPolicies/ruleGroups/Delete | Usuwa grupę reguł zasad zapory |
> | Akcja | Microsoft. Network/firewallPolicies/ruleGroups/odczyt | Pobiera grupę reguł zasad zapory |
> | Akcja | Microsoft. Network/firewallPolicies/ruleGroups/Write | Tworzy grupę reguł zasad zapory lub aktualizuje istniejącą grupę reguł zasad zapory |
> | Akcja | Microsoft. Network/firewallPolicies/Write | Tworzy zasady zapory lub aktualizuje istniejące zasady zapory |
> | Akcja | Microsoft. Network/usługi frontdoor/backendPools/Delete | Usuwa pulę zaplecza |
> | Akcja | Microsoft. Network/usługi frontdoor/backendPools/odczyt | Pobiera pulę zaplecza |
> | Akcja | Microsoft. Network/usługi frontdoor/backendPools/Write | Tworzy lub aktualizuje pulę zaplecza |
> | Akcja | Microsoft. Network/usługi frontdoor/Delete | Usuwa drzwiczki z przodu |
> | Akcja | Microsoft. Network/usługi frontdoor/frontendEndpoints/Delete | Usuwa punkt końcowy frontonu |
> | Akcja | Microsoft. Network/usługi frontdoor/frontendEndpoints/disableHttps/Action | Wyłącza protokół HTTPS w punkcie końcowym frontonu |
> | Akcja | Microsoft. Network/usługi frontdoor/frontendEndpoints/enableHttps/Action | Włącza protokół HTTPS w punkcie końcowym frontonu |
> | Akcja | Microsoft. Network/usługi frontdoor/frontendEndpoints/odczyt | Pobiera punkt końcowy frontonu |
> | Akcja | Microsoft. Network/usługi frontdoor/frontendEndpoints/Write | Tworzy lub aktualizuje punkt końcowy frontonu |
> | Akcja | Microsoft. Network/usługi frontdoor/healthProbeSettings/Delete | Usuwa ustawienia sondy kondycji |
> | Akcja | Microsoft. Network/usługi frontdoor/healthProbeSettings/odczyt | Pobiera ustawienia sondy kondycji |
> | Akcja | Microsoft. Network/usługi frontdoor/healthProbeSettings/Write | Tworzy lub aktualizuje ustawienia sondy kondycji |
> | Akcja | Microsoft. Network/usługi frontdoor/loadBalancingSettings/Delete | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Akcja | Microsoft. Network/usługi frontdoor/loadBalancingSettings/odczyt | Pobiera ustawienia równoważenia obciążenia |
> | Akcja | Microsoft. Network/usługi frontdoor/loadBalancingSettings/Write | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Akcja | Microsoft. Network/usługi frontdoor/przeczyszczanie/akcja | Przeczyszczanie zawartości w pamięci podręcznej z drzwi przednich |
> | Akcja | Microsoft. Network/usługi frontdoor/odczyt | Pobiera tylne drzwi |
> | Akcja | Microsoft. Network/usługi frontdoor/routingRules/Delete | Usuwa regułę routingu |
> | Akcja | Microsoft. Network/usługi frontdoor/routingRules/odczyt | Pobiera regułę routingu |
> | Akcja | Microsoft. Network/usługi frontdoor/routingRules/Write | Tworzy lub aktualizuje regułę routingu |
> | Akcja | Microsoft. Network/usługi frontdoor/validateCustomDomain/akcja | Sprawdza poprawność punktu końcowego frontonu dla drzwi z przodu |
> | Akcja | Microsoft. Network/usługi frontdoor/Write | Tworzy lub aktualizuje tylne drzwi |
> | Akcja | Microsoft. Network/frontDoorWebApplicationFirewallManagedRuleSets/odczyt | Pobiera zestawy reguł zarządzane przez zaporę aplikacji sieci Web |
> | Akcja | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Delete | Usuwa zasady zapory aplikacji sieci Web |
> | Akcja | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/odczyt | Pobiera zasady zapory aplikacji sieci Web |
> | Akcja | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Write | Tworzy lub aktualizuje zasady zapory aplikacji sieci Web |
> | Akcja | Microsoft. Network/loadBalancers/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | Akcja | Microsoft. Network/loadBalancers/backendAddressPools/odczyt | Pobiera definicję puli adresów zaplecza modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/Delete | Usuwa moduł równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/frontonu używającej/Join/Action | Sprzęga konfigurację adresu IP frontonu Load Balancer. Brak alertów. |
> | Akcja | Microsoft. Network/loadBalancers/frontonu używającej/odczyt | Pobiera definicję konfiguracji adresu IP frontonu modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/inboundNatPools/Join/Action | Przyłącza do puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia. Brak alertów. |
> | Akcja | Microsoft. Network/loadBalancers/inboundNatPools/odczyt | Pobiera definicję puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/inboundNatRules/Delete | Usuwa regułę NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/inboundNatRules/Join/Action | Przyłącza do przychodzącej reguły NAT modułu równoważenia obciążenia. Brak alertów. |
> | Akcja | Microsoft. Network/loadBalancers/inboundNatRules/odczyt | Pobiera definicję reguły NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/inboundNatRules/Write | Tworzy regułę NAT dla ruchu przychodzącego modułu równoważenia obciążenia lub aktualizuje istniejącą regułę NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/loadBalancingRules/odczyt | Pobiera definicję reguły równoważenia obciążenia modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/networkInterfaces/odczyt | Pobiera odwołania do wszystkich interfejsów sieciowych w ramach modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/outboundRules/odczyt | Pobiera definicję reguły ruchu wychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/sondy/akcja | Umożliwia korzystanie z sond modułu równoważenia obciążenia. Na przykład za pomocą tej właściwości uprawnienia healthProbe zestawu skalowania maszyn wirtualnych można odwoływać się do sondy. Brak alertów. |
> | Akcja | Microsoft. Network/loadBalancers/sondy/odczyt | Pobiera sondę modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/odczyt | Pobiera definicję modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/virtualMachines/odczyt | Pobiera odwołania do wszystkich maszyn wirtualnych w ramach modułu równoważenia obciążenia |
> | Akcja | Microsoft. Network/loadBalancers/Write | Tworzy moduł równoważenia obciążenia lub aktualizuje istniejący moduł równoważenia obciążenia |
> | Akcja | Microsoft. Network/localnetworkgateways/Delete | Usuwa LocalNetworkGateway |
> | Akcja | Microsoft. Network/localnetworkgateways/odczyt | Pobiera LocalNetworkGateway |
> | Akcja | Microsoft. Network/localnetworkgateways/Write | Tworzy lub aktualizuje istniejący LocalNetworkGateway |
> | Akcja | Microsoft. Network/Locations/autoApprovedPrivateLinkServices/Read | Pobiera usługi linków prywatnych z autozatwierdzeniem |
> | Akcja | Microsoft. Network/Locations/availableDelegations/Read | Pobiera dostępne delegacje |
> | Akcja | Microsoft. Network/Locations/availablePrivateEndpointTypes/Read | Pobiera dostępne zasoby prywatnego punktu końcowego |
> | Akcja | Microsoft. Network/Locations/availableServiceAliases/Read | Pobiera dostępne aliasy usługi |
> | Akcja | Microsoft. Network/Locations/bareMetalTenants/Action | Przydziela lub sprawdza poprawność dzierżawy systemu operacyjnego |
> | Akcja | Microsoft. Network/Locations/checkAcceleratedNetworkingSupport/Action | Sprawdza obsługę przyspieszonej sieci |
> | Akcja | Microsoft. Network/Locations/checkDnsNameAvailability/Read | Sprawdza, czy etykieta DNS jest dostępna w określonej lokalizacji |
> | Akcja | Microsoft. Network/Locations/checkPrivateLinkServiceVisibility/Action | Sprawdza widoczność usługi link prywatny |
> | Akcja | Microsoft. Network/Locations/operationResults/Read | Pobiera wynik operacji asynchronicznej operacji POST lub DELETE |
> | Akcja | Microsoft. Network/Locations/Operations/Read | Pobiera zasób operacji reprezentujący stan operacji asynchronicznej |
> | Akcja | Microsoft. Network/Locations/servicetags/Read | Pobierz Tagi usługi |
> | Akcja | Microsoft. Network/Locations/supportedVirtualMachineSizes/Read | Pobiera obsługiwane rozmiary maszyn wirtualnych |
> | Akcja | Microsoft. Network/lokalizacje/użycia/odczyt | Pobiera metryki użycia zasobów |
> | Akcja | Microsoft. Network/Locations/virtualNetworkAvailableEndpointServices/Read | Pobiera listę dostępnych Virtual Network usług punktu końcowego |
> | Akcja | Microsoft. Network/networkIntentPolicies/Delete | Usuwa zasady dotyczące opcji sieci |
> | Akcja | Microsoft. Network/networkIntentPolicies/odczyt | Pobiera opis zasad przeznaczenie sieci |
> | Akcja | Microsoft. Network/networkIntentPolicies/Write | Tworzy zasady założeń sieciowych lub aktualizuje istniejące zasady założeń sieci |
> | Akcja | Microsoft. Network/networkInterfaces/Delete | Usuwa interfejs sieciowy |
> | Akcja | Microsoft. Network/networkInterfaces/effectiveNetworkSecurityGroups/akcja | Pobieranie sieciowych grup zabezpieczeń skonfigurowanych dla interfejsu sieciowego maszyny wirtualnej |
> | Akcja | Microsoft. Network/networkInterfaces/effectiveRouteTable/akcja | Pobieranie tabeli tras skonfigurowanych dla interfejsu sieciowego maszyny wirtualnej |
> | Akcja | Microsoft. Network/networkInterfaces/ipconfiguration/sprzężenie/akcja | Umożliwia dołączenie do konfiguracji protokołu IP interfejsu sieciowego. Brak alertów. |
> | Akcja | Microsoft. Network/networkInterfaces/ipconfiguration/odczyt | Pobiera definicję konfiguracji protokołu IP interfejsu sieciowego.  |
> | Akcja | Microsoft. Network/networkInterfaces/Join/Action | Przyłączanie maszyny wirtualnej do interfejsu sieciowego. Brak alertów. |
> | Akcja | Microsoft. Network/networkInterfaces/loadBalancers/odczyt | Pobiera wszystkie usługi równoważenia obciążenia, do których należy interfejs sieciowy |
> | Akcja | Microsoft. Network/networkInterfaces/odczyt | Pobiera definicję interfejsu sieciowego.  |
> | Akcja | Microsoft. Network/networkInterfaces/tapConfigurations/Delete | Usuwa konfigurację naciskania interfejsu sieciowego. |
> | Akcja | Microsoft. Network/networkInterfaces/tapConfigurations/odczyt | Pobiera konfigurację naciskania interfejsu sieciowego. |
> | Akcja | Microsoft. Network/networkInterfaces/tapConfigurations/Write | Tworzy interfejs sieciowy naciśnij pozycję Konfiguracja lub aktualizuje istniejący interfejs sieciowy naciśnij pozycję Konfiguracja. |
> | Akcja | Microsoft. Network/networkInterfaces/Write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Akcja | Microsoft. Network/networkProfiles/Delete | Usuwa profil sieciowy |
> | Akcja | Microsoft. Network/networkProfiles/odczyt | Pobiera profil sieciowy |
> | Akcja | Microsoft. Network/networkProfiles/removeContainers/akcja | Usuwa kontenery |
> | Akcja | Microsoft. Network/networkProfiles/setcontainers/Action | Ustawia kontenery |
> | Akcja | Microsoft. Network/networkProfiles/setNetworkInterfaces/akcja | Ustawia interfejsy sieciowe kontenerów |
> | Akcja | Microsoft. Network/networkProfiles/Write | Tworzy lub aktualizuje profil sieciowy |
> | Akcja | Microsoft. Network/networkSecurityGroups/defaultSecurityRules/odczyt | Pobiera domyślną definicję reguły zabezpieczeń |
> | Akcja | Microsoft. Network/networkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń |
> | Akcja | Microsoft. Network/networkSecurityGroups/Join/Action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | Akcja | Microsoft. Network/networkSecurityGroups/odczyt | Pobiera definicję sieciowej grupy zabezpieczeń |
> | Akcja | Microsoft. Network/networkSecurityGroups/securityRules/Delete | Usuwa regułę zabezpieczeń |
> | Akcja | Microsoft. Network/networkSecurityGroups/securityRules/odczyt | Pobiera definicję reguły zabezpieczeń |
> | Akcja | Microsoft. Network/networkSecurityGroups/securityRules/Write | Tworzy regułę zabezpieczeń lub aktualizuje istniejącą regułę zabezpieczeń |
> | Akcja | Microsoft. Network/networkSecurityGroups/Write | Tworzy sieciową grupę zabezpieczeń lub aktualizuje istniejącą sieciową grupę zabezpieczeń |
> | Akcja | Microsoft. Network/networkWatchers/availableProvidersList/akcja | Zwraca wszystkich dostępnych usługodawców internetowych dla określonego regionu świadczenia usługi Azure. |
> | Akcja | Microsoft. Network/networkWatchers/azureReachabilityReport/akcja | Zwraca wynik względnego opóźnienia dla usługodawców internetowych z określonej lokalizacji do regionów świadczenia usługi Azure. |
> | Akcja | Microsoft. Network/networkWatchers/configureFlowLog/akcja | Konfiguruje rejestrowanie przepływu dla zasobu docelowego. |
> | Akcja | Microsoft. Network/networkWatchers/connectionMonitors/Delete | Usuwa monitor połączeń |
> | Akcja | Microsoft. Network/networkWatchers/connectionMonitors/kwerenda/akcja | Zbadaj łączność monitorowania między określonymi punktami końcowymi |
> | Akcja | Microsoft. Network/networkWatchers/connectionMonitors/odczyt | Pobierz szczegóły monitora połączeń |
> | Akcja | Microsoft. Network/networkWatchers/connectionMonitors/Start/akcja | Rozpocznij Monitorowanie łączności między określonymi punktami końcowymi |
> | Akcja | Microsoft. Network/networkWatchers/connectionMonitors/akcja | Zatrzymaj/Wstrzymaj Monitorowanie łączności między określonymi punktami końcowymi |
> | Akcja | Microsoft. Network/networkWatchers/connectionMonitors/Write | Tworzy monitor połączeń |
> | Akcja | Microsoft. Network/networkWatchers/connectivityCheck/akcja | Weryfikuje możliwość ustanowienia bezpośredniego połączenia TCP z maszyny wirtualnej do danego punktu końcowego, w tym innej maszyny wirtualnej lub dowolnego serwera zdalnego. |
> | Akcja | Microsoft. Network/networkWatchers/Delete | Usuwa obserwatora sieciowego |
> | Akcja | Microsoft. Network/networkWatchers/ipFlowVerify/akcja | Zwraca czy pakiet jest dozwolony, czy odrzucany do lub z określonego miejsca docelowego. |
> | Akcja | Microsoft. Network/networkWatchers/soczewki/usuwanie | Usuwa soczewkę |
> | Akcja | Microsoft. Network/networkWatchers/soczewki/kwerenda/akcja | Zbadaj ruch sieciowy monitorowania w określonym punkcie końcowym |
> | Akcja | Microsoft. Network/networkWatchers/soczewki/odczyt | Pobierz szczegóły obiektywu |
> | Akcja | Microsoft. Network/networkWatchers/soczewki/uruchomienie/akcja | Rozpocznij monitorowanie ruchu sieciowego w określonym punkcie końcowym |
> | Akcja | Microsoft. Network/networkWatchers/soczewki/akcja | Zatrzymaj/Wstrzymaj monitorowanie ruchu sieciowego w określonym punkcie końcowym |
> | Akcja | Microsoft. Network/networkWatchers/soczewki/Write | Tworzy soczewkę |
> | Akcja | Microsoft. Network/networkWatchers/networkConfigurationDiagnostic/akcja | Diagnostyka konfiguracji sieci. |
> | Akcja | Microsoft. Network/networkWatchers/skoku/akcja | W przypadku określonego docelowego i docelowego adresu IP Zwróć wartość typu następnego przeskoku i Następny adres IP. |
> | Akcja | Microsoft. Network/networkWatchers/packetCaptures/Delete | Usuwa przechwycenie pakietu |
> | Akcja | Microsoft. Network/networkWatchers/packetCaptures/queryStatus/Action | Pobiera informacje o właściwościach i stanie zasobu przechwytywania pakietów. |
> | Akcja | Microsoft. Network/networkWatchers/packetCaptures/odczyt | Pobierz definicję przechwytywania pakietów |
> | Akcja | Microsoft. Network/networkWatchers/packetCaptures/akcja | Zatrzymaj uruchomioną sesję przechwytywania pakietów. |
> | Akcja | Microsoft. Network/networkWatchers/packetCaptures/Write | Tworzy przechwycenie pakietu |
> | Akcja | Microsoft. Network/networkWatchers/pingMeshes/Delete | Usuwa element PingMesh |
> | Akcja | Microsoft. Network/networkWatchers/pingMeshes/odczyt | Pobierz szczegóły PingMesh |
> | Akcja | Microsoft. Network/networkWatchers/pingMeshes/Start/akcja | Rozpocznij PingMesh między określonymi maszynami wirtualnymi |
> | Akcja | Microsoft. Network/networkWatchers/pingMeshes/akcja | Zatrzymaj PingMesh między określonymi maszynami wirtualnymi |
> | Akcja | Microsoft. Network/networkWatchers/pingMeshes/Write | Tworzy element PingMesh |
> | Akcja | Microsoft. Network/networkWatchers/queryConnectionMonitors/akcja | Połączenie wsadowe monitorowania zapytań między określonymi punktami końcowymi |
> | Akcja | Microsoft. Network/networkWatchers/queryFlowLogStatus/akcja | Pobiera stan rejestrowania przepływu dla zasobu. |
> | Akcja | Microsoft. Network/networkWatchers/queryTroubleshootResult/akcja | Pobiera wynik rozwiązywania problemów z wcześniej uruchomionej lub aktualnie uruchomionej operacji rozwiązywania problemów. |
> | Akcja | Microsoft. Network/networkWatchers/odczyt | Pobierz definicję obserwatora sieciowego |
> | Akcja | Microsoft. Network/networkWatchers/securityGroupView/akcja | Wyświetl skonfigurowane i efektywne reguły sieciowej grupy zabezpieczeń zastosowane na maszynie wirtualnej. |
> | Akcja | Microsoft. Network/networkWatchers/topologia/akcja | Pobiera widok poziomu sieci zasobów i ich relacje z grupą zasobów. |
> | Akcja | Microsoft. Network/networkWatchers/Rozwiązywanie problemów/akcja | Uruchamia rozwiązywanie problemów dotyczących zasobów sieciowych na platformie Azure. |
> | Akcja | Microsoft. Network/networkWatchers/Write | Tworzy obserwatora sieciowego lub aktualizuje istniejący obserwator sieci |
> | Akcja | Microsoft. Network/Operations/Read | Pobierz dostępne operacje |
> | Akcja | Microsoft. Network/p2sVpnGateways/Delete | Usuwa element P2SVpnGateway. |
> | Akcja | Microsoft. Network/p2sVpnGateways/generatevpnprofile/akcja | Generuj profil sieci VPN dla P2SVpnGateway |
> | Akcja | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealth/akcja | Pobiera kondycję połączenia sieci VPN P2S dla P2SVpnGateway |
> | Akcja | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/akcja | Pobiera szczegóły kondycji połączenia sieci VPN P2S dla P2SVpnGateway |
> | Akcja | Microsoft. Network/p2sVpnGateways/odczyt | Pobiera P2SVpnGateway. |
> | Akcja | Microsoft. Network/p2sVpnGateways/Write | P2SVpnGateway. |
> | Akcja | Microsoft. Network/privateDnsOperationResults/odczyt | Pobiera wyniki operacji Prywatna strefa DNS |
> | Akcja | Microsoft. Network/privateDnsOperationStatuses/odczyt | Pobiera stan operacji Prywatna strefa DNS |
> | Akcja | Microsoft. Network/privateDnsZones/A/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "A" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/A/Odczyt | Pobierz zestaw rekordów typu "A" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/privateDnsZones/A/Write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/privateDnsZones/AAAA/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "AAAA" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/AAAA/odczyt | Pobierz zestaw rekordów typu "AAAA" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/privateDnsZones/AAAA/Write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/privateDnsZones/ALL/Read | Pobiera Prywatna strefa DNS zestawy rekordów dla różnych typów |
> | Akcja | Microsoft. Network/privateDnsZones/CNAME/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "CNAME" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/CNAME/odczyt | Pobierz zestaw rekordów typu "CNAME" w ramach strefy Prywatna strefa DNS w formacie JSON. |
> | Akcja | Microsoft. Network/privateDnsZones/CNAME/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w ramach strefy Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/Delete | Usuń strefę Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/MX/Delete | Usuń zestaw rekordów o danej nazwie i typie "MX" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/MX/odczyt | Pobierz zestaw rekordów typu "MX" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/privateDnsZones/MX/Write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/privateDnsZones/PTR/usuwanie | Usuń zestaw rekordów o danej nazwie i wpisz "PTR" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/PTR/odczyt | Pobierz zestaw rekordów typu "PTR" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/privateDnsZones/PTR/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/privateDnsZones/odczyt | Pobierz Prywatna strefa DNS właściwości strefy w formacie JSON. Należy zauważyć, że to polecenie nie pobiera sieci wirtualnych, do których jest połączona strefa Prywatna strefa DNS lub zestawy rekordów zawarte w strefie. |
> | Akcja | Microsoft. Network/privateDnsZones/zestawy rekordów/odczyt | Pobiera Prywatna strefa DNS zestawy rekordów dla różnych typów |
> | Akcja | Microsoft. Network/privateDnsZones/SOA/Read | Pobierz zestaw rekordów typu "SOA" w ramach strefy Prywatna strefa DNS w formacie JSON. |
> | Akcja | Microsoft. Network/privateDnsZones/SOA/Write | Zaktualizuj zestaw rekordów typu "SOA" w ramach strefy Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/SRV/Delete | Usuń zestaw rekordów o danej nazwie i typie "SRV" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/SRV/Read | Pobierz zestaw rekordów typu "SRV" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/privateDnsZones/SRV/Write | Utwórz lub zaktualizuj zestaw rekordów typu "SRV" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/privateDnsZones/TXT/Delete | Usuń zestaw rekordów o danej nazwie i typie "TXT" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft. Network/privateDnsZones/TXT/odczyt | Pobierz zestaw rekordów typu "TXT" w strefie Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/privateDnsZones/TXT/Write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Delete | Usuń link strefy Prywatna strefa DNS do sieci wirtualnej. |
> | Akcja | Microsoft. Network/privateDnsZones/virtualNetworkLinks/odczyt | Pobierz łącze strefy Prywatna strefa DNS do właściwości sieci wirtualnej w formacie JSON. |
> | Akcja | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write | Utwórz lub zaktualizuj link strefy Prywatna strefa DNS do sieci wirtualnej. |
> | Akcja | Microsoft. Network/privateDnsZones/Write | Utwórz lub zaktualizuj strefę Prywatna strefa DNS w grupie zasobów. Należy pamiętać, że tego polecenia nie można użyć do tworzenia lub aktualizowania linków sieci wirtualnej lub zestawów rekordów w strefie. |
> | Akcja | Microsoft. Network/privateEndpointRedirectMaps/odczyt | Pobiera prywatny punkt końcowy RedirectMap |
> | Akcja | Microsoft. Network/privateEndpointRedirectMaps/Write | Tworzy prywatny punkt końcowy RedirectMap lub aktualizuje istniejący prywatny punkt końcowy RedirectMap |
> | Akcja | Microsoft. Network/privateEndpoints/Delete | Usuwa prywatny zasób punktu końcowego. |
> | Akcja | Microsoft. Network/privateEndpoints/odczyt | Pobiera prywatny zasób punktu końcowego. |
> | Akcja | Microsoft. Network/privateEndpoints/Write | Tworzy nowy prywatny punkt końcowy lub aktualizuje istniejący prywatny punkt końcowy. |
> | Akcja | Microsoft. Network/privateLinkServices/Delete | Usuwa zasób usługi link prywatny. |
> | Akcja | Microsoft. Network/privateLinkServices/privateEndpointConnections/Delete | Usuwa połączenie prywatnego punktu końcowego. |
> | Akcja | Microsoft. Network/privateLinkServices/privateEndpointConnections/odczyt | Pobiera definicję połączenia z prywatnym punktem końcowym. |
> | Akcja | Microsoft. Network/privateLinkServices/privateEndpointConnections/Write | Tworzy nowe połączenie prywatnego punktu końcowego lub aktualizuje istniejące połączenie prywatnego punktu końcowego. |
> | Akcja | Microsoft. Network/privateLinkServices/odczyt | Pobiera zasób usługi linku prywatnego. |
> | Akcja | Microsoft. Network/privateLinkServices/Write | Tworzy nową usługę linku prywatnego lub aktualizuje istniejącą usługę linku prywatnego. |
> | Akcja | Microsoft. Network/adresów publicipaddress/Delete | Usuwa publiczny adres IP. |
> | Akcja | Microsoft. Network/adresów publicipaddress/Join/Action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | Akcja | Microsoft. Network/adresów publicipaddress/odczyt | Pobiera definicję publicznego adresu IP. |
> | Akcja | Microsoft. Network/adresów publicipaddress/Write | Tworzy publiczny adres IP lub aktualizuje istniejący publiczny adres IP.  |
> | Akcja | Microsoft. Network/publicIPPrefixes/Delete | Usuwa prefiks publicznego adresu IP |
> | Akcja | Microsoft. Network/publicIPPrefixes/Join/Action | Sprzęga PublicIPPrefix. Brak alertów. |
> | Akcja | Microsoft. Network/publicIPPrefixes/odczyt | Pobiera definicję prefiksu publicznego adresu IP |
> | Akcja | Microsoft. Network/publicIPPrefixes/Write | Tworzy publiczny prefiks adresu IP lub aktualizuje istniejący prefiks publicznego adresu IP |
> | Akcja | Microsoft. Network/Register/Action | Rejestruje subskrypcję |
> | Akcja | Microsoft. Network/routeFilters/Delete | Usuwa definicję filtru tras |
> | Akcja | Microsoft. Network/routeFilters/Join/Action | Sprzęga filtr tras. Brak alertów. |
> | Akcja | Microsoft. Network/routeFilters/odczyt | Pobiera definicję filtru tras |
> | Akcja | Microsoft. Network/routeFilters/routeFilterRules/Delete | Usuwa definicję reguły filtru trasy |
> | Akcja | Microsoft. Network/routeFilters/routeFilterRules/odczyt | Pobiera definicję reguły filtru trasy |
> | Akcja | Microsoft. Network/routeFilters/routeFilterRules/Write | Tworzy regułę filtru tras lub aktualizuje istniejącą regułę filtru tras |
> | Akcja | Microsoft. Network/routeFilters/Write | Tworzy filtr trasy lub aktualizuje istniejący filtr tras |
> | Akcja | Microsoft. Network/routeTables/Delete | Usuwa definicję tabeli tras |
> | Akcja | Microsoft. Network/routeTables/Join/Action | Sprzęga tabelę tras. Brak alertów. |
> | Akcja | Microsoft. Network/routeTables/odczyt | Pobiera definicję tabeli tras |
> | Akcja | Microsoft. Network/routeTables/Routes/Delete | Usuwa definicję trasy |
> | Akcja | Microsoft. Network/routeTables/Routes/Read | Pobiera definicję trasy |
> | Akcja | Microsoft. Network/routeTables/Routes/write | Tworzy trasę lub aktualizuje istniejącą trasę |
> | Akcja | Microsoft. Network/routeTables/Write | Tworzy tabelę tras lub aktualizuje istniejącą tabelę tras |
> | Akcja | Microsoft. Network/securegateways/Delete | Usuwanie bramy Secure Gateway |
> | Akcja | Microsoft. Network/securegateways/odczyt | Uzyskaj bezpieczną bramę |
> | Akcja | Microsoft. Network/securegateways/Write | Tworzy lub aktualizuje bramę Secure Gateway |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/Delete | Usuwa zasady punktu końcowego usługi |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/Join/Action | Przyłącza zasady punktu końcowego usługi. Brak alertów. |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/joinSubnet/akcja | Przyłączanie podsieci do zasad punktu końcowego usługi. Brak alertów. |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/odczyt | Pobiera opis zasad punktu końcowego usługi |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Delete | Usuwa definicję zasad punktu końcowego usługi |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/odczyt | Pobiera opis definicji zasad punktu końcowego usługi |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Write | Tworzy definicję zasad punktu końcowego usługi lub aktualizuje istniejącą definicję zasad punktu końcowego usługi |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/Write | Tworzy zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi |
> | Akcja | Microsoft. Network/trafficManagerGeographicHierarchies/odczyt | Pobiera Traffic Manager hierarchię geograficzną zawierającą regiony, które mogą być używane z metodą routingu ruchu geograficznego |
> | Akcja | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Delete | Usuwa punkt końcowy platformy Azure z istniejącego profilu Traffic Manager. Traffic Manager przestanie kierować ruch do usuniętego punkt końcowy platformy Azure. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/azureEndpoints/odczyt | Pobiera punkt końcowy platformy Azure, które należy do profilu Traffic Manager, w tym wszystkie właściwości tego punkt końcowy platformy Azure. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Write | Dodaj nowy punkt końcowy platformy Azure w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącej punkt końcowy platformy Azure w tym profilu Traffic Manager. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/Delete | Usuń profil Traffic Manager. Wszystkie ustawienia skojarzone z profilem Traffic Manager zostaną utracone i nie będzie można już używać profilu do kierowania ruchem. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Delete | Usuwa zewnętrzny punkt końcowy z istniejącego profilu Traffic Manager. Traffic Manager zatrzyma kierowanie ruchu do usuniętego zewnętrznego punktu końcowego. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/externalEndpoints/odczyt | Pobiera zewnętrzny punkt końcowy, który należy do profilu Traffic Manager, łącznie ze wszystkimi właściwościami tego zewnętrznego punktu końcowego. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Write | Dodaj nowy zewnętrzny punkt końcowy w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącego zewnętrznego punktu końcowego w tym profilu Traffic Manager. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/map cieplnych/odczyt | Pobiera Traffic Managerową mapę cieplną dla danego profilu Traffic Manager, który zawiera liczby zapytań i dane opóźnienia według lokalizacji i źródłowego adresu IP. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Delete | Usuwa zagnieżdżony punkt końcowy z istniejącego profilu Traffic Manager. Traffic Manager przestanie kierować ruch do usuniętego zagnieżdżonego punktu końcowego. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/odczyt | Pobiera zagnieżdżony punkt końcowy, który należy do profilu Traffic Manager, łącznie ze wszystkimi właściwościami tego zagnieżdżonego punktu końcowego. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Write | Dodaj nowy zagnieżdżony punkt końcowy w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącego zagnieżdżonego punktu końcowego w tym Traffic Manager profilu. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/odczyt | Pobierz konfigurację profilu Traffic Manager. Obejmuje to ustawienia DNS, ustawienia routingu ruchu, ustawienia monitorowania punktu końcowego oraz listę punktów końcowych kierowanych przez ten profil Traffic Manager. |
> | Akcja | Microsoft. Network/trafficManagerProfiles/Write | Utwórz profil Traffic Manager lub zmodyfikuj konfigurację istniejącego profilu Traffic Manager.<br>Obejmuje to Włączanie lub wyłączanie profilu oraz modyfikowanie ustawień DNS, ustawień routingu ruchu lub ustawień monitorowania punktu końcowego.<br>Punkty końcowe kierowane przez profil Traffic Manager można dodawać, usuwać, włączać lub wyłączać. |
> | Akcja | Microsoft. Network/trafficManagerUserMetricsKeys/Delete | Usuwa klucz poziomu subskrypcji używany do zbierania metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft. Network/trafficManagerUserMetricsKeys/odczyt | Pobiera klucz poziomu subskrypcji używany na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft. Network/trafficManagerUserMetricsKeys/Write | Tworzy nowy klucz poziomu subskrypcji, który będzie używany na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft. Network/Unregister/Action | Wyrejestrowuje subskrypcję |
> | Akcja | Microsoft. Network/virtualHubs/Delete | Usuwa koncentrator wirtualny |
> | Akcja | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/Delete | Usuwa element HubVirtualNetworkConnection |
> | Akcja | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/odczyt | Pobierz HubVirtualNetworkConnection |
> | Akcja | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/Write | Utwórz lub zaktualizuj HubVirtualNetworkConnection |
> | Akcja | Microsoft. Network/virtualHubs/odczyt | Pobierz koncentrator wirtualny |
> | Akcja | Microsoft. Network/virtualHubs/routeTables/Delete | Usuń VirtualHubRouteTableV2 |
> | Akcja | Microsoft. Network/virtualHubs/routeTables/odczyt | Pobierz VirtualHubRouteTableV2 |
> | Akcja | Microsoft. Network/virtualHubs/routeTables/Write | Utwórz lub zaktualizuj VirtualHubRouteTableV2 |
> | Akcja | Microsoft. Network/virtualHubs/Write | Tworzenie lub aktualizowanie koncentratora wirtualnego |
> | Akcja | Microsoft. Network/virtualnetworkgateways/Connections/Read | Pobierz VirtualNetworkGatewayConnection |
> | Akcja | Microsoft. Network/virtualNetworkGateways/Delete | Usuwa element virtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/generatevpnclientpackage/akcja | Generuj pakiet klienta dla virtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/generatevpnprofile/akcja | Generuj pakiet VpnProfile dla VirtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/getadvertisedroutes/akcja | Pobiera trasy anonsowane przez virtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/getbgppeerstatus/akcja | Pobiera stan elementu równorzędnego BGP virtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/getlearnedroutes/akcja | Pobiera virtualnetworkgatewayne trasy |
> | Akcja | Microsoft. Network/virtualnetworkgateways/getvpnclientconnectionhealth/akcja | Pobierz kondycję połączenia klienta sieci VPN dla VirtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/getvpnclientipsecparameters/akcja | Pobierz parametry klienta IPSec dla klienta VirtualNetworkGateway P2S. |
> | Akcja | Microsoft. Network/virtualnetworkgateways/getvpnprofilepackageurl/akcja | Pobiera adres URL wstępnie wygenerowanego pakietu profilu klienta VPN |
> | Akcja | Microsoft. Network/virtualNetworkGateways/odczyt | Pobiera VirtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/Reset/akcja | Resetuje virtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/akcja | Zresetuj klucz współużytkowany klienta dla klienta VirtualNetworkGateway P2S. |
> | Akcja | Microsoft. Network/virtualnetworkgateways/setvpnclientipsecparameters/akcja | Ustaw parametry klienta IPSec dla klienta VirtualNetworkGateway P2S. |
> | Akcja | Microsoft. Network/virtualnetworkgateways/supportedvpndevices/akcja | Wyświetla listę obsługiwanych urządzeń sieci VPN |
> | Akcja | Microsoft. Network/virtualNetworkGateways/Write | Tworzy lub aktualizuje VirtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualNetworks/BastionHosts/akcja | Pobiera odwołania do hostów bastionu w Virtual Network. |
> | Akcja | Microsoft. Network/virtualNetworks/bastionHosts/default/Action | Pobiera odwołania do hostów bastionu w Virtual Network. |
> | Akcja | Microsoft. Network/virtualNetworks/checkIpAddressAvailability/odczyt | Sprawdź, czy adres IP jest dostępny w określonej sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/Delete | Usuwa sieć wirtualną |
> | Akcja | Microsoft. Network/virtualNetworks/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Akcja | Microsoft. Network/virtualNetworks/peer/Action | Tworzy równorzędną sieć wirtualną z inną siecią wirtualną |
> | Akcja | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Delete | Usuwa zasady punktu końcowego usługi kontekstowej |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Read | Pobiera zasady punktu końcowego usługi kontekstowej |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Write | Tworzy kontekstowe zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi kontekstowej |
> | Akcja | Microsoft. Network/virtualNetworks/podsieci/usuwanie | Usuwa podsieć sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/prepareNetworkPolicies/Action | Przygotowuje podsieć przez zastosowanie niezbędnych zasad sieciowych |
> | Akcja | Microsoft. Network/virtualNetworks/podsieci/odczyt | Pobiera definicję podsieci sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/unprepareNetworkPolicies/Action | Cofnięcie przygotowania podsieci przez usunięcie zastosowanych zasad sieciowych |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/virtualMachines/Read | Pobiera odwołania do wszystkich maszyn wirtualnych w podsieci sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/podsieci/zapis | Tworzy podsieć sieci wirtualnej lub aktualizuje istniejącą podsieć sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/Usages/Read | Uzyskaj użycie adresów IP dla każdej podsieci sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/virtualMachines/odczyt | Pobiera odwołania do wszystkich maszyn wirtualnych w sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Delete | Usuwa komunikację równorzędną sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/odczyt | Pobiera definicję komunikacji równorzędnej sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Tworzy komunikację równorzędną sieci wirtualnej lub aktualizuje istniejącą komunikację równorzędną sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/Write | Tworzy sieć wirtualną lub aktualizuje istniejącą sieć wirtualną |
> | Akcja | Microsoft. Network/virtualNetworkTaps/Delete | Usuń naciśnięcie Virtual Network |
> | Akcja | Microsoft. Network/virtualNetworkTaps/Join/Action | Umożliwia dołączenie do programu TAP sieci wirtualnej. Brak alertów. |
> | Akcja | Microsoft. Network/virtualNetworkTaps/odczyt | Pobierz Virtual Network naciśnij pozycję |
> | Akcja | Microsoft. Network/virtualNetworkTaps/Write | Utwórz lub zaktualizuj Virtual Network TAP |
> | Akcja | Microsoft. Network/virtualRouters/Delete | Usuwa element VirtualRouter |
> | Akcja | Microsoft. Network/virtualRouters/Join/Action | Sprzęga VirtualRouter. Brak alertów. |
> | Akcja | Microsoft. Network/virtualRouters/odczyt | Pobiera VirtualRouter |
> | Akcja | Microsoft. Network/virtualRouters/virtualRouterPeerings/Delete | Usuwa element VirtualRouterPeering |
> | Akcja | Microsoft. Network/virtualRouters/virtualRouterPeerings/odczyt | Pobiera VirtualRouterPeering |
> | Akcja | Microsoft. Network/virtualRouters/virtualRouterPeerings/Write | Tworzy VirtualRouterPeering lub aktualizuje istniejący VirtualRouterPeering |
> | Akcja | Microsoft. Network/virtualRouters/Write | Tworzy VirtualRouter lub aktualizuje istniejący VirtualRouter |
> | Akcja | Microsoft. Network/virtualWans/Delete | Usuwa wirtualną sieć WAN |
> | Akcja | Microsoft. Network/virtualwans/generateVpnProfile/akcja | Generuj VirtualWanVpnServerConfiguration VpnProfile |
> | Akcja | Microsoft. Network/virtualWans/odczyt | Uzyskaj wirtualną sieć WAN |
> | Akcja | Microsoft. Network/virtualwans/supportedSecurityProviders/odczyt | Pobiera obsługiwanych dostawców zabezpieczeń VirtualWan. |
> | Akcja | Microsoft. Network/virtualWans/virtualHubs/odczyt | Pobiera wszystkie centra wirtualne odwołujące się do wirtualnej sieci WAN. |
> | Akcja | Microsoft. Network/virtualwans/vpnconfiguration/akcja | Pobiera konfigurację sieci VPN |
> | Akcja | Microsoft. Network/virtualwans/vpnServerConfigurations/akcja | Pobierz VirtualWanVpnServerConfigurations |
> | Akcja | Microsoft. Network/virtualWans/vpnSites/odczyt | Pobiera wszystkie lokacje sieci VPN odwołujące się do wirtualnej sieci WAN. |
> | Akcja | Microsoft. Network/virtualWans/Write | Utwórz lub zaktualizuj wirtualną sieć WAN |
> | Akcja | Microsoft. Network/vpnGateways/Delete | Usuwa element bramy vpngateway. |
> | Akcja | Microsoft. Network/vpngateways/listvpnconnectionshealth/akcja | Pobiera kondycję połączenia dla wszystkich lub podzbioru połączeń na bramy vpngateway |
> | Akcja | Microsoft. Network/vpnGateways/odczyt | Pobiera bramy vpngateway. |
> | Akcja | Microsoft. Network/vpngateways/Reset/akcja | Resetuje bramy vpngateway |
> | Akcja | Microsoft. Network/vpnGateways/vpnConnections/Delete | Usuwa element VpnConnection. |
> | Akcja | Microsoft. Network/vpnGateways/vpnConnections/odczyt | Pobiera VpnConnection. |
> | Akcja | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Pobiera połączenie sieci VPN |
> | Akcja | Microsoft. Network/vpnGateways/vpnConnections/Write | VpnConnection. |
> | Akcja | Microsoft. Network/vpnGateways/Write | Bramy vpngateway. |
> | Akcja | Microsoft. Network/vpnServerConfigurations/Delete | Usuń VpnServerConfiguration |
> | Akcja | Microsoft. Network/vpnServerConfigurations/odczyt | Pobierz VpnServerConfiguration |
> | Akcja | Microsoft. Network/vpnServerConfigurations/Write | Utwórz lub zaktualizuj VpnServerConfiguration |
> | Akcja | Microsoft. Network/vpnsites/Delete | Usuwa zasób witryny sieci VPN. |
> | Akcja | Microsoft. Network/vpnsites/odczyt | Pobiera zasób witryny sieci VPN. |
> | Akcja | Microsoft. Network/vpnSites/vpnSiteLinks/odczyt | Pobiera łącze do witryny sieci VPN |
> | Akcja | Microsoft. Network/vpnsites/Write | Tworzy lub aktualizuje zasób witryny sieci VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. NotificationHubs/CheckNamespaceAvailability/Action | Sprawdza, czy dana nazwa zasobu przestrzeni nazw jest dostępna w ramach usługi NotificationHub. |
> | Akcja | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Action | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Akcja | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Akcja | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/regenerateKeys/Action | Reguła autoryzacji przestrzeni nazw Wygeneruj ponownie podstawowy/SecondaryKey, określ klucz, który należy ponownie wygenerować |
> | Akcja | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Akcja | Microsoft. NotificationHubs/Namespaces/CheckNotificationHubAvailability/Action | Sprawdza, czy dana nazwa NotificationHub jest dostępna wewnątrz przestrzeni nazw. |
> | Akcja | Microsoft. NotificationHubs/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Action | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Delete | Usuń reguły autoryzacji centrum powiadomień |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do centrum powiadomień |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Read | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/regenerateKeys/Action | Reguła autoryzacji centrum powiadomień Regenerate Primary/SecondaryKey, określ klucz, który należy ponownie wygenerować |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Write | Utwórz reguły autoryzacji centrum powiadomień i zaktualizuj jego właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/debugSend/Action | Wyślij testowe Powiadomienie wypychane. |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Delete | Usuwanie zasobu centrum powiadomień |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/Action | Pobierz wszystkie poświadczenia PNS centrum powiadomień. Obejmuje to, WNS, usługi MPNS, APNS, GCM i Baidu |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Read | Pobierz listę opisów zasobów centrum powiadomień |
> | Akcja | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Write | Utwórz centrum powiadomień i zaktualizuj jego właściwości. Jego właściwości obejmują głównie poświadczenia PNS. Reguły autoryzacji i czas wygaśnięcia |
> | Akcja | Microsoft. NotificationHubs/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft. NotificationHubs/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Akcja | Microsoft. NotificationHubs/operationResults/odczyt | Zwraca wyniki operacji dla dostawcy Notification Hubs |
> | Akcja | Microsoft. NotificationHubs/Operations/Read | Zwraca listę obsługiwanych operacji dla dostawcy Notification Hubs |
> | Akcja | Microsoft. NotificationHubs/Register/Action | Rejestruje subskrypcję dostawcy zasobów NotificationHubs i umożliwia tworzenie przestrzeni nazw i NotificationHubs |
> | Akcja | Microsoft. NotificationHubs/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy zasobów NotificationHubs i umożliwia tworzenie przestrzeni nazw i NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. OffAzure/HyperVSites/Clusters/Read | Pobiera właściwości klastra funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/klastrów/zapis | Tworzy lub aktualizuje klaster funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/Delete | Usuwa lokację funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/healthsummary/Read | Pobiera podsumowanie kondycji zasobu funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/hosty/odczyt | Pobiera właściwości hosta funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/hosty/zapis | Tworzy lub aktualizuje hosta funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/Jobs/odczyt | Pobiera właściwości zadań funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/Machines/Read | Pobiera właściwości maszyn funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/operationsstatus/Read | Pobiera właściwości stanu operacji funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/odczyt | Pobiera właściwości lokacji funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/Odśwież/akcja | Odświeża obiekty w lokacji funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/runasaccounts/Read | Pobiera właściwości kont Uruchom jako funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/użycie/odczyt | Pobiera użycie lokacji funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/HyperVSites/Write | Tworzy lub aktualizuje lokację funkcji Hyper-V |
> | Akcja | Microsoft. OffAzure/Operations/Read | Odczytuje uwidocznione operacje |
> | Akcja | Microsoft. OffAzure/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. OffAzure |
> | Akcja | Microsoft. OffAzure/ServerSites/Jobs/odczyt | Pobiera właściwości zadań serwera |
> | Akcja | Microsoft. OffAzure/ServerSites/Machines/Read | Pobiera właściwości komputerów serwera |
> | Akcja | Microsoft. OffAzure/ServerSites/Machines/Write | Zapisz właściwości komputera serwera |
> | Akcja | Microsoft. OffAzure/ServerSites/operationsstatus/Read | Pobiera właściwości stanu operacji serwera |
> | Akcja | Microsoft. OffAzure/ServerSites/odczyt | Pobiera właściwości lokacji serwera |
> | Akcja | Microsoft. OffAzure/ServerSites/Odśwież/akcja | Odświeża obiekty w ramach lokacji serwera |
> | Akcja | Microsoft. OffAzure/ServerSites/runasaccounts/Read | Pobiera właściwości kont Uruchom jako serwera |
> | Akcja | Microsoft. OffAzure/ServerSites/użycie/odczyt | Pobiera użycie lokacji serwera |
> | Akcja | Microsoft. OffAzure/ServerSites/Write | Tworzy lub aktualizuje lokację serwera |
> | Akcja | Microsoft. OffAzure/VMwareSites/Delete | Usuwa witrynę programu VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/healthsummary/Read | Pobiera podsumowanie kondycji zasobu VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/Jobs/odczyt | Pobiera właściwości zadań programu VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/Machines/Read | Pobiera właściwości maszyn VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/Machines/Start/Action | Uruchom maszyny VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/Machines/akcja | Powoduje zatrzymanie maszyn VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/operationsstatus/Read | Pobiera właściwości stanu operacji VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/odczyt | Pobiera właściwości witryny programu VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/Odśwież/akcja | Odświeża obiekty w witrynie programu VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/runasaccounts/Read | Pobiera właściwości kont Uruchom jako VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/użycie/odczyt | Pobiera użycie witryny programu VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/vCenter/odczyt | Pobiera właściwości programu VMware vCenter |
> | Akcja | Microsoft. OffAzure/VMwareSites/vcenters/Write | Tworzy lub aktualizuje program VMware vCenter |
> | Akcja | Microsoft. OffAzure/VMwareSites/Write | Tworzy lub aktualizuje witrynę programu VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. OperationalInsights/linkTargets/odczyt | Wyświetla listę istniejących kont, które nie są skojarzone z subskrypcją platformy Azure. Aby połączyć tę subskrypcję platformy Azure z obszarem roboczym, użyj identyfikatora klienta zwróconego przez tę operację we właściwości identyfikator klienta operacji Utwórz obszar roboczy. |
> | Akcja | Microsoft. operationalinsights/Operations/Read | Wyświetla wszystkie dostępne operacje interfejsu API REST OperationalInsights. |
> | Akcja | Microsoft. operationalinsights/Register/Action | Rergisters subskrypcję. |
> | Akcja | Microsoft. OperationalInsights/Register/Action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Akcja | Microsoft. operationalinsights/Unregister/Action | Wyrejestrowuje subskrypcję. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/analiza/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Analytics/Query/Schema/Read | Pobierz schemat wyszukiwania w wersji 2. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/interfejs API/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/API/Query/Schema/Read | Pobierz schemat wyszukiwania w wersji 2. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/configurationScopes/Delete | Usuń zakres konfiguracji |
> | Akcja | Microsoft. OperationalInsights/Workspaces/configurationScopes/Read | Pobierz zakres konfiguracji |
> | Akcja | Microsoft. OperationalInsights/Workspaces/configurationScopes/Write | Ustaw zakres konfiguracji |
> | Akcja | Microsoft. OperationalInsights/Workspaces/websources/Delete | Usuń źródła danych w obszarze roboczym. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/DataSources/odczyt | Pobierz źródła danych w obszarze roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/DataSources/Write | Utwórz/zaktualizuj źródła danych w obszarze roboczym. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/usuwanie | Usuwa obszar roboczy. Jeśli obszar roboczy został połączony z istniejącym obszarem roboczym podczas tworzenia, a obszar roboczy, z którym został on połączony, nie został usunięty. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/bramy/usuwanie | Usuwa bramę skonfigurowaną dla obszaru roboczego. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/generateregistrationcertificate/akcja | Generuje certyfikat rejestracji dla obszaru roboczego. Ten certyfikat służy do łączenia programu Microsoft System Center Operations Manager z obszarem roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Disable/Action | Wyłącza pakiet Intelligence Pack dla danego obszaru roboczego. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Enable/Action | Włącza pakiet Intelligence Pack dla danego obszaru roboczego. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Read | Wyświetla listę wszystkich pakietów analizy, które są widoczne dla danego obszaru roboczego, a także listę informacji o tym, czy pakiet jest włączony, czy wyłączony dla tego obszaru roboczego. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/linkedServices/Delete | Usuń połączone usługi w podanym obszarze roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/linkedServices/Read | Pobierz połączone usługi w podanym obszarze roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/linkedServices/Write | Utwórz/zaktualizuj połączone usługi w podanym obszarze roboczym. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/listKeys/akcja | Pobiera klucze list dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/listKeys/Read | Pobiera klucze list dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/managementGroups/Read | Pobiera nazwy i metadane dla System Center Operations Manager grup zarządzania połączonych z tym obszarem roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/metricDefinitions/Read | Pobierz definicje metryki w obszarze roboczym |
> | Akcja | Microsoft. OperationalInsights/Workspaces/notificationSettings/Delete | Usuń ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/notificationSettings/Read | Pobierz ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/notificationSettings/Write | Ustaw ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft. operationalinsights/obszary robocze/operacje/odczyt | Pobiera stan operacji obszaru roboczego OperationalInsights. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/przeczyszczanie/akcja | Usuń określone dane z obszaru roboczego |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountLogon/Read | Odczytaj dane z tabeli AADDomainServicesAccountLogon |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountManagement/Read | Odczytaj dane z tabeli AADDomainServicesAccountManagement |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesDirectoryServiceAccess/Read | Odczytaj dane z tabeli AADDomainServicesDirectoryServiceAccess |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesLogonLogoff/Read | Odczytaj dane z tabeli AADDomainServicesLogonLogoff |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPolicyChange/Read | Odczytaj dane z tabeli AADDomainServicesPolicyChange |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPrivilegeUse/Read | Odczytaj dane z tabeli AADDomainServicesPrivilegeUse |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesSystemSecurity/Read | Odczytaj dane z tabeli AADDomainServicesSystemSecurity |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ADAssessmentRecommendation/Read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupAlerts/Read | Odczytaj dane z tabeli AddonAzureBackupAlerts |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupJobs/Read | Odczytaj dane z tabeli AddonAzureBackupJobs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupPolicy/Read | Odczytaj dane z tabeli AddonAzureBackupPolicy |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupProtectedInstance/Read | Odczytaj dane z tabeli AddonAzureBackupProtectedInstance |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupStorage/Read | Odczytaj dane z tabeli AddonAzureBackupStorage |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ADFActivityRun/Read | Odczytaj dane z tabeli ADFActivityRun |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ADFPipelineRun/Read | Odczytaj dane z tabeli ADFPipelineRun |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ADFTriggerRun/Read | Odczytaj dane z tabeli ADFTriggerRun |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ADReplicationResult/Read | Odczytaj dane z tabeli ADReplicationResult |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ADSecurityAssessmentRecommendation/Read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/alert/odczyt | Odczytaj dane z tabeli alertów |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AlertHistory/Read | Odczytaj dane z tabeli AlertHistory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterEvent/Read | Odczytaj dane z tabeli AmlComputeClusterEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterNodeEvent/Read | Odczytaj dane z tabeli AmlComputeClusterNodeEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeJobEvent/Read | Odczytaj dane z tabeli AmlComputeJobEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ApiManagementGatewayLogs/Read | Odczytaj dane z tabeli ApiManagementGatewayLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AppCenterError/Read | Odczytaj dane z tabeli AppCenterError |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ApplicationInsights/Read | Odczytaj dane z tabeli ApplicationInsights |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AppPlatformLogsforSpring/Read | Odczytaj dane z tabeli AppPlatformLogsforSpring |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AppServiceEnvironmentPlatformLogs/Read | Odczytaj dane z tabeli AppServiceEnvironmentPlatformLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AuditLogs/Read | Odczytaj dane z tabeli AuditLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AutoscaleEvaluationsLog/Read | Odczytaj dane z tabeli AutoscaleEvaluationsLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AutoscaleScaleActionsLog/Read | Odczytaj dane z tabeli AutoscaleScaleActionsLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AWSCloudTrail/Read | Odczytaj dane z tabeli AWSCloudTrail |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/Azure — odczyt | Odczytywanie danych z tabeli platformy Azure |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AzureAssessmentRecommendation/Read | Odczytaj dane z tabeli AzureAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AzureMetrics/Read | Odczytaj dane z tabeli AzureMetrics |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterEvent/Read | Odczytaj dane z tabeli BaiClusterEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterNodeEvent/Read | Odczytaj dane z tabeli BaiClusterNodeEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BaiJobEvent/Read | Odczytaj dane z tabeli BaiJobEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BlockchainApplicationLog/Read | Odczytaj dane z tabeli BlockchainApplicationLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BlockchainProxyLog/Read | Odczytaj dane z tabeli BlockchainProxyLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BoundPort/Read | Odczytaj dane z tabeli BoundPort |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/CommonSecurityLog/Read | Odczytaj dane z tabeli CommonSecurityLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/Computers/Read | Odczytywanie danych z tabeli komputerów |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/Zmianakonfiguracji/Read | Odczytaj dane z tabeli Zmianakonfiguracji |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ConfigurationData/Read | Odczytaj dane z tabeli ConfigurationData |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerImageInventory/Read | Odczytaj dane z tabeli ContainerImageInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerInventory/Read | Odczytaj dane z tabeli ContainerInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerLog/Read | Odczytaj dane z tabeli ContainerLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerNodeInventory/Read | Odczytaj dane z tabeli ContainerNodeInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryLoginEvents/Read | Odczytaj dane z tabeli ContainerRegistryLoginEvents |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryRepositoryEvents/Read | Odczytaj dane z tabeli ContainerRegistryRepositoryEvents |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerServiceLog/Read | Odczytaj dane z tabeli ContainerServiceLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/CoreAzureBackup/Read | Odczytaj dane z tabeli CoreAzureBackup |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksAccounts/Read | Odczytaj dane z tabeli DatabricksAccounts |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksClusters/Read | Odczytaj dane z tabeli DatabricksClusters |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksDBFS/Read | Odczytaj dane z tabeli DatabricksDBFS |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksInstancePools/Read | Odczytaj dane z tabeli DatabricksInstancePools |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksJobs/Read | Odczytaj dane z tabeli DatabricksJobs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksNotebook/Read | Odczytaj dane z tabeli DatabricksNotebook |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSecrets/Read | Odczytaj dane z tabeli DatabricksSecrets |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSQLPermissions/Read | Odczytaj dane z tabeli DatabricksSQLPermissions |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSSH/Read | Odczytaj dane z tabeli DatabricksSSH |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksTables/Read | Odczytaj dane z tabeli DatabricksTables |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksWorkspace/Read | Odczytaj dane z tabeli DatabricksWorkspace |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceAppCrash/Read | Odczytaj dane z tabeli DeviceAppCrash |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceAppLaunch/Read | Odczytaj dane z tabeli DeviceAppLaunch |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceCalendar/Read | Odczytaj dane z tabeli DeviceCalendar |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceCleanup/Read | Odczytaj dane z tabeli DeviceCleanup |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceConnectSession/Read | Odczytaj dane z tabeli DeviceConnectSession |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceEtw/Read | Odczytaj dane z tabeli DeviceEtw |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceHardwareHealth/Read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/zameldowaniem/Read | Odczytaj dane z tabeli zameldowaniem |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceHeartbeat/Read | Odczytaj dane z tabeli DeviceHeartbeat |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceSkypeHeartbeat/Read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceSkypeSignIn/Read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceSleepState/Read | Odczytaj dane z tabeli DeviceSleepState |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHAppFailure/Read | Odczytaj dane z tabeli DHAppFailure |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHAppReliability/Read | Odczytaj dane z tabeli DHAppReliability |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHDriverReliability/Read | Odczytaj dane z tabeli DHDriverReliability |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHLogonFailures/Read | Odczytaj dane z tabeli DHLogonFailures |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHLogonMetrics/Read | Odczytaj dane z tabeli DHLogonMetrics |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHOSCrashData/Read | Odczytaj dane z tabeli DHOSCrashData |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHOSReliability/Read | Odczytaj dane z tabeli DHOSReliability |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHWipAppLearning/Read | Odczytaj dane z tabeli DHWipAppLearning |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DnsEvents/Read | Odczytaj dane z tabeli DnsEvents |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DnsInventory/Read | Odczytaj dane z tabeli DnsInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ETWEvent/Read | Odczytaj dane z tabeli ETWEvent |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytanie/zdarzenie/odczyt | Odczytaj dane z tabeli zdarzeń |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ExchangeAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ExchangeOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/FailedIngestion/Read | Odczytaj dane z tabeli FailedIngestion |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/puls/odczyt | Odczytaj dane z tabeli pulsu |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/HuntingBookmark/Read | Odczytaj dane z tabeli HuntingBookmark |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/IISAssessmentRecommendation/Read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/InboundConnection/Read | Odczytaj dane z tabeli InboundConnection |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/InsightsMetrics/Read | Odczytaj dane z tabeli InsightsMetrics |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/IntuneAuditLogs/Read | Odczytaj dane z tabeli IntuneAuditLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/IntuneOperationalLogs/Read | Odczytaj dane z tabeli IntuneOperationalLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/KubeEvents/Read | Odczytaj dane z tabeli KubeEvents |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/KubeHealth/Read | Odczytaj dane z tabeli KubeHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/KubeNodeInventory/Read | Odczytaj dane z tabeli KubeNodeInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/KubePodInventory/Read | Odczytaj dane z tabeli KubePodInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/KubeServices/Read | Odczytaj dane z tabeli KubeServices |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/LinuxAuditLog/Read | Odczytaj dane z tabeli LinuxAuditLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplication/Read | Odczytaj dane z tabeli MAApplication |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealth/Read | Odczytaj dane z tabeli MAApplicationHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthAlternativeVersions/Read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthIssues/Read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationInstance/Read | Odczytaj dane z tabeli MAApplicationInstance |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationInstanceReadiness/Read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationReadiness/Read | Odczytaj dane z tabeli MAApplicationReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADeploymentPlan/Read | Odczytaj dane z tabeli MADeploymentPlan |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADevice/Read | Odczytaj dane z tabeli MADevice |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNotEnrolled/Read | Odczytaj dane z tabeli MADeviceNotEnrolled |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNRT/Read | Odczytaj dane z tabeli MADeviceNRT |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADevicePnPHealth/Read | Odczytaj dane z tabeli MADevicePnPHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADevicePnPHealthAlternativeVersions/Read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADevicePnPHealthIssues/Read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADeviceReadiness/Read | Odczytaj dane z tabeli MADeviceReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADriverInstanceReadiness/Read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADriverReadiness/Read | Odczytaj dane z tabeli MADriverReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddin/Read | Odczytaj dane z tabeli MAOfficeAddin |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinEntityHealth/Read | Odczytaj dane z tabeli MAOfficeAddinEntityHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinHealth/Read | Odczytaj dane z tabeli MAOfficeAddinHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinHealthEventNRT/Read | Odczytaj dane z tabeli MAOfficeAddinHealthEventNRT |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinHealthIssues/Read | Odczytaj dane z tabeli MAOfficeAddinHealthIssues |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinInstance/Read | Odczytaj dane z tabeli MAOfficeAddinInstance |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinInstanceReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeApp/Read | Odczytaj dane z tabeli MAOfficeApp |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppCrashesNRT/Read | Odczytaj dane z tabeli MAOfficeAppCrashesNRT |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppHealth/Read | Odczytaj dane z tabeli MAOfficeAppHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppInstance/Read | Odczytaj dane z tabeli MAOfficeAppInstance |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppInstanceHealth/Read | Odczytaj dane z tabeli MAOfficeAppInstanceHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppReadiness/Read | Odczytaj dane z tabeli MAOfficeAppReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppSessionsNRT/Read | Odczytaj dane z tabeli MAOfficeAppSessionsNRT |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeBuildInfo/Read | Odczytaj dane z tabeli MAOfficeBuildInfo |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeCurrencyAssessment/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessment |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeDeploymentStatus/Read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeDeploymentStatusNRT/Read | Odczytaj dane z tabeli MAOfficeDeploymentStatusNRT |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroErrorNRT/Read | Odczytaj dane z tabeli MAOfficeMacroErrorNRT |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroGlobalHealth/Read | Odczytaj dane z tabeli MAOfficeMacroGlobalHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealth/Read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealthIssues/Read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroSummary/Read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeSuite/Read | Odczytaj dane z tabeli MAOfficeSuite |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeSuiteInstance/Read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAProposedPilotDevices/Read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsBuildInfo/Read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsCurrencyAssessment/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsDeploymentStatus/Read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsDeploymentStatusNRT/Read | Odczytaj dane z tabeli MAWindowsDeploymentStatusNRT |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsSysReqInstanceReadiness/Read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/McasShadowItReporting/Read | Odczytaj dane z tabeli McasShadowItReporting |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareShareLog/Read | Odczytaj dane z tabeli MicrosoftDataShareShareLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftHealthcareApisAuditLogs/Read | Odczytaj dane z tabeli MicrosoftHealthcareApisAuditLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftInsightsAzureActivityLog/Read | Odczytaj dane z tabeli MicrosoftInsightsAzureActivityLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebApplicationLog/Read | Odczytaj dane z tabeli MicrosoftWebApplicationLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebFunctionExecutionLogs/Read | Odczytaj dane z tabeli MicrosoftWebFunctionExecutionLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebStdOutStdErrLog/Read | Odczytaj dane z tabeli MicrosoftWebStdOutStdErrLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebW3CLog/Read | Odczytaj dane z tabeli MicrosoftWebW3CLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/NetworkMonitoring/Read | Odczytaj dane z tabeli NetworkMonitoring |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/Office/odczyt | Odczytaj dane z tabeli pakietu Office |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/operacja/odczyt | Odczytaj dane z tabeli operacji |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/OutboundConnection/Read | Odczytaj dane z tabeli OutboundConnection |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/perf/Read | Odczytaj dane z tabeli wydajności |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ProtectionStatus/Read | Odczytaj dane z tabeli ProtectionStatus |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytanie/odczyt | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ReservedAzureCommonFields/Read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ReservedCommonFields/Read | Odczytaj dane z tabeli ReservedCommonFields |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SCCMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SCOMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityAlert/Read | Odczytaj dane z tabeli SecurityAlert |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaseline/Read | Odczytaj dane z tabeli SecurityBaseline |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaselineSummary/Read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityDetection/Read | Odczytaj dane z tabeli SecurityDetection |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityEvent/Read | Odczytaj dane z tabeli SecurityEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityIoTRawEvent/Read | Odczytaj dane z tabeli SecurityIoTRawEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityRecommendation/Read | Odczytaj dane z tabeli SecurityRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricOperationalEvent/Read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricReliableActorEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricReliableServiceEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SfBAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SfBOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SharePointOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SigninLogs/Read | Odczytaj dane z tabeli SigninLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SPAssessmentRecommendation/Read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SQLAssessmentRecommendation/Read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SQLQueryPerformance/Read | Odczytaj dane z tabeli SQLQueryPerformance |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SqlThreatProtectionLoginAudits/Read | Odczytaj dane z tabeli SqlThreatProtectionLoginAudits |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SqlVulnerabilityAssessmentResult/Read | Odczytaj dane z tabeli SqlVulnerabilityAssessmentResult |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SucceededIngestion/Read | Odczytaj dane z tabeli SucceededIngestion |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/dziennik systemowy/odczyt | Odczytaj dane z tabeli dziennika systemowego |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SysmonEvent/Read | Odczytaj dane z tabeli SysmonEvent |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/tabele. Custom/Read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ThreatIntelligenceIndicator/Read | Odczytaj dane z tabeli ThreatIntelligenceIndicator |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAApp/Read | Odczytaj dane z tabeli UAApp |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAComputer/Read | Odczytaj dane z tabeli UAComputer |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAComputerRank/Read | Odczytaj dane z tabeli UAComputerRank |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UADriver/Read | Odczytaj dane z tabeli UADriver |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UADriverProblemCodes/Read | Odczytaj dane z tabeli UADriverProblemCodes |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAFeedback/Read | Odczytaj dane z tabeli UAFeedback |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAHardwareSecurity/Read | Odczytaj dane z tabeli UAHardwareSecurity |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAIESiteDiscovery/Read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAOfficeAddIn/Read | Odczytaj dane z tabeli UAOfficeAddIn |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAProposedActionPlan/Read | Odczytaj dane z tabeli UAProposedActionPlan |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UASysReqIssue/Read | Odczytaj dane z tabeli UASysReqIssue |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAUpgradedComputer/Read | Odczytaj dane z tabeli UAUpgradedComputer |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/Update/Read | Odczytaj dane z tabeli aktualizacji |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UpdateRunProgress/Read | Odczytaj dane z tabeli UpdateRunProgress |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UpdateSummary/Read | Odczytaj dane z tabeli UpdateSummary |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/użycie/odczyt | Odczytywanie danych z tabeli użycia |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/VMBoundPort/Read | Odczytaj dane z tabeli VMBoundPort |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/VMConnection/Read | Odczytaj dane z tabeli VMConnection |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/W3CIISLog/Read | Odczytaj dane z tabeli W3CIISLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WaaSDeploymentStatus/Read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WaaSInsiderStatus/Read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WaaSUpdateStatus/Read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WDAVStatus/Read | Odczytaj dane z tabeli WDAVStatus |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WDAVThreat/Read | Odczytaj dane z tabeli WDAVThreat |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WindowsClientAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WindowsEvent/Read | Odczytaj dane z tabeli WindowsEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WindowsFirewall/Read | Odczytaj dane z tabeli WindowsFirewall |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WindowsServerAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/typowe/Read | Odczytaj dane z tabeli typowe |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WorkloadMonitoringPerf/Read | Odczytaj dane z tabeli WorkloadMonitoringPerf |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WUDOAggregatedStatus/Read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WUDOStatus/Read | Odczytaj dane z tabeli WUDOStatus |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/odczyt | Pobiera istniejący obszar roboczy |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/regeneratesharedkey/akcja | Ponownie generuje klucz współużytkowany określonego obszaru roboczego |
> | Akcja | Microsoft. operationalinsights/obszary robocze/reguły/odczyt | Pobierz wszystkie reguły alertów. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/savedSearches/Delete | Usuwa zapisane zapytanie wyszukiwania |
> | Akcja | Microsoft. OperationalInsights/Workspaces/savedSearches/Read | Pobiera zapisane zapytanie wyszukiwania |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/wyniki/odczyt | Pobierz wyniki zapisanych wyszukiwań. Przestarzałe |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Delete | Usuń zaplanowane akcje wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Read | Pobierz zaplanowane akcje wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Write | Utwórz lub zaktualizuj zaplanowane akcje wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Delete | Usuń zaplanowane wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Read | Pobierz zaplanowane wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Write | Utwórz lub zaktualizuj zaplanowane wyszukiwania. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/savedSearches/Write | Tworzy zapytanie zapisanego wyszukiwania |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/schemat/odczyt | Pobiera schemat wyszukiwania dla obszaru roboczego.  Schemat wyszukiwania obejmuje uwidocznione pola i ich typy. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/wyszukiwanie/akcja | Wykonuje zapytanie wyszukiwania |
> | Akcja | Microsoft. operationalinsights/obszary robocze/wyszukiwanie/odczyt | Pobierz wyniki wyszukiwania. Przestarzałe. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/sharedKeys/akcja | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/sharedKeys/Read | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Delete | Usuwa konfigurację magazynu. Spowoduje to zatrzymanie przez program Microsoft Operational Insights odczytywania danych z konta magazynu. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Read | Pobiera konfigurację magazynu. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Write | Tworzy nową konfigurację magazynu. Te konfiguracje są używane do ściągania danych z lokalizacji na istniejącym koncie magazynu. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/upgradetranslationfailures/Read | Pobierz dziennik błędów tłumaczenia aktualizacji wyszukiwania dla obszaru roboczego |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/użycie/odczyt | Pobiera dane użycia dla obszaru roboczego, w tym ilość danych odczytanych przez obszar roboczy. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapis | Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącego obszaru roboczego. |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. OperationsManagement/managementAssociations/Delete | Usuń istniejące skojarzenie zarządzania |
> | Akcja | Microsoft. OperationsManagement/managementAssociations/odczyt | Pobierz istniejące skojarzenie zarządzania |
> | Akcja | Microsoft. OperationsManagement/managementAssociations/Write | Utwórz nowe skojarzenie zarządzania |
> | Akcja | Microsoft. OperationsManagement/managementConfigurations/Delete | Usuń istniejącą konfigurację zarządzania |
> | Akcja | Microsoft. OperationsManagement/managementConfigurations/odczyt | Pobieranie istniejącej konfiguracji zarządzania |
> | Akcja | Microsoft. OperationsManagement/managementConfigurations/Write | Utwórz nową konfigurację zarządzania |
> | Akcja | Microsoft. OperationsManagement/Register/Action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Akcja | Microsoft. OperationsManagement/Solutions/Delete | Usuń istniejące rozwiązanie OMS |
> | Akcja | Microsoft. OperationsManagement/Solutions/Read | Pobierz wyjście z rozwiązania OMS |
> | Akcja | Microsoft. OperationsManagement/Solutions/Write | Utwórz nowe rozwiązanie OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. PolicyInsights/asyncOperationResults/odczyt | Pobiera wynik operacji asynchronicznej. |
> | Akcja dataaction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Sprawdź stan zgodności danego składnika względem zasad dotyczących danych. |
> | Akcja | Microsoft. PolicyInsights/Operations/Read | Pobiera obsługiwane operacje w przestrzeni nazw Microsoft. PolicyInsights |
> | Akcja dataaction | Microsoft. PolicyInsights/policyEvents/logDataEvents/akcja | Rejestruj zdarzenia zasad składnika zasobów. |
> | Akcja | Microsoft. PolicyInsights/policyEvents/queryResults/akcja | Zapytanie informacji o zdarzeniach zasad. |
> | Akcja | Microsoft. PolicyInsights/policyEvents/queryResults/Read | Zapytanie informacji o zdarzeniach zasad. |
> | Akcja | Microsoft. PolicyInsights/policyStates/queryResults/akcja | Zapytanie o informacje dotyczące stanów zasad. |
> | Akcja | Microsoft. PolicyInsights/policyStates/queryResults/Read | Zapytanie o informacje dotyczące stanów zasad. |
> | Akcja | Microsoft. PolicyInsights/policyStates/podsumowanie/akcja | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych Stanów zasad. |
> | Akcja | Microsoft. PolicyInsights/policyStates/podsumowanie/odczyt | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych Stanów zasad. |
> | Akcja | Microsoft. PolicyInsights/policyStates/triggerEvaluation/akcja | Wyzwala nową ocenę zgodności dla wybranego zakresu. |
> | Akcja | Microsoft. PolicyInsights/policyTrackedResources/queryResults/Read | Zapytanie dotyczące zasobów wymaganych przez zasady DeployIfNotExistsymi. |
> | Akcja | Microsoft. PolicyInsights/Register/Action | Rejestruje dostawcę zasobów usługi Microsoft Policy Insights i włącza na nim akcje. |
> | Akcja | Microsoft. PolicyInsights/korygowania/anulowanie/akcja | Anuluj korygowanie zasad firmy Microsoft w toku. |
> | Akcja | Microsoft. PolicyInsights/korygowania/usuwanie | Usuń korygowanie zasad. |
> | Akcja | Microsoft. PolicyInsights/korygowania/listDeployments/odczyt | Wyświetla listę wdrożeń wymaganych przez korygowanie zasad. |
> | Akcja | Microsoft. PolicyInsights/korygowania/odczyt | Pobierz korygowanie zasad. |
> | Akcja | Microsoft. PolicyInsights/korygowania/zapis | Utwórz lub zaktualizuj korekty zasad firmy Microsoft. |
> | Akcja | Microsoft. PolicyInsights/Unregister/Action | Wyrejestrowuje dostawcę zasobów usługi Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Portal/konsole/usuwanie | Usuwa wystąpienie Cloud Shell. |
> | Akcja | Microsoft. Portal/konsole/odczyt | Odczytuje wystąpienie Cloud Shell. |
> | Akcja | Microsoft. Portal/konsole/zapis | Utwórz lub zaktualizuj wystąpienie Cloud Shell. |
> | Akcja | Microsoft. Portal/pulpity nawigacyjne/usuwanie | Usuwa pulpit nawigacyjny z subskrypcji. |
> | Akcja | Microsoft. Portal/pulpity nawigacyjne/odczyt | Odczytuje pulpity nawigacyjne subskrypcji. |
> | Akcja | Microsoft. Portal/pulpity nawigacyjne/zapis | Dodawanie lub modyfikowanie pulpitu nawigacyjnego do subskrypcji. |
> | Akcja | Microsoft. Portal/rejestr/akcja | Zarejestruj się w portalu |
> | Akcja | Microsoft. Portal/UserSettings/Usuń | Usuwa Cloud Shell ustawienia użytkownika. |
> | Akcja | Microsoft. Portal/UserSettings/odczyt | Odczytuje ustawienia użytkownika Cloud Shell. |
> | Akcja | Microsoft. Portal/UserSettings/zapis | Utwórz lub zaktualizuj ustawienie użytkownika Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. PowerBIDedicated/pojemność/usuwanie | Usuwa dedykowaną pojemność Power BI. |
> | Akcja | Microsoft. PowerBIDedicated/pojemność/odczyt | Pobiera informacje o określonej Power BI dedykowanej pojemności. |
> | Akcja | Microsoft. PowerBIDedicated/pojemność/wznowienie/akcja | Wznawia pojemność. |
> | Akcja | Microsoft. PowerBIDedicated/pojemność/jednostki SKU/odczyt | Pobierz informacje o dostępnych jednostkach SKU dla pojemności |
> | Akcja | Microsoft. PowerBIDedicated/pojemność/wstrzymywanie/akcja | Wstrzymuje pojemność. |
> | Akcja | Microsoft. PowerBIDedicated/pojemność/zapis | Tworzy lub aktualizuje określony Power BI dedykowanej pojemności. |
> | Akcja | Microsoft. PowerBIDedicated/Locations/checkNameAvailability/Action | Sprawdza, czy dana nazwa dedykowanej pojemności Power BI jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft. PowerBIDedicated/Locations/operationresults/Read | Pobiera informacje o wyniku określonej operacji. |
> | Akcja | Microsoft. PowerBIDedicated/Locations/operationstatuses/Read | Pobiera informacje o stanie określonej operacji. |
> | Akcja | Microsoft. PowerBIDedicated/Operations/Read | Pobiera informacje o operacjach |
> | Akcja | Microsoft. PowerBIDedicated/Register/Action | Rejestruje Power BI dedykowanego dostawcę zasobów. |
> | Akcja | Microsoft. PowerBIDedicated/jednostki SKU/odczyt | Pobiera informacje o jednostkach SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | Akcja | Microsoft. RecoveryServices/Locations/allocateStamp/Action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Akcja | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Akcja | Microsoft. RecoveryServices/Locations/backupProtectedItem/Write | Utwórz chroniony element kopii zapasowej |
> | Akcja | Microsoft. RecoveryServices/Locations/backupProtectedItems/Read | Zwraca listę wszystkich elementów chronionych. |
> | Akcja | Microsoft. RecoveryServices/Locations/backupStatus/Action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Akcja | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Weryfikuj funkcje |
> | Akcja | Microsoft. RecoveryServices/Locations/checkNameAvailability/Action | Sprawdź dostępność nazwy zasobu jest interfejsem API, aby sprawdzić, czy nazwa zasobu jest dostępna |
> | Akcja | Microsoft. RecoveryServices/Locations/operationStatus/Read | Pobiera stan operacji dla danej operacji |
> | Akcja | Microsoft. RecoveryServices/Operations/Read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Akcja | Microsoft. RecoveryServices/Register/Action | Rejestruje subskrypcję dla danego dostawcy zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupconfig/odczyt | Zwraca konfigurację magazynu Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupconfig/zapis | Aktualizuje konfigurację magazynu Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupEngines/odczyt | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/usuwanie | Usuń zamiar ochrony kopii zapasowej |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/odczyt | Pobierz cel ochrony kopii zapasowej |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/zapis | Tworzenie opcji ochrony kopii zapasowej |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/operationResults/odczyt | Zwraca stan operacji |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/operationsStatus/odczyt | Zwraca stan operacji |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectableContainers/odczyt | Pobierz wszystkie kontenery z ochroną |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/usuwanie | Usuwa zarejestrowany kontener |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/zapytania/akcja | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/Items/Read | Pobierz wszystkie elementy w kontenerze |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/operationResults/Read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/operationsStatus/Read | Pobiera stan operacji wykonanej na kontenerze ochrony. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/kopia zapasowa/akcja | Wykonuje kopię zapasową dla chronionego elementu. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Delete | Usuwa chroniony element |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/operationResults/odczyt | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/operationsStatus/odczyt | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Read | Zwraca szczegóły obiektu chronionego elementu |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Zapewnij natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/odczyt | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/przywracanie/akcja | Przywracanie punktów odzyskiwania dla elementów chronionych. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Odwołaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Write | Utwórz chroniony element kopii zapasowej |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/odczyt | Zwraca wszystkie zarejestrowane kontenery |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/zapis | Tworzy zarejestrowany kontener |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/refreshContainers/akcja | Odświeża listę kontenerów |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupJobs/anulowanie/akcja | Anuluj zadanie |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupJobs/operationResults/odczyt | Zwraca wynik operacji zadania. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupJobs/operationsStatus/odczyt | Zwraca stan operacji zadania. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupJobs/odczyt | Zwraca wszystkie obiekty zadań |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupJobsExport/akcja | Eksportuj zadania |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupOperationResults/odczyt | Zwraca wynik operacji tworzenia kopii zapasowej dla magazynu Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupOperations/odczyt | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupPolicies/usuwanie | Usuwanie zasad ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupPolicies/operationResults/odczyt | Pobierz wyniki operacji zasad. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupPolicies/operacje/odczyt | Pobierz stan operacji zasad. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupPolicies/odczyt | Zwraca wszystkie zasady ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupPolicies/zapis | Tworzy zasady ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupProtectableItems/odczyt | Zwraca listę wszystkich elementów podlegających ochronie. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupProtectedItems/odczyt | Zwraca listę wszystkich elementów chronionych. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupProtectionContainers/odczyt | Zwraca wszystkie kontenery należące do subskrypcji |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupProtectionIntents/odczyt | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupSecurityPIN/akcja | Zwraca informacje zabezpieczającego numeru PIN dla magazynu Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupstorageconfig/odczyt | Zwraca konfigurację magazynu dla magazynu Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupstorageconfig/zapis | Aktualizuje konfigurację magazynu dla magazynu Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupUsageSummaries/odczyt | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupValidateOperation/akcja | Sprawdź poprawność operacji dla chronionego elementu |
> | Akcja | Microsoft. RecoveryServices/magazyny/certyfikaty/zapis | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Akcja | Microsoft. RecoveryServices/magazyny/usuwanie | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft. RecoveryServices/magazyny/extendedInformation/usuwanie | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft. RecoveryServices/magazyny/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft. RecoveryServices/magazyny/extendedInformation/zapis | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft. RecoveryServices/magazyny/monitoringAlerts/odczyt | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/monitoringAlerts/zapis | Rozwiązuje alert. |
> | Akcja | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/odczyt | Pobiera konfigurację powiadomień magazynu usługi Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/zapis | Konfiguruje powiadomienia e-mail dla magazynu usługi Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft. RecoveryServices/magazyny/registeredIdentities/usuwanie | Operacja Wyrejestruj kontener może służyć do wyrejestrowywania kontenera. |
> | Akcja | Microsoft. RecoveryServices/magazyny/registeredIdentities/operationResults/odczyt | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Akcja | Microsoft. RecoveryServices/magazyny/registeredIdentities/odczyt | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Akcja | Microsoft. RecoveryServices/magazyny/registeredIdentities/zapis | Operacji rejestrowania kontenera usługi można użyć do zarejestrowania kontenera z usługą odzyskiwania. |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationAlertSettings/odczyt | Odczytaj wszystkie ustawienia alertów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationAlertSettings/zapis | Utwórz lub zaktualizuj ustawienia alertów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationEvents/odczyt | Odczytaj wszystkie zdarzenia |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/checkConsistency/akcja | Sprawdza spójność sieci szkieletowej |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/usuwanie | Usuń wszystkie sieci szkieletowe |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/deployProcessServerImage/akcja | Wdróż obraz serwera przetwarzania |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/migratetoaad/akcja | Migrowanie sieci szkieletowej do usługi AAD |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/operationresults/odczyt | Śledź wyniki operacji asynchronicznej w sieciach szkieletowych zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/odczyt | Odczytaj wszystkie sieci szkieletowe |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/reassociateGateway/akcja | Skojarz ponownie bramę |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/usuwanie/akcja | Usuń sieć szkieletową |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/renewcertificate/akcja | Odnów certyfikat sieci szkieletowej |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationLogicalNetworks/odczyt | Odczytaj wszystkie sieci logiczne |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/odczyt | Odczytaj wszystkie sieci |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/replicationNetworkMappings/Delete | Usuń wszystkie mapowania sieci |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Odczytaj wszystkie mapowania sieci |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/replicationNetworkMappings/Write | Utwórz lub zaktualizuj dowolne mapowania sieci |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/Action | Odkryj element objęty ochroną |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/operationresults/Read | Śledź wyniki operacji asynchronicznej w kontenerach ochrony zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/odczyt | Odczytaj wszystkie kontenery ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/usuwanie/akcja | Usuń kontener ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Delete | Usuń wszystkie elementy migracji |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Migruj/akcja | Migrowanie elementu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/odczyt | Odczytaj wszystkie punkty odzyskiwania migracji |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na elementach migracji zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Read | Odczytaj wszystkie elementy migracji |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/akcja | Migracja testowa |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/akcja | Oczyszczanie migracji testów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Write | Utwórz lub zaktualizuj dowolne elementy migracji |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Odczytaj dowolne elementy podlegające ochronie |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/adddisks/Action | Dodawanie dysków |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/akcja | Zastosuj punkt odzyskiwania |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Delete | Usuń wszystkie chronione elementy |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/akcja | Zatwierdzanie trybu failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na elementach chronionych przez zasoby |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/akcja | Planowana praca w trybie failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Odczytaj wszystkie chronione elementy |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/odczyt | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/usuwanie/akcja | Usuń chroniony element |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/akcja | Usuń dyski |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/akcja | Napraw replikację |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Włącz ochronę/akcję | Włącz ponownie ochronę chronionego elementu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/akcja |  |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/akcja | Prześlij opinię |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/odczyt | Odczytaj wszystkie docelowe rozmiary obliczeń |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/akcja | Test pracy w trybie failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/akcja | Czyszczenie testu pracy w trybie failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/akcja | Tryb failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/akcja | Aktualizowanie usługi mobilności |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Write | Utwórz lub zaktualizuj wszystkie chronione elementy |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Delete | Usuń wszystkie mapowania kontenerów ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na mapowaniach kontenerów ochrony zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/usuwanie/akcja | Usuń mapowanie kontenera ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Write | Utwórz lub zaktualizuj wszelkie mapowania kontenerów ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Przełącz kontener ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/zapis | Tworzenie lub aktualizowanie wszelkich kontenerów ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/usuwanie | Usuń wszystkich dostawców Recovery Services |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/operationresults/Read | Śledź wyniki operacji asynchronicznej dla dostawców Recovery Services zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/odczyt | Odczytaj wszystkich dostawców Recovery Services |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Odśwież dostawcę |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/usuwanie/akcja | Usuń dostawcę Recovery Services |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/zapis | Utwórz lub zaktualizuj wszelkich Recovery Services dostawców |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/odczyt | Odczytaj wszystkie klasyfikacje magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Delete | Usuń wszystkie mapowania klasyfikacji magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na mapowaniach klasyfikacji magazynu zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Write | Utwórz lub zaktualizuj dowolne mapowania klasyfikacji magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/usuwanie | Usuń wszystkie vCenter |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/operationresults/Read | Śledź wyniki operacji asynchronicznej na serwerze vCenter zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/odczyt | Odczytaj dowolne vCenter |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/zapis | Utwórz lub zaktualizuj dowolne vCenter |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/zapis | Utwórz lub zaktualizuj wszystkie sieci szkieletowe |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationJobs/anulowanie/akcja | Anuluj zadanie |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationJobs/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na zadaniach zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationJobs/odczyt | Odczytaj wszystkie zadania |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationJobs/ponowne uruchomienie/akcja | Uruchom ponownie zadanie |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationJobs/wznowienie/akcja | Wznów zadanie |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationMigrationItems/odczyt | Odczytaj wszystkie elementy migracji |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationNetworkMappings/odczyt | Odczytaj wszystkie mapowania sieci |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationNetworks/odczyt | Odczytaj wszystkie sieci |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationPolicies/usuwanie | Usuń wszystkie zasady |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationPolicies/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na zasadach zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationPolicies/odczyt | Odczytaj wszystkie zasady |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationPolicies/zapis | Tworzenie lub aktualizowanie zasad |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationProtectedItems/odczyt | Odczytaj wszystkie chronione elementy |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationProtectionContainerMappings/odczyt | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationProtectionContainers/odczyt | Odczytaj wszystkie kontenery ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/usuwanie | Usuń wszystkie plany odzyskiwania |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/failoverCommit/akcja | Plan odzyskiwania zatwierdzania w trybie failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na planach odzyskiwania zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/plannedFailover/akcja | Plan odzyskiwania planowanej pracy w trybie failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/odczyt | Zapoznaj się z planami odzyskiwania |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/ponowne Włączanie/działanie | Ponowne włączanie ochrony planu odzyskiwania |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/testFailover/akcja | Plan odzyskiwania testowego trybu failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/testFailoverCleanup/akcja | Plan odzyskiwania oczyszczania testów w trybie failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/unplannedFailover/akcja | Plan odzyskiwania w trybie failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/zapis | Tworzenie lub aktualizowanie planów odzyskiwania |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryServicesProviders/odczyt | Odczytaj wszystkich dostawców Recovery Services |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationStorageClassificationMappings/odczyt | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationStorageClassifications/odczyt | Odczytaj wszystkie klasyfikacje magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationSupportedOperatingSystems/odczyt | Odczytaj wszystkie  |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationUsages/odczyt | Odczytaj wszystkie użycia replikacji magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na kondycji replikacji magazynu zasobów |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/odczyt | Odczytaj wszystkie kondycje replikacji magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/odświeżanie/akcja | Odśwież kondycję magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationVaultSettings/odczyt | Odczytaj wszystkie  |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationVaultSettings/zapis | Utwórz lub zaktualizuj dowolne  |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationvCenters/odczyt | Odczytaj dowolne vCenter |
> | Akcja | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Odczytaj wszystkie użycia magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Akcja | Microsoft. RecoveryServices/magazyny/vaultTokens/odczyt | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Akcja | Microsoft. RecoveryServices/magazyny/zapis | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Relay/checkNameAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft. Relay/checkNamespaceAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Akcja | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Akcja | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. Relay/przestrzenie nazw/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft. Relay/Namespaces/disasterRecoveryConfigs/reguł autoryzacji/ListKeys/Action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft. Relay/Namespaces/disasterRecoveryConfigs/reguł autoryzacji/Read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/breakPairing/Action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/disasterrecoveryconfigs/checkNameAvailability/Action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/Delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/tryb failover/akcja | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/Read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/Write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Action | Operacja zaktualizowania HybridConnection. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji HybridConnection |
> | Akcja | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do HybridConnection |
> | Akcja | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji HybridConnection |
> | Akcja | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/regeneratekeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Write | Utwórz reguły autoryzacji HybridConnection i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/HybridConnections/Delete | Operacja usuwania zasobu HybridConnection |
> | Akcja | Microsoft. Relay/przestrzenie nazw/HybridConnections/Read | Pobierz listę opisów zasobów HybridConnection |
> | Akcja | Microsoft. Relay/przestrzenie nazw/HybridConnections/Write | Utwórz lub zaktualizuj właściwości HybridConnection. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/operationresults/Read | Pobierz stan operacji przestrzeni nazw |
> | Akcja | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Akcja | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Write | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Akcja | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/logDefinitions/Read | Pobierz listę opisów zasobów dzienników przestrzeni nazw |
> | Akcja | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Akcja | Microsoft. Relay/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft. Relay/przestrzenie nazw/removeAcsNamepsace/Action | Usuń przestrzeń nazw ACS |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Action | Operacja zaktualizowania WcfRelay. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji WcfRelay |
> | Akcja | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do WcfRelay |
> | Akcja | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji WcfRelay |
> | Akcja | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/regeneratekeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Write | Utwórz reguły autoryzacji WcfRelay i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/Delete | Operacja usuwania zasobu WcfRelay |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/Read | Pobierz listę opisów zasobów WcfRelay |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/Write | Utwórz lub zaktualizuj właściwości WcfRelay. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Akcja | Microsoft. Relay/Operations/Read | Pobierz operacje |
> | Akcja | Microsoft. Relay/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i umożliwia tworzenie zasobów przekazywania |
> | Akcja | Microsoft. Relay/Unregister/Action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i umożliwia tworzenie zasobów przekazywania |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ResourceHealth/AvailabilityStatuses/Current/Read | Pobiera stan dostępności dla określonego zasobu |
> | Akcja | Microsoft. ResourceHealth/AvailabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Akcja | Microsoft. ResourceHealth/Events/odczyt | Pobierz zdarzenia Service Health dla danej subskrypcji |
> | Akcja | Microsoft. Resourcehealth/healthevent/Action | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft. Resourcehealth/healthevent/aktywowany/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft. Resourcehealth/healthevent/InProgress/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft. Resourcehealth/healthevent/Pending/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft. Resourcehealth/healthevent/rozwiązane/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft. Resourcehealth/healthevent/zaktualizowane/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft. ResourceHealth/impactedResources/odczyt | Pobierz zasoby, których dotyczy problem dla danej subskrypcji |
> | Akcja | Microsoft. ResourceHealth/Metadata/Read | Pobiera metadane |
> | Akcja | Microsoft. ResourceHealth/Operations/Read | Pobierz operacje dostępne dla programu Microsoft ResourceHealthe |
> | Akcja | Microsoft. ResourceHealth/Register/Action | Rejestruje subskrypcję programu Microsoft ResourceHealthe |
> | Akcja | Microsoft. ResourceHealth/Unregister/Action | Wyrejestrowuje subskrypcję programu Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. resources/calculateTemplateHash/Action | Oblicz wartość skrótu podanego szablonu. |
> | Akcja | Microsoft. resources/checkPolicyCompliance/Action | Sprawdź stan zgodności danego zasobu względem zasad zasobów. |
> | Akcja | Microsoft. resources/checkResourceName/Action | Sprawdź nazwę zasobu, aby sprawdzić poprawność. |
> | Akcja | Microsoft. resources/Deployments/Cancel/Action | Anuluje wdrożenie. |
> | Akcja | Microsoft. resources/Deployments/Delete | Usuwa wdrożenie. |
> | Akcja | Microsoft. resources/Deployments/exportTemplate/Action | Eksportuj szablon wdrożenia |
> | Akcja | Microsoft. resources/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Akcja | Microsoft. resources/Deployments/operationstatuses/Read | Pobiera lub wyświetla stan operacji wdrożenia. |
> | Akcja | Microsoft. resources/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | Akcja | Microsoft. resources/Deployments/Validate/Action | Sprawdza poprawność wdrożenia. |
> | Akcja | Microsoft. resources/Deployments/whatIf/Action | Przewiduje zmiany wdrożenia szablonu. |
> | Akcja | Microsoft. resources/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft. resources/Links/Delete | Usuwa łącze zasobu. |
> | Akcja | Microsoft. resources/Links/Read | Pobiera linki do zasobów lub wyświetla ich listę. |
> | Akcja | Microsoft. resources/Links/Write | Tworzy lub aktualizuje łącze zasobu. |
> | Akcja | Microsoft. resources/Marketplace/Purchase/Action | Zakup zasobu z portalu Marketplace. |
> | Akcja | Microsoft. resources/Providers/Read | Pobierz listę dostawców. |
> | Akcja | Microsoft. resources/Resources/Read | Pobierz listę zasobów na podstawie filtrów. |
> | Akcja | Microsoft. resources/subscriptions/Locations/Read | Pobiera listę obsługiwanych lokalizacji. |
> | Akcja | Microsoft. resources/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | Akcja | Microsoft. resources/subscriptions/Providers/Read | Pobiera dostawców zasobów lub wyświetla ich listę. |
> | Akcja | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Akcja | Microsoft. resources/subscriptions/resourceGroups/Delete | Usuwa grupę zasobów i wszystkie jej zasoby. |
> | Akcja | Microsoft. resources/subscriptions/ResourceGroups/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Akcja | Microsoft. resources/subscriptions/ResourceGroups/Deployments/operationstatuses/Read | Pobiera lub wyświetla stan operacji wdrożenia. |
> | Akcja | Microsoft. resources/subscriptions/ResourceGroups/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | Akcja | Microsoft. resources/subscriptions/ResourceGroups/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft. resources/subscriptions/resourceGroups/moveResources/Action | Przenosi zasoby z jednej grupy zasobów do innej. |
> | Akcja | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Akcja | Microsoft. resources/subscriptions/ResourceGroups/Resources/Read | Pobiera zasoby dla grupy zasobów. |
> | Akcja | Microsoft. resources/subscriptions/resourceGroups/validateMoveResources/Action | Weryfikowanie przenoszenia zasobów z jednej grupy zasobów do innej. |
> | Akcja | Microsoft. resources/subscriptions/resourceGroups/Write | Tworzy lub aktualizuje grupę zasobów. |
> | Akcja | Microsoft. resources/subscriptions/sources/Read | Pobiera zasoby subskrypcji. |
> | Akcja | Microsoft. resources/subscriptions/tagNames/Delete | Usuwa tag subskrypcji. |
> | Akcja | Microsoft. resources/subscriptions/tagNames/Read | Pobiera lub wyświetla Tagi subskrypcji. |
> | Akcja | Microsoft. resources/subscriptions/tagNames/tagValues/Delete | Usuwa wartość tagu subskrypcji. |
> | Akcja | Microsoft. resources/subscriptions/tagNames/tagValues/Read | Pobiera lub wyświetla wartości tagów subskrypcji. |
> | Akcja | Microsoft. resources/subscriptions/tagNames/tagValues/Write | Dodaje wartość tagu subskrypcji. |
> | Akcja | Microsoft. resources/subscriptions/tagNames/Write | Dodaje tag subskrypcji. |
> | Akcja | Microsoft. resources/Tags/Delete | Usuwa wszystkie znaczniki w zasobie. |
> | Akcja | Microsoft. resources/Tags/Read | Pobiera wszystkie znaczniki w zasobie. |
> | Akcja | Microsoft. resources/Tags/Write | Aktualizuje Tagi w zasobie, zastępując lub scalając istniejące Tagi nowym zestawem tagów lub usuwając istniejące Tagi. |
> | Akcja | Microsoft. resources/dzierżawcy/odczyt | Pobiera listę dzierżawców. |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Scheduler/jobcollections/Delete | Usuwa kolekcję zadań. |
> | Akcja | Microsoft. Scheduler/jobcollections/Disable/akcja | Wyłącza kolekcję zadań. |
> | Akcja | Microsoft. Scheduler/jobcollections/Włącz/akcja | Włącza zbieranie zadań. |
> | Akcja | Microsoft. Scheduler/jobcollections/Jobs/Delete | Usuwa zadanie. |
> | Akcja | Microsoft. Scheduler/jobcollections/Jobs/generateLogicAppDefinition/Action | Generuje definicję aplikacji logiki na podstawie zadania usługi Scheduler. |
> | Akcja | Microsoft. Scheduler/jobcollections/Jobs/jobhistories/Read | Pobiera historię zadania. |
> | Akcja | Microsoft. Scheduler/jobcollections/Jobs/Read | Pobiera zadanie. |
> | Akcja | Microsoft. Scheduler/jobcollections/Jobs/Run/Action | Uruchamia zadanie. |
> | Akcja | Microsoft. Scheduler/jobcollections/Jobs/Write | Tworzy lub aktualizuje zadanie. |
> | Akcja | Microsoft. Scheduler/jobcollections/odczyt | Pobierz kolekcję zadań |
> | Akcja | Microsoft. Scheduler/jobcollections/Write | Tworzy lub aktualizuje kolekcję zadań. |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Search/checkNameAvailability/Action | Sprawdza dostępność nazwy usługi. |
> | Akcja | Microsoft. Search/Operations/Read | Wyświetla wszystkie dostępne operacje dostawcy Microsoft. Search. |
> | Akcja | Microsoft. Search/Register/Action | Rejestruje subskrypcję dostawcy zasobów wyszukiwania i umożliwia tworzenie usług wyszukiwania. |
> | Akcja | Microsoft. Search/searchServices/createQueryKey/Action | Tworzy klucz zapytania. |
> | Akcja | Microsoft. Search/searchServices/Delete | Usuwa usługę wyszukiwania. |
> | Akcja | Microsoft. Search/searchServices/deleteQueryKey/Delete | Usuwa klucz zapytania. |
> | Akcja | Microsoft. Search/searchServices/listAdminKeys/Action | Odczytuje klucze administratora. |
> | Akcja | Microsoft. Search/searchServices/listQueryKeys/Action | Zwraca listę kluczy interfejsu API zapytania dla danej usługi Wyszukiwanie poznawcze platformy Azure. |
> | Akcja | Microsoft. Search/searchServices/listQueryKeys/Read | Zwraca listę kluczy interfejsu API zapytania dla danej usługi Wyszukiwanie poznawcze platformy Azure. |
> | Akcja | Microsoft. Search/searchServices/Read | Odczytuje usługę wyszukiwania. |
> | Akcja | Microsoft. Search/searchServices/regenerateAdminKey/Action | Generuje ponownie klucz administratora. |
> | Akcja | Microsoft. Search/searchServices/Start/akcja | Uruchamia usługę wyszukiwania. |
> | Akcja | Microsoft. Search/searchServices/Stop/Action | Powoduje zatrzymanie usługi wyszukiwania. |
> | Akcja | Microsoft. Search/searchServices/Write | Tworzy lub aktualizuje usługę wyszukiwania. |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Security/adaptiveNetworkHardenings/Wymuszaj/akcja | Wymusza stosowanie zasad ograniczania ruchu przez tworzenie reguł zabezpieczeń zgodnych z podaną grupą zabezpieczeń sieci |
> | Akcja | Microsoft. Security/adaptiveNetworkHardenings/Read | Pobiera adaptacyjne zalecenia dotyczące ograniczania przepustowości sieci chronionej przez platformę Azure |
> | Akcja | Microsoft. Security/advancedThreatProtectionSettings/Read | Pobiera ustawienia zaawansowanej ochrony przed zagrożeniami dla zasobu |
> | Akcja | Microsoft. Security/advancedThreatProtectionSettings/Write | Aktualizuje ustawienia zaawansowanej ochrony przed zagrożeniami dla zasobu |
> | Akcja | Microsoft. Security/Alerts/Read | Pobiera wszystkie dostępne alerty zabezpieczeń |
> | Akcja | Microsoft. Security/applicationWhitelistings/Read | Pobiera aplikację whitelistings |
> | Akcja | Microsoft. Security/applicationWhitelistings/Write | Tworzy nową aplikację listy dozwolonych lub aktualizuje istniejącą |
> | Akcja | Microsoft. Security/assessmentMetadata/Read | Uzyskaj dostępne metadane oceny zabezpieczeń w ramach subskrypcji |
> | Akcja | Microsoft. Security/assessmentMetadata/Write | Tworzenie lub aktualizowanie metadanych oceny zabezpieczeń |
> | Akcja | Microsoft. Security/Assessments/Read | Uzyskaj oceny zabezpieczeń w ramach subskrypcji |
> | Akcja | Microsoft. Security/Assessments/Write | Utwórz lub zaktualizuj oceny zabezpieczeń w ramach subskrypcji |
> | Akcja | Microsoft. Security/complianceResults/Read | Pobiera wyniki zgodności dla zasobu |
> | Akcja | Microsoft. Security/informationProtectionPolicies/Read | Pobiera zasady usługi Information Protection dla zasobu |
> | Akcja | Microsoft. Security/informationProtectionPolicies/Write | Aktualizuje zasady usługi Information Protection dla zasobu |
> | Akcja | Microsoft. Security/Locations/Alerts/Activate/Action | Aktywuj alert zabezpieczeń |
> | Akcja | Microsoft. zabezpieczenia/lokalizacje/alerty/Odrzuć/akcja | Odrzuć alert zabezpieczeń |
> | Akcja | Microsoft. Security/Locations/Alerts/Read | Pobiera wszystkie dostępne alerty zabezpieczeń |
> | Akcja | Microsoft. Security/Locations/jitNetworkAccessPolicies/Delete | Usuwa zasady dostępu just in Time do sieci |
> | Akcja | Microsoft. Security/Locations/jitNetworkAccessPolicies/initiate/Action | Inicjuje żądanie zasady dostępu just in Time do sieci |
> | Akcja | Microsoft. Security/Locations/jitNetworkAccessPolicies/Read | Pobiera zasady dostępu just in Time do sieci |
> | Akcja | Microsoft. Security/Locations/jitNetworkAccessPolicies/Write | Tworzy nowe zasady dostępu just in Time do sieci lub aktualizuje istniejące |
> | Akcja | Microsoft. Security/Locations/Read | Pobiera lokalizację danych zabezpieczeń |
> | Akcja | Microsoft. Security/Locations/Tasks/Activate/Action | Aktywuj zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft. zabezpieczenia/lokalizacje/zadania/Odrzuć/akcja | Odrzuć zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft. Security/Locations/Tasks/Read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Akcja | Microsoft. zabezpieczenia/lokalizacje/zadania/Rozwiązywanie/akcja | Rozwiązywanie zalecenia dotyczącego zabezpieczeń |
> | Akcja | Microsoft. Security/Locations/Tasks/Start/Action | Rozpocznij zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft. Security/Policies/Read | Pobiera zasady zabezpieczeń |
> | Akcja | Microsoft. Security/Policies/Write | Aktualizuje zasady zabezpieczeń |
> | Akcja | Microsoft. Security/cenniki/usuwanie | Usuwa ustawienia cen dla zakresu |
> | Akcja | Microsoft. zabezpieczenia/cenniki/odczyt | Pobiera ustawienia cen dla zakresu |
> | Akcja | Microsoft. zabezpieczenia/cenniki/zapis | Aktualizuje ustawienia cen dla zakresu |
> | Akcja | Microsoft. Security/Register/Action | Rejestruje subskrypcję dla Azure Security Center |
> | Akcja | Microsoft. Security/securityContacts/Delete | Usuwa kontakt zabezpieczeń |
> | Akcja | Microsoft. Security/securityContacts/Read | Pobiera kontakt zabezpieczeń |
> | Akcja | Microsoft. Security/securityContacts/Write | Aktualizuje kontakt zabezpieczeń |
> | Akcja | Microsoft. Security/securitySolutions/Delete | Usuwa rozwiązanie zabezpieczeń |
> | Akcja | Microsoft. Security/securitySolutions/Read | Pobiera rozwiązania zabezpieczeń |
> | Akcja | Microsoft. Security/securitySolutions/Write | Tworzy nowe rozwiązanie zabezpieczeń lub aktualizuje istniejące |
> | Akcja | Microsoft. Security/securitySolutionsReferenceData/Read | Pobiera dane referencyjne rozwiązań zabezpieczeń |
> | Akcja | Microsoft. Security/securityStatuses/Read | Pobiera stan kondycji zabezpieczeń dla zasobów platformy Azure |
> | Akcja | Microsoft. Security/securityStatusesSummaries/Read | Pobiera podsumowania stanu zabezpieczeń dla zakresu |
> | Akcja | Microsoft. Security/Settings/Read | Pobiera ustawienia dla zakresu |
> | Akcja | Microsoft. Security/Settings/Write | Aktualizuje ustawienia zakresu |
> | Akcja | Microsoft. Security/Tasks/Read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Akcja | Microsoft. Security/Unregister/Action | Wyrejestrowuje subskrypcję z Azure Security Center |
> | Akcja | Microsoft. Security/webApplicationFirewalls/Delete | Usuwa zaporę aplikacji sieci Web |
> | Akcja | Microsoft. Security/webApplicationFirewalls/Read | Pobiera zapory aplikacji sieci Web |
> | Akcja | Microsoft. Security/webApplicationFirewalls/Write | Tworzy nową zaporę aplikacji sieci Web lub aktualizuje istniejącą |
> | Akcja | Microsoft. Security/workspaceSettings/Connect/Action | Zmień ustawienia ponownego połączenia ustawień obszaru roboczego |
> | Akcja | Microsoft. Security/workspaceSettings/Delete | Usuwa ustawienia obszaru roboczego |
> | Akcja | Microsoft. Security/workspaceSettings/Read | Pobiera ustawienia obszaru roboczego |
> | Akcja | Microsoft. Security/workspaceSettings/Write | Aktualizuje ustawienia obszaru roboczego |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. SecurityGraph/diagnosticsettings/Delete | Usuwanie ustawień diagnostycznych |
> | Akcja | Microsoft. SecurityGraph/diagnosticsettings/odczyt | Odczytywanie ustawień diagnostycznych |
> | Akcja | Microsoft. SecurityGraph/diagnosticsettings/Write | Zapisywanie ustawień diagnostycznych |
> | Akcja | Microsoft. SecurityGraph/diagnosticsettingscategories/odczyt | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ServiceBus/checkNameAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft. ServiceBus/checkNamespaceAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Akcja | Microsoft. ServiceBus/Locations/deleteVirtualNetworkOrSubnets/Action | Usuwa reguły sieci wirtualnej w dostawcy zasobów ServiceBus dla określonej sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/reguł autoryzacji/ListKeys/Action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/reguł autoryzacji/Read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/breakPairing/Action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/disasterrecoveryconfigs/checkNameAvailability/Action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/Delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/tryb failover/akcja | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/Read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/Write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/eventGridFilters/Delete | Usuwa filtr Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/eventGridFilters/Read | Pobiera Filtr Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/eventGridFilters/Write | Tworzy lub aktualizuje filtr Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/eventhubs/Read | Pobierz listę opisów zasobów EventHub |
> | Akcja | Microsoft. ServiceBus/Namespaces/ipFilterRules/Delete | Usuń zasób filtru IP |
> | Akcja | Microsoft. ServiceBus/Namespaces/ipFilterRules/Read | Pobierz zasób filtru IP |
> | Akcja | Microsoft. ServiceBus/Namespaces/ipFilterRules/Write | Utwórz zasób filtru IP |
> | Akcja dataaction | Microsoft. ServiceBus/przestrzenie nazw/komunikaty/odbieranie/akcja | Odbieranie komunikatów |
> | Akcja dataaction | Microsoft. ServiceBus/przestrzenie nazw/komunikaty/wysyłanie/akcja | Wysyłanie komunikatów |
> | Akcja | Microsoft. ServiceBus/Namespaces/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. ServiceBus/Namespaces/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/migracja/akcja | Operacja migracji przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Delete | Usuwa konfigurację migracji. |
> | Akcja | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Read | Pobiera konfigurację migracji, która wskazuje stan migracji i oczekujących operacji replikacji |
> | Akcja | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Przywróć/akcja | Przywraca migrację przestrzeni nazw Standard do warstwy Premium |
> | Akcja | Microsoft. ServiceBus/Namespaces/migrationConfigurations/upgrade/Action | Przypisuje serwer DNS skojarzony z przestrzenią nazw w warstwie Premium, która kończy migrację i kończy synchronizowanie zasobów ze standardowego do Premium przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Write | Tworzy lub aktualizuje konfigurację migracji. Spowoduje to rozpoczęcie synchronizowania zasobów ze standardowego do przestrzeni nazw Premium |
> | Akcja | Microsoft. ServiceBus/Namespaces/networkruleset/Delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/Namespaces/networkruleset/Read | Pobiera zasób NetworkRuleSet |
> | Akcja | Microsoft. ServiceBus/Namespaces/networkruleset/Write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/Namespaces/networkrulesets/Delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/Namespaces/networkrulesets/Read | Pobiera zasób NetworkRuleSet |
> | Akcja | Microsoft. ServiceBus/Namespaces/networkrulesets/Write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/Namespaces/operationresults/Read | Pobierz stan operacji przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Write | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/Providers/Microsoft. Insights/logDefinitions/Read | Pobierz listę opisów zasobów dzienników przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/Providers/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/kolejki/reguł autoryzacji/akcja | Operacja aktualizacji kolejki. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Akcja | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji kolejki |
> | Akcja | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia z kolejką |
> | Akcja | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji kolejki |
> | Akcja | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. ServiceBus/Namespaces/Queues/reguł autoryzacji/Write | Utwórz reguły autoryzacji kolejki i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/kolejki/usuwanie | Operacja usuwania zasobu kolejki |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/kolejki/odczyt | Pobierz listę opisów zasobów kolejki |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/kolejki/zapis | Utwórz lub zaktualizuj właściwości kolejki. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/removeAcsNamepsace/Action | Usuń przestrzeń nazw ACS |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/akcja | Operacja zaktualizowania tematu. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/usuwanie | Operacja usuwania tematu reguły autoryzacji |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/ListKeys/akcja | Pobierz parametry połączenia z tematem |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/odczyt |  Pobierz listę reguł autoryzacji tematów |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/regenerateKeys/akcja | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/zapis | Tworzenie reguł autoryzacji tematów i aktualizowanie ich właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/Usuwanie | Operacja usuwania zasobu tematu |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/odczyt | Pobierz listę opisów zasobów tematu |
> | Akcja | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/usuwanie | Operacja usuwania zasobu TopicSubscription |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/subskrypcje/odczyt | Pobierz listę opisów zasobów TopicSubscription |
> | Akcja | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/usuwanie | Operacja usuwania zasobu reguły |
> | Akcja | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/odczyt | Pobierz listę opisów zasobów reguł |
> | Akcja | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/zapis | Utwórz lub zaktualizuj właściwości reguły. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/subskrypcje/zapis | Utwórz lub zaktualizuj właściwości TopicSubscription. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/zapis | Utwórz lub zaktualizuj właściwości tematu. |
> | Akcja | Microsoft. ServiceBus/Namespaces/virtualNetworkRules/Delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/Namespaces/virtualNetworkRules/Read | Pobiera zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/Namespaces/virtualNetworkRules/Write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Akcja | Microsoft. ServiceBus/Operations/Read | Pobierz operacje |
> | Akcja | Microsoft. ServiceBus/Register/Action | Rejestruje subskrypcję dostawcy zasobów ServiceBus i umożliwia tworzenie zasobów ServiceBus |
> | Akcja | Microsoft. ServiceBus/SKU/odczyt | Pobierz listę opisów zasobów jednostki SKU |
> | Akcja | Microsoft. ServiceBus/SKU/regiony/odczyt | Pobierz listę opisów zasobów SkuRegions |
> | Akcja | Microsoft. ServiceBus/Unregister/Action | Rejestruje subskrypcję dostawcy zasobów ServiceBus i umożliwia tworzenie zasobów ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft. servicefabric

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. servicefabric/klastry/aplikacje/usuwanie | Usuń dowolną aplikację |
> | Akcja | Microsoft. servicefabric/klastry/aplikacje/odczyt | Odczytaj dowolną aplikację |
> | Akcja | Microsoft. servicefabric/klastry/aplikacje/usługi/usuwanie | Usuwanie dowolnej usługi |
> | Akcja | Microsoft. servicefabric/klastry/aplikacje/usługi/partycje/odczyt | Odczytaj dowolną partycję |
> | Akcja | Microsoft. servicefabric/klastry/aplikacje/usługi/partycje/repliki/odczyt | Odczytaj dowolną replikę |
> | Akcja | Microsoft. servicefabric/klastry/aplikacje/usługi/odczyt | Odczytaj dowolną usługę |
> | Akcja | Microsoft. servicefabric/klastry/aplikacje/usługi/Stany/odczyt | Odczytaj wszystkie stany usług |
> | Akcja | Microsoft. servicefabric/klastry/aplikacje/usługi/zapis | Utwórz lub zaktualizuj dowolną usługę |
> | Akcja | Microsoft. servicefabric/klastry/aplikacje/zapis | Utwórz lub zaktualizuj dowolną aplikację |
> | Akcja | Microsoft. servicefabric/klastry/applicationTypes/Delete | Usuń dowolny typ aplikacji |
> | Akcja | Microsoft. servicefabric/klastry/applicationTypes/Read | Odczytaj dowolny typ aplikacji |
> | Akcja | Microsoft. servicefabric/klastry/applicationTypes/wersje/Usuń | Usuń wszystkie wersje typu aplikacji |
> | Akcja | Microsoft. servicefabric/klastry/applicationTypes/wersje/odczyt | Odczytaj dowolną wersję typu aplikacji |
> | Akcja | Microsoft. servicefabric/klastry/applicationTypes/wersje/zapis | Utwórz lub zaktualizuj dowolną wersję typu aplikacji |
> | Akcja | Microsoft. servicefabric/klastry/applicationTypes/Write | Utwórz lub zaktualizuj dowolny typ aplikacji |
> | Akcja | Microsoft. servicefabric/klastry/usuwanie | Usuń dowolny klaster |
> | Akcja | Microsoft. servicefabric/klastry/węzły/odczyt | Odczytaj dowolny węzeł |
> | Akcja | Microsoft. servicefabric/klastry/odczyt | Odczytaj dowolny klaster |
> | Akcja | Microsoft. servicefabric/klastry/Stany/odczyt | Odczytaj wszystkie Stany klastra |
> | Akcja | Microsoft. servicefabric/klastry/zapis | Utwórz lub zaktualizuj dowolny klaster |
> | Akcja | Microsoft. servicefabric/Locations/clusterVersions/Read | Odczytaj dowolną wersję klastra |
> | Akcja | Microsoft. servicefabric/Locations/Environments/clusterVersions/Read | Odczytaj dowolną wersję klastra dla określonego środowiska |
> | Akcja | Microsoft. servicefabric/Locations/operationresults/Read | Odczytaj wszystkie wyniki operacji |
> | Akcja | Microsoft. servicefabric/Locations/Operations/Read | Odczytaj wszystkie operacje według lokalizacji |
> | Akcja | Microsoft. servicefabric/Operations/Read | Odczytaj wszystkie dostępne operacje |
> | Akcja | Microsoft. servicefabric/Register/Action | Zarejestruj dowolną akcję |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. SignalRService/Locations/checknameavailability/Action | Sprawdza, czy nazwa jest dostępna do użycia z nową usługą sygnalizującego |
> | Akcja | Microsoft. SignalRService/Locations/operationresults/sygnalizujący/odczytany | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Akcja | Microsoft. SignalRService/Locations/operationStatuses/operationId/Read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Akcja | Microsoft. SignalRService/lokalizacje/użycia/odczyt | Pobierz użycie przydziału dla usługi Azure Signal Service |
> | Akcja | Microsoft. SignalRService/operationresults/odczyt | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Akcja | Microsoft. SignalRService/Operations/Read | Wyświetl listę operacji usługi Azure Signal Service. |
> | Akcja | Microsoft. SignalRService/operationstatus/odczyt | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Akcja | Microsoft. SignalRService/Register/Action | Rejestruje dostawcę zasobów "Microsoft. SignalRService" z subskrypcją |
> | Akcja | Microsoft. SignalRService/sygnalizujący/Usuń | Usuwanie całej usługi sygnalizującej |
> | Akcja | Microsoft. SignalRService/Signaler/eventGridFilters/Delete | Usuń filtr usługi Event Grid ze sygnalizującego. |
> | Akcja | Microsoft. SignalRService/Signaler/eventGridFilters/odczyt | Pobierz właściwości określonego filtru usługi Event Grid lub Wyświetl listę wszystkich filtrów usługi Event Grid dla określonego sygnalizującego. |
> | Akcja | Microsoft. SignalRService/Signaler/eventGridFilters/Write | Utwórz lub zaktualizuj filtr usługi Event Grid dla sygnalizującego o określonych parametrach. |
> | Akcja | Microsoft. SignalRService/Signaler/ListKeys/Action | Wyświetlanie wartości kluczy dostępu sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Akcja | Microsoft. SignalRService/sygnalizujący/odczytający | Wyświetlanie ustawień i konfiguracji sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Akcja | Microsoft. SignalRService/Signaler/regeneratekey/Action | Zmiana wartości kluczy dostępu sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Akcja | Microsoft. SignalRService/sygnalizujący/Uruchom ponownie/akcję | Aby ponownie uruchomić usługę sygnałów platformy Azure w portalu zarządzania lub za pomocą interfejsu API. Zostanie określony przestój. |
> | Akcja | Microsoft. SignalRService/sygnalizujący/Write | Modyfikowanie ustawień i konfiguracji sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Akcja | Microsoft. SignalRService/Unregister/Action | Wyrejestrowuje dostawcę zasobów "Microsoft. SignalRService" z subskrypcją |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Solutions/applicationDefinitions/applicationArtifacts/odczyt | Wyświetla listę artefaktów aplikacji definicji aplikacji. |
> | Akcja | Microsoft. Solutions/applicationDefinitions/Delete | Usuwa definicję aplikacji. |
> | Akcja | Microsoft. Solutions/applicationDefinitions/Read | Pobiera listę definicji aplikacji. |
> | Akcja | Microsoft. Solutions/applicationDefinitions/Write | Dodaj lub zmodyfikuj definicję aplikacji. |
> | Akcja | Microsoft. Solutions/Applications/applicationArtifacts/Read | Wyświetla listę artefaktów aplikacji. |
> | Akcja | Microsoft. Solutions/Applications/Delete | Usuwa aplikację. |
> | Akcja | Microsoft. Solutions/Applications/Read | Pobiera listę aplikacji. |
> | Akcja | Microsoft. Solutions/Applications/refreshPermissions/Action | Odświeża uprawnienia aplikacji. |
> | Akcja | Microsoft. Solutions/Applications/updateAccess/Action | Aktualizuje dostęp do aplikacji. |
> | Akcja | Microsoft. Solutions/Applications/Write | Tworzy aplikację. |
> | Akcja | Microsoft. Solutions/jitRequests/Delete | Usuń JitRequest |
> | Akcja | Microsoft. Solutions/jitRequests/Read | Pobiera listę JitRequests |
> | Akcja | Microsoft. Solutions/jitRequests/Write | Tworzy element JitRequest |
> | Akcja | Microsoft. Solutions/Locations/operationStatuses/Read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft. Solutions/Register/Action | Zarejestruj się w rozwiązaniach. |
> | Akcja | Microsoft. Solutions/Unregister/Action | Wyrejestrowuje z rozwiązań. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. SQL/checkNameAvailability/akcja | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Akcja | Microsoft. SQL/instancePools/Delete | Usuwa pulę wystąpień |
> | Akcja | Microsoft. SQL/instancePools/odczyt | Pobiera pulę wystąpień |
> | Akcja | Microsoft. SQL/instancePools/Usages/Read | Pobiera informacje o użyciu puli wystąpień |
> | Akcja | Microsoft. SQL/instancePools/Write | Tworzy lub aktualizuje pulę wystąpień |
> | Akcja | Microsoft. SQL/Locations/auditingSettingsAzureAsyncOperation/Read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Akcja | Microsoft. SQL/Locations/auditingSettingsOperationResults/Read | Pobierz wynik operacji ustawiania zasad inspekcji obiektu BLOB serwera |
> | Akcja | Microsoft. SQL/lokalizacje/możliwości/odczyt | Pobiera możliwości dla tej subskrypcji w danej lokalizacji |
> | Akcja | Microsoft. SQL/Locations/databaseAzureAsyncOperation/Read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft. SQL/Locations/databaseOperationResults/Read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft. SQL/Locations/deletedServerAsyncOperation/Read | Pobiera operacje w toku na usuniętym serwerze |
> | Akcja | Microsoft. SQL/Locations/deletedServerOperationResults/Read | Pobiera operacje w toku na usuniętym serwerze |
> | Akcja | Microsoft. SQL/Locations/deletedServers/Read | Zwróć listę usuniętych serwerów lub pobiera właściwości dla określonego usuniętego serwera. |
> | Akcja | Microsoft. SQL/Locations/deletedServers/Recover/Action | Odzyskiwanie usuniętego serwera |
> | Akcja | Microsoft. SQL/Locations/elasticPoolAzureAsyncOperation/Read | Pobiera asynchroniczną operację platformy Azure dla operacji asynchronicznej puli elastycznej |
> | Akcja | Microsoft. SQL/Locations/elasticPoolOperationResults/Read | Pobiera wynik operacji puli elastycznej. |
> | Akcja | Microsoft. SQL/Locations/encryptionProtectorAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania danych transparentu |
> | Akcja | Microsoft. SQL/Locations/encryptionProtectorOperationResults/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania danych transparentu |
> | Akcja | Microsoft. SQL/Locations/extendedAuditingSettingsAzureAsyncOperation/Read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Akcja | Microsoft. SQL/Locations/extendedAuditingSettingsOperationResults/Read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Akcja | Microsoft. SQL/Locations/firewallRulesAzureAsyncOperation/Read | Pobiera stan operacji reguły zapory. |
> | Akcja | Microsoft. SQL/Locations/firewallRulesOperationResults/Read | Pobiera stan operacji reguły zapory. |
> | Akcja | Microsoft. SQL/Locations/instanceFailoverGroups/Delete | Usuwa istniejącą grupę trybu failover wystąpienia. |
> | Akcja | Microsoft. SQL/Locations/instanceFailoverGroups/tryb failover/akcja | Wykonuje planowaną pracę w trybie failover w istniejącej grupie trybu failover wystąpienia. |
> | Akcja | Microsoft. SQL/Locations/instanceFailoverGroups/forceFailoverAllowDataLoss/Action | Wykonuje wymuszone przejście w tryb failover w istniejącej grupie trybu failover wystąpienia. |
> | Akcja | Microsoft. SQL/Locations/instanceFailoverGroups/Read | Zwraca listę grup trybu failover wystąpienia lub pobiera właściwości dla określonego wystąpienia grupy trybu failover. |
> | Akcja | Microsoft. SQL/Locations/instanceFailoverGroups/Write | Tworzy grupę trybu failover wystąpienia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej grupy trybu failover. |
> | Akcja | Microsoft. SQL/Locations/instancePoolAzureAsyncOperation/Read | Pobiera stan operacji puli wystąpień |
> | Akcja | Microsoft. SQL/Locations/instancePoolOperationResults/Read | Pobiera wynik operacji puli wystąpień |
> | Akcja | Microsoft. SQL/Locations/interfaceEndpointProfileAzureAsyncOperation/Read | Zwraca szczegółowe informacje o określonym interfejsie operacji asynchronicznej platformy Azure |
> | Akcja | Microsoft. SQL/Locations/interfaceEndpointProfileOperationResults/Read | Zwraca szczegóły podanej operacji profilu punktu końcowego interfejsu |
> | Akcja | Microsoft. SQL/Locations/jobAgentAzureAsyncOperation/Read | Pobiera stan operacji agenta zadań. |
> | Akcja | Microsoft. SQL/Locations/jobAgentOperationResults/Read | Pobiera wynik operacji agenta zadań. |
> | Akcja | Microsoft. SQL/Locations/longTermRetentionBackups/Read | Wyświetla długoterminowe kopie zapasowe przechowywania dla każdej bazy danych na każdym serwerze w lokalizacji |
> | Akcja | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read | Wyświetla kopie zapasowe przechowywania długoterminowego dla każdej bazy danych na serwerze |
> | Akcja | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete | Usuwa długoterminową kopię zapasową przechowywania |
> | Akcja | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read | Wyświetla kopie zapasowe przechowywania długoterminowego dla bazy danych |
> | Akcja | Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/Action | Kończy operację przywracania zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania wystąpienia zarządzanego szyfrowania danych |
> | Akcja | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorOperationResults/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania wystąpienia zarządzanego szyfrowania danych |
> | Akcja | Microsoft. SQL/Locations/managedInstanceKeyAzureAsyncOperation/Read | Pobiera operacje w toku na przezroczystych kluczach wystąpienia zarządzanego szyfrowania danych |
> | Akcja | Microsoft. SQL/Locations/managedInstanceKeyOperationResults/Read | Pobiera operacje w toku na przezroczystych kluczach wystąpienia zarządzanego szyfrowania danych |
> | Akcja | Microsoft. SQL/Locations/managedTransparentDataEncryptionAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/Locations/managedTransparentDataEncryptionOperationResults/Read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/Locations/privateEndpointConnectionOperationResults/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/lokalizacje/odczyt | Pobiera dostępne lokalizacje dla danej subskrypcji |
> | Akcja | Microsoft. SQL/Locations/serverKeyAzureAsyncOperation/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Akcja | Microsoft. SQL/Locations/serverKeyOperationResults/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Akcja | Microsoft. SQL/Locations/syncAgentOperationResults/Read | Pobierz wynik operacji zasobu agenta synchronizacji |
> | Akcja | Microsoft. SQL/Locations/syncDatabaseIds/Read | Pobieranie identyfikatorów bazy danych synchronizacji dla określonego regionu i subskrypcji |
> | Akcja | Microsoft. SQL/Locations/syncGroupOperationResults/Read | Pobierz wynik operacji zasobu grupy synchronizacji |
> | Akcja | Microsoft. SQL/Locations/syncMemberOperationResults/Read | Pobierz wynik operacji synchronizacji zasobu elementu członkowskiego |
> | Akcja | Microsoft. SQL/lokalizacje/użycia/odczyt | Pobiera kolekcję metryk użycia dla tej subskrypcji w lokalizacji |
> | Akcja | Microsoft. SQL/Locations/virtualNetworkRulesAzureAsyncOperation/Read | Zwraca szczegóły określonych reguł sieci wirtualnej Azure asynchronicznej operacji  |
> | Akcja | Microsoft. SQL/Locations/virtualNetworkRulesOperationResults/Read | Zwraca szczegóły określonej operacji dotyczącej reguł sieci wirtualnej  |
> | Akcja | Microsoft. SQL/managedInstances/Administratorzy/usuwanie | Usuwa istniejącego administratora wystąpienia zarządzanego. |
> | Akcja | Microsoft. SQL/managedInstances/Administratorzy/odczyt | Pobiera listę administratorów wystąpienia zarządzanego. |
> | Akcja | Microsoft. SQL/managedInstances/Administratorzy/zapis | Tworzy lub aktualizuje administratora wystąpienia zarządzanego o określonych parametrach. |
> | Akcja | Microsoft. SQL/managedInstances/Databases/backupShortTermRetentionPolicies/Read | Pobiera krótkoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/backupShortTermRetentionPolicies/Write | Aktualizuje krótkoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/kolumny/odczyt | Zwracanie listy kolumn dla zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/Write | Etykiety czułości aktualizacji partii |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/usuwanie | Usuwa istniejącą zarządzaną bazę danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla baz danych wystąpienia zarządzanego |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/Odczyt | Pobiera istniejącą zarządzaną bazę danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/Write | Etykiety czułości zalecanych aktualizacji wsadowych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/restoreDetails/Read | Zwraca szczegóły przywracania zarządzanej bazy danych, gdy przywracanie jest w toku. |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/odczyt | Pobierz schemat zarządzanej bazy danych. |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/odczyt | Pobieranie kolumny zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/usuwanie | Usuń etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Disable/Action | Wyłącz zalecenia dotyczące czułości w danej kolumnie |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Włącz/akcja | Włącz zalecenia dotyczące czułości w danej kolumnie |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/odczyt | Pobierz etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/zapis | Utwórz lub zaktualizuj etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/odczyt | Pobieranie tabeli zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/securityAlertPolicies/Read | Pobieranie listy zasad wykrywania zagrożeń zarządzanych baz danych skonfigurowanych dla danego serwera |
> | Akcja | Microsoft. SQL/managedInstances/Databases/securityAlertPolicies/Write | Zmień zasady wykrywania zagrożeń bazy danych dla danej zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/securityEvents/Read | Pobiera zdarzenia zabezpieczeń zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/Read | Pobierz szczegóły Transparent Data Encryption bazy danych w danej zarządzanej bazie danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/Write | Zmień Transparent Data Encryption bazy danych dla danej zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Delete | Usuń ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Read | Pobieranie zasad oceny luk w zabezpieczeniach na givendatabase |
> | Akcja | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Rules/baselines/Delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Rules/baselines/Read | Pobierz linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Rules/baselines/Write | Zmień linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/skany/eksport/akcja | Przekonwertuj istniejący wynik skanowania na format możliwy do odczytania przez człowieka. Jeśli już nie istnieje, nic się nie dzieje |
> | Akcja | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/skany/initiateScan/akcja | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Akcja | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/skany/odczyt | Zwróć listę rekordów skanowania oceny luk w zabezpieczeniach bazy danych lub Pobierz rekord skanowania pod kątem określonego identyfikatora skanowania. |
> | Akcja | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/zapis | Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych. |
> | Akcja | Microsoft. SQL/managedInstances/Delete | Usuwa istniejące wystąpienie zarządzane. |
> | Akcja | Microsoft. SQL/managedInstances/encryptionProtector/odczyt | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/akcja | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft. SQL/managedInstances/encryptionProtector/Write | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft. SQL/managedInstances/klucze/usuwanie | Usuwa istniejący klucz wystąpienia zarządzanego Azure SQL. |
> | Akcja | Microsoft. SQL/managedInstances/klucze/odczyt | Zwróć listę kluczy wystąpienia zarządzanego lub pobiera właściwości dla określonego klucza wystąpienia zarządzanego. |
> | Akcja | Microsoft. SQL/managedInstances/klucze/zapis | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza wystąpienia zarządzanego. |
> | Akcja | Microsoft. SQL/managedInstances/metricDefinitions/odczyt | Pobierz definicje metryk wystąpienia zarządzanego |
> | Akcja | Microsoft. SQL/managedInstances/Metrics/Read | Pobierz metryki wystąpienia zarządzanego |
> | Akcja | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla wystąpień zarządzanych |
> | Akcja | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla wystąpień zarządzanych |
> | Akcja | Microsoft. SQL/managedInstances/odczyt | Zwróć listę wystąpień zarządzanych lub pobiera właściwości dla określonego wystąpienia zarządzanego. |
> | Akcja | Microsoft. SQL/managedInstances/recoverableDatabases/odczyt | Zwraca listę możliwych do odzyskania zarządzanych baz danych |
> | Akcja | Microsoft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/Read | Pobiera krótkoterminowe zasady przechowywania dla porzuconej zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/Write | Aktualizuje krótkoterminowe zasady przechowywania dla porzuconej zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/restorableDroppedDatabases/odczyt | Zwraca listę dostępnych usuniętych zarządzanych baz danych. |
> | Akcja | Microsoft. SQL/managedInstances/securityAlertPolicies/odczyt | Pobieranie listy zasad wykrywania zagrożeń serwera zarządzanego skonfigurowanych dla danego serwera |
> | Akcja | Microsoft. SQL/managedInstances/securityAlertPolicies/Write | Zmień zasady wykrywania zagrożeń serwera zarządzanego dla danego serwera zarządzanego |
> | Akcja | Microsoft. SQL/managedInstances/tdeCertificates/akcja | Utwórz/zaktualizuj certyfikat TDE |
> | Akcja | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Delete | Usuń ocenę luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Akcja | Microsoft. SQL/managedInstances/vulnerabilityAssessments/odczyt | Pobieranie zasad oceny luk w zabezpieczeniach w danym wystąpieniu zarządzanym |
> | Akcja | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Write | Zmiana oceny luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Akcja | Microsoft. SQL/managedInstances/Write | Tworzy wystąpienie zarządzane o określonych parametrach lub aktualizuje właściwości lub Tagi dla określonego wystąpienia zarządzanego. |
> | Akcja | Microsoft. SQL/Operations/Read | Pobiera dostępne operacje REST |
> | Akcja | Microsoft. SQL/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/Register/akcja | Rejestruje subskrypcję dostawcy zasobów programu Microsoft SQL Database i umożliwia tworzenie baz danych Microsoft SQL. |
> | Akcja | Microsoft. SQL/serwery/Administratorzy/usuwanie | Usuwa określony obiekt administratora Azure Active Directory |
> | Akcja | Microsoft. SQL/serwery/Administratorzy/odczyt | Pobiera określony obiekt administratora Azure Active Directory |
> | Akcja | Microsoft. SQL/serwery/Administratorzy/zapis | Dodaje lub aktualizuje określony obiekt administratora Azure Active Directory |
> | Akcja | Microsoft. SQL/serwery/doradcy/odczyt | Zwraca listę doradców dostępnych dla serwera |
> | Akcja | Microsoft. SQL/serwery/doradcy/recommendedActions/odczyt | Zwraca listę zalecanych akcji określonego klasyfikatora dla serwera |
> | Akcja | Microsoft. SQL/serwery/doradcy/recommendedActions/zapis | Zastosuj zalecaną akcję na serwerze |
> | Akcja | Microsoft. SQL/serwery/doradcy/zapis | Aktualizuje stan autouruchomienia klasyfikatora na poziomie serwera. |
> | Akcja | Microsoft. SQL/serwery/auditingPolicies/odczyt | Pobierz szczegóły domyślnych zasad inspekcji tabeli serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft. SQL/serwery/auditingPolicies/Write | Zmień domyślną inspekcję tabeli serwera dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/auditingSettings/operationResults/odczyt | Pobierz wynik operacji ustawiania zasad inspekcji obiektu BLOB serwera |
> | Akcja | Microsoft. SQL/serwery/auditingSettings/odczyt | Pobierz szczegóły zasad inspekcji obiektów BLOB serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft. SQL/serwery/auditingSettings/Write | Zmień inspekcję obiektu BLOB serwera dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/automaticTuning/odczyt | Zwraca ustawienia automatycznego dostrajania dla serwera. |
> | Akcja | Microsoft. SQL/serwery/automaticTuning/Write | Aktualizuje ustawienia dostrajania automatycznego dla serwera i zwraca zaktualizowane ustawienia |
> | Akcja | Microsoft. SQL/serwery/backupLongTermRetentionVaults/Delete | Usuwa istniejący magazyn archiwum kopii zapasowych. |
> | Akcja | Microsoft. SQL/serwery/backupLongTermRetentionVaults/odczyt | Ta operacja służy do pobierania magazynu długoterminowego przechowywania kopii zapasowych. Zwraca informacje o magazynie zarejestrowanym na tym serwerze |
> | Akcja | Microsoft. SQL/serwery/backupLongTermRetentionVaults/Write | Ta operacja służy do rejestrowania magazynu długoterminowego przechowywania kopii zapasowej na serwerze programu. |
> | Akcja | Microsoft. SQL/serwery/communicationLinks/Delete | Usuwa istniejące łącze komunikacji z serwerem. |
> | Akcja | Microsoft. SQL/serwery/communicationLinks/odczyt | Zwróć listę linków komunikacyjnych określonego serwera. |
> | Akcja | Microsoft. SQL/serwery/communicationLinks/Write | Utwórz lub zaktualizuj łącze do komunikacji z serwerem. |
> | Akcja | Microsoft. SQL/serwery/connectionPolicies/odczyt | Zwróć listę zasad połączenia serwera określonego serwera. |
> | Akcja | Microsoft. SQL/serwery/connectionPolicies/Write | Utwórz lub zaktualizuj zasady połączenia z serwerem. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/doradcy/odczyt | Zwraca listę doradców dostępnych dla bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/doradcy/recommendedActions/odczyt | Zwraca listę zalecanych akcji określonego klasyfikatora dla bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/doradcy/recommendedActions/Write | Zastosuj zalecaną akcję w bazie danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/doradcy/zapis | Aktualizowanie stanu autouzupełniania usługi Advisor na poziomie bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/auditingPolicies/odczyt | Pobierz szczegóły zasad inspekcji tabeli skonfigurowanych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/auditingPolicies/Write | Zmień zasady inspekcji tabeli dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/auditingSettings/odczyt | Pobierz szczegóły zasad inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/auditingSettings/Write | Zmień zasady inspekcji obiektów BLOB dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/auditRecords/odczyt | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/automaticTuning/odczyt | Zwraca ustawienia automatycznego dostrajania dla bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/automaticTuning/Write | Aktualizuje ustawienia dostrajania automatycznego dla bazy danych i zwraca zaktualizowane ustawienia |
> | Akcja | Microsoft. SQL/serwery/bazy danych/azureAsyncOperation/odczyt | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/backupLongTermRetentionPolicies/odczyt | Zwróć listę zasad archiwizacji kopii zapasowych określonej bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/backupLongTermRetentionPolicies/Write | Utwórz lub zaktualizuj Zasady archiwizacji kopii zapasowej bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/backupShortTermRetentionPolicies/odczyt | Pobiera krótkoterminowe zasady przechowywania dla bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/backupShortTermRetentionPolicies/Write | Aktualizuje krótkoterminowe zasady przechowywania dla bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/kolumny/odczyt | Zwracanie listy kolumn dla bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/connectionPolicies/odczyt | Pobierz szczegóły zasad połączenia skonfigurowanych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/connectionPolicies/Write | Zmień zasady połączenia dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/currentSensitivityLabels/odczyt | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/currentSensitivityLabels/Write | Etykiety czułości aktualizacji partii |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/odczyt | Zwróć listę zasad maskowania danych bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/reguły/usuwanie | Usuń regułę zasad maskowania danych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/reguły/odczyt | Pobierz szczegóły reguły zasad maskowania danych skonfigurowanej w danej bazie danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/reguły/zapis | Zmień regułę zasad maskowania danych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/Write | Zmień zasady maskowania danych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dataWarehouseQueries/dataWarehouseQuerySteps/odczyt | Zwraca informacje o kroku zapytania rozproszonego dotyczące zapytania hurtowni danych dla wybranego identyfikatora kroku |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dataWarehouseQueries/odczyt | Zwraca informacje o zapytaniu dystrybucji magazynu danych dla wybranego identyfikatora zapytania |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dataWarehouseUserActivities/odczyt | Pobiera działania użytkownika wystąpienia SQL Data Warehouse, w tym uruchomione i wstrzymane zapytania |
> | Akcja | Microsoft. SQL/serwery/bazy danych/usuwanie | Usuwa istniejącą bazę danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/eksport/akcja | Eksportuj Azure SQL Database |
> | Akcja | Microsoft. SQL/serwery/bazy danych/extendedAuditingSettings/odczyt | Pobierz szczegóły zasad rozszerzonej inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/extendedAuditingSettings/Write | Zmiana zasad rozszerzonej inspekcji obiektów BLOB dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/rozszerzenia/odczyt | Pobiera kolekcję rozszerzeń dla bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/rozszerzenia/zapis | Zmień rozszerzenie dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/tryb failover/akcja | Klient przełączenia w tryb failover bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/geoBackupPolicies/odczyt | Pobierz zasady geograficznej kopii zapasowej dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/geoBackupPolicies/Write | Tworzenie lub aktualizowanie zasad tworzenia kopii zapasowej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/importExportOperationResults/odczyt | Pobiera operacje importu/eksportu w toku |
> | Akcja | Microsoft. SQL/serwery/bazy danych/maintenanceWindowOptions/odczyt | Pobiera listę dostępnych okien obsługi dla wybranej bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/maintenanceWindows/odczyt | Pobiera ustawienia okien obsługi dla wybranej bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/maintenanceWindows/Write | Ustawia ustawienia okien obsługi dla wybranej bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/metricDefinitions/odczyt | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/metryki/odczyt | Metryki zwracane dla baz danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/Przenieś/akcja | Zmień nazwę istniejącej bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/operationResults/odczyt | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/operacje/Anuluj/akcja | Anuluje Azure SQL Database oczekującej operacji asynchronicznej, która nie została jeszcze zakończona. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/operacje/odczyt | Zwróć listę operacji wykonanych na bazie danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/wstrzymywanie/akcja | Wstrzymywanie bazy danych usługi Azure SQL DataWarehouse |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla baz danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/queryStore/queryTexts/odczyt | Zwraca kolekcję tekstów zapytania, które odpowiadają określonym parametrom. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/queryStore/odczyt | Zwraca bieżące wartości ustawień magazynu zapytań dla bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/queryStore/Write | Aktualizuje ustawienie magazynu zapytań dla bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/Odczyt | Zwróć listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/recommendedSensitivityLabels/odczyt | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/recommendedSensitivityLabels/Write | Etykiety czułości zalecanych aktualizacji wsadowych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/replicationLinks/Usuń | Zakończenie wymuszonej relacji replikacji z potencjalną utratą danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/replicationLinks/tryb failover/akcja | Przełączenie w tryb failover po zsynchronizowaniu wszystkich zmian z poziomu podstawowego, co sprawia, że ta baza danych jest podzielona na relationship\u0027s replikacji i udostępnianie zdalnego elementu głównego do pomocniczego |
> | Akcja | Microsoft. SQL/serwery/bazy danych/replicationLinks/forceFailoverAllowDataLoss/akcja | Natychmiastowe przejście w tryb failover z potencjalną utratą danych, dzięki czemu ta baza danych jest relationship\u0027s replikacją podstawową i udostępniająca ją jako pomocniczy. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/replicationLinks/odczyt | Zwróć listę linków replikacji lub pobiera właściwości dla określonych linków replikacji. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/replicationLinks/Odłącz/akcja | Wymuszone zakończenie relacji replikacji lub po zsynchronizowaniu z partnerem |
> | Akcja | Microsoft. SQL/serwery/bazy danych/replicationLinks/updateReplicationMode/akcja | Aktualizowanie trybu replikacji dla łącza do trybu synchronicznego lub asynchronicznego |
> | Akcja | Microsoft. SQL/serwery/bazy danych/restorePoints/akcja | Tworzy nowy punkt przywracania |
> | Akcja | Microsoft. SQL/serwery/bazy danych/restorePoints/Usuń | Usuwa punkt przywracania bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/restorePoints/odczyt | Zwraca punkty przywracania bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/wznowienie/akcja | Wznów bazę danych usługi Azure SQL DataWarehouse |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/odczyt | Pobierz schemat bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/odczyt | Pobierz kolumnę bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Delete | Usuń etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Disable/Action | Wyłącz zalecenia dotyczące czułości w danej kolumnie |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Włącz/akcja | Włącz zalecenia dotyczące czułości w danej kolumnie |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/odczyt | Pobierz etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Write | Utwórz lub zaktualizuj etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/odczyt | Pobierz tabelę bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/recommendedIndexes/odczyt | Pobierz listę zaleceń dotyczących indeksów w bazie danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/recommendedIndexes/Write | Zastosuj zalecenie dotyczące indeksu |
> | Akcja | Microsoft. SQL/serwery/bazy danych/securityAlertPolicies/odczyt | Pobieranie listy zasad wykrywania zagrożeń bazy danych skonfigurowanych dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/bazy danych/securityAlertPolicies/Write | Zmień zasady wykrywania zagrożeń bazy danych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/securityMetrics/odczyt | Pobiera kolekcję metryk zabezpieczeń bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/sensitivityLabels/odczyt | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/serviceTierAdvisors/odczyt | Zwróć sugestię dotyczącą skalowania bazy danych w górę lub w dół na podstawie statystyk wykonywania zapytań w celu zwiększenia wydajności lub obniżenia kosztów |
> | Akcja | Microsoft. SQL/serwery/bazy danych/jednostki SKU/odczyt | Pobiera kolekcję jednostek SKU dostępnych dla bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/cancelSync/akcja | Anuluj synchronizację grupy synchronizacji |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/Usuń | Usuwa istniejącą grupę synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/hubSchemas/odczyt | Zwróć listę schematów bazy danych Centrum synchronizacji |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/Logs/odczyt | Zwróć listę dzienników grup synchronizacji |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/odczyt | Zwróć listę grup synchronizacji lub pobiera właściwości dla określonej grupy synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/refreshHubSchema/akcja | Odśwież schemat bazy danych Centrum synchronizacji |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/refreshHubSchemaOperationResults/odczyt | Pobierz wynik operacji odświeżania schematu narzędzia Sync Hub |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/Delete | Usuwa istniejący element członkowski synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/odczyt | Zwróć listę elementów członkowskich synchronizacji lub pobiera właściwości dla określonego elementu członkowskiego synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/refreshSchema/akcja | Odśwież schemat elementu członkowskiego synchronizacji |
> | Akcja | Microsoft. SQL/Servers/Databases/syncGroups/syncMembers/refreshSchemaOperationResults/Read | Pobierz wynik operacji odświeżania schematu elementu członkowskiego synchronizacji |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/schematy/odczyt | Zwróć listę schematów bazy danych elementu członkowskiego synchronizacji |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/zapis | Tworzy element członkowski synchronizacji z określonymi parametrami lub aktualizuje właściwości określonego elementu członkowskiego synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/triggerSync/akcja | Wyzwalanie synchronizacji grupy synchronizacji |
> | Akcja | Microsoft. SQL/serwery/bazy danych/syncGroups/Write | Tworzy grupę synchronizacji z określonymi parametrami lub aktualizuje właściwości określonej grupy synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/topQueries/queryText/akcja | Zwraca tekst Transact-SQL dla wybranego identyfikatora zapytania |
> | Akcja | Microsoft. SQL/serwery/bazy danych/topQueries/odczyt | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Akcja | Microsoft. SQL/serwery/bazy danych/topQueries/Statystyka/odczyt | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Akcja | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/operationResults/odczyt | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/odczyt | Pobierz stan i szczegóły funkcji zabezpieczenia szyfrowania danych przezroczystych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/Write | Zmień stan przezroczystego szyfrowania danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/upgradeDataWarehouse/akcja | Uaktualnianie bazy danych usługi Azure SQL DataWarehouse |
> | Akcja | Microsoft. SQL/serwery/bazy danych/użycia/odczyt | Pobiera informacje o użyciach Azure SQL Database |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/Usuń | Usuń ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/odczyt | Pobieranie zasad oceny luk w zabezpieczeniach na givendatabase |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/reguły/linie bazowe/usuwanie | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/reguły/linie bazowe/odczyt | Pobierz linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/reguły/linie bazowe/zapis | Zmień linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/skanowania/eksport/akcja | Przekonwertuj istniejący wynik skanowania na format możliwy do odczytania przez człowieka. Jeśli już nie istnieje, nic się nie dzieje |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/skanowania/initiateScan/akcja | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/skany/odczyt | Zwróć listę rekordów skanowania oceny luk w zabezpieczeniach bazy danych lub Pobierz rekord skanowania pod kątem określonego identyfikatora skanowania. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/Write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentScans/akcja | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentScans/operationResults/odczyt | Pobieranie wyniku operacji skanowania oceny luk w zabezpieczeniach bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentSettings/odczyt | Pobierz szczegóły oceny luk w zabezpieczeniach skonfigurowanej w danej bazie danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentSettings/Write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/zapis | Tworzy bazę danych z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej bazy danych. |
> | Akcja | Microsoft. SQL/serwery/usuwanie | Usuwa istniejący serwer. |
> | Akcja | Microsoft. SQL/serwery/disasterRecoveryConfiguration/Delete | Usuwa istniejące konfiguracje odzyskiwania po awarii dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/disasterRecoveryConfiguration/tryb failover/akcja | Przełączenie w tryb failover DisasterRecoveryConfiguration |
> | Akcja | Microsoft. SQL/serwery/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/akcja | Wymuś tryb failover DisasterRecoveryConfiguration |
> | Akcja | Microsoft. SQL/serwery/disasterRecoveryConfiguration/odczyt | Pobiera kolekcję konfiguracji odzyskiwania po awarii, która obejmuje ten serwer |
> | Akcja | Microsoft. SQL/serwery/disasterRecoveryConfiguration/Write | Zmień konfigurację odzyskiwania po awarii serwera |
> | Akcja | Microsoft. SQL/serwery/elasticPoolEstimates/odczyt | Zwraca listę oszacowań elastycznej puli już utworzonych dla tego serwera |
> | Akcja | Microsoft. SQL/serwery/elasticPoolEstimates/Write | Tworzy nowe szacowanie puli elastycznej dla listy dostarczonych baz danych |
> | Akcja | Microsoft. SQL/serwery/elasticPools/doradcy/odczyt | Zwraca listę doradców dostępnych dla puli elastycznej |
> | Akcja | Microsoft. SQL/Servers/elasticPools/Advisors/recommendedActions/Read | Zwraca listę zalecanych akcji określonego klasyfikatora dla puli elastycznej |
> | Akcja | Microsoft. SQL/Servers/elasticPools/Advisors/recommendedActions/Write | Zastosuj zalecaną akcję w puli elastycznej |
> | Akcja | Microsoft. SQL/serwery/elasticPools/doradcy/zapis | Aktualizacja stanu autouruchomienia klasyfikatora na poziomie puli elastycznej. |
> | Akcja | Microsoft. SQL/serwery/elasticPools/bazy danych/Odczyt | Pobiera listę baz danych dla puli elastycznej |
> | Akcja | Microsoft. SQL/serwery/elasticPools/Delete | Usuń istniejącą pulę elastyczną |
> | Akcja | Microsoft. SQL/serwery/elasticPools/elasticPoolActivity/odczyt | Pobieranie działań i szczegółów w danej elastycznej puli baz danych |
> | Akcja | Microsoft. SQL/serwery/elasticPools/elasticPoolDatabaseActivity/odczyt | Pobierz działania i szczegóły dotyczące danej bazy danych będącej częścią elastycznej puli baz danych |
> | Akcja | Microsoft. SQL/serwery/elasticPools/tryb failover/akcja | Klient zainicjował pracę awaryjną puli elastycznej. |
> | Akcja | Microsoft. SQL/serwery/elasticPools/metricDefinitions/odczyt | Zwracaj typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Akcja | Microsoft. SQL/Server/elasticPools/Metrics/Read | Metryki zwracane dla elastycznych pul baz danych |
> | Akcja | Microsoft. SQL/Servers/elasticPools/Operations/Cancel/Action | Anuluje oczekującą operację asynchroniczną w puli elastycznej SQL platformy Azure, która nie została jeszcze zakończona. |
> | Akcja | Microsoft. SQL/serwery/elasticPools/Operations/Read | Zwróć listę operacji wykonywanych w puli elastycznej |
> | Akcja | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Akcja | Microsoft. SQL/serwery/elasticPools/odczyt | Pobierz szczegóły puli elastycznej na danym serwerze |
> | Akcja | Microsoft. SQL/Server/elasticPools/SKU/odczyt | Pobiera kolekcję jednostek SKU dostępnych dla puli elastycznej |
> | Akcja | Microsoft. SQL/serwery/elasticPools/Write | Utwórz nowe lub zmień właściwości istniejącej puli elastycznej |
> | Akcja | Microsoft. SQL/serwery/encryptionProtector/odczyt | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft. SQL/serwery/encryptionProtector/ponownie Zweryfikuj/akcja | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft. SQL/serwery/encryptionProtector/Write | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft. SQL/serwery/extendedAuditingSettings/odczyt | Pobierz szczegóły zasad inspekcji rozszerzonego serwera obiektów BLOB skonfigurowanych na danym serwerze |
> | Akcja | Microsoft. SQL/serwery/extendedAuditingSettings/Write | Zmień rozszerzoną inspekcję obiektów BLOB serwera dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/failoverGroups/Delete | Usuwa istniejącą grupę trybu failover. |
> | Akcja | Microsoft. SQL/serwery/failoverGroups/tryb failover/akcja | Wykonuje planowaną pracę w trybie failover w istniejącej grupie trybu failover. |
> | Akcja | Microsoft. SQL/serwery/failoverGroups/forceFailoverAllowDataLoss/akcja | Wykonuje wymuszone przejście w tryb failover w istniejącej grupie trybu failover. |
> | Akcja | Microsoft. SQL/serwery/failoverGroups/odczyt | Zwraca listę grup trybu failover lub pobiera właściwości dla określonej grupy trybu failover. |
> | Akcja | Microsoft. SQL/serwery/failoverGroups/Write | Tworzy grupę trybu failover z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej grupy trybu failover. |
> | Akcja | Microsoft. SQL/serwery/firewallRules/Delete | Usuwa istniejącą regułę zapory serwera. |
> | Akcja | Microsoft. SQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory serwera lub pobiera właściwości dla określonej reguły zapory serwera. |
> | Akcja | Microsoft. SQL/serwery/firewallRules/Write | Tworzy regułę zapory serwera z określonymi parametrami, aktualizuje właściwości określonej reguły lub zastępuje wszystkie istniejące reguły przy użyciu nowych reguł zapory serwera. |
> | Akcja | Microsoft. SQL/serwery/import/akcja | Utwórz nową bazę danych na serwerze i Wdróż schemat oraz dane z pakietu DacPac |
> | Akcja | Microsoft. SQL/serwery/importExportOperationResults/odczyt | Pobiera operacje importu/eksportu w toku |
> | Akcja | Microsoft. SQL/serwery/interfaceEndpointProfiles/Delete | Usuwa określony profil punktu końcowego interfejsu |
> | Akcja | Microsoft. SQL/serwery/interfaceEndpointProfiles/odczyt | Zwraca właściwości dla określonego profilu punktu końcowego interfejsu |
> | Akcja | Microsoft. SQL/serwery/interfaceEndpointProfiles/Write | Tworzy profil punktu końcowego interfejsu z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego punktu końcowego interfejsu |
> | Akcja | Microsoft. SQL/serwery/jobAgents/Delete | Usuwa agenta zadań usługi Azure SQL DB |
> | Akcja | Microsoft. SQL/serwery/jobAgents/odczyt | Pobiera agenta zadań usługi Azure SQL DB |
> | Akcja | Microsoft. SQL/serwery/jobAgents/Write | Tworzy lub aktualizuje agenta zadań usługi Azure SQL DB |
> | Akcja | Microsoft. SQL/serwery/klucze/usuwanie | Usuwa istniejący klucz serwera. |
> | Akcja | Microsoft. SQL/serwery/klucze/odczyt | Zwróć listę kluczy serwera lub pobiera właściwości dla określonego klucza serwera. |
> | Akcja | Microsoft. SQL/serwery/klucze/zapis | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza serwera. |
> | Akcja | Microsoft. SQL/serwery/operationResults/odczyt | Pobiera operacje serwera w toku |
> | Akcja | Microsoft. SQL/serwery/privateEndpointConnectionProxies/Delete | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/serwery/privateEndpointConnectionProxies/odczyt | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft. SQL/serwery/privateEndpointConnectionProxies/Validate/akcja | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Akcja | Microsoft. SQL/serwery/privateEndpointConnectionProxies/Write | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft. SQL/serwery/privateEndpointConnections/Delete | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/serwery/privateEndpointConnections/odczyt | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft. SQL/serwery/privateEndpointConnections/Write | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/serwery/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/serwery/privateLinkResources/odczyt | Pobierz zasoby linku prywatnego dla odpowiedniego programu SQL Server |
> | Akcja | Microsoft. SQL/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla serwerów |
> | Akcja | Microsoft. SQL/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft. SQL/serwery/recommendedElasticPools/bazy danych/Odczyt | Pobierz metryki dla zalecanych elastycznych pul baz danych dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/recommendedElasticPools/odczyt | Pobieranie zalecenia dla elastycznych pul baz danych w celu ograniczenia kosztów lub zwiększenia wydajności w oparciu o historyczne wykorzystanie zasobów |
> | Akcja | Microsoft. SQL/serwery/recoverableDatabases/odczyt | Ta operacja służy do odzyskiwania po awarii usługi Live Database w celu przywrócenia bazy danych do ostatniego znanego dobrego punktu kopii zapasowej. Zwraca informacje o ostatniej dobrej kopii zapasowej, ale doesn\u0027t w rzeczywistości przywraca bazę danych. |
> | Akcja | Microsoft. SQL/serwery/replicationLinks/odczyt | Zwróć listę linków replikacji lub pobiera właściwości dla określonych linków replikacji. |
> | Akcja | Microsoft. SQL/serwery/restorableDroppedDatabases/odczyt | Pobierz listę baz danych, które zostały porzucone na danym serwerze, które są nadal w ramach zasad przechowywania. |
> | Akcja | Microsoft. SQL/serwery/securityAlertPolicies/operationResults/odczyt | Pobierz wyniki operacji zapisu zasad wykrywania zagrożeń serwera |
> | Akcja | Microsoft. SQL/serwery/securityAlertPolicies/odczyt | Pobieranie listy zasad wykrywania zagrożeń serwera skonfigurowanych dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/securityAlertPolicies/Write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/servicedążyć/odczyt | Pobierz listę celów poziomu usług (nazywanych również warstwami wydajności) dostępnych na danym serwerze |
> | Akcja | Microsoft. SQL/serwery/syncAgents/Delete | Usuwa istniejącego agenta synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/syncAgents/do generowania kluczy/akcja | Generuj klucz rejestracji agenta synchronizacji |
> | Akcja | Microsoft. SQL/serwery/syncAgents/linkedDatabases/odczyt | Zwróć listę połączonych baz danych agenta synchronizacji |
> | Akcja | Microsoft. SQL/serwery/syncAgents/odczyt | Zwróć listę agentów synchronizacji lub pobiera właściwości dla określonego agenta synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/syncAgents/Write | Tworzy agenta synchronizacji z określonymi parametrami lub aktualizuje właściwości określonego agenta synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/tdeCertificates/akcja | Utwórz/zaktualizuj certyfikat TDE |
> | Akcja | Microsoft. SQL/serwery/użycia/odczyt | Limit przydziału jednostek DTU serwera i bieżące użycie jednostek DTU przez wszystkie bazy danych na serwerze |
> | Akcja | Microsoft. SQL/serwery/virtualNetworkRules/Delete | Usuwa istniejącą regułę Virtual Network |
> | Akcja | Microsoft. SQL/serwery/virtualNetworkRules/odczyt | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft. SQL/serwery/virtualNetworkRules/Write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft. SQL/serwery/vulnerabilityAssessments/Delete | Usuń ocenę luk w zabezpieczeniach dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/vulnerabilityAssessments/odczyt | Pobierz zasady oceny luk w zabezpieczeniach na danym serwerze |
> | Akcja | Microsoft. SQL/serwery/vulnerabilityAssessments/Write | Zmiana oceny luk w zabezpieczeniach dla danego serwera |
> | Akcja | Microsoft. SQL/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |
> | Akcja | Microsoft. SQL/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy zasobów SQL Database firmy Microsoft i umożliwia tworzenie baz danych Microsoft SQL. |
> | Akcja | Microsoft. SQL/virtualClusters/Delete | Usuwa istniejący klaster wirtualny. |
> | Akcja | Microsoft. SQL/virtualClusters/odczyt | Zwróć listę klastrów wirtualnych lub pobiera właściwości dla określonego klastra wirtualnego. |
> | Akcja | Microsoft. SQL/virtualClusters/Write | Aktualizuje Tagi klastra wirtualnego. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Storage/checknameavailability/Read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft. Storage/Locations/checknameavailability/Read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft. Storage/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadamia Microsoft. Storage, że sieć wirtualna lub podsieć jest usuwana |
> | Akcja | Microsoft. Storage/lokalizacje/użycia/odczyt | Zwraca limit i bieżącą liczbę użycia dla zasobów w określonej subskrypcji |
> | Akcja | Microsoft. Storage/Operations/Read | Sonduje stan operacji asynchronicznej. |
> | Akcja | Microsoft. Storage/Register/Action | Rejestruje subskrypcję dostawcy zasobów magazynu i umożliwia tworzenie kont magazynu. |
> | Akcja | Microsoft. Storage/SKU/odczyt | Wyświetla listę jednostek SKU obsługiwanych przez firmę Microsoft. Storage. |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Dodawanie/działanie | Zwraca wynik dodania zawartości obiektu BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie | Zwraca wynik usunięcia obiektu BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/deleteAutomaticSnapshot/akcja | Zwraca wynik usunięcia automatycznej migawki |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/Blobs/Filter/Action | Zwraca listę obiektów BLOB w ramach konta ze zgodnym filtrem tagów |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/runAsSuperUser/akcja | Zwraca wynik polecenia obiektu BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Tagi/odczyt | Zwraca wynik odczytywania tagów obiektów BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Tagi/zapis | Zwraca wynik zapisywania tagów obiektów BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/zapis | Zwraca wynik zapisania obiektu BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/clearLegalHold/akcja | Wyczyść blokadę prawną kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/usuwanie | Zwraca wynik usunięcia kontenera |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/immutabilityPolicies/Delete | Usuwanie zasad niezmienności kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/rozszerzając/Action | Rozwiń zasady niezmienności kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/Lock/Action | Blokuj zasady niezmienności kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/immutabilityPolicies/Read | Pobierz zasady niezmienności kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/immutabilityPolicies/Write | Umieść zasady niezmienności kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/dzierżawa/akcja | Zwraca wynik dzierżawy kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca kontener |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca listę kontenerów |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/setLegalHold/akcja | Ustaw blokadę prawną kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik poprawki kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik umieszczenia kontenera obiektów BLOB. |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla usługi BLOB Service |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/odczyt |  |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/odczyt | Zwraca statystykę lub właściwości usługi BLOB Service |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/Write | Zwraca wynik właściwości Put usługi BLOB Service |
> | Akcja | Microsoft. Storage/storageAccounts/Delete | Usuwa istniejące konto magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/encryptionScopes/odczyt |  |
> | Akcja | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Akcja | Microsoft. Storage/storageAccounts/tryb failover/akcja | Klient może kontrolować pracę w trybie failover w przypadku problemów z dostępnością |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/Files/actassuperuser/Action | Uzyskaj uprawnienia administratora pliku |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/usuwanie | Zwraca wynik usunięcia pliku/folderu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/Files/modifypermissions/Action | Zwraca wynik modyfikacji uprawnienia do pliku/folderu. |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/odczyt | Zwraca plik/folder lub listę plików/folderów |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/zapis | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/odczyt |  |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/odczyt | Pobierz właściwości usługi plików |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/udziały/usuwanie |  |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/udziały/odczyt |  |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/udziały/odczyt |  |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/udziały/zapis |  |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/Write |  |
> | Akcja | Microsoft. Storage/storageAccounts/listAccountSas/akcja | Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/ListKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/listServiceSas/akcja | Zwraca token sygnatury dostępu współdzielonego usługi dla określonego konta magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/managementPolicies/Delete | Usuwanie zasad zarządzania kontami magazynu |
> | Akcja | Microsoft. Storage/storageAccounts/managementPolicies/odczyt | Pobierz Zasady konta zarządzania magazynem |
> | Akcja | Microsoft. Storage/storageAccounts/managementPolicies/Write | Umieść zasady zarządzania kontami magazynu |
> | Akcja | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Delete |  |
> | Akcja | Microsoft. Storage/storageAccounts/objectReplicationPolicies/odczyt |  |
> | Akcja | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Akcja | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Delete | Usuwanie prywatnych serwerów proxy połączeń punktu końcowego |
> | Akcja | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/odczyt | Pobierz serwer proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Write | Umieść proxy połączeń prywatnych punktów końcowych |
> | Akcja | Microsoft. Storage/storageAccounts/privateEndpointConnections/Delete | Usuń połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. Storage/storageAccounts/privateEndpointConnections/odczyt | Pobierz połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. Storage/storageAccounts/privateEndpointConnections/Write | Umieść połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/akcja | Zatwierdzanie połączeń prywatnych punktów końcowych |
> | Akcja | Microsoft. Storage/storageAccounts/privateLinkResources/odczyt | Pobierz StorageAccount groupids |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/Queues/Delete | Zwraca wynik usunięcia kolejki. |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Add/Action | Zwraca wynik dodania komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Delete | Zwraca wynik usunięcia komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Process/Action | Zwraca wynik przetwarzania komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Read | Zwraca komunikat |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Write | Zwraca wynik zapisania komunikatu |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/Queues/Read | Zwraca kolejkę lub listę kolejek. |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/Queues/Write | Zwraca wynik zapisania kolejki |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/odczyt | Pobierz usługa kolejki właściwości |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/odczyt | Zwraca właściwości usługi kolejki lub dane statystyczne. |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/Write | Zwraca wynik ustawienia właściwości usługi kolejki |
> | Akcja | Microsoft. Storage/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/regeneratekey/akcja | Generuje ponownie klucze dostępu dla określonego konta magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/restoreBlobRanges/akcja | Przywróć zakresy obiektów BLOB do stanu określonego czasu |
> | Akcja | Microsoft. Storage/storageAccounts/revokeUserDelegationKeys/akcja | Odwołuje wszystkie klucze delegowania użytkowników dla określonego konta magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/Services/diagnosticSettings/Write | Utwórz/zaktualizuj ustawienia diagnostyczne konta magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/tableServices/odczyt | Pobierz Table service właściwości |
> | Akcja | Microsoft. Storage/storageAccounts/Write | Tworzy konto magazynu z określonymi parametrami lub aktualizuje właściwości lub Tagi albo dodaje niestandardową domenę dla określonego konta magazynu. |
> | Akcja | Microsoft. Storage/Usages/Read | Zwraca limit i bieżącą liczbę użycia dla zasobów w określonej subskrypcji |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. storagesync/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa usługi synchronizacji magazynu jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft. storagesync/lokalizacje/przepływy pracy/operacje/odczyt | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft. storagesync/Operations/Read | Pobiera listę obsługiwanych operacji |
> | Akcja | Microsoft. storagesync/Register/Action | Rejestruje subskrypcję dostawcy synchronizacji magazynu |
> | Akcja | Microsoft. storagesync/storageSyncServices/Delete | Usuń wszystkie usługi synchronizacji magazynu |
> | Akcja | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla usług synchronizacji magazynu |
> | Akcja | Microsoft. storagesync/storageSyncServices/odczyt | Odczytaj wszystkie usługi synchronizacji magazynu |
> | Akcja | Microsoft. storagesync/storageSyncServices/registeredServers/Delete | Usuń wszystkie zarejestrowane serwery |
> | Akcja | Microsoft. storagesync/storageSyncServices/registeredServers/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla zarejestrowanego serwera |
> | Akcja | Microsoft. storagesync/storageSyncServices/registeredServers/Read | Odczytaj wszystkie zarejestrowane serwery |
> | Akcja | Microsoft. storagesync/storageSyncServices/registeredServers/Write | Utwórz lub zaktualizuj dowolny zarejestrowany serwer |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/Delete | Usuń wszystkie punkty końcowe chmury |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/Read | Pobiera stan asynchronicznej operacji tworzenia kopii zapasowej/przywracania |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/Action | Wywołaj tę akcję po utworzeniu kopii zapasowej |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/PostRestore/Action | Wywołaj tę akcję po przywróceniu |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/Action | Wywołaj tę akcję przed wykonaniem kopii zapasowej |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/PreRestore/Action | Wywołaj tę akcję przed przywróceniem |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/Read | Odczytaj wszystkie punkty końcowe w chmurze |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/Action | Przywróć puls |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/Write | Utwórz lub zaktualizuj wszystkie punkty końcowe w chmurze |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/Delete | Usuń wszystkie grupy synchronizacji |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla grup synchronizacji |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/Read | Odczytaj wszystkie grupy synchronizacji |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Delete | Usuń wszystkie punkty końcowe serwera |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla punktów końcowych serwera |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Read | Odczytaj wszystkie punkty końcowe serwera |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/Action | Wywołaj tę akcję, aby odwołać pliki do serwera |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Write | Utwórz lub zaktualizuj wszystkie punkty końcowe serwera |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/Write | Utwórz lub zaktualizuj wszystkie grupy synchronizacji |
> | Akcja | Microsoft. storagesync/storageSyncServices/Workflows/operationresults/Read | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft. storagesync/storageSyncServices/przepływy pracy/operacje odczytu | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft. storagesync/storageSyncServices/przepływy pracy/odczyt | Odczytaj przepływy pracy |
> | Akcja | Microsoft. storagesync/storageSyncServices/Write | Utwórz lub zaktualizuj wszystkie usługi synchronizacji magazynu |
> | Akcja | Microsoft. storagesync/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy synchronizacji magazynu |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. StorSimple/menedżerowie/accessControlRecords/Usuń | Usuwa rekordy Access Control |
> | Akcja | Microsoft. StorSimple/menedżerowie/accessControlRecords/operationResults/Read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/accessControlRecords/odczyt | Wyświetla lub pobiera Access Control rekordy |
> | Akcja | Microsoft. StorSimple/kierownicy/accessControlRecords/Write | Tworzenie lub aktualizowanie rekordów Access Control |
> | Akcja | Microsoft. StorSimple/menedżerowie/alerty/odczyt | Wyświetla lub pobiera alerty |
> | Akcja | Microsoft. StorSimple/menedżerowie/kopie zapasowe/odczyt | Wyświetla lub Pobiera zestaw kopii zapasowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/bandwidthSettings/Usuń | Usuwa istniejące ustawienia przepustowości (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/bandwidthSettings/operationResults/Read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/bandwidthSettings/odczyt | Wyświetl listę ustawień przepustowości (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/kierownicy/bandwidthSettings/Write | Tworzy nowe lub aktualizuje ustawienia przepustowości (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/certyfikaty/zapis | Utwórz lub zaktualizuj certyfikaty |
> | Akcja | Microsoft. StorSimple/menedżerowie/certyfikaty/zapis | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Akcja | Microsoft. StorSimple/kierownicy/clearAlerts/akcja | Wyczyść wszystkie alerty skojarzone z menedżerem urządzeń. |
> | Akcja | Microsoft. StorSimple/kierownicy/cloudApplianceConfigurations/odczyt | Wyświetl listę obsługiwanych konfiguracji urządzenia w chmurze |
> | Akcja | Microsoft. StorSimple/kierownicy/configureDevice/akcja | Konfiguruje urządzenie |
> | Akcja | Microsoft. StorSimple/menedżerowie/usuwanie | Usuwa menedżerów urządzeń |
> | Akcja | Microsoft. StorSimple/menedżerowie/usuwanie | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/alertSettings/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/alertSettings/odczyt | Wyświetla lub pobiera ustawienia alertu |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/alertSettings/zapis | Utwórz lub zaktualizuj ustawienia alertu |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/authorizeForServiceEncryptionKeyRollover/akcja | Autoryzuj do przerzucania kluczy szyfrowania usługi |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/kopia zapasowa/akcja | Wykonaj ręczną kopię zapasową, aby utworzyć kopię zapasową wszystkich woluminów chronionych przez zasady. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/Usuń | Usuwa istniejące zasady tworzenia kopii zapasowych (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/odczyt | Utwórz listę zasad tworzenia kopii zapasowych (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/harmonogramy/usuwanie | Usuwa istniejące harmonogramy |
> | Akcja | Microsoft. StorSimple/kierownicy/Devices/backupPolicies/Schedules/operationResults/Read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/harmonogramy/odczyt | Wyświetlanie listy harmonogramów |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/harmonogramy/zapis | Tworzy harmonogramy nowych lub aktualizacji |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/backupPolicies/zapis | Tworzy nowe lub aktualizuje zasady tworzenia kopii zapasowych (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/usuwanie | Usuwa zestaw kopii zapasowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/elementy/klon/akcja | Klonowanie udziału lub woluminu przy użyciu elementu kopii zapasowej. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/elementy/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/odczyt | Wyświetla lub Pobiera zestaw kopii zapasowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/przywracanie/akcja | Przywróć wszystkie woluminy z zestawu kopii zapasowych. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupScheduleGroups/Usuń | Usuwa grupy harmonogramów kopii zapasowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupScheduleGroups/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupScheduleGroups/odczyt | Wyświetla lub pobiera grupy harmonogramów kopii zapasowych |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/backupScheduleGroups/zapis | Tworzenie lub aktualizowanie grup harmonogramów kopii zapasowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/chapSettings/Usuń | Usuwa ustawienia protokołu CHAP |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/chapSettings/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/chapSettings/odczyt | Wyświetla lub pobiera ustawienia protokołu CHAP |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/chapSettings/zapis | Utwórz lub zaktualizuj ustawienia protokołu CHAP |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/dezaktywowanie/akcja | Dezaktywuje urządzenie. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/usuwanie | Usuwa urządzenia |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/dyski/odczyt | Wyświetla lub pobiera dyski |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/pobieranie/akcja | Pobierz aktualizacje dla urządzenia. |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/tryb failover/akcja | Przełączenie urządzenia w tryb failover. |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/tryb failover/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/failoverTargets/odczyt | Wyświetla lub Pobiera elementy docelowe trybu failover urządzeń |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/kopia zapasowa/akcja | Utwórz kopię zapasową serwera plików. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/Usuń | Usuwa serwery plików |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/fileservers/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/odczyt | Wyświetla lub pobiera serwery plików |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/usuwanie | Usuwa udziały |
> | Akcja | Microsoft. StorSimple/kierownicy/Devices/fileservers/udziały/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/zapis | Utwórz lub zaktualizuj udziały |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/fileservers/zapis | Utwórz lub zaktualizuj serwery plików |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/hardwareComponentGroups/changeControllerPowerState/akcja | Zmień stan elektrowni sprzętowych grup składników |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/hardwareComponentGroups/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/hardwareComponentGroups/odczyt | Wyświetl listę grup składników sprzętowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/instalacja/akcja | Zainstaluj aktualizacje na urządzeniu. |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/installUpdates/akcja | Instaluje aktualizacje na urządzeniach (tylko seria 8000). |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/kopia zapasowa/akcja | Utwórz kopię zapasową serwera iSCSI. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/Usuń | Usuwa serwery iSCSI |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/dyski/usuwanie | Usuwa dyski |
> | Akcja | Microsoft. StorSimple/kierownicy/Devices/iscsiservers/disks/Metrics/Read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft. StorSimple/kierownicy/Devices/iscsiservers/disks/metricsDefinitions/Read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft. StorSimple/kierownicy/Devices/iscsiservers/disks/operationResults/Read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/iscsiservers/dyski/odczyt | Wyświetla lub pobiera dyski |
> | Akcja | Microsoft. StorSimple/kierownicy/Devices/iscsiservers/disks/Write | Utwórz lub zaktualizuj dyski |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/iscsiservers/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/odczyt | Wyświetla lub pobiera serwery iSCSI |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/iscsiservers/zapis | Utwórz lub zaktualizuj serwery iSCSI |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/zadania/Anuluj/akcja | Anuluj uruchomione zadanie |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/zadania/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/zadania/odczyt | Wyświetla lub pobiera zadania |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/listFailoverSets/akcja | Wyświetl listę zestawów trybu failover dla istniejącego urządzenia (tylko seria 8000). |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/listFailoverTargets/akcja | Utwórz listę elementów docelowych trybu failover dla urządzeń (tylko seria 8000). |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/confirmMigration/akcja | Potwierdza pomyślną migrację i jej zatwierdzenie. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/confirmMigrationStatus/odczyt | Wyświetlenie listy Potwierdź stan migracji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/fetchConfirmMigrationStatus/akcja | Pobierz potwierdzenie stanu migracji. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/fetchMigrationEstimate/akcja | Pobierz stan zadania szacowania migracji. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/fetchMigrationStatus/akcja | Pobierz stan migracji. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/import/akcja | Importuj konfiguracje źródeł do migracji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/migrationEstimate/odczyt | Wystaw oszacowanie migracji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/migrationStatus/odczyt | Wyświetl stan migracji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/startMigration/akcja | Rozpocznij migrację przy użyciu konfiguracji źródła |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/startMigrationEstimate/akcja | Rozpocznij zadanie, aby oszacować czas trwania procesu migracji. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/networkSettings/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/networkSettings/odczyt | Wyświetla lub pobiera ustawienia sieci |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/networkSettings/zapis | Tworzy nowe lub aktualizuje ustawienia sieci |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/publicEncryptionKey/akcja | Wyświetl publiczny klucz szyfrowania Menedżera urządzeń |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/publishSupportPackage/akcja | Opublikuj pakiet pomocy technicznej dla istniejącego urządzenia. Pakiet pomocy technicznej StorSimple to łatwy w użyciu mechanizm, który gromadzi wszystkie odpowiednie dzienniki, aby ułatwić pomoc techniczna firmy Microsoft Rozwiązywanie problemów z urządzeniami StorSimple. |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/odczyt | Wyświetla lub pobiera urządzenia |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/scanForUpdates/akcja | Skanuj w poszukiwaniu aktualizacji na urządzeniu. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/odczyt | Wyświetl listę ustawień zabezpieczeń |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/syncRemoteManagementCertificate/akcja | Zsynchronizuj certyfikat zarządzania zdalnego dla urządzenia. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/aktualizacja/akcja | Zaktualizuj ustawienia zabezpieczeń. |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/securitySettings/zapis | Tworzy nowe lub aktualizuje ustawienia zabezpieczeń |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/sendTestAlertEmail/akcja | Wyślij wiadomość e-mail z alertem testowym do skonfigurowanych adresatów wiadomości e-mail. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/timeSettings/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/timeSettings/odczyt | Wyświetla lub pobiera ustawienia czasu |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/timeSettings/zapis | Tworzy nowe lub zaktualizowane ustawienia czasu |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/aktualizacje/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/updateSummary/odczyt | Wyświetla lub pobiera podsumowanie aktualizacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/Usuń | Usuwa istniejące kontenery woluminów (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/volumeContainers/metryki/odczyt | Wyświetl metryki |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/metricsDefinitions/odczyt | Wyświetl listę definicji metryk |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/odczyt | Utwórz listę kontenerów woluminów (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/usuwanie | Usuwa istniejące woluminy |
> | Akcja | Microsoft. StorSimple/kierownicy/Devices/volumeContainers/Volumes/Metrics/Read | Wyświetl metryki |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/metricsDefinitions/odczyt | Wyświetl listę definicji metryk |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/odczyt | Wyświetl listę woluminów |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/zapis | Tworzy nowe lub aktualizuje woluminy |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/volumeContainers/zapis | Tworzy nowe lub zaktualizowane kontenery woluminów (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/woluminy/odczyt | Wyświetl listę woluminów |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/zapis | Utwórz lub zaktualizuj urządzenia |
> | Akcja | Microsoft. StorSimple/kierownicy/encryptionSettings/odczyt | Wyświetla lub pobiera ustawienia szyfrowania |
> | Akcja | Microsoft. StorSimple/menedżerowie/extendedInformation/Usuń | Usuwa informacje o rozszerzonym magazynie |
> | Akcja | Microsoft. StorSimple/menedżerowie/extendedInformation/Usuń | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft. StorSimple/kierownicy/extendedInformation/odczyt | Wyświetla lub pobiera rozszerzone informacje o magazynie |
> | Akcja | Microsoft. StorSimple/kierownicy/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft. StorSimple/kierownicy/extendedInformation/Write | Utwórz lub zaktualizuj informacje o rozszerzonym magazynie |
> | Akcja | Microsoft. StorSimple/kierownicy/extendedInformation/Write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft. StorSimple/menedżerowie/funkcje/odczyt | Wyświetl listę funkcji |
> | Akcja | Microsoft. StorSimple/kierownicy/fileservers/odczyt | Wyświetla lub pobiera serwery plików |
> | Akcja | Microsoft. StorSimple/kierownicy/getEncryptionKey/akcja | Pobierz klucz szyfrowania dla Menedżera urządzeń. |
> | Akcja | Microsoft. StorSimple/kierownicy/iscsiservers/odczyt | Wyświetla lub pobiera serwery iSCSI |
> | Akcja | Microsoft. StorSimple/menedżerowie/zadania/odczyt | Wyświetla lub pobiera zadania |
> | Akcja | Microsoft. StorSimple/kierownicy/listActivationKey/akcja | Pobiera klucz aktywacji Menedżer urządzeń StorSimple. |
> | Akcja | Microsoft. StorSimple/kierownicy/listPublicEncryptionKey/akcja | Wyświetl listę publicznych kluczy szyfrowania StorSimple Menedżer urządzeń. |
> | Akcja | Microsoft. StorSimple/menedżerowie/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft. StorSimple/kierownicy/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft. StorSimple/kierownicy/migrateClassicToResourceManager/akcja | Migrowanie klasyczne do Menedżer zasobów menedżerów |
> | Akcja | Microsoft. StorSimple/kierownicy/migrationSourceConfigurations/odczyt | Wyświetl listę konfiguracji źródła migracji (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/kierownicy/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/provisionCloudAppliance/akcja | Utwórz nowe urządzenie w chmurze. |
> | Akcja | Microsoft. StorSimple/menedżerowie/odczyt | Wyświetla lub pobiera menedżerów urządzeń |
> | Akcja | Microsoft. StorSimple/menedżerowie/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft. StorSimple/kierownicy/regenerateActivationKey/akcja | Wygeneruj ponownie klucz aktywacji dla istniejących Menedżer urządzeń StorSimple. |
> | Akcja | Microsoft. StorSimple/menedżerowie/storageAccountCredentials/Usuń | Usuwa poświadczenia konta magazynu |
> | Akcja | Microsoft. StorSimple/menedżerowie/storageAccountCredentials/operationResults/Read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/storageAccountCredentials/odczyt | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Akcja | Microsoft. StorSimple/kierownicy/storageAccountCredentials/Write | Utwórz lub zaktualizuj poświadczenia konta magazynu |
> | Akcja | Microsoft. StorSimple/menedżerowie/storageDomains/Usuń | Usuwa domeny magazynu |
> | Akcja | Microsoft. StorSimple/menedżerowie/storageDomains/operationResults/Read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/storageDomains/odczyt | Wyświetla lub pobiera domeny magazynu |
> | Akcja | Microsoft. StorSimple/kierownicy/storageDomains/Write | Utwórz lub zaktualizuj domeny magazynu |
> | Akcja | Microsoft. StorSimple/menedżerowie/zapis | Utwórz lub zaktualizuj menedżerów urządzeń |
> | Akcja | Microsoft. StorSimple/menedżerowie/zapis | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft. StorSimple/Operations/Read | Wyświetla lub pobiera operacje |
> | Akcja | Microsoft. StorSimple/Register/Action | Zarejestruj dostawcę Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. StreamAnalytics/lokalizacje/przydziały/odczyt | Odczytaj Stream Analytics przydziału subskrypcji |
> | Akcja | Microsoft. StreamAnalytics/Operations/Read | Odczyt Stream Analytics operacji |
> | Akcja | Microsoft. StreamAnalytics/Register/Action | Zarejestruj subskrypcję za pomocą dostawcy zasobów Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Delete | Usuwanie zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Functions/Delete | Usuwanie funkcji zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Functions/operationresults/Read | Wyniki operacji odczytu dla funkcji zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Functions/Read | Odczytaj Stream Analytics funkcji zadania |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Functions/RetrieveDefaultDefinition/Action | Pobieranie domyślnej definicji funkcji zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Functions/test/Action | Funkcja zadania Stream Analytics testowego |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Functions/Write | Zapisz funkcję zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Input/Delete | Usuń dane wejściowe zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Inputs/operationresults/Read | Wyniki operacji odczytu dla danych wejściowych zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Inputs/Read | Odczytaj Stream Analytics dane wejściowe zadania |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Input/Sample/Action | Przykładowe dane wejściowe zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Inputs/test/Action | Dane wejściowe zadania Stream Analytics testu |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Inputs/Write | Zapisz dane wejściowe zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/metricdefinitions/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/operationresults/Read | Odczytaj wyniki operacji dla zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Output/Delete | Usuń dane wyjściowe zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Output/operationresults/Read | Wyniki operacji odczytu dla danych wyjściowych zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Output/odczyt | Odczytaj Stream Analytics dane wyjściowe zadania |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/wyjście/test/akcja | Testowanie danych wyjściowych zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Output/Write | Zapisz dane wyjściowe zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne. |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/diagnosticSettings/Write | Zapisz ustawienie diagnostyczne. |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla streamingjobs |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla streamingjobs |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/odczyt | Odczytaj Stream Analytics zadania |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Start/akcja | Uruchom zadanie Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Stop/akcja | Zatrzymaj zadanie Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Transformations/Delete | Usuń transformację zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Transformations/Read | Odczytaj Stream Analytics przekształcenie zadania |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Transformations/Write | Zapisz transformację zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Write | Stream Analytics zadania zapisu |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Subscription/Anuluj/akcja | Anuluje subskrypcję |
> | Akcja | Microsoft. subskrypcja/subskrypcja/akcja | Tworzenie subskrypcji platformy Azure |
> | Akcja | Microsoft. Subscription/rejestr/akcja | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. Subscription |
> | Akcja | Microsoft. Subscription/zmiana nazwy/akcji | Zmienia nazwę subskrypcji |
> | Akcja | Microsoft. Subscription/SubscriptionDefinitions/Read | Pobierz definicję subskrypcji platformy Azure w grupie zarządzania. |
> | Akcja | Microsoft. Subscription/SubscriptionDefinitions/Write | Tworzenie definicji subskrypcji platformy Azure |

## <a name="microsoftsupport"></a>Microsoft. Support

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Support/register/action | Przeprowadza rejestrację u dostawcy zasobów pomocy technicznej |
> | Akcja | Microsoft.Support/supportTickets/read | Pobiera szczegóły biletu pomocy technicznej (w tym stan, ważność, szczegóły dotyczące kontaktu i komunikacji) lub pobiera listę biletów pomocy technicznej dla różnych subskrypcji. |
> | Akcja | Microsoft.Support/supportTickets/write | Tworzy lub aktualizuje bilet pomocy technicznej. Można utworzyć bilet pomocy technicznej dotyczący problemów technicznych, rozliczeń, limitów przydziału lub problemów związanych z zarządzaniem subskrypcją. Można także zaktualizować ważność, szczegóły dotyczące kontaktu i komunikacji dla istniejących biletów pomocy technicznej. |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Delete | Usuwa zasady dostępu. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Read | Pobierz właściwości zasad dostępu. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Write | Tworzy nowe zasady dostępu dla środowiska lub aktualizuje istniejące zasady dostępu. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/Delete | Usuwa środowisko. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/eventsources/Delete | Usuwa Źródło zdarzenia. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/eventsources/Read | Pobierz właściwości źródła zdarzeń. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/eventsources/Write | Tworzy nowe źródło zdarzenia dla środowiska lub aktualizuje istniejące źródło zdarzeń. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/Read | Pobierz właściwości środowiska. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/referencedatasets/Delete | Usuwa zestaw danych referencyjnych. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/referencedatasets/Read | Pobierz właściwości zestawu danych referencyjnych. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/referencedatasets/Write | Tworzy nowy zestaw danych referencyjnych dla środowiska lub aktualizuje istniejący zestaw danych referencyjnych. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/status/Read | Pobierz stan środowiska, stan skojarzonych operacji, takich jak ruch przychodzący. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/Write | Tworzy nowe środowisko lub aktualizuje istniejące środowisko. |
> | Akcja | Microsoft. TimeSeriesInsights/Register/Action | Rejestruje subskrypcję dostawcy zasobów Time Series Insights i umożliwia tworzenie środowisk Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. VisualStudio/Account/Delete | Usuń konto |
> | Akcja | Microsoft. VisualStudio/Account/Extension/Read | Odczytaj konto/rozszerzenie |
> | Akcja | Microsoft. VisualStudio/Account/Project/Read | Odczytaj konto/projekt |
> | Akcja | Microsoft. VisualStudio/Account/Project/Write | Ustaw konto/projekt |
> | Akcja | Microsoft. VisualStudio/Account/Read | Odczytaj konto |
> | Akcja | Microsoft. VisualStudio/Account/Write | Ustaw konto |
> | Akcja | Microsoft. VisualStudio/rozszerzenie/usuwanie | Usuń rozszerzenie |
> | Akcja | Microsoft. VisualStudio/rozszerzenie/odczyt | Odczyt rozszerzenia |
> | Akcja | Microsoft. VisualStudio/rozszerzenie/zapis | Ustaw rozszerzenie |
> | Akcja | Microsoft. VisualStudio/Project/Delete | Usuń projekt |
> | Akcja | Microsoft. VisualStudio/Project/Read | Odczytaj projekt |
> | Akcja | Microsoft. VisualStudio/Project/Write | Ustaw projekt |
> | Akcja | Microsoft. VisualStudio/Register/Action | Zarejestruj subskrypcję platformy Azure przy użyciu dostawcy Microsoft. VisualStudio |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Web/apimanagementaccounts/apiacls/odczyt | Pobierz Apiacls kont usługi API Management. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/apiacls/Delete | Usuwanie interfejsów API kont usługi API Management Apiacls. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/apiacls/odczyt | Pobierz interfejsy API kont usługi API Management Apiacls. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/apiacls/Write | Aktualizowanie interfejsów API kont usługi API Management Apiacls. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/connectionacls/odczyt | Pobierz interfejsy API kont usługi API Management Connectionacls. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/Connections/confirmconsentcode/Action | Potwierdzanie połączeń interfejsów API kont usługi Management API kodu zgody. |
> | Akcja | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacls/Delete | Usuwanie interfejsów API kont usługi API Management połączeń Connectionacls. |
> | Akcja | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacls/Read | Pobierz interfejsy API kont usługi API Management Connectionacls. |
> | Akcja | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacls/Write | Aktualizowanie interfejsów API kont usługi API Management połączenia Connectionacls. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia/usuwanie | Usuwanie połączeń interfejsów API kont usługi API Management. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/Connections/getconsentlinks/Action | Uzyskaj linki do wyrażania zgody dla połączeń interfejsów API dla kont usługi API Management. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/Connections/listconnectionkeys/Action | Wyświetlanie listy kluczy połączenia interfejsy API kont usługi Management. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/Connections/listsecrets/Action | Wyświetlanie listy Secret usługi API accounts API Connections. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia/odczyt | Pobierz połączenia interfejsów API kont usługi API Management. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia/zapis | Aktualizowanie połączeń interfejsów API kont usługi API Management. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/usuwanie | Usuń interfejsy API kont usługi API Management. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/localizeddefinitions/Delete | Usuwanie usługi API Management konta interfejsy API zlokalizowane definicje. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/localizeddefinitions/odczyt | Pobierz zlokalizowane definicje interfejsów API kont usługi API Management. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/localizeddefinitions/Write | Aktualizowanie interfejsów API kont usługi API Management zlokalizowane definicje. |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/odczyt | Pobierz interfejsy API konta zarządzania |
> | Akcja | Microsoft. Web/apimanagementaccounts/interfejsy API/zapis | Aktualizowanie interfejsów API kont usługi API Management. |
> | Akcja | Microsoft. Web/apimanagementaccounts/connectionacls/odczyt | Pobierz Connectionacls kont usługi API Management. |
> | Akcja | Microsoft. Web/availablestacks/odczyt | Pobierz dostępne stosy. |
> | Akcja | Microsoft. Web/certyfikaty/usuwanie | Usuń istniejący certyfikat. |
> | Akcja | Microsoft. Web/Certificates/Read | Pobierz listę certyfikatów. |
> | Akcja | Microsoft. Web/Certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejący. |
> | Akcja | Microsoft. Web/checknameavailability/odczyt | Sprawdź, czy nazwa zasobu jest dostępna. |
> | Akcja | Microsoft. Web/classicmobileservices/odczyt | Pobierz klasyczne Mobile Services. |
> | Akcja | Microsoft. Web/connectionGateways/Delete | Usuwa bramę połączenia. |
> | Akcja | Microsoft. Web/connectionGateways/Join/Action | Sprzęga bramę połączenia. |
> | Akcja | Microsoft. Web/connectionGateways/ListStatus/akcja | Wyświetla stan bramy połączenia. |
> | Akcja | Microsoft. Web/connectionGateways/Przenieś/akcja | Przenosi bramę połączenia. |
> | Akcja | Microsoft. Web/connectionGateways/odczyt | Pobierz listę bram połączeń. |
> | Akcja | Microsoft. Web/connectionGateways/Write | Tworzy lub aktualizuje bramę połączenia. |
> | Akcja | Microsoft. Web/Connections/confirmconsentcode/Action | Potwierdź kod zgody na połączenia. |
> | Akcja | Microsoft. Web/Connections/Delete | Usuwa połączenie. |
> | Akcja | Microsoft. Web/Connections/Join/Action | Sprzęga połączenie. |
> | Akcja | Microsoft. Web/Connections/listconsentlinks/Action | Wyświetl listę linków zgody dla połączeń. |
> | Akcja | Microsoft. Web/Connections/Move/Action | Przenosi połączenie. |
> | Akcja | Microsoft. Web/Connections/Read | Pobierz listę połączeń. |
> | Akcja | Microsoft. Web/Connections/Write | Tworzy lub aktualizuje połączenie. |
> | Akcja | Microsoft. Web/customApis/Delete | Usuwa niestandardowy interfejs API. |
> | Akcja | Microsoft. Web/customApis/extractApiDefinitionFromWsdl/akcja | Wyodrębnia definicję interfejsu API z WSDL. |
> | Akcja | Microsoft. Web/customApis/Join/Action | Sprzęga niestandardowy interfejs API. |
> | Akcja | Microsoft. Web/customApis/listWsdlInterfaces/akcja | Wyświetla listę interfejsów WSDL dla niestandardowego interfejsu API. |
> | Akcja | Microsoft. Web/customApis/Przenieś/akcja | Przenosi niestandardowy interfejs API. |
> | Akcja | Microsoft. Web/customApis/odczyt | Pobierz listę niestandardowych interfejsów API. |
> | Akcja | Microsoft. Web/customApis/Write | Tworzy lub aktualizuje niestandardowy interfejs API. |
> | Akcja | Microsoft. Web/deletedSites/odczyt | Pobierz właściwości usuniętej aplikacji sieci Web |
> | Akcja | Microsoft. Web/deploymentlocations/odczyt | Pobierz lokalizacje wdrożenia. |
> | Akcja | Microsoft. Web/georegions/Read | Pobierz listę regionów geograficznych. |
> | Akcja | Microsoft. Web/hostingenvironments/pojemności/odczyt | Uzyskaj możliwości środowiska hostingu. |
> | Akcja | Microsoft. Web/hostingEnvironments/Delete | Usuwanie App Service Environment |
> | Akcja | Microsoft. Web/hostingenvironments/detektory/odczyt | Pobierz detektory środowisk hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/Diagnostyka/odczyt | Uzyskaj diagnostykę środowisk hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/inboundnetworkdependenciesendpoints/odczyt | Pobierz punkty końcowe sieci wszystkich zależności przychodzących. |
> | Akcja | Microsoft. Web/hostingEnvironments/Join/Action | Sprzęga App Service Environment |
> | Akcja | Microsoft. Web/hostingenvironments/metricdefinitions/odczyt | Pobierz definicje metryk środowisk hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/multirolepools/metricdefinitions/odczyt | Pobierz definicje metryk pul wielu ról środowiska hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/multirolepools/Metrics/Read | Uzyskaj metryki pul wielu ról. |
> | Akcja | Microsoft. Web/hostingEnvironments/multiRolePools/odczyt | Pobierz właściwości puli frontonu w App Service Environment |
> | Akcja | Microsoft. Web/hostingenvironments/multirolepools/SKU/odczyt | Uzyskaj środowiska hostingu pule wieloról jednostek SKU. |
> | Akcja | Microsoft. Web/hostingenvironments/multirolepools/Usages/Read | Uzyskaj środowiska hostingu użycia pul wielorolach. |
> | Akcja | Microsoft. Web/hostingEnvironments/multiRolePools/zapis | Utwórz nową pulę frontonu w App Service Environment lub zaktualizuj istniejącą |
> | Akcja | Microsoft. Web/hostingenvironments/Operations/Read | Pobierz operacje środowiska hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/outboundnetworkdependenciesendpoints/odczyt | Pobierz punkty końcowe sieci wszystkich zależności wychodzących. |
> | Akcja | Microsoft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/akcja | Zatwierdzanie połączeń prywatnych punktów końcowych |
> | Akcja | Microsoft. Web/hostingEnvironments/odczyt | Pobierz właściwości App Service Environment |
> | Akcja | Microsoft. Web/hostingEnvironments/ponowny rozruch/akcja | Uruchom ponownie wszystkie maszyny w App Service Environment |
> | Akcja | Microsoft. Web/hostingenvironments/Resume/akcja | Wznów środowiska hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/dopuszczalna/odczyt | Pobierz środowiska hostingu App Service plany. |
> | Akcja | Microsoft. Web/hostingenvironments/sites/odczyt | Pobierz środowiska hostingu Web Apps. |
> | Akcja | Microsoft. Web/hostingenvironments/Suspend/akcja | Wstrzymywanie środowisk hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/Usages/Read | Uzyskaj użycie środowisk hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/workerpools/metricdefinitions/odczyt | Pobierz środowiska hostingu Workerpools definicje metryki. |
> | Akcja | Microsoft. Web/hostingenvironments/workerpools/Metrics/Read | Uzyskaj środowiska hostingu Workerpools metryki. |
> | Akcja | Microsoft. Web/hostingEnvironments/workerPools/odczyt | Pobierz właściwości puli procesów roboczych w App Service Environment |
> | Akcja | Microsoft. Web/hostingenvironments/workerpools/SKU/odczyt | Uzyskaj środowiska hostingu Workerpools jednostki SKU. |
> | Akcja | Microsoft. Web/hostingenvironments/workerpools/Usages/Read | Uzyskaj środowiska hostingu Workerpools użycia. |
> | Akcja | Microsoft. Web/hostingEnvironments/workerPools/zapis | Utwórz nową pulę procesów roboczych w App Service Environment lub zaktualizuj istniejącą |
> | Akcja | Microsoft. Web/hostingEnvironments/Write | Utwórz nowy App Service Environment lub zaktualizuj istniejący |
> | Akcja | Microsoft. Web/ishostingenvironmentnameavailable/odczyt | Pobierz, jeśli nazwa środowiska hostingu jest dostępna. |
> | Akcja | Microsoft. Web/ishostnameavailable/odczyt | Sprawdź, czy nazwa hosta jest dostępna. |
> | Akcja | Microsoft. Web/isusernameavailable/odczyt | Sprawdź, czy nazwa użytkownika jest dostępna. |
> | Akcja | Microsoft. Web/listSitesAssignedToHostName/odczyt | Pobieranie nazw lokacji przypisanych do nazwy hosta. |
> | Akcja | Microsoft. Web/Locations/apioperations/Read | Pobieranie lokalizacji operacji interfejsu API. |
> | Akcja | Microsoft. Web/Locations/connectiongatewayinstallations/Read | Pobierz lokalizacje instalacji bramy połączeń. |
> | Akcja | Microsoft. Web/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadomienie o usunięciu sieci wirtualnej lub podsieci dla lokalizacji. |
> | Akcja | Microsoft. Web/Locations/extractapidefinitionfromwsdl/Action | Wyodrębnij definicję interfejsu API z WSDL dla lokalizacji. |
> | Akcja | Microsoft. Web/Locations/listwsdlinterfaces/Action | Wyświetl listę interfejsów WSDL dla lokalizacji. |
> | Akcja | Microsoft. Web/Locations/managedapis/apioperations/Read | Pobierz lokalizacje zarządzane interfejsu API. |
> | Akcja | Microsoft. Web/Locations/managedapis/Join/Action | Sprzęga zarządzany interfejs API. |
> | Akcja | Microsoft. Web/Locations/managedapis/Read | Pobierz zarządzane lokalizacje interfejsy API. |
> | Akcja | Microsoft. Web/Locations/operationResults/Read | Pobierz operacje. |
> | Akcja | Microsoft. Web/Locations/Operations/Read | Pobierz operacje. |
> | Akcja | Microsoft. Web/Operations/Read | Pobierz operacje. |
> | Akcja | Microsoft. Web/publishingusers/odczyt | Pobierz użytkowników publikacji. |
> | Akcja | Microsoft. Web/publishingusers/Write | Aktualizowanie użytkowników publikowanych. |
> | Akcja | Microsoft. Web/zalecenia/odczyt | Pobierz listę zaleceń dotyczących subskrypcji. |
> | Akcja | Microsoft. Web/Register/Action | Zarejestruj dostawcę zasobów Microsoft. Web dla subskrypcji. |
> | Akcja | Microsoft. Web/resourcehealthmetadata/odczyt | Pobierz metadane Resource Health. |
> | Akcja | Microsoft. Web/dopuszczalna/możliwości/odczyt | Pobierz możliwości planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/Delete | Usuń istniejący plan App Service |
> | Akcja | Microsoft. Web/dopuszczalna/eventGridFilters/Delete | Usuń filtr Event Grid w farmie serwerów. |
> | Akcja | Microsoft. Web/dopuszczalna/eventGridFilters/odczyt | Pobierz filtr Event Grid w farmie serwerów. |
> | Akcja | Microsoft. Web/dopuszczalna/eventGridFilters/zapis | Umieść filtr Event Grid w farmie serwerów. |
> | Akcja | Microsoft. Web/dopuszczalna/firstpartyapps/Settings/Delete | Usuń ustawienia App Service plany aplikacji pierwszej strony. |
> | Akcja | Microsoft. Web/dopuszczalna/firstpartyapps/Settings/Read | Pobierz App Service plany. |
> | Akcja | Microsoft. Web/dopuszczalna/firstpartyapps/Settings/Write | Aktualizacja App Service planuje ustawienia aplikacji innych firm. |
> | Akcja | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/Relay/usuwanie | Usuwanie App Service planów przestrzenie nazw połączeń hybrydowych przekaźników. |
> | Akcja | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/Relay/odczyt | Pobierz App Service plany przestrzeni nazw połączenia hybrydowego. |
> | Akcja | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/przekaźniki/witryny/odczyt | Pobierz App Service plany przestrzeń nazw połączenia hybrydowego przekazuje Web Apps. |
> | Akcja | Microsoft. Web/dopuszczalna/hybridconnectionplanlimits/odczyt | Pobierz limity planu połączeń hybrydowych planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/hybridconnectionrelays/odczyt | Pobierz App Service plany połączeń hybrydowych. |
> | Akcja | Microsoft. Web/dopuszczalna/metricdefinitions/odczyt | Pobierz definicje metryk planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/Metrics/Read | Pobierz metryki planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/operationresults/odczyt | Pobierz wyniki operacji planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/odczyt | Pobierz właściwości planu App Service |
> | Akcja | Microsoft. Web/dopuszczalna/restartSites/akcja | Uruchom ponownie wszystkie Web Apps w planie App Service |
> | Akcja | Microsoft. Web/dopuszczalna/sites/odczyt | Pobierz Web Apps planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/SKU/odczyt | Pobierz jednostki SKU planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/Usages/Read | Pobierz użycia planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/virtualnetworkconnections/Gateways/Write | App Service plany Virtual Network połączeń z bramą. |
> | Akcja | Microsoft. Web/dopuszczalna/virtualnetworkconnections/odczyt | Pobierz App Service plany Virtual Network połączeń. |
> | Akcja | Microsoft. Web/dopuszczalna/virtualnetworkconnections/Routes/Delete | Usuń App Service plany Virtual Network połączeń. |
> | Akcja | Microsoft. Web/dopuszczalna/virtualnetworkconnections/Routes/Read | Pobierz App Service plany Virtual Network połączeń. |
> | Akcja | Microsoft. Web/dopuszczalna/virtualnetworkconnections/Routes/write | Aktualizowanie App Service planów Virtual Network połączeń. |
> | Akcja | Microsoft. Web/dopuszczalna/procesy robocze/ponowny rozruch/akcja | Uruchom ponownie App Service plany pracowników. |
> | Akcja | Microsoft. Web/dopuszczalna/Write | Utwórz nowy plan App Service lub zaktualizuj istniejący |
> | Akcja | Microsoft. Web/Sites/analyzecustomhostname/odczyt | Analizuj niestandardową nazwę hosta. |
> | Akcja | Microsoft. Web/Sites/applySlotConfig/akcja | Zastosuj konfigurację miejsca aplikacji sieci Web z miejsca docelowego do bieżącej aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/kopia zapasowa/akcja | Utwórz nową kopię zapasową aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/kopia zapasowa/odczyt | Pobierz Web Apps kopii zapasowej. |
> | Akcja | Microsoft. Web/Sites/kopia zapasowa/zapis | Aktualizacja Web Apps kopii zapasowej. |
> | Akcja | Microsoft. Web/Sites/kopie zapasowe/akcja | Wykrywa istniejącą kopię zapasową aplikacji, która może zostać przywrócona z obiektu BLOB w usłudze Azure Storage. |
> | Akcja | Microsoft. Web/Sites/kopie zapasowe/usuwanie | Usuń kopie zapasowe Web Apps. |
> | Akcja | Microsoft. Web/Sites/kopie zapasowe/lista/akcja | Utwórz listę Web Apps kopii zapasowych. |
> | Akcja | Microsoft. Web/Sites/kopie zapasowe/odczyt | Pobierz właściwości kopii zapasowej aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/kopie zapasowe/przywracanie/akcja | Przywracanie Web Apps kopii zapasowych. |
> | Akcja | Microsoft. Web/Sites/kopie zapasowe/zapis | Aktualizacja Web Apps kopii zapasowych. |
> | Akcja | Microsoft. Web/Sites/config/Delete | Usuń konfigurację Web Apps. |
> | Akcja | Microsoft. Web/Sites/config/lista/akcja | Wyświetlanie ustawień poufnych zabezpieczeń aplikacji sieci Web, takich jak poświadczenia publikowania, ustawienia aplikacji i parametry połączenia |
> | Akcja | Microsoft. Web/Sites/config/odczyt | Pobierz ustawienia konfiguracji aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/konfiguracja/migawki/odczyt | Pobierz migawki konfiguracji Web Apps. |
> | Akcja | Microsoft. Web/Sites/config/Write | Zaktualizuj ustawienia konfiguracji aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/containerlogs/akcja | Pobierz spakowane dzienniki kontenerów dla aplikacji internetowej. |
> | Akcja | Microsoft. Web/Sites/containerlogs/Pobierz/akcja | Pobierz Web Apps dzienniki kontenerów. |
> | Akcja | Microsoft. Web/Sites/continuouswebjobs/Delete | Usuwanie Web Apps ciągłych zadań w sieci Web. |
> | Akcja | Microsoft. Web/Sites/continuouswebjobs/odczyt | Uzyskaj Web Apps ciągłe zadania w sieci Web. |
> | Akcja | Microsoft. Web/Sites/continuouswebjobs/Start/akcja | Zacznij Web Apps ciągłe zadania w sieci Web. |
> | Akcja | Microsoft. Web/Sites/continuouswebjobs/akcja | Zatrzymaj Web Apps ciągłe zadania w sieci Web. |
> | Akcja | Microsoft. Web/Sites/Delete | Usuwanie istniejącej aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Deployments/Delete | Usuń wdrożenia Web Apps. |
> | Akcja | Microsoft. Web/Sites/Deployments/log/Read | Pobierz dziennik wdrożeń Web Apps. |
> | Akcja | Microsoft. Web/Sites/Deployments/Read | Pobierz Web Apps wdrożenia. |
> | Akcja | Microsoft. Web/Sites/Deployments/Write | Aktualizacje Web Apps wdrożenia. |
> | Akcja | Microsoft. Web/Sites/detektory/odczyt | Pobierz Web Apps wykrywaczy. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/analizy/wykonanie/akcja | Uruchom analizę diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/analizy/odczyt | Pobierz analizę diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/aspnetcore/odczyt | Uzyskaj Web Apps diagnostykę dla ASP.NET Core aplikacji. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/autozabliźnione/odczyt | Pobierz Web Apps autozabliźnione diagnostyki. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/wdrożenie/odczyt | Pobierz wdrożenie diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostics/Deployments/Read | Pobierz Web Apps wdrożenia diagnostyki. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/detektory/wykonaj/akcję | Uruchom narzędzie wykrywania diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/detektory/odczyt | Pobierz detektor diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/failedrequestsperuri/odczyt | Pobierz Nieudane żądania diagnostyki Web Apps na identyfikator URI. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/frebanalysis/odczyt | Web Apps Pobierz analizę FREB diagnostyki. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/loganalyzer/odczyt | Pobierz Web Apps Analizator dziennika diagnostyki. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/odczyt | Pobierz kategorie diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/runtimeavailability/odczyt | Pobierz dostępność Web Apps diagnostyki. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/servicehealth/odczyt | Pobierz Service Health Web Apps diagnostyki. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/sitecpuanalysis/odczyt | Pobierz analizę procesora CPU lokacji diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/sitecrashes/odczyt | Pobierz awarie witryny diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/sitelatency/odczyt | Pobierz opóźnienie witryny diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/sitememoryanalysis/odczyt | Pobierz analizę pamięci lokacji diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/siterestartsettingupdate/odczyt | Pobierz aktualizację ustawienia ponownego uruchomienia witryny diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/siterestartuserinitiated/odczyt | Pobierz zainicjowane przez użytkownika ponowne uruchomienie witryny diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/siteswap/odczyt | Pobierz Web Apps wymiany lokacji diagnostyki. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/ThreadCount/odczyt | Pobierz Web Apps liczbę wątków diagnostycznych. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/workeravailability/odczyt | Pobierz Web Apps diagnostyki Workeravailability. |
> | Akcja | Microsoft. Web/Sites/Diagnostyka/workerprocessrecycle/odczyt | Pobierz procedurę odtwarzania procesu roboczego diagnostyki Web Apps. |
> | Akcja | Microsoft. Web/Sites/domainownershipidentifiers/odczyt | Pobierz identyfikatory własności domeny Web Apps. |
> | Akcja | Microsoft. Web/Sites/domainownershipidentifiers/Write | Aktualizowanie identyfikatorów własności domeny Web Apps. |
> | Akcja | Microsoft. Web/Sites/eventGridFilters/Delete | Usuń filtr Event Grid w aplikacji sieci Web. |
> | Akcja | Microsoft. Web/Sites/eventGridFilters/odczyt | Pobierz filtr Event Grid w aplikacji sieci Web. |
> | Akcja | Microsoft. Web/Sites/eventGridFilters/Write | Umieść filtr Event Grid w aplikacji sieci Web. |
> | Akcja | Microsoft. Web/Sites/rozszerzenia/usuwanie | Usuń rozszerzenia Web Apps lokacji. |
> | Akcja | Microsoft. Web/Sites/rozszerzenia/odczyt | Pobierz rozszerzenia witryny Web Apps. |
> | Akcja | Microsoft. Web/Sites/rozszerzenia/zapis | Aktualizowanie rozszerzeń lokacji Web Apps. |
> | Akcja | Microsoft. Web/Sites/Functions/Action | Web Apps funkcji. |
> | Akcja | Microsoft. Web/Sites/Functions/Delete | Usuń funkcje Web Apps. |
> | Akcja | Microsoft. Web/Sites/funkcje/klucze/usuwanie | Usuń klucze funkcji. |
> | Akcja | Microsoft. Web/Sites/Functions/Keys/Write | Zaktualizuj klucze funkcji. |
> | Akcja | Microsoft. Web/Sites/Functions/ListKeys/Action | Wyświetl klucze funkcji. |
> | Akcja | Microsoft. Web/Sites/Functions/listsecrets/Action | Utwórz listę wpisów tajnych funkcji. |
> | Akcja | Microsoft. Web/Sites/Functions/MasterKey/Read | Pobierz funkcje Web Apps MasterKey. |
> | Akcja | Microsoft. Web/Sites/Functions/Properties/Read | Odczytywanie właściwości Web Apps funkcji. |
> | Akcja | Microsoft. Web/Sites/Functions/Properties/Write | Aktualizowanie właściwości funkcji Web Apps. |
> | Akcja | Microsoft. Web/Sites/Functions/Read | Pobierz funkcje Web Apps. |
> | Akcja | Microsoft. Web/Sites/Functions/token/Read | Pobierz token funkcji Web Apps. |
> | Akcja | Microsoft. Web/Sites/Functions/Write | Aktualizowanie funkcji Web Apps. |
> | Akcja | Microsoft. Web/Sites/Host/functionkeys/Delete | Usuń klucze funkcji hosta funkcji. |
> | Akcja | Microsoft. Web/Sites/Host/functionkeys/Write | Aktualizowanie kluczy funkcji hosta funkcji. |
> | Akcja | Microsoft. Web/Sites/Host/ListKeys/akcja | Lista kluczy hosta funkcji. |
> | Akcja | Microsoft. Web/Sites/Host/listsyncstatus/akcja | Lista Stanów wyzwalaczy funkcji synchronizacji. |
> | Akcja | Microsoft. Web/Sites/Host/właściwości/odczyt | Odczytywanie właściwości hosta funkcji Web Apps Functions. |
> | Akcja | Microsoft. Web/Sites/Host/synchronizacja/akcja | Wyzwalacze funkcji synchronizacji. |
> | Akcja | Microsoft. Web/Sites/Host/systemkeys/Delete | Usuwanie funkcji klucze systemowe hosta. |
> | Akcja | Microsoft. Web/Sites/Host/systemkeys/Write | Aktualizowanie kluczy systemu hosta funkcji. |
> | Akcja | Microsoft. Web/Sites/hostnamebindings/Delete | Usuń powiązania Web Apps nazwy hosta. |
> | Akcja | Microsoft. Web/Sites/hostnamebindings/odczyt | Pobierz powiązania Web Apps nazwy hosta. |
> | Akcja | Microsoft. Web/Sites/hostnamebindings/Write | Aktualizowanie powiązań nazwy hosta Web Apps. |
> | Akcja | Microsoft. Web/Sites/hostruntime/Functions/Keys/odczyt | Pobierz Web Apps klucze funkcji Hostruntime. |
> | Akcja | Microsoft. Web/Sites/hostruntime/Host/_master/Read | Pobierz klucz główny aplikacja funkcji dla operacji administracyjnych |
> | Akcja | Microsoft. Web/Sites/hostruntime/Host/akcja | Wykonywanie akcji środowiska uruchomieniowego aplikacja funkcji, takich jak wyzwalacze synchronizacji, Dodawanie funkcji, wywoływanie funkcji, funkcje Delete itd. |
> | Akcja | Microsoft. Web/Sites/hostruntime/Host/odczyt | Pobierz Web Apps hosta Hostruntime. |
> | Akcja | Microsoft. Web/Sites/hybridconnection/Delete | Usuń połączenie hybrydowe Web Apps. |
> | Akcja | Microsoft. Web/Sites/hybridconnection/odczyt | Pobierz Web Apps połączenie hybrydowe. |
> | Akcja | Microsoft. Web/Sites/hybridconnection/Write | Aktualizacja Web Apps połączenie hybrydowe. |
> | Akcja | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/usuwanie | Usuń Web Apps przekaźników przestrzeni nazw połączenia hybrydowego. |
> | Akcja | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/ListKeys/Action | Klucze List Web Apps przekaźników przestrzeni nazw połączeń hybrydowych. |
> | Akcja | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/odczyt | Uzyskaj Web Apps przekaźników przestrzeni nazw połączenia hybrydowego. |
> | Akcja | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/Write | Aktualizacja Web Apps przekaźników przestrzeni nazw połączenia hybrydowego. |
> | Akcja | Microsoft. Web/Sites/hybridconnectionrelays/odczyt | Pobierz Web Apps przekaźników połączeń hybrydowych. |
> | Akcja | Microsoft. Web/Sites/Instances/Deployments/Delete | Usuń wdrożenia wystąpień Web Apps. |
> | Akcja | Microsoft. Web/Sites/Instances/Deployments/Read | Pobierz wdrożenia wystąpień Web Apps. |
> | Akcja | Microsoft. Web/Sites/Instances/Extensions/log/Read | Pobierz dziennik rozszerzeń wystąpień Web Apps. |
> | Akcja | Microsoft. Web/Sites/Instances/Extensions/processs/Read | Pobierz procesy rozszerzenia Web Apps Instances. |
> | Akcja | Microsoft. Web/Sites/Instances/Extensions/Read | Pobierz rozszerzenia wystąpień Web Apps. |
> | Akcja | Microsoft. Web/Sites/Instances/processs/Delete | Usuwanie procesów wystąpień Web Apps. |
> | Akcja | Microsoft. Web/Sites/Instances/processs/modules/Read | Pobierz wystąpienia Web Apps procesy przetwarzają moduły. |
> | Akcja | Microsoft. Web/Sites/Instances/processs/Read | Pobierz procesy Web Apps wystąpień. |
> | Akcja | Microsoft. Web/Sites/Instances/processs/Threads/Read | Pobierz wystąpienia Web Apps przetwarza wątki. |
> | Akcja | Microsoft. Web/Sites/Instances/Read | Pobierz wystąpienia Web Apps. |
> | Akcja | Microsoft. Web/Sites/listsyncfunctiontriggerstatus/akcja | Wyświetl stan wyzwalacza funkcji synchronizacji. |
> | Akcja | Microsoft. Web/Sites/metricdefinitions/odczyt | Pobierz definicje metryk Web Apps. |
> | Akcja | Microsoft. Web/Sites/Metrics/odczyt | Pobierz metryki Web Apps. |
> | Akcja | Microsoft. Web/Sites/metricsdefinitions/odczyt | Pobierz definicje metryk Web Apps. |
> | Akcja | Microsoft. Web/Sites/migratemysql/akcja | Migrowanie Web Apps MySql. |
> | Akcja | Microsoft. Web/Sites/migratemysql/odczyt | Pobierz Web Apps przeprowadzić migrację bazy danych MySql. |
> | Akcja | Microsoft. Web/Sites/networktrace/akcja | Web Apps śledzenia sieci. |
> | Akcja | Microsoft. Web/Sites/networktraces/operationresults/odczyt | Pobierz wyniki operacji śledzenia sieci Web Apps. |
> | Akcja | Microsoft. Web/Sites/NoweHasło/akcja | NoweHasło Web Apps. |
> | Akcja | Microsoft. Web/Sites/operationresults/odczyt | Pobierz wyniki operacji Web Apps. |
> | Akcja | Microsoft. Web/Sites/Operations/Read | Pobierz Web Apps operacji. |
> | Akcja | Microsoft. Web/Sites/perfcounters/odczyt | Pobierz Web Apps liczniki wydajności. |
> | Akcja | Microsoft. Web/Sites/premieraddons/Delete | Usuń Web Apps Dodatki Premier. |
> | Akcja | Microsoft. Web/Sites/premieraddons/odczyt | Uzyskaj Web Apps Dodatki Premier. |
> | Akcja | Microsoft. Web/Sites/premieraddons/Write | Aktualizacja dodatków do Web Apps Premier. |
> | Akcja | Microsoft. Web/Sites/privateaccess/odczyt | Uzyskaj dane dotyczące włączania dostępu do lokacji prywatnej i autoryzowanych sieci wirtualnych, które mogą uzyskać dostęp do witryny. |
> | Akcja | Microsoft. Web/Sites/PrivateEndpointConnectionsApproval/akcja | Zatwierdzanie połączeń prywatnych punktów końcowych |
> | Akcja | Microsoft. Web/Sites/processs/modules/Read | Pobierz moduły Web Apps procesów. |
> | Akcja | Microsoft. Web/Sites/processs/Read | Pobierz Web Apps procesy. |
> | Akcja | Microsoft. Web/Sites/processs/Threads/Read | Pobierz wątki procesów Web Apps. |
> | Akcja | Microsoft. Web/Sites/publiccertificates/Delete | Usuń Web Apps certyfikaty publiczne. |
> | Akcja | Microsoft. Web/Sites/publiccertificates/odczyt | Pobierz Web Apps certyfikaty publiczne. |
> | Akcja | Microsoft. Web/Sites/publiccertificates/Write | Aktualizowanie Web Apps certyfikatów publicznych. |
> | Akcja | Microsoft. Web/Sites/Publikuj/akcja | Publikowanie aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/publishxml/akcja | Pobieranie pliku XML profilu publikowania dla aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/publishxml/odczyt | Pobierz Web Apps publikowania XML. |
> | Akcja | Microsoft. Web/Sites/odczyt | Pobieranie właściwości aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/recommendationhistory/odczyt | Pobierz historię rekomendacji Web Apps. |
> | Akcja | Microsoft. Web/witryny/zalecenia/Wyłącz/akcję | Wyłącz zalecenia dotyczące Web Apps. |
> | Akcja | Microsoft. Web/Sites/zalecenia/odczyt | Pobierz listę zaleceń dotyczących aplikacji sieci Web. |
> | Akcja | Microsoft. Web/Sites/odzyskiwanie/akcja | Odzyskaj Web Apps. |
> | Akcja | Microsoft. Web/Sites/resetSlotConfig/akcja | Zresetuj konfigurację aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/resourcehealthmetadata/odczyt | Pobierz metadane Resource Health Web Apps. |
> | Akcja | Microsoft. Web/Sites/restart/akcja | Ponowne uruchamianie aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Restore/Read | Pobierz Web Apps przywracanie. |
> | Akcja | Microsoft. Web/Sites/Restore/Write | Przywracanie Web Apps. |
> | Akcja | Microsoft. Web/Sites/restorefrombackupblob/akcja | Przywróć aplikację internetową z obiektu BLOB kopii zapasowej. |
> | Akcja | Microsoft. Web/Sites/restorefromdeletedapp/akcja | Przywracanie Web Apps z usuniętej aplikacji. |
> | Akcja | Microsoft. Web/Sites/restoresnapshot/akcja | Przywracanie migawek Web Apps. |
> | Akcja | Microsoft. Web/Sites/siteextensions/Delete | Usuń rozszerzenia Web Apps lokacji. |
> | Akcja | Microsoft. Web/Sites/siteextensions/odczyt | Pobierz rozszerzenia witryny Web Apps. |
> | Akcja | Microsoft. Web/Sites/siteextensions/Write | Aktualizowanie rozszerzeń lokacji Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/analyzecustomhostname/Read | Uzyskaj Web Apps gniazda analizują niestandardową nazwę hosta. |
> | Akcja | Microsoft. Web/Sites/szczeliny/applySlotConfig/akcja | Zastosuj konfigurację miejsca aplikacji sieci Web z gniazda docelowego do bieżącego gniazda. |
> | Akcja | Microsoft. Web/Sites/gniazda/kopia zapasowa/akcja | Utwórz nową kopię zapasową miejsca aplikacji sieci Web. |
> | Akcja | Microsoft. Web/Sites/gniazda/kopia zapasowa/odczyt | Pobierz kopie zapasowe Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/gniazda/kopia zapasowa/zapis | Aktualizacja kopii zapasowej Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/gniazda/kopie zapasowe/akcja | Odkryj kopie zapasowe Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/gniazda/kopie zapasowe/usuwanie | Usuń kopie zapasowe miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/szczeliny/kopie zapasowe/lista/akcja | Utwórz listę kopii zapasowych miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/gniazda/kopie zapasowe/odczyt | Pobierz właściwości kopii zapasowej miejsc aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/gniazda/kopie zapasowe/przywracanie/akcja | Przywracanie kopii zapasowych miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/szczeliny/konfiguracja/usuwanie | Usuń konfigurację gniazd Web Apps. |
> | Akcja | Microsoft. Web/Sites/szczeliny/konfiguracja/lista/akcja | Wyświetlanie ustawień poufnych informacji o zabezpieczeniach miejsca aplikacji sieci Web, takich jak poświadczenia publikowania, ustawienia aplikacji i parametry połączenia |
> | Akcja | Microsoft. Web/Sites/szczeliny/konfiguracja/odczyt | Pobierz ustawienia konfiguracji miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/config/Write | Zaktualizuj ustawienia konfiguracji miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/szczeliny/containerlogs/akcja | Pobierz spakowane dzienniki kontenerów dla miejsca aplikacji sieci Web. |
> | Akcja | Microsoft. Web/Sites/szczeliny/containerlogs/pobieranie/akcja | Pobierz dzienniki kontenerów Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/continuouswebjobs/Delete | Usuń Web Apps miejsca ciągłe zadania w sieci Web. |
> | Akcja | Microsoft. Web/Sites/Slots/continuouswebjobs/Read | Uzyskaj miejsca na Web Apps ciągłe zadania w sieci Web. |
> | Akcja | Microsoft. Web/Sites/szczeliny/continuouswebjobs/Start/akcja | Rozpocznij Web Apps miejsca na ciągłe zadania w sieci Web. |
> | Akcja | Microsoft. Web/Sites/szczeliny/continuouswebjobs/akcja | Zatrzymaj Web Apps miejsca na ciągłe zadania w sieci Web. |
> | Akcja | Microsoft. Web/Sites/Slots/Delete | Usuń istniejące miejsce aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/Deployments/Delete | Usuń wdrożenia Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Deployments/log/Read | Pobierz dziennik wdrożeń Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Deployments/Read | Pobierz wdrożenia z Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Deployments/Write | Aktualizowanie wdrożeń na Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/gniazda/detektory/odczyt | Pobierz Web Apps wykrywaczy gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/analiza/wykonywanie/akcja | Uruchom analizę diagnostyki gniazd Web Apps. |
> | Akcja | Microsoft. Web/Sites/szczeliny/Diagnostyka/analizy/odczyt | Pobierz analizę diagnostyki miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/aspnetcore/Read | Pobierz diagnostykę Web Appsych miejsc dla aplikacji ASP.NET Core. |
> | Akcja | Microsoft. Web/Sites/szczeliny/Diagnostyka/autozabliźnione/odczyt | Pobierz Web Apps autozabliźnione diagnostyki miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/Deployment/Read | Pobierz wdrożenie diagnostyki miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/Deployments/Read | Pobierz Web Apps wdrożenia diagnostyczne. |
> | Akcja | Microsoft. Web/Sites/szczeliny/Diagnostyka/detektory/wykonaj/akcję | Uruchom detektor diagnostyki Web Appsych gniazd. |
> | Akcja | Microsoft. Web/Sites/szczeliny/Diagnostyka/detektory/odczyt | Pobierz detektor diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/frebanalysis/Read | Pobierz Web Apps FREB analiza diagnostyki gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/loganalyzer/Read | Pobierz Analizator dziennika diagnostyki gniazd Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/Read | Pobierz diagnostykę Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/runtimeavailability/Read | Pobierz dostępność w czasie wykonywania diagnostyki Web Apps gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/servicehealth/Read | Pobierz Service Health Web Apps diagnostyki miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/sitecpuanalysis/Read | Pobierz przeanalizowanie procesora CPU lokacji diagnostyki Web Apps gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/sitecrashes/Read | Pobierz awarie lokacji diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/sitelatency/Read | Pobierz opóźnienie witryny diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/sitememoryanalysis/Read | Pobierz analizę pamięci lokacji diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/siterestartsettingupdate/Read | Pobierz aktualizację ustawień ponownego uruchamiania lokacji diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/siterestartuserinitiated/Read | Pobierz lokację diagnostyki Web Apps miejsc ponownie zainicjowane przez użytkownika. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/siteswap/Read | Pobierz zamianę lokacji diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/ThreadCount/Read | Pobierz liczbę wątków diagnostyki Web Apps gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/workeravailability/Read | Pobierz Web Apps diagnostyki Workeravailability miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/workerprocessrecycle/Read | Pobierz procedurę odzyskiwania procesu roboczego diagnostyki gniazd Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/domainownershipidentifiers/Read | Pobierz identyfikatory własności domeny miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/Functions/listsecrets/Action | Wyświetl klucze tajne Web Apps funkcji miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Functions/Read | Pobierz funkcje Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/hostnamebindings/Delete | Usuń powiązania nazwy hosta Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/hostnamebindings/Read | Pobierz powiązania nazwy hosta Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/hostnamebindings/Write | Aktualizowanie powiązań nazwy hosta Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/hybridconnection/Delete | Usuń połączenie hybrydowe miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/hybridconnection/Read | Pobierz połączenie hybrydowe miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/hybridconnection/Write | Aktualizowanie połączenia hybrydowego dla gniazd Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/hybridconnectionnamespaces/Relay/Delete | Usuń Web Apps gniazda przestrzenie nazw połączenia hybrydowego. |
> | Akcja | Microsoft. Web/Sites/Slots/hybridconnectionnamespaces/Relay/Write | Aktualizacja funkcji przekazywania przestrzeni nazw połączeń hybrydowych na Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/hybridconnectionrelays/Read | Pobierz Web Apps gniazda połączeń hybrydowych. |
> | Akcja | Microsoft. Web/Sites/Slots/Instances/Deployments/Read | Pobierz wystąpienia Web Apps wystąpień. |
> | Akcja | Microsoft. Web/Sites/Slots/Instances/processs/Delete | Usuwanie procesów wystąpień Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/Instances/processs/Read | Pobierz procesy Web Apps wystąpieniach gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/Instances/Read | Pobierz wystąpienia Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/metricdefinitions/Read | Pobierz definicje metryk Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Metrics/Read | Pobierz metryki Web Apps gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/migratemysql/Read | Pobierz Web Apps gniazda migracji bazy danych MySql. |
> | Akcja | Microsoft. Web/Sites/szczeliny/networktrace/akcja | Gniazda Web Apps śledzenia sieci. |
> | Akcja | Microsoft. Web/Sites/Slots/networktraces/operationresults/Read | Pobierz wyniki operacji śledzenia sieci Web Apps gniazd. |
> | Akcja | Microsoft. Web/Sites/szczeliny/NoweHasło/akcja | NoweHasło Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/operationresults/Read | Pobierz wyniki operacji dla miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/Operations/Read | Pobierz operacje na Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/perfcounters/Read | Pobierz liczniki wydajności Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/phplogging/Read | Pobierz Web Apps Phplogging miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/premieraddons/Delete | Usuń dodatki Web Apps miejsc premier. |
> | Akcja | Microsoft. Web/Sites/Slots/premieraddons/Read | Uzyskaj Web Apps miejsca na dodatki Premier. |
> | Akcja | Microsoft. Web/Sites/Slots/premieraddons/Write | Aktualizowanie dodatków do Web Apps miejsc premier. |
> | Akcja | Microsoft. Web/Sites/Slots/processs/Read | Pobierz procesy Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/publiccertificates/Delete | Usuwanie Web Apps miejscowych certyfikatów publicznych. |
> | Akcja | Microsoft. Web/Sites/Slots/publiccertificates/Read | Pobierz Web Apps gniazda certyfikatów publicznych. |
> | Akcja | Microsoft. Web/Sites/Slots/publiccertificates/Write | Utwórz lub zaktualizuj certyfikaty publiczne na Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/szczeliny/publikowanie/akcja | Publikowanie miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/szczeliny/publishxml/akcja | Pobierz plik XML profilu publikowania dla miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/gniazda/odczyt | Pobierz właściwości miejsca wdrożenia aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/gniazda/odzyskiwanie/akcja | Odzyskaj Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/szczeliny/resetSlotConfig/akcja | Resetuj konfigurację miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/resourcehealthmetadata/Read | Pobierz Web Apps miejsca Resource Health metadanych. |
> | Akcja | Microsoft. Web/Sites/szczeliny/ponowne uruchomienie/akcja | Uruchom ponownie miejsce aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/Restore/Read | Pobierz Web Apps miejsc przywracania. |
> | Akcja | Microsoft. Web/Sites/Slots/Restore/Write | Przywracanie Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/szczeliny/restorefrombackupblob/akcja | Przywróć miejsce Web Apps z obiektu BLOB kopii zapasowej. |
> | Akcja | Microsoft. Web/Sites/szczeliny/restorefromdeletedapp/akcja | Przywróć miejsca aplikacji sieci Web z usuniętej aplikacji. |
> | Akcja | Microsoft. Web/Sites/szczeliny/restoresnapshot/akcja | Przywracanie migawek miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/siteextensions/Delete | Usuwanie rozszerzeń lokacji Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/siteextensions/Read | Pobierz rozszerzenia witryny Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/siteextensions/Write | Aktualizowanie rozszerzeń lokacji Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/szczeliny/slotsdiffs/akcja | Poznaj różnice między konfiguracją aplikacji sieci Web i miejsc |
> | Akcja | Microsoft. Web/Sites/szczeliny/slotsswap/akcja | Wymiana miejsc wdrożenia aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/snapshots/Read | Pobierz migawki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/sourcecontrols/Read | Pobierz ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/szczeliny/uruchomienie/akcja | Uruchom miejsce aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/akcja | Zatrzymaj miejsce aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/szczeliny/synchronizacja/akcja | Zsynchronizuj Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/triggeredwebjobs/Delete | Usuwanie Web Appsych gniazd wyzwalanych przez Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/Slots/triggeredwebjobs/Read | Pobierz Web Apps gniazda wyzwolone Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/Slots/triggeredwebjobs/Run/Action | Uruchom uruchomione Web Apps gniazda wyzwalane przez Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/Slots/Usages/Read | Uzyskaj Web Apps użycia miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/virtualnetworkconnections/Delete | Usuwanie Web Apps miejsc Virtual Network połączeń. |
> | Akcja | Microsoft. Web/Sites/szczeliny/virtualnetworkconnections/Gateways/Write | Aktualizowanie Web Apps miejsc Virtual Network połączeń bram. |
> | Akcja | Microsoft. Web/Sites/Slots/virtualnetworkconnections/Read | Pobierz Web Apps gniazda Virtual Network połączeń. |
> | Akcja | Microsoft. Web/Sites/Slots/virtualnetworkconnections/Write | Aktualizacja Virtual Network połączeń Web Apps. |
> | Akcja | Microsoft. Web/Sites/szczeliny/Zadania WebJob/odczyt | Pobierz Zadania WebJob na Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/szczeliny/zapis | Utwórz nowe miejsce aplikacji sieci Web lub zaktualizuj istniejące |
> | Akcja | Microsoft. Web/Sites/slotsdiffs/akcja | Poznaj różnice między konfiguracją aplikacji sieci Web i miejsc |
> | Akcja | Microsoft. Web/Sites/slotsswap/akcja | Wymiana miejsc wdrożenia aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/snapshots/Read | Pobierz migawki Web Apps. |
> | Akcja | Microsoft. Web/Sites/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/sourcecontrols/odczyt | Pobierz ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Start/akcja | Uruchamianie aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/zatrzymywanie/akcja | Zatrzymaj aplikację internetową |
> | Akcja | Microsoft. Web/Sites/synchronizacja/akcja | Web Apps synchronizacji. |
> | Akcja | Microsoft. Web/Sites/syncfunctiontriggers/akcja | Wyzwalacze funkcji synchronizacji. |
> | Akcja | Microsoft. Web/Sites/triggeredwebjobs/Delete | Usuń Web Apps wyzwolone Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/triggeredwebjobs/historia/odczyt | Pobierz Web Apps wyzwoloną historię zadań WebJob. |
> | Akcja | Microsoft. Web/Sites/triggeredwebjobs/odczyt | Pobierz Web Apps wyzwolone Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/triggeredwebjobs/Run/Action | Uruchom Web Apps wyzwolone Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites//użycia/odczyt | Pobierz Web Apps użycia. |
> | Akcja | Microsoft. Web/Sites/virtualnetworkconnections/Delete | Usuń połączenia Virtual Network Web Apps. |
> | Akcja | Microsoft. Web/Sites/virtualnetworkconnections/Gateways/Read | Pobierz Web Apps bram Virtual Network połączeń. |
> | Akcja | Microsoft. Web/Sites/virtualnetworkconnections/Gateways/Write | Aktualizowanie bram Web Apps Virtual Network połączeń. |
> | Akcja | Microsoft. Web/Sites/virtualnetworkconnections/odczyt | Uzyskaj Web Apps połączenia Virtual Network. |
> | Akcja | Microsoft. Web/Sites/virtualnetworkconnections/Write | Aktualizowanie Web Apps Virtual Network połączeń. |
> | Akcja | Microsoft. Web/Sites/WebJobs/odczyt | Pobierz Web Apps Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/Write | Utwórz nową aplikację sieci Web lub zaktualizuj istniejącą |
> | Akcja | Microsoft. Web/SKU/odczyt | Pobierz jednostki SKU. |
> | Akcja | Microsoft. Web/sourcecontrols/odczyt | Pobierz kontrolę źródła. |
> | Akcja | Microsoft. Web/sourcecontrols/Write | Aktualizowanie kontroli źródła. |
> | Akcja | Microsoft. Web/Unregister/akcja | Wyrejestruj dostawcę zasobów Microsoft. Web dla subskrypcji. |
> | Akcja | Microsoft. Web/Validate/akcja | Legalizacj. |
> | Akcja | Microsoft. Web/verifyhostingenvironmentvnet/akcja | Sprawdź sieć wirtualną środowiska hostingu. |

## <a name="microsoftworkloadmonitor"></a>Microsoft. Monitor obciążenia został

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Monitor obciążenia został/Components/Read | Pobiera składniki dla zasobu |
> | Akcja | Microsoft. Monitor obciążenia został/componentsSummary/odczyt | Pobiera podsumowanie składników |
> | Akcja | Microsoft. Monitor obciążenia został/monitorInstances/odczyt | Pobiera wystąpienia monitorów dla zasobu |
> | Akcja | Microsoft. Monitor obciążenia został/monitorInstancesSummary/odczyt | Pobiera podsumowanie wystąpień monitora |
> | Akcja | Microsoft. Monitor obciążenia został/Monitors/Read | Pobiera monitory dla zasobu |
> | Akcja | Microsoft. Monitor obciążenia został/Monitors/Write | Konfigurowanie monitora dla zasobu |
> | Akcja | Microsoft. Monitor obciążenia został/notificationSettings/odczyt | Pobiera ustawienia powiadomień dla zasobu |
> | Akcja | Microsoft. Monitor obciążenia został/notificationSettings/Write | Konfigurowanie ustawień powiadomień dla zasobu |
> | Akcja | Microsoft. Monitor obciążenia został/Operations/Read | Pobiera obsługiwane operacje |

## <a name="next-steps"></a>Następne kroki

- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
