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
ms.openlocfilehash: 092c3b4ac6ce163e9fcf8aaad9e74f398559e9e2
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546335"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operacje dostawcy zasobów Azure Resource Manager

W tym artykule wymieniono operacje dostępne dla każdego dostawcy zasobów Azure Resource Manager. Te operacje mogą być używane w [rolach niestandardowych](custom-roles.md) w celu zapewnienia szczegółowej [kontroli dostępu opartej na rolach (RBAC)](overview.md) na potrzeby zasobów na platformie Azure. Ciągi operacji mają następujący format: `{Company}.{ProviderName}/{resourceType}/{action}`. Aby uzyskać listę sposobu mapowania przestrzeni nazw dostawcy zasobów na usługi platformy Azure, zobacz [dopasowanie dostawcy zasobów do usługi](../azure-resource-manager/azure-services-resource-providers.md).

Operacje dostawcy zasobów są zawsze rozwijane. Aby uzyskać najnowsze operacje, Użyj listy operacji [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) lub [AZ Provider](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. AAD/domainServices/Delete | Usuwanie usługi domeny |
> | Działanie | Microsoft. AAD/domainServices/oucontainer/usuwanie | Usuń kontener jednostki organizacyjnej |
> | Działanie | Microsoft. AAD/domainServices/oucontainer/odczyt | Odczytaj kontenery jednostek organizacyjnych |
> | Działanie | Microsoft. AAD/domainServices/oucontainer/zapis | Zapisz kontener jednostki organizacyjnej |
> | Działanie | Microsoft. AAD/domainServices/odczyt | Odczytaj usługi domenowe |
> | Działanie | Microsoft. AAD/domainServices/replicaSets/usuwanie | Usuń lokację klastra |
> | Działanie | Microsoft. AAD/domainServices/replicaSets/odczyt | Odczytaj lokację klastra |
> | Działanie | Microsoft. AAD/domainServices/replicaSets/zapis | Zapisz witrynę klastra |
> | Działanie | Microsoft. AAD/domainServices/Write | Zapisz usługę domenową |
> | Działanie | Microsoft. AAD/Locations/operationresults/Read |  |
> | Działanie | Microsoft. AAD/Operations/Read |  |
> | Działanie | Microsoft. AAD/rejestrowanie/akcja | Zarejestruj usługę domeny |
> | Działanie | Microsoft. AAD/Wyrejestruj/akcja | Wyrejestruj usługę domenową |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. aadiam/diagnosticsettings/Delete | Usuwanie ustawień diagnostycznych |
> | Działanie | Microsoft. aadiam/diagnosticsettings/odczyt | Odczytywanie ustawień diagnostycznych |
> | Działanie | Microsoft. aadiam/diagnosticsettings/Write | Zapisywanie ustawień diagnostycznych |
> | Działanie | Microsoft. aadiam/diagnosticsettingscategories/odczyt | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftaddons"></a>Microsoft. Dodatki

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Dodatki/operacje/odczyt | Pobiera obsługiwane operacje RP. |
> | Działanie | Microsoft. Dodatki/rejestrowanie/akcja | Zarejestruj określoną subskrypcję w programie Microsoft. Dodatki |
> | Działanie | Microsoft. Dodatki/supportProviders/listsupportplaninfo/akcja | Wyświetla bieżące informacje o planach pomocy technicznej dla określonej subskrypcji. |
> | Działanie | Microsoft. Dodatki/supportProviders/supportPlanTypes/usuwanie | Usuwa określony plan obsługi kanonicznej |
> | Działanie | Microsoft. Dodatki/supportProviders/supportPlanTypes/odczyt | Pobierz określony stan kanonicznego planu pomocy technicznej. |
> | Działanie | Microsoft. Dodatki/supportProviders/supportPlanTypes/Write | Dodaje określony typ planu obsługi kanonicznej. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

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

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Advisor/konfiguracje/odczyt | Pobierz konfiguracje |
> | Działanie | Microsoft. Advisor/konfiguracje/zapis | Tworzy/aktualizuje konfigurację |
> | Działanie | Microsoft. Advisor/generateRecommendations/akcja | Generuje zalecenia |
> | Działanie | Microsoft. Advisor/generateRecommendations/Read | Pobiera stan zaleceń generowania |
> | Działanie | Microsoft. Advisor/Metadata/Read | Pobierz metadane |
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

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. AlertsManagement/actionRules/Delete | Usuń regułę akcji w danej subskrypcji. |
> | Działanie | Microsoft. AlertsManagement/actionRules/odczyt | Pobierz wszystkie reguły akcji dla filtrów wejściowych. |
> | Działanie | Microsoft. AlertsManagement/actionRules/Write | Utwórz lub zaktualizuj regułę akcji w danej subskrypcji |
> | Działanie | Microsoft. AlertsManagement/Alerts/changestate/Action | Zmień stan alertu. |
> | Działanie | Microsoft. AlertsManagement/alerty/Diagnostyka/odczyt | Pobierz całą diagnostykę dla alertu |
> | Działanie | Microsoft. AlertsManagement/alerty/historia/odczyt | Pobierz historię alertu |
> | Działanie | Microsoft. AlertsManagement/Alerts/Read | Pobierz wszystkie alerty dla filtrów wejściowych. |
> | Działanie | Microsoft. AlertsManagement/alertsList/odczyt | Pobierz wszystkie alerty dla filtrów wejściowych między subskrypcjami |
> | Działanie | Microsoft. AlertsManagement/alertsMetaData/odczyt | Pobierz metadane alertów dla parametru wejściowego. |
> | Działanie | Microsoft. AlertsManagement/alertsSummary/odczyt | Pobierz podsumowanie alertów |
> | Działanie | Microsoft. AlertsManagement/alertsSummaryList/odczyt | Pobierz podsumowanie alertów w ramach subskrypcji |
> | Działanie | Microsoft. AlertsManagement/Operations/Read | Odczytuje podane operacje |
> | Działanie | Microsoft. AlertsManagement/Register/Action | Rejestruje subskrypcję zarządzania alertami firmy Microsoft |
> | Działanie | Microsoft. AlertsManagement/smartDetectorAlertRules/Delete | Usuń regułę alertu inteligentnego wykrywania w danej subskrypcji |
> | Działanie | Microsoft. AlertsManagement/smartDetectorAlertRules/odczyt | Pobieranie wszystkich reguł alertów dotyczących detektora inteligentnego dla filtrów wejściowych |
> | Działanie | Microsoft. AlertsManagement/smartDetectorAlertRules/Write | Utwórz lub zaktualizuj regułę alertu inteligentnego wykrywania w danej subskrypcji |
> | Działanie | Microsoft. AlertsManagement/smartGroups/changestate/akcja | Zmiana stanu grupy inteligentnej |
> | Działanie | Microsoft. AlertsManagement/smartGroups/History/odczyt | Pobierz historię grupy inteligentnej |
> | Działanie | Microsoft. AlertsManagement/smartGroups/odczyt | Pobierz wszystkie grupy inteligentne dla filtrów wejściowych |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. AnalysisServices/Locations/checkNameAvailability/Action | Sprawdza, czy dana nazwa Analysis Server jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft. AnalysisServices/Locations/operationresults/Read | Pobiera informacje o wyniku określonej operacji. |
> | Działanie | Microsoft. AnalysisServices/Locations/operationstatuses/Read | Pobiera informacje o stanie określonej operacji. |
> | Działanie | Microsoft. AnalysisServices/Operations/Read | Pobiera informacje o operacjach |
> | Działanie | Microsoft. AnalysisServices/Register/Action | Rejestruje dostawcę zasobów Analysis Services. |
> | Działanie | Microsoft. AnalysisServices/serwery/usuwanie | Usuwa Analysis Server. |
> | Działanie | Microsoft. AnalysisServices/serwery/listGatewayStatus/akcja | Wyświetlenie listy stan bramy skojarzonej z serwerem. |
> | Działanie | Microsoft. AnalysisServices/serwery/odczyt | Pobiera informacje o określonym Analysis Server. |
> | Działanie | Microsoft. AnalysisServices/serwery/wznowienie/akcja | Wznawia Analysis Server. |
> | Działanie | Microsoft. AnalysisServices/serwery/jednostki SKU/odczyt | Pobierz informacje o dostępnych jednostkach SKU dla serwera |
> | Działanie | Microsoft. AnalysisServices/serwery/wstrzymywanie/akcja | Wstrzymuje Analysis Server. |
> | Działanie | Microsoft. AnalysisServices/serwery/zapis | Tworzy lub aktualizuje określony Analysis Server. |
> | Działanie | Microsoft. AnalysisServices/jednostki SKU/odczyt | Pobiera informacje o jednostkach SKU |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ApiManagement/checkNameAvailability/odczyt | Sprawdza, czy podana nazwa usługi jest dostępna |
> | Działanie | Microsoft. ApiManagement/Operations/Read | Odczytuj wszystkie operacje interfejsu API dostępne dla zasobu Microsoft. ApiManagement |
> | Działanie | Microsoft. ApiManagement/Register/Action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. ApiManagement |
> | Działanie | Microsoft. ApiManagement/Reports/Read | Uzyskuj raporty zagregowane według okresów, regionu geograficznego, deweloperów, produktów, interfejsów API, operacji, subskrypcji i byRequest. |
> | Działanie | Microsoft. ApiManagement/Service/API/Delete | Usuwa określony interfejs API wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/API/Diagnostyka/usuwanie | Usuwa określoną diagnostykę z interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/Diagnostics/Read | Wyświetla listę wszystkich diagnostyki interfejsu API. lub pobiera szczegóły diagnostyki dla interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/Diagnostyka/zapis | Tworzy nową diagnostykę dla interfejsu API lub aktualizuje istniejący. lub aktualizuje szczegóły diagnostyki dla interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/załączniki/usuwanie | Usuwa określony komentarz z problemu. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/załączniki/odczyt | Wyświetla listę wszystkich załączników dla problemu skojarzonego z określonym interfejsem API. lub pobiera szczegóły załącznika problemu dla interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/załączniki/zapis | Tworzy nowy załącznik dla problemu w interfejsie API lub aktualizuje istniejący. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/Komentarze/usuwanie | Usuwa określony komentarz z problemu. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/Komentarze/odczyt | Wyświetla listę wszystkich komentarzy dotyczących problemu skojarzonego z określonym interfejsem API. lub pobiera szczegóły komentarza dotyczącego problemu dla interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/Komentarze/zapis | Tworzy nowy komentarz dotyczący problemu w interfejsie API lub aktualizuje istniejący. |
> | Działanie | Microsoft. ApiManagement/Service/API/problemy/usuwanie | Usuwa określony problem z interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/Issues/Read | Wyświetla listę wszystkich problemów skojarzonych z określonym interfejsem API. lub pobiera szczegóły problemu dotyczącego interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/Issues/Write | Tworzy nowy problem dla interfejsu API lub aktualizuje istniejący. lub aktualizuje istniejący problem dla interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/Delete | Usuwa określoną operację w interfejsie API. |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/policies/Delete | Usuwa konfigurację zasad w operacji interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/policies/Read | Pobierz listę konfiguracji zasad na poziomie operacji interfejsu API. lub Pobierz konfigurację zasad na poziomie operacji interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/policies/Write | Tworzy lub aktualizuje konfigurację zasad dla poziomu operacji interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/Policy/Delete | Usuń konfigurację zasad na poziomie operacji |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/Policy/Read | Pobierz konfigurację zasad na poziomie operacji |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/Policy/Write | Utwórz konfigurację zasad na poziomie operacji |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/Read | Wyświetla kolekcję operacji dla określonego interfejsu API. lub pobiera szczegóły operacji interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/Tags/Delete | Odłącz tag od operacji. |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/Tags/Read | Wyświetla listę wszystkich tagów skojarzonych z operacją. lub Pobierz tag skojarzony z operacją. |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/Tags/Write | Przypisz tag do operacji. |
> | Działanie | Microsoft. ApiManagement/Service/API/Operations/Write | Tworzy nową operację w interfejsie API lub aktualizuje istniejącą. lub aktualizuje szczegóły operacji w interfejsie API określonym przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/operationsByTags/Read | Wyświetla kolekcję operacji skojarzonych ze znacznikami. |
> | Działanie | Microsoft. ApiManagement/Service/API/policies/Delete | Usuwa konfigurację zasad w interfejsie API. |
> | Działanie | Microsoft. ApiManagement/Service/API/zasady/odczyt | Pobierz konfigurację zasad na poziomie interfejsu API. lub Pobierz konfigurację zasad na poziomie interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/zasady/zapis | Tworzy lub aktualizuje konfigurację zasad dla interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/Policy/Delete | Usuń konfigurację zasad na poziomie interfejsu API |
> | Działanie | Microsoft. ApiManagement/Service/API/Policy/Read | Pobierz konfigurację zasad na poziomie interfejsu API |
> | Działanie | Microsoft. ApiManagement/Service/API/Policy/Write | Utwórz konfigurację zasad na poziomie interfejsu API |
> | Działanie | Microsoft. ApiManagement/Service/API/Products/Read | Wyświetla listę wszystkich produktów, które są częścią interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/odczyt | Wyświetla wszystkie interfejsy API wystąpienia usługi API Management. lub pobiera szczegóły interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/releases/Delete | Usuwa wszystkie wersje interfejsu API lub usuwa określoną wersję w interfejsie API. |
> | Działanie | Microsoft. ApiManagement/Service/API/releases/Read | Wyświetla wszystkie wersje interfejsu API.<br>Wersja interfejsu API jest tworzona podczas aktualnej wersji interfejsu API.<br>Wersje są również używane do wycofywania do poprzednich wersji.<br>Wyniki będą stronicowane i mogą być ograniczone przez $top i $skip parametrów.<br>lub zwraca szczegółowe informacje o wersji interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/releases/Write | Tworzy nową wersję interfejsu API. lub aktualizuje szczegóły wydania interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/API/Revisions/Delete | Usuwa wszystkie poprawki interfejsu API |
> | Działanie | Microsoft. ApiManagement/Service/API/Revisions/Read | Wyświetla wszystkie wersje interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/schematy/usuwanie | Usuwa konfigurację schematu w interfejsie API. |
> | Działanie | Microsoft. ApiManagement/Service/API/schematy/odczyt | Pobierz konfigurację schematu na poziomie interfejsu API. lub zapoznaj się z konfiguracją schematu na poziomie interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/schematy/zapis | Tworzy lub aktualizuje konfigurację schematu dla interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/tagDescriptions/Delete | Usuń opis tagu dla interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/tagDescriptions/Read | Wyświetla listę wszystkich opisów tagów w zakresie interfejsu API. Model podobny do struktury Swagger-tagDescription jest zdefiniowany na poziomie interfejsu API, ale tag może być przypisany do operacji lub uzyskać opis tagu w zakresie interfejsu API |
> | Działanie | Microsoft. ApiManagement/Service/API/tagDescriptions/Write | Utwórz/zaktualizuj opis tagu w zakresie interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/interfejsy API/Tagi/usuwanie | Odłącz tag od interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/interfejsy API/Tagi/odczyt | Wyświetla listę wszystkich tagów skojarzonych z interfejsem API. lub uzyskaj tag skojarzony z interfejsem API. |
> | Działanie | Microsoft. ApiManagement/Service/interfejsy API/Tagi/zapis | Przypisz tag do interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/API/Write | Tworzy nowy lub aktualizuje istniejący interfejs API wystąpienia usługi API Management. lub aktualizuje określony interfejs API wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/apisByTags/Read | Wyświetla kolekcję interfejsów API skojarzonych ze znacznikami. |
> | Działanie | Microsoft. ApiManagement/Service/apiVersionSets/Delete | Usuwa określony zestaw wersji interfejsu API. |
> | Działanie | Microsoft. ApiManagement/Service/apiVersionSets/Read | Wyświetla kolekcję zestawów wersji interfejsu API w określonym wystąpieniu usługi. lub pobiera szczegóły zestawu wersji interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/apiVersionSets/wersje/odczyt | Pobierz listę jednostek wersji |
> | Działanie | Microsoft. ApiManagement/Service/apiVersionSets/Write | Tworzy lub aktualizuje zestaw wersji interfejsu API. lub aktualizuje szczegóły VersionSet interfejsu API określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/applynetworkconfigurationupdates/Action | Aktualizuje zasoby Microsoft. ApiManagement uruchomione w Virtual Network w celu pobrania zaktualizowanych ustawień sieci. |
> | Działanie | Microsoft. ApiManagement/Service/authorizationServers/Delete | Usuwa określone wystąpienie serwera autoryzacji. |
> | Działanie | Microsoft. ApiManagement/Service/authorizationServers/Read | Wyświetla kolekcję serwerów autoryzacji zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły serwera autoryzacji określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/authorizationServers/Write | Tworzy nowy serwer autoryzacji lub aktualizuje istniejący serwer autoryzacji. lub aktualizuje szczegóły serwera autoryzacji określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/nadkończenie/usuwanie | Usuwa określone zaplecze. |
> | Działanie | Microsoft. ApiManagement/Service/zakończyła się/odczytywać | Wyświetla kolekcję punktów końcowych w określonym wystąpieniu usługi. lub pobiera szczegóły zaplecza określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/nadkończenie/ponowne łączenie/akcja | Powiadamia serwer proxy APIM o utworzeniu nowego połączenia z zapleczem po upływie określonego limitu czasu. Jeśli nie określono limitu czasu, zostanie użyty limit czasu wynoszący 2 minuty. |
> | Działanie | Microsoft. ApiManagement/Service/nadkończenie/zapis | Tworzy lub aktualizuje zaplecze. lub aktualizuje istniejące zaplecze. |
> | Działanie | Microsoft. ApiManagement/Service/kopia zapasowa/akcja | Usługa tworzenia kopii zapasowych API Management do określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Działanie | Microsoft. ApiManagement/Service/Caches/Delete | Usuwa określoną pamięć podręczną. |
> | Działanie | Microsoft. ApiManagement/Service/Caches/Read | Wyświetla kolekcję wszystkich zewnętrznych pamięci podręcznych w określonym wystąpieniu usługi. lub pobiera szczegóły pamięci podręcznej określonej przez jej identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Caches/Write | Tworzy lub aktualizuje zewnętrzną pamięć podręczną, która ma być używana w wystąpieniu usługi API Management. lub aktualizuje szczegóły pamięci podręcznej określonej przez jej identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Certificates/Delete | Usuwa określony certyfikat. |
> | Działanie | Microsoft. ApiManagement/Service/Certificates/Read | Wyświetla kolekcję wszystkich certyfikatów w określonym wystąpieniu usługi. lub pobiera szczegóły certyfikatu określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Certificates/Write | Tworzy lub aktualizuje certyfikat używany do uwierzytelniania w zapleczu. |
> | Działanie | Microsoft. ApiManagement/Service/contentTypes/contentItems/Delete | Usuwa określony element zawartości. |
> | Działanie | Microsoft. ApiManagement/Service/contentTypes/contentItems/Read | Zwraca listę elementów zawartości lub zwraca szczegóły elementu zawartości |
> | Działanie | Microsoft. ApiManagement/Service/contentTypes/contentItems/Write | Tworzy nowy element zawartości lub aktualizuje określony element zawartości |
> | Działanie | Microsoft. ApiManagement/Service/contentTypes/Read | Zwraca listę typów zawartości |
> | Działanie | Microsoft. ApiManagement/Service/Delete | Usuń wystąpienie usługi API Management |
> | Działanie | Microsoft. ApiManagement/Service/Diagnostics/Delete | Usuwa określoną diagnostykę. |
> | Działanie | Microsoft. ApiManagement/Service/Diagnostics/Read | Wyświetla listę wszystkich diagnostyki wystąpienia usługi API Management. lub pobiera szczegóły diagnostyki określone przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Diagnostics/Write | Tworzy nową diagnostykę lub aktualizuje istniejącą. lub aktualizuje szczegóły diagnostyki określonej przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/Action | Pobiera konfigurację bramy. lub aktualizuje puls bramy. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/Delete | Usuwa określoną bramę. |
> | Działanie | Microsoft. ApiManagement/Service/Gateway//klucze/akcja | Pobiera klucze bramy. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/Read | Wyświetla kolekcję bram zarejestrowanych w wystąpieniu usługi. lub pobiera szczegóły bramy określone przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/regeneratePrimaryKey/Action | Ponownie generuje podstawowy klucz bramy invalidationg wszystkie utworzone przez niego tokeny. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/regenerateSecondaryKey/Action | Ponownie generuje pomocniczy klucz bramy invalidationg wszystkie utworzone przez niego tokeny. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/token/Action | Pobiera token autoryzacji dostępu współdzielonego dla bramy. |
> | Działanie | Microsoft. ApiManagement/Service/Gateways/Write | Tworzy lub aktualizuje bramę do użycia w wystąpieniu usługi API Management. lub aktualizuje szczegóły bramy określone przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/getssotoken/Action | Pobiera token rejestracji jednokrotnej, którego można użyć do zalogowania się do portalu usługi API Management starszej wersji jako administrator |
> | Działanie | Microsoft. ApiManagement/Service/Groups/Delete | Usuwa konkretną grupę wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/Groups/Read | Wyświetla kolekcję grup zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły grupy określonej przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Groups/users/Delete | Usuń istniejącego użytkownika z istniejącej grupy. |
> | Działanie | Microsoft. ApiManagement/Service/Groups/users/Read | Wyświetla kolekcję jednostek użytkownika skojarzonych z grupą. |
> | Działanie | Microsoft. ApiManagement/Service/Groups/users/Write | Dodaj istniejącego użytkownika do istniejącej grupy |
> | Działanie | Microsoft. ApiManagement/Service/Groups/Write | Tworzy lub aktualizuje grupę. lub aktualizuje Szczegóły grupy określonej przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/skojarzeni/Delete | Usuwa określoną konfigurację dostawcy tożsamości. |
> | Działanie | Microsoft. ApiManagement/Service/skojarzeni/Read | Wyświetla kolekcję dostawców tożsamości skonfigurowanych w określonym wystąpieniu usługi. lub pobiera szczegóły konfiguracji dostawcy tożsamości skonfigurowanego w określonym wystąpieniu usługi. |
> | Działanie | Microsoft. ApiManagement/Service/skojarzeni/Write | Tworzy lub aktualizuje konfigurację IdentityProvider. lub aktualizuje istniejącą konfigurację IdentityProvider. |
> | Działanie | Microsoft. ApiManagement/Service/Issues/Read | Wyświetla kolekcję problemów w określonym wystąpieniu usługi. lub pobiera API Management Szczegóły problemu |
> | Działanie | Microsoft. ApiManagement/Service/Locations/NetworkStatus/Read | Pobiera stan dostępu do sieci dla zasobów, od których zależy usługa w danej lokalizacji. |
> | Działanie | Microsoft. ApiManagement/Service/rejestratory/usuwanie | Usuwa określony rejestrator. |
> | Działanie | Microsoft. ApiManagement/Service/rejestratory/odczyt | Wyświetla kolekcję rejestratorów w określonym wystąpieniu usługi. lub pobiera szczegóły rejestratora określony przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/rejestratory/zapis | Tworzy lub aktualizuje rejestrator. lub aktualizuje istniejący rejestrator. |
> | Działanie | Microsoft. ApiManagement/Service/managedeployments/Action | Zmień jednostkę SKU/jednostki, Dodaj/Usuń wdrożenia regionalne usługi API Management |
> | Działanie | Microsoft. ApiManagement/Service/NetworkStatus/Read | Pobiera stan dostępu do sieci dla zasobów, od których zależy usługa. |
> | Działanie | Microsoft. ApiManagement/Service/Notifications/Action | Wysyła powiadomienie do określonego użytkownika |
> | Działanie | Microsoft. ApiManagement/Service/Notifications/Read | Wyświetla kolekcję właściwości zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły powiadomienia określone przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Delete | Usuwa wiadomość e-mail z listy powiadomień. |
> | Działanie | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Read | Pobiera listę wiadomości E-mail dotyczących adresatów powiadomień subskrybowanych w ramach powiadomienia. |
> | Działanie | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Write | Dodaje adres E-mail do listy adresatów powiadomienia. |
> | Działanie | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Delete | Usuwa użytkownika API Management z listy powiadomień. |
> | Działanie | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Read | Pobiera listę użytkowników adresatów powiadomień, którzy zasubskrybują powiadomienie. |
> | Działanie | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Write | Dodaje API Management użytkownika do listy adresatów powiadomienia. |
> | Działanie | Microsoft. ApiManagement/Service/Notifications/Write | Utwórz lub zaktualizuj powiadomienia wydawcy API Management. |
> | Działanie | Microsoft. ApiManagement/Service/openidConnectProviders/Delete | Usuwa określonego dostawcę połączenia OpenID Connect dla wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/openidConnectProviders/Read | Listy wszystkich dostawców połączeń OpenID Connect. lub pobiera określonego dostawcę połączenia OpenID Connect. |
> | Działanie | Microsoft. ApiManagement/Service/openidConnectProviders/Write | Tworzy lub aktualizuje dostawcę połączenia OpenID Connect. lub aktualizuje określonego dostawcę połączenia OpenID Connect. |
> | Działanie | Microsoft. ApiManagement/Service/operationresults/Read | Pobiera bieżący stan długotrwałej operacji |
> | Działanie | Microsoft. ApiManagement/Service/policies/Delete | Usuwa globalną konfigurację zasad usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/policies/Read | Wyświetla wszystkie globalne definicje zasad usługi API Management. lub uzyskać definicję zasad globalnych usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/policies/Write | Tworzy lub aktualizuje globalną konfigurację zasad usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/Policy/Delete | Usuń konfigurację zasad na poziomie dzierżawy |
> | Działanie | Microsoft. ApiManagement/Service/Policy/Read | Pobierz konfigurację zasad na poziomie dzierżawy |
> | Działanie | Microsoft. ApiManagement/Service/Policy/Write | Utwórz konfigurację zasad na poziomie dzierżawy |
> | Działanie | Microsoft. ApiManagement/Service/policyDescriptions/Read | Wyświetla listę wszystkich opisów zasad. |
> | Działanie | Microsoft. ApiManagement/Service/policySnippets/Read | Wyświetla wszystkie fragmenty kodu zasad. |
> | Działanie | Microsoft. ApiManagement/Service/portalsettings/Read | Uzyskaj ustawienia logowania dla portalu lub uzyskaj ustawienia rejestracji w portalu lub uzyskaj ustawienia delegowania dla portalu. |
> | Działanie | Microsoft. ApiManagement/Service/portalsettings/Write | Zaktualizuj ustawienia logowania. lub Utwórz lub zaktualizuj ustawienia logowania. lub zaktualizuj ustawienia rejestracji lub zaktualizuj ustawienia rejestracji lub zaktualizuj ustawienia delegowania. lub Utwórz lub zaktualizuj ustawienia delegowania. |
> | Działanie | Microsoft. ApiManagement/Service/Products/API/Delete | Usuwa określony interfejs API z określonego produktu. |
> | Działanie | Microsoft. ApiManagement/Service/Products/API/odczyt | Wyświetla kolekcję interfejsów API skojarzonych z produktem. |
> | Działanie | Microsoft. ApiManagement/Service/Products/API/Write | Dodaje interfejs API do określonego produktu. |
> | Działanie | Microsoft. ApiManagement/Service/Products/Delete | Usuń produkt. |
> | Działanie | Microsoft. ApiManagement/Service/Products/Groups/Delete | Usuwa skojarzenie między określoną grupą i produktem. |
> | Działanie | Microsoft. ApiManagement/Service/Products/Groups/Read | Wyświetla listę grup deweloperów skojarzonych z określonym produktem. |
> | Działanie | Microsoft. ApiManagement/Service/Products/Groups/Write | Dodaje skojarzenie między określoną grupą deweloperów i określonym produktem. |
> | Działanie | Microsoft. ApiManagement/Service/Products/zasady/usuwanie | Usuwa konfigurację zasad w produkcie. |
> | Działanie | Microsoft. ApiManagement/Service/Products/zasady/odczyt | Pobierz konfigurację zasad na poziomie produktu. lub Pobierz konfigurację zasad na poziomie produktu. |
> | Działanie | Microsoft. ApiManagement/Service/Products/zasady/zapis | Tworzy lub aktualizuje konfigurację zasad dla produktu. |
> | Działanie | Microsoft. ApiManagement/Service/Products/Policy/Delete | Usuń konfigurację zasad na poziomie produktu |
> | Działanie | Microsoft. ApiManagement/Service/Products/Policy/odczyt | Pobierz konfigurację zasad na poziomie produktu |
> | Działanie | Microsoft. ApiManagement/Service/Products/Policy/Write | Utwórz konfigurację zasad na poziomie produktu |
> | Działanie | Microsoft. ApiManagement/Service/Products/Read | Wyświetla kolekcję produktów w określonym wystąpieniu usługi. lub pobiera szczegóły produktu określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Products/subscriptions/Read | Zawiera listę kolekcji subskrypcji dla określonego produktu. |
> | Działanie | Microsoft. ApiManagement/usługa/produkty/Tagi/Usuń | Odłącz tag od produktu. |
> | Działanie | Microsoft. ApiManagement/Service/Products/Tagi/odczyt | Wyświetla listę wszystkich tagów skojarzonych z produktem. lub Pobierz tag skojarzony z produktem. |
> | Działanie | Microsoft. ApiManagement/usługa/produkty/Tagi/zapis | Przypisz tag do produktu. |
> | Działanie | Microsoft. ApiManagement/Service/Products/Write | Tworzy lub aktualizuje produkt. lub zaktualizuj istniejące informacje o produkcie. |
> | Działanie | Microsoft. ApiManagement/Service/productsByTags/Read | Wyświetla kolekcję produktów skojarzonych ze znacznikami. |
> | Działanie | Microsoft. ApiManagement/Service/Properties/Delete | Usuwa określoną właściwość z wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/Properties/Read | Wyświetla kolekcję właściwości zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły właściwości określonej przez jej identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Properties/Write | Tworzy lub aktualizuje właściwość. lub aktualizuje konkretną właściwość. |
> | Działanie | Microsoft. ApiManagement/Service/limit przydziału/okresy/odczyt | Pobierz wartość licznika przydziału dla okresu |
> | Działanie | Microsoft. ApiManagement/Service/limit przydziału/okresy/zapis | Ustaw bieżącą wartość licznika przydziału |
> | Działanie | Microsoft. ApiManagement/Service/przydziały/odczyt | Pobierz wartości przydziału |
> | Działanie | Microsoft. ApiManagement/Service/przydziały/zapis | Ustaw bieżącą wartość licznika przydziału |
> | Działanie | Microsoft. ApiManagement/Service/Read | Odczytaj metadane dla wystąpienia usługi API Management |
> | Działanie | Microsoft. ApiManagement/Service/Regions/Read | Wyświetla listę wszystkich regionów świadczenia usługi Azure, w których istnieje usługa. |
> | Działanie | Microsoft. ApiManagement/Service/Reports/Read | Pobierz raport zagregowany według okresów lub Pobierz raport zagregowany według regionu geograficznego lub Pobierz raport zagregowany przez deweloperów.<br>lub uzyskać raport zagregowany według produktów.<br>lub uzyskać raport zagregowany przez interfejsy API lub uzyskać raport zagregowany według operacji lub uzyskać raport zagregowany według subskrypcji.<br>lub Pobierz żądania danych raportowania |
> | Działanie | Microsoft. ApiManagement/Service/Restore/Action | Przywracanie usługi API Management z określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Działanie | Microsoft. ApiManagement/Service/subscriptions/Delete | Usuwa określoną subskrypcję. |
> | Działanie | Microsoft. ApiManagement/Service/subscriptions/Read | Wyświetla wszystkie subskrypcje wystąpienia usługi API Management. lub pobiera określoną jednostkę subskrypcji. |
> | Działanie | Microsoft. ApiManagement/Service/subscriptions/regeneratePrimaryKey/Action | Ponownie generuje klucz podstawowy istniejącej subskrypcji wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/subscriptions/regenerateSecondaryKey/Action | Generuje ponownie klucz pomocniczy istniejącej subskrypcji wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/subscriptions/Write | Tworzy lub aktualizuje subskrypcję określonego użytkownika dla określonego produktu. lub aktualizuje szczegóły subskrypcji określonej przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/tagResources/Read | Wyświetla kolekcję zasobów skojarzonych ze znacznikami. |
> | Działanie | Microsoft. ApiManagement/Service/Tagi/Usuń | Usuwa konkretny tag wystąpienia usługi API Management. |
> | Działanie | Microsoft. ApiManagement/Service/Tags/odczyt | Wyświetla kolekcję tagów zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły tagu określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Tags/Write | Tworzy tag. lub aktualizuje szczegóły tagu określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/templates/Delete | Resetuj szablon wiadomości e-mail domyślnego API Management |
> | Działanie | Microsoft. ApiManagement/Service/templates/Read | Pobiera wszystkie szablony wiadomości e-mail lub pobiera API Management szczegóły szablonu wiadomości e-mail |
> | Działanie | Microsoft. ApiManagement/Service/templates/Write | Utwórz lub zaktualizuj szablon wiadomości e-mail API Management lub API Management szablon wiadomości e-mail |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/usuwanie | Usuń konfigurację zasad dla dzierżawy |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/wdrożenie/akcja | Uruchamia zadanie wdrażania, aby zastosować zmiany z określonej gałęzi git do konfiguracji w bazie danych. |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/operationResults/odczyt | Pobierz listę wyników operacji lub Pobierz wynik określonej operacji |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/odczyt | Pobierz definicję zasad globalnych usługi API Management. lub Uzyskaj szczegółowe informacje o dostępie dzierżawy |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/regeneratePrimaryKey/akcja | Wygeneruj ponownie podstawowy klucz dostępu |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/regenerateSecondaryKey/akcja | Ponowne generowanie pomocniczego klucza dostępu |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/Zapisz/akcja | Tworzy potwierdzeń z migawką konfiguracji do określonej gałęzi w repozytorium |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/syncState/odczyt | Pobierz stan ostatniej synchronizacji git |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/weryfikacja/akcja | Sprawdza poprawność zmian z określonej gałęzi git |
> | Działanie | Microsoft. ApiManagement/Service/dzierżawca/zapis | Ustaw konfigurację zasad dla dzierżawy lub Zaktualizuj szczegóły informacji o dostępie dzierżawy |
> | Działanie | Microsoft. ApiManagement/Service/updatecertificate/Action | Przekaż certyfikat SSL dla usługi API Management |
> | Działanie | Microsoft. ApiManagement/Service/updatehostname/Action | Instalowanie, aktualizowanie lub usuwanie niestandardowych nazw domen dla usługi API Management |
> | Działanie | Microsoft. ApiManagement/Service/users/Action | Rejestrowanie nowego użytkownika |
> | Działanie | Microsoft. ApiManagement/Service/users/CONFIRMS/Send/Action | Wysyła potwierdzenie |
> | Działanie | Microsoft. ApiManagement/Service/users/Delete | Usuwa określonego użytkownika. |
> | Działanie | Microsoft. ApiManagement/Service/users/generateSsoUrl/Action | Pobiera adres URL przekierowania zawierający token uwierzytelniania do podpisywania danego użytkownika w portalu dla deweloperów. |
> | Działanie | Microsoft. ApiManagement/Service/users/Groups/Read | Wyświetla listę wszystkich grup użytkowników. |
> | Działanie | Microsoft. ApiManagement/Service/users/tożsamość/odczyt | Lista wszystkich tożsamości użytkowników. |
> | Działanie | Microsoft. ApiManagement/Service/users/klucze/odczyt | Pobierz klucze skojarzone z użytkownikiem |
> | Działanie | Microsoft. ApiManagement/Service/users/odczyt | Wyświetla kolekcję zarejestrowanych użytkowników w określonym wystąpieniu usługi. lub pobiera szczegóły użytkownika określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/users/subscriptions/Read | Zawiera listę kolekcji subskrypcji określonego użytkownika. |
> | Działanie | Microsoft. ApiManagement/Service/users/token/Action | Pobiera token autoryzacji dostępu współdzielonego dla użytkownika. |
> | Działanie | Microsoft. ApiManagement/Service/users/Write | Tworzy lub aktualizuje użytkownika. lub aktualizuje szczegóły użytkownika określonego przez jego identyfikator. |
> | Działanie | Microsoft. ApiManagement/Service/Write | Utwórz nowe wystąpienie usługi API Management Service |
> | Działanie | Microsoft. ApiManagement/Unregister/Action | Wyrejestrowywanie subskrypcji dla dostawcy zasobów Microsoft. ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft. autoryzacja

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
> | Działanie | Microsoft. Authorization/elevateAccess/akcja | Przyznaje administratorowi dostępu użytkownika wywołującego w zakresie dzierżawy |
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
> | Działanie | Microsoft. Authorization/roleDefinitions/Read | Pobierz informacje o definicji roli. |
> | Działanie | Microsoft. Authorization/roleDefinitions/Write | Utwórz lub zaktualizuj niestandardową definicję roli z określonymi uprawnieniami i możliwymi do przypisania zakresami. |

## <a name="microsoftautomation"></a>Microsoft. Automation

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
> | Działanie | Microsoft. Automation/automationAccounts/konfiguracje/GetCount/Action | Odczytuje liczbę zawartości Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/konfiguracje/odczyt | Pobiera zawartość Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/konfiguracje/zapis | Zapisuje zawartość Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/Connections/Delete | Usuwa zasób połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Connections/akcja | Odczytuje liczbę połączeń |
> | Działanie | Microsoft. Automation/automationAccounts/Connections/Read | Pobiera zasób połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Connections/Write | Tworzy lub aktualizuje zasób połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/connectionTypes/Delete | Usuwa zasób typu połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/connectionTypes/odczyt | Pobiera zasób typu połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/connectionTypes/Write | Tworzy zasób typu połączenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/poświadczenia/usuwanie | Usuwa zasób poświadczenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Credentials/GetCount/Action | Odczytuje liczbę poświadczeń |
> | Działanie | Microsoft. Automation/automationAccounts/poświadczenia/odczyt | Pobiera zasób poświadczenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/poświadczenia/zapis | Tworzy lub aktualizuje zasób poświadczenia Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Delete | Usuwa konto Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Delete | Usuwa zasoby hybrydowego procesu roboczego elementu Runbook |
> | Działanie | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/odczyt | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/Read | Pobiera zadanie Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/Resume/Action | Wznawia zadanie Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/runbookContent/Action | Pobiera zawartość elementu Runbook Azure Automation w czasie wykonywania zadania |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/akcja | Kończy zadanie Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Pobiera strumień zadania Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Pobiera strumień zadania Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/Suspend/Action | Wstrzymuje zadanie Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Jobs/Write | Tworzy zadanie Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/jobSchedules/Delete | Usuwa harmonogram zadań Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/jobSchedules/odczyt | Pobiera harmonogram zadań Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/jobSchedules/Write | Tworzy harmonogram zadań Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/linkedWorkspace/odczyt | Pobiera obszar roboczy połączony z kontem usługi Automation |
> | Działanie | Microsoft. Automation/automationAccounts/listKeys/akcja | Odczytuje klucze dla konta usługi Automation |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/działania/odczyt | Pobiera działania Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/usuwanie | Usuwa moduł Azure Automation PowerShell |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/GetCount/Action | Pobiera liczbę modułów programu PowerShell w ramach konta usługi Automation |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/odczyt | Pobiera moduł Azure Automation PowerShell |
> | Działanie | Microsoft. Automation/automationAccounts/moduły/zapis | Tworzy lub aktualizuje moduł Azure Automation PowerShell |
> | Działanie | Microsoft. Automation/automationAccounts/nodeConfigurations/Delete | Usuwa konfigurację węzła Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodeConfigurations/rawContent/Action | Odczytuje zawartość konfiguracji węzła Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodeConfigurations/odczyt | Odczytuje konfigurację węzła Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodeConfigurations/Write | Zapisuje konfigurację węzła Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodecounts/odczyt | Odczytuje podsumowanie liczby węzłów dla określonego typu |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Delete | Usuwa Azure Automation węzły DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Read | Odczytuje Azure Automation węzłów DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Reports/Content/Read | Odczytuje zawartość raportu Azure Automation DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Reports/Read | Odczytuje Azure Automation raporty DSC |
> | Działanie | Microsoft. Automation/automationAccounts/nodes/Write | Tworzy lub aktualizuje Azure Automation węzłów DSC |
> | Działanie | Microsoft. Automation/automationAccounts/objectDataTypes/Fields/Read | Pobiera Azure Automation TypeFields |
> | Działanie | Microsoft. Automation/automationAccounts/python2Packages/Delete | Usuwa pakiet Azure Automation Python 2 |
> | Działanie | Microsoft. Automation/automationAccounts/python2Packages/odczyt | Pobiera pakiet Azure Automation Python 2 |
> | Działanie | Microsoft. Automation/automationAccounts/python2Packages/Write | Tworzy lub aktualizuje pakiet Azure Automation Python 2 |
> | Działanie | Microsoft. Automation/automationAccounts/python3Packages/Delete | Usuwa pakiet Azure Automation Python 3 |
> | Działanie | Microsoft. Automation/automationAccounts/python3Packages/odczyt | Pobiera pakiet Azure Automation Python 3 |
> | Działanie | Microsoft. Automation/automationAccounts/python3Packages/Write | Tworzy lub aktualizuje pakiet Azure Automation Python 3 |
> | Działanie | Microsoft. Automation/automationAccounts/Read | Pobiera konto Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Content/Read | Pobiera zawartość elementu Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Delete | Usuwa element Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Draft/Content/Write | Tworzy zawartość wersji roboczej elementu Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Draft/operationResults/Read | Pobiera wyniki operacji wersji roboczej elementu Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Draft/Read | Pobiera wersję roboczą elementu Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/Read | Pobiera zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/Resume/Action | Wznawia zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/testJob/Stop/Action | Kończy zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/Suspend/Action | Wstrzymuje zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/testJob/Write | Tworzy zadanie testowe wersji roboczej elementu Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/undoEdit/Action | Cofnij edycję wersji roboczej elementu Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/GetCount/Action | Pobiera liczbę elementów Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Publish/Action | Publikuje wersję roboczą elementu Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Read | Pobiera element Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Runbooks/Write | Tworzy lub aktualizuje element Runbook Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/harmonogramy/usuwanie | Usuwa zasób harmonogramu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Schedules/GetCount/Action | Pobiera liczbę harmonogramów Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Schedules/Read | Pobiera zasób harmonogramu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/Schedules/Write | Tworzy lub aktualizuje zasób harmonogramu Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/odczyt | Pobiera Azure Automation uruchomienie komputera z konfiguracją aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/odczyt | Pobiera Azure Automation uruchomienie konfiguracji aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Delete | Usuwa Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Delete | Usuwa Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/odczyt | Pobiera Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/odczyt | Pobiera Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Tworzy lub aktualizuje Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Tworzy lub aktualizuje Azure Automation konfigurację aktualizacji oprogramowania |
> | Działanie | Microsoft. Automation/automationAccounts/Statystyka/odczyt | Pobiera statystyki Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/updateDeploymentMachineRuns/odczyt | Pobierz maszynę wdrożenia aktualizacji Azure Automation |
> | Działanie | Microsoft. Automation/automationAccounts/updateManagementPatchJob/odczyt | Pobiera zadanie aktualizacji Azure Automation Update Management |
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
> | Działanie | Microsoft. Automation/automationAccounts/Write | Tworzy lub aktualizuje konto Azure Automation |
> | Działanie | Microsoft. Automation/Operations/Read | Pobiera dostępne operacje dla Azure Automation zasobów |
> | Działanie | Microsoft. Automation/rejestrowanie/akcja | Rejestruje subskrypcję w usłudze Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft. usługi azureactivedirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. usługi azureactivedirectory/b2cDirectories/Delete | Usuń zasób katalogu B2C |
> | Działanie | Microsoft. usługi azureactivedirectory/b2cDirectories/odczyt | Wyświetl zasób katalogu B2C |
> | Działanie | Microsoft. usługi azureactivedirectory/b2cDirectories/Write | Utwórz lub zaktualizuj zasób katalogu B2C |
> | Działanie | Microsoft. usługi azureactivedirectory/b2ctenants/odczyt | Wyświetla wszystkie dzierżawy B2C, w których użytkownik jest członkiem |
> | Działanie | Microsoft. usługi azureactivedirectory/Operations/Read | Przeczytaj wszystkie operacje interfejsu API dostępne dla dostawcy zasobów Microsoft. usługi azureactivedirectory |
> | Działanie | Microsoft. usługi azureactivedirectory/Register/Action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. usługi azureactivedirectory |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. AzureStack/Operations/Read | Pobiera właściwości operacji dostawcy zasobów |
> | Działanie | Microsoft. AzureStack/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. AzureStack |
> | Działanie | Microsoft. AzureStack/registrations/customerSubscriptions/Delete | Usuwa subskrypcję klienta Azure Stack |
> | Działanie | Microsoft. AzureStack/registrations/customerSubscriptions/Read | Pobiera właściwości subskrypcji klienta Azure Stack |
> | Działanie | Microsoft. AzureStack/registrations/customerSubscriptions/Write | Tworzy lub aktualizuje subskrypcję klienta Azure Stack |
> | Działanie | Microsoft. AzureStack/rejestracje/usuwanie | Usuwa rejestrację Azure Stack |
> | Działanie | Microsoft. AzureStack/registrations/getActivationKey/Action | Pobiera najnowszy klucz aktywacji Azure Stack |
> | Działanie | Microsoft. AzureStack/rejestracje/produkty/listDetails/akcja | Pobiera rozszerzone szczegóły dotyczące produktu Azure Stack Marketplace |
> | Działanie | Microsoft. AzureStack/rejestracje/produkty/odczyt | Pobiera właściwości produktu Azure Stack Marketplace |
> | Działanie | Microsoft. AzureStack/rejestracje/produkty/uploadProductLog/akcja | Rejestrowanie stanu i sygnatury czasowej operacji produktu Azure Stack Marketplace |
> | Działanie | Microsoft. AzureStack/rejestracje/odczyt | Pobiera właściwości rejestracji Azure Stack |
> | Działanie | Microsoft. AzureStack/rejestracje/zapis | Tworzy lub aktualizuje rejestrację Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Batch/batchAccounts/Applications/Delete | Usuwa aplikację |
> | Działanie | Microsoft. Batch/batchAccounts/Applications/Read | Wyświetla listę aplikacji lub pobiera właściwości aplikacji |
> | Działanie | Microsoft. Batch/batchAccounts/Applications/Versions/Activate/Action | Aktywuje pakiet aplikacji |
> | Działanie | Microsoft. Batch/batchAccounts/Applications/Versions/Delete | Usuwa pakiet aplikacji |
> | Działanie | Microsoft. Batch/batchAccounts/Applications/Versions/Read | Pobiera właściwości pakietu aplikacji |
> | Działanie | Microsoft. Batch/batchAccounts/Applications/Versions/Write | Tworzy nowy pakiet aplikacji lub aktualizuje istniejący pakiet aplikacji |
> | Działanie | Microsoft. Batch/batchAccounts/Applications/Write | Tworzy nową aplikację lub aktualizuje istniejącą aplikację |
> | Działanie | Microsoft. Batch/batchAccounts/certificateOperationResults/odczyt | Pobiera wyniki długotrwałej operacji certyfikatu na koncie wsadowym |
> | Działanie | Microsoft. Batch/batchAccounts/Certificates/cancelDelete/Action | Anuluje nieudane usunięcie certyfikatu na koncie wsadowym |
> | Działanie | Microsoft. Batch/batchAccounts/Certificates/Delete | Usuwa certyfikat z konta usługi Batch |
> | Działanie | Microsoft. Batch/batchAccounts/Certificates/Read | Wyświetla listę certyfikatów na koncie wsadowym lub pobiera właściwości certyfikatu |
> | Działanie | Microsoft. Batch/batchAccounts/Certificates/Write | Tworzy nowy certyfikat na koncie wsadowym lub aktualizuje istniejący certyfikat |
> | Działanie | Microsoft. Batch/batchAccounts/Delete | Usuwa konto wsadowe |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Delete | Usuwa zadanie z konta usługi Batch |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Read | Wyświetla listę zadań na koncie wsadowym lub pobiera właściwości zadania |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Write | Tworzy nowe zadanie na koncie wsadowym lub aktualizuje istniejące zadanie |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Delete | Usuwa harmonogram zadań z konta usługi Batch |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/odczyt | Wyświetla listę harmonogramów zadań na koncie wsadowym lub pobiera właściwości harmonogramu zadań |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Write | Tworzy nowy harmonogram zadań na koncie wsadowym lub aktualizuje istniejący harmonogram zadań |
> | Działanie | Microsoft. Batch/batchAccounts/ListKeys/Action | Wyświetla klucze dostępu dla konta usługi Batch |
> | Działanie | Microsoft. Batch/batchAccounts/operationResults/odczyt | Pobiera wyniki długotrwałej operacji konta partii |
> | Działanie | Microsoft. Batch/batchAccounts/poolOperationResults/odczyt | Pobiera wyniki długotrwałej operacji puli na koncie wsadowym |
> | Działanie | Microsoft. Batch/batchAccounts/pule/usuwanie | Usuwa pulę z konta usługi Batch |
> | Działanie | Microsoft. Batch/batchAccounts/pule/disableAutoscale/akcja | Wyłącza automatyczne skalowanie puli kont usługi Batch |
> | Działanie | Microsoft. Batch/batchAccounts/pule/odczyt | Wyświetla listę pul na koncie wsadowym lub pobiera właściwości puli |
> | Działanie | Microsoft. Batch/batchAccounts/pule/stopResize/akcja | Kończy trwającą operację zmiany rozmiaru w puli kont wsadowych |
> | Działanie | Microsoft. Batch/batchAccounts/pule/zapis | Tworzy nową pulę na koncie wsadowym lub aktualizuje istniejącą pulę |
> | Działanie | Microsoft. Batch/batchAccounts/Read | Wyświetla listę kont wsadowych lub pobiera właściwości konta w usłudze Batch |
> | Działanie | Microsoft. Batch/batchAccounts/regeneratekeys/Action | Generuje ponownie klucze dostępu dla konta usługi Batch |
> | Działanie | Microsoft. Batch/batchAccounts/syncAutoStorageKeys/Action | Synchronizuje klucze dostępu dla konta usługi autostorage skonfigurowanego dla konta usługi Batch |
> | Działanie | Microsoft. Batch/batchAccounts/Write | Tworzy nowe konto wsadowe lub aktualizuje istniejące konto w usłudze Batch |
> | Działanie | Microsoft. Batch/Locations/accountOperationResults/Read | Pobiera wyniki długotrwałej operacji konta partii |
> | Działanie | Microsoft. Batch/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft. Batch/lokalizacje/przydziały/odczyt | Pobiera przydziały usługi Batch dla określonej subskrypcji w określonym regionie platformy Azure |
> | Działanie | Microsoft. Batch/Operations/Read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. Batch |
> | Działanie | Microsoft. Batch/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi Batch i umożliwia tworzenie kont usługi Batch |
> | Działanie | Microsoft. Batch/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Batch uniemożliwiającą tworzenie kont usługi Batch |

## <a name="microsoftbilling"></a>Microsoft. rozliczenia

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
> | Działanie | Microsoft. rozliczenia/rejestr/akcja |  |
> | Działanie | Microsoft. rozliczenia/validateAddress/akcja |  |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. BingMaps/mapApis/Delete | Operacja usuwania |
> | Działanie | Microsoft. BingMaps/mapApis/listSecrets/akcja | Wystaw wpisy tajne |
> | Działanie | Microsoft. BingMaps/mapApis/listSingleSignOnToken/akcja | Odczytaj Token autoryzacji logowania jednokrotnego dla zasobu |
> | Działanie | Microsoft. BingMaps/mapApis/odczyt | Operacja odczytu |
> | Działanie | Microsoft. BingMaps/mapApis/regenerateKey/akcja | Generuje ponownie klucz |
> | Działanie | Microsoft. BingMaps/mapApis/Write | Operacja zapisu |
> | Działanie | Microsoft. BingMaps/Operations/Read | Opis operacji. |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/Delete | Usuwa istniejącego członka łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/listApiKeys/akcja | Pobiera lub Wyświetla istniejące klucze interfejsu API elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/odczyt | Pobiera lub Wyświetla istniejące elementy członkowskie łańcucha bloków. |
> | Akcja dataaction | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Connect/Action | Nawiązuje połączenie z węzłem transakcji elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Delete | Usuwa istniejący węzeł transakcji elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/listApiKeys/Action | Pobiera lub Wyświetla istniejące klucze interfejsu API węzła transakcji elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Read | Pobiera lub Wyświetla istniejące węzły transakcji składowych łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Write | Tworzy lub aktualizuje węzeł transakcji elementu członkowskiego łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/blockchainMembers/Write | Tworzy lub aktualizuje element członkowski łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/Locations/blockchainMemberOperationResults/Read | Pobiera wyniki operacji elementów członkowskich łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa zasobu jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft. łańcucha bloków/Operations/Read | Wyświetl listę wszystkich operacji w dostawcy zasobów programu Microsoft łańcucha bloków. |
> | Działanie | Microsoft. łańcucha bloków/Register/Action | Rejestruje subskrypcję dostawcy zasobów łańcucha bloków. |

## <a name="microsoftblueprint"></a>Microsoft. plan

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

## <a name="microsoftbotservice"></a>Microsoft. BotService

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
> | Działanie | Microsoft. BotService/Locations/operationresults/Read | Odczytywanie stanu operacji asynchronicznej |
> | Działanie | Microsoft. BotService/Operations/Read | Odczytaj operacje dla wszystkich typów zasobów |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. cache/checknameavailability/akcja | Sprawdza, czy nazwa jest dostępna do użycia z nowym Redis Cache |
> | Działanie | Microsoft. cache/Locations/operationresults/Read | Pobiera wynik długotrwałej operacji, dla której nagłówek "Location" został wcześniej zwrócony do klienta |
> | Działanie | Microsoft. cache/Operations/Read | Wyświetla listę operacji obsługiwanych przez dostawcę "Microsoft. cache". |
> | Działanie | Microsoft. cache/Redis/Delete | Usuń cały Redis Cache |
> | Działanie | Microsoft. cache/Redis/eksport/akcja | Eksportuj dane Redis do przedprawionych obiektów blob magazynu w określonym formacie |
> | Działanie | Microsoft. cache/Redis/firewallRules/Delete | Usuwanie reguł zapory adresów IP Redis Cache |
> | Działanie | Microsoft. cache/Redis/firewallRules/odczyt | Pobieranie reguł zapory adresów IP Redis Cache |
> | Działanie | Microsoft. cache/Redis/firewallRules/zapis | Edytowanie reguł zapory adresów IP Redis Cache |
> | Działanie | Microsoft. cache/Redis/forceReboot/akcja | Wymuś ponowne uruchomienie wystąpienia pamięci podręcznej, potencjalnie z utratą danych. |
> | Działanie | Microsoft. cache/Redis/import/akcja | Importuj dane określonego formatu z wielu obiektów BLOB do Redis |
> | Działanie | Microsoft. cache/Redis/LinkedServers/Delete | Usuwanie połączonego serwera z Redis Cache |
> | Działanie | Microsoft. cache/Redis/LinkedServers/odczyt | Pobierz połączone serwery skojarzone z pamięcią podręczną Redis. |
> | Działanie | Microsoft. cache/Redis/LinkedServers/zapis | Dodawanie połączonego serwera do Redis Cache |
> | Działanie | Microsoft. cache/Redis/listKeys/akcja | Wyświetlanie wartości Redis Cache kluczy dostępu w portalu zarządzania |
> | Działanie | Microsoft. cache/Redis/listUpgradeNotifications/odczyt | Wyświetl listę najnowszych powiadomień dotyczących uaktualnienia dla dzierżawy pamięci podręcznej. |
> | Działanie | Microsoft. cache/Redis/metricDefinitions/odczyt | Pobiera dostępne metryki dla Redis Cache |
> | Działanie | Microsoft. cache/Redis/patchSchedules/Delete | Usuwanie harmonogramu poprawek Redis Cache |
> | Działanie | Microsoft. cache/Redis/patchSchedules/odczyt | Pobiera harmonogram stosowania poprawek Redis Cache |
> | Działanie | Microsoft. cache/Redis/patchSchedules/zapis | Modyfikowanie harmonogramu poprawek Redis Cache |
> | Działanie | Microsoft. cache/Redis/odczyt | Wyświetlanie ustawień i konfiguracji Redis Cache w portalu zarządzania |
> | Działanie | Microsoft. cache/Redis/regenerateKey/akcja | Zmiana wartości Redis Cache kluczy dostępu w portalu zarządzania |
> | Działanie | Microsoft. cache/Redis/Start/akcja | Uruchom wystąpienie pamięci podręcznej. |
> | Działanie | Microsoft. cache/Redis/Stop/akcja | Zatrzymaj wystąpienie pamięci podręcznej. |
> | Działanie | Microsoft. cache/Redis/Write | Modyfikowanie ustawień i konfiguracji Redis Cache w portalu zarządzania |
> | Działanie | Microsoft. cache/Register/Action | Rejestruje dostawcę zasobów "Microsoft. cache" z subskrypcją |
> | Działanie | Microsoft. cache/Unregister/akcja | Wyrejestrowuje dostawcę zasobów "Microsoft. cache" z subskrypcją |

## <a name="microsoftcapacity"></a>Microsoft. Pojemność

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Pojemność/appliedreservations/odczyt | Odczytuj wszystkie rezerwacje |
> | Działanie | Microsoft. Pojemność/calculateexchange/akcja | Oblicza kwotę i cenę wymiany nowych zakupów i zwraca błędy zasad. |
> | Działanie | Microsoft. Pojemność/calculateprice/akcja | Oblicz dowolną cenę rezerwacji |
> | Działanie | Microsoft. Pojemność/wykazy/odczyt | Odczytaj wykaz rezerwacji |
> | Działanie | Microsoft. Pojemność/checkoffers/akcja | Sprawdź wszystkie oferty subskrypcji |
> | Działanie | Microsoft. Pojemność/checkscopes/akcja | Sprawdź dowolną subskrypcję |
> | Działanie | Microsoft. Pojemność/commercialreservationorders/odczyt | Pobieranie zamówień rezerwacji utworzonych w dowolnej dzierżawie |
> | Działanie | Microsoft. Pojemność/wymiana/akcja | Wymiana dowolnej rezerwacji |
> | Działanie | Microsoft. Pojemność/operacje/odczyt | Odczytaj dowolną operację |
> | Działanie | Microsoft. Pojemność/rejestr/akcja | Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie zasobów pojemności. |
> | Działanie | Microsoft. Pojemność/reservationorders/akcja | Aktualizowanie dowolnej rezerwacji |
> | Działanie | Microsoft. Pojemność/reservationorders/availablescopes/akcja | Znajdowanie dowolnego dostępnego zakresu |
> | Działanie | Microsoft. Pojemność/reservationorders/calculaterefund/akcja | Oblicza kwotę zwrotu i cenę nowego zakupu oraz zwraca błędy zasad. |
> | Działanie | Microsoft. Pojemność/reservationorders/usuwanie | Usuń dowolne zastrzeżenie |
> | Działanie | Microsoft. Pojemność/reservationorders/scalanie/akcja | Scalanie wszelkich rezerwacji |
> | Działanie | Microsoft. Pojemność/reservationorders/odczyt | Odczytuj wszystkie rezerwacje |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/akcja | Aktualizowanie dowolnej rezerwacji |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/availablescopes/akcja | Znajdowanie dowolnego dostępnego zakresu |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/usuwanie | Usuń dowolne zastrzeżenie |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/odczyt | Odczytuj wszystkie rezerwacje |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/poprawki/odczyt | Odczytuj wszystkie rezerwacje |
> | Działanie | Microsoft. Pojemność/reservationorders/rezerwacje/zapis | Utwórz dowolną rezerwację |
> | Działanie | Microsoft. Pojemność/reservationorders/powrót/akcja | Zwróć wszelkie rezerwacje |
> | Działanie | Microsoft. Pojemność/reservationorders/Split/akcja | Podziel wszystkie rezerwacje |
> | Działanie | Microsoft. Pojemność/reservationorders/swap/akcja | Zamień wszystkie rezerwacje |
> | Działanie | Microsoft. Pojemność/reservationorders/zapis | Utwórz dowolną rezerwację |
> | Działanie | Microsoft. Pojemność/dzierżawcy/rejestr/akcja | Rejestracja dowolnej dzierżawy |
> | Działanie | Microsoft. Pojemność/Wyrejestrowanie/akcja | Wyrejestrowywanie dzierżawy |
> | Działanie | Microsoft. Pojemność/validatereservationorder/akcja | Weryfikowanie wszelkich rezerwacji |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. CDN/CheckNameAvailability/akcja |  |
> | Działanie | Microsoft. CDN/CheckResourceUsage/akcja |  |
> | Działanie | Microsoft. CDN/edgenodes/Delete |  |
> | Działanie | Microsoft. CDN/edgenodes/odczyt |  |
> | Działanie | Microsoft. CDN/edgenodes/Write |  |
> | Działanie | Microsoft. CDN/operationresults/Delete |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/CheckResourceUsage/Action |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/Delete |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/CheckResourceUsage/Action |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Delete |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/Action |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/Action |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Read |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Write |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/Delete |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/obciążenie/akcja |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Delete |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Read |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Write |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/przeczyszczanie/akcja |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/Read |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/Start/akcja |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/Stop/akcja |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/ValidateCustomDomain/Action |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/endpointresults/zapis |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/GenerateSsoUri/Action |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/GetSupportedOptimizationTypes/Action |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/odczyt |  |
> | Działanie | Microsoft. CDN/operationresults/profileresults/zapis |  |
> | Działanie | Microsoft. CDN/operationresults/odczyt |  |
> | Działanie | Microsoft. CDN/operationresults/Write |  |
> | Działanie | Microsoft. CDN/Operations/Read |  |
> | Działanie | Microsoft. CDN/profile/CheckResourceUsage/akcja |  |
> | Działanie | Microsoft. CDN/profile/usuwanie |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/CheckResourceUsage/akcja |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/customdomains/Delete |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/customdomains/DisableCustomHttps/Action |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/customdomains/EnableCustomHttps/Action |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/customdomains/odczyt |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/customdomains/Write |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/usuwanie |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/obciążenie/akcja |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/pochodzenia/usuwanie |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/źródła/odczyt |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/źródła/zapis |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/Przeczyść/akcja |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/odczyt |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/uruchomienie/akcja |  |
> | Działanie | Microsoft. CDN/profile/Endpoints/akcja |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/ValidateCustomDomain/akcja |  |
> | Działanie | Microsoft. CDN/profile/punkty końcowe/zapis |  |
> | Działanie | Microsoft. CDN/profile/GenerateSsoUri/akcja |  |
> | Działanie | Microsoft. CDN/profile/GetSupportedOptimizationTypes/akcja |  |
> | Działanie | Microsoft. CDN/profile/odczyt |  |
> | Działanie | Microsoft. CDN/profile/zapis |  |
> | Działanie | Microsoft. CDN/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi CDN i umożliwia tworzenie profilów sieci CDN. |
> | Działanie | Microsoft. CDN/ValidateProbe/akcja |  |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/Certificates/Delete | Usuń istniejący certyfikat |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/Certificates/Read | Pobieranie listy certyfikatów |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/Certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejący |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/Delete | Usuń istniejący AppServiceCertificate |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/odczyt | Pobierz listę CertificateOrders |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/Reissue/akcja | Wydaj ponownie istniejące zamówienia certyfikatu |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/Renew/akcja | Odnów istniejący zamówienia certyfikatu |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/resendEmail/akcja | Wyślij ponownie wiadomość e-mail z certyfikatem |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/resendRequestEmails/akcja | Wyślij ponownie wiadomości e-mail z żądaniem na inny adres e-mail |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/resendRequestEmails/akcja | Pobierz pieczęć witryny dla wystawionego Certyfikat usługi App Service |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/retrieveCertificateActions/akcja | Pobieranie listy akcji certyfikatów |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/retrieveEmailHistory/akcja | Pobierz historię poczty e-mail certyfikatu |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/verifyDomainOwnership/akcja | Weryfikuj własność domeny |
> | Działanie | Microsoft. CertificateRegistration/certificateOrders/Write | Dodaj nowy zamówienia certyfikatu lub zaktualizuj istniejący |
> | Działanie | Microsoft. CertificateRegistration/Operations/Read | Wyświetl listę wszystkich operacji z rejestracji certyfikatu usługi App Service |
> | Działanie | Microsoft. CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Zainicjuj obsługę główną usługi dla jednostki aplikacji usługi |
> | Działanie | Microsoft. CertificateRegistration/Register/Action | Zarejestruj dostawcę zasobów Microsoft Certificates dla subskrypcji |
> | Działanie | Microsoft. CertificateRegistration/validateCertificateRegistrationInformation/Action | Weryfikowanie obiektu zakupu certyfikatu bez jego przesyłania |

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
> | Działanie | Microsoft. ClassicCompute/domainNames/Delete | Usuń nazwy domen dla zasobów. |
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
> | Działanie | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/Read | Odczytuje stan operacji dla wewnętrznych modułów równoważenia obciążenia nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Read | Pobiera wewnętrzne moduły równoważenia obciążenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Write | Tworzy nowe wewnętrzne równoważenie obciążenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/Read | Odczytuje stan operacji dla zestawów punktów końcowych z równoważeniem obciążenia nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Read | Pobierz zestawy punktów końcowych ze zrównoważonym obciążeniem. |
> | Działanie | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Write | Dodaj zestaw punktów końcowych ze zrównoważonym obciążeniem. |
> | Działanie | Microsoft. ClassicCompute/domainNames/operationstatuses/Read | Pobierz stan operacji dla nazwy domeny. |
> | Działanie | Microsoft. ClassicCompute/domainNames/operationStatuses/Read | Odczytuje stan operacji dla rozszerzeń nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/odczyt | Zwróć nazwy domen dla zasobów. |
> | Działanie | Microsoft. ClassicCompute/domainNames/servicecertificates/Delete | Usuń używane certyfikaty usługi. |
> | Działanie | Microsoft. ClassicCompute/domainNames/servicecertificates/operationStatuses/Read | Odczytuje stan operacji dla certyfikatów usługi nazw domen. |
> | Działanie | Microsoft. ClassicCompute/domainNames/servicecertificates/Read | Zwraca używane certyfikaty usługi. |
> | Działanie | Microsoft. ClassicCompute/domainNames/servicecertificates/Write | Dodaj lub zmodyfikuj używane certyfikaty usługi. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/abortMigration/Action | Przerywa migrację miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/commitMigration/Action | Zatwierdza migrację miejsca wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/Delete | Usuwa określone miejsce wdrożenia. |
> | Działanie | Microsoft. ClassicCompute/domainNames/Slots/operationStatuses/Read | Odczytuje stan operacji dla gniazd nazw domen. |
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
> | Działanie | Microsoft. ClassicCompute/domainNames/Write | Dodaj lub zmodyfikuj nazwy domen dla zasobów. |
> | Działanie | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Przenieś wszystkie zasoby klasyczne do innej subskrypcji. |
> | Działanie | Microsoft. ClassicCompute/operatingSystemFamilies/odczyt | Wyświetla listę rodzin systemów operacyjnych gościa dostępnych w Microsoft Azure, a także listę wersji systemu operacyjnego dostępnych dla każdej rodziny. |
> | Działanie | Microsoft. ClassicCompute/operatingSystems/odczyt | Wyświetla listę wersji systemu operacyjnego gościa, które są obecnie dostępne w Microsoft Azure. |
> | Działanie | Microsoft. ClassicCompute/Operations/Read | Pobiera listę operacji. |
> | Działanie | Microsoft. ClassicCompute/operationStatuses/odczyt | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft. ClassicCompute/przydziały/odczyt | Pobieranie przydziału dla subskrypcji. |
> | Działanie | Microsoft. ClassicCompute/Register/Action | Zarejestruj się w ramach obliczeń klasycznych |
> | Działanie | Microsoft. ClassicCompute/resourceTypes/SKU/odczyt | Pobiera listę jednostek SKU dla obsługiwanych typów zasobów. |
> | Działanie | Microsoft. ClassicCompute/validateSubscriptionMoveAvailability/Action | Sprawdź dostępność subskrypcji dla klasycznej operacji przenoszenia. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/Read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszynami wirtualnymi. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Read | Pobiera sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Write | Dodaje sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Pobiera możliwe operacje asynchroniczne |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/attachDisk/akcja | Dołącza dysk danych do maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/przechwytywanie/akcja | Przechwyć maszynę wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Delete | Usuwa maszyny wirtualne. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/detachDisk/akcja | Odłącza dysk danych z maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/diagnosticsettings/Read | Pobierz ustawienia diagnostyki maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/disks/Read | Pobiera listę dysków z danymi |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/akcja | Pobiera plik RDP dla maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Extensions/operationStatuses/Read | Odczytuje stan operacji dla rozszerzeń maszyn wirtualnych. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Extensions/odczyt | Pobiera rozszerzenie maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Extensions/Write | Umieszcza rozszerzenie maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/metricdefinitions/Read | Pobierz definicję metryki maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Metrics/odczyt | Pobiera metryki. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/Read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszynami wirtualnymi. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Read | Pobiera sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Write | Dodaje sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/operationStatuses/Read | Odczytuje stan operacji dla maszyn wirtualnych. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/performMaintenance/akcja | Wykonuje konserwację na maszynie wirtualnej. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/odczyt | Pobiera listę maszyn wirtualnych. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/redeploy/akcja | Ponownie wdraża maszynę wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/restart/akcja | Uruchamia ponownie maszyny wirtualne. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Shutdown/akcja | Zamknij maszynę wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Start/akcja | Uruchom maszynę wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Stop/akcja | Wyłącza maszynę wirtualną. |
> | Działanie | Microsoft. ClassicCompute/virtualMachines/Write | Dodaj lub zmodyfikuj maszyny wirtualne. |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/operationstatuses/Read | Pobierz stan operacji połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/Komunikacja równorzędna/usuwanie | Usuń komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/Peers/operationstatuses/Read | Pobierz stan operacji komunikacji równorzędnej połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/Peers/Read | Pobierz komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/Peers/Write | Dodaj komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/odczyt | Pobierz połączenia krzyżowe w usłudze Express Route. |
> | Działanie | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Dodaj połączenia krzyżowe dla trasy Express Route. |
> | Działanie | Microsoft. ClassicNetwork/gatewaySupportedDevices/odczyt | Pobiera listę obsługiwanych urządzeń. |
> | Działanie | Microsoft. ClassicNetwork/networkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń. |
> | Działanie | Microsoft. ClassicNetwork/networkSecurityGroups/operationStatuses/Read | Odczytuje stan operacji dla sieciowej grupy zabezpieczeń. |
> | Działanie | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienia diagnostyczne sieciowych grup zabezpieczeń |
> | Działanie | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienia diagnostyczne grup zabezpieczeń sieci. Ta operacja jest uzupełniana przez dostawcę zasobów usługi Insights. |
> | Działanie | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera zdarzenia dla sieciowej grupy zabezpieczeń |
> | Działanie | Microsoft. ClassicNetwork/networkSecurityGroups/odczyt | Pobiera sieciową grupę zabezpieczeń. |
> | Działanie | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Delete | Usuwa regułę zabezpieczeń. |
> | Działanie | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/Read | Odczytuje stan operacji dla reguł zabezpieczeń sieciowych grup zabezpieczeń. |
> | Działanie | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Read | Pobiera regułę zabezpieczeń. |
> | Działanie | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Write | Dodaje lub aktualizuje regułę zabezpieczeń. |
> | Działanie | Microsoft. ClassicNetwork/networkSecurityGroups/Write | Dodaje nową sieciową grupę zabezpieczeń. |
> | Działanie | Microsoft. ClassicNetwork/Operations/Read | Pobierz operacje dotyczące sieci klasycznej. |
> | Działanie | Microsoft. ClassicNetwork/przydziały/odczyt | Pobieranie przydziału dla subskrypcji. |
> | Działanie | Microsoft. ClassicNetwork/Register/Action | Zarejestruj w sieci klasycznej |
> | Działanie | Microsoft. ClassicNetwork/reservedIps/Delete | Usuń zastrzeżony adres IP. |
> | Działanie | Microsoft. ClassicNetwork/reservedIps/Join/Action | Dołącz zastrzeżony adres IP |
> | Działanie | Microsoft. ClassicNetwork/reservedIps/link/akcja | Połącz zastrzeżony adres IP |
> | Działanie | Microsoft. ClassicNetwork/reservedIps/operationStatuses/Read | Odczytuje stan operacji dla zarezerwowanych adresów IP. |
> | Działanie | Microsoft. ClassicNetwork/reservedIps/odczyt | Pobiera zastrzeżone adresy IP |
> | Działanie | Microsoft. ClassicNetwork/reservedIps/Write | Dodaj nowy zastrzeżony adres IP |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/akcja | Przerywa migrację Virtual Network |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/możliwości/odczyt | Pokazuje możliwości |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/akcja | Sprawdza dostępność danego adresu IP w sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/akcja | Zatwierdza migrację Virtual Network |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Delete | Usuwa sieć wirtualną. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRevokedCertificates/Delete | Cofa odwołanie certyfikatu klienta. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRevokedCertificates/Read | Odczytaj odwołane certyfikaty klienta. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRevokedCertificates/Write | Odwołuje certyfikat klienta. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Delete | Usuwa certyfikat klienta bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Download/Action | Pobiera certyfikat według odcisku palca. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/listPackage/Action | Wyświetla pakiet certyfikatu bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Read | Znajdź certyfikaty główne klienta. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Write | Przekazuje nowy certyfikat główny klienta. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Connect/Action | Nawiązuje połączenie z lokacją usługi Site Gateway. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Disconnect/Action | Rozłącza połączenie między lokacją a bramą lokacji. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Read | Pobiera listę połączeń. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/test/akcja | Testuje połączenie bramy lokacja-lokacja. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Delete | Usuwa bramę sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/downloadDeviceConfigurationScript/Action | Pobiera skrypt konfiguracji urządzenia. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/downloadDiagnostics/Action | Pobiera diagnostykę bramy. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/listCircuitServiceKey/Action | Pobiera klucz usługi obwodu. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/listPackage/Action | Wyświetla pakiet bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/operationStatuses/Read | Odczytuje stan operacji dla bram sieci wirtualnych. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Packages/Read | Pobiera pakiet bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Read | Pobiera bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/startDiagnostics/Action | Uruchamia diagnostykę dla bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Gateways/stopDiagnostics/Action | Wyłącza diagnostykę bramy sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Brama/zapis | Dodaje bramę sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Join/Action | Przyłącza do sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/operationStatuses/Read | Odczytuje stan operacji dla sieci wirtualnych. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/peer/Action | Tworzy równorzędną sieć wirtualną z inną siecią wirtualną. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/akcja | Przygotowuje migrację Virtual Network |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/odczyt | Pobierz sieć wirtualną. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Delete | Usuwa zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Read | Pobiera zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Write | Dodaje lub aktualizuje zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Subnets/associatedNetworkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Subnets/associatedNetworkSecurityGroups/operationStatuses/Read | Odczytuje stan operacji dla grupy zabezpieczeń sieci skojarzonej z podsiecią sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Subnets/associatedNetworkSecurityGroups/Read | Pobiera sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Subnets/associatedNetworkSecurityGroups/Write | Dodaje sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/akcja | Sprawdza poprawność migracji Virtual Network |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/virtualNetworkPeerings/Read | Pobiera komunikację równorzędną sieci wirtualnej. |
> | Działanie | Microsoft. ClassicNetwork/virtualNetworks/Write | Dodaj nową sieć wirtualną. |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ClassicStorage/możliwości/odczyt | Pokazuje możliwości |
> | Działanie | Microsoft. ClassicStorage/checkStorageAccountAvailability/Action | Sprawdza dostępność konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/checkStorageAccountAvailability/odczyt | Pobierz dostępność konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/disks/Read | Zwraca dysk konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/images/operationstatuses/Read | Pobiera stan operacji obrazu. |
> | Działanie | Microsoft. ClassicStorage/images/Read | Zwraca obraz. |
> | Działanie | Microsoft. ClassicStorage/Operations/Read | Pobiera operacje magazynu klasycznego |
> | Działanie | Microsoft. ClassicStorage/osImages/odczyt | Zwraca obraz systemu operacyjnego. |
> | Działanie | Microsoft. ClassicStorage/osPlatformImages/odczyt | Pobiera obraz platformy systemu operacyjnego. |
> | Działanie | Microsoft. ClassicStorage/publicImages/odczyt | Pobiera publiczny obraz maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicStorage/przydziały/odczyt | Pobieranie przydziału dla subskrypcji. |
> | Działanie | Microsoft. ClassicStorage/Register/Action | Zarejestruj do magazynu klasycznego |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/abortMigration/akcja | Przerywa migrację konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/commitMigration/akcja | Zatwierdza migrację konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Delete | Usuń konto magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/disks/Delete | Usuwa dany dysk konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/disks/operationStatuses/Read | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/disks/Read | Zwraca dysk konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/disks/Write | Dodaje dysk konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/images/Delete | Usuwa dany obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/images/operationstatuses/Read | Zwraca stan operacji obrazu konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/images/Read | Zwraca obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/listKeys/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/operationStatuses/Read | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/osImages/Delete | Usuwa dany obraz systemu operacyjnego konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/osImages/Read | Zwraca obraz systemu operacyjnego konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/osImages/Write | Dodaje dany obraz systemu operacyjnego konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/prepareMigration/akcja | Przygotowuje migrację konta magazynu. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/odczyt | Zwróć konto magazynu z danym kontem. |
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
> | Działanie | Microsoft. ClassicStorage/storageAccounts/vmImages/Delete | Usuwa dany obraz maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/vmImages/operationstatuses/Read | Pobiera stan operacji obrazu maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/vmImages/Read | Zwraca obraz maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/vmImages/Write | Dodaje dany obraz maszyny wirtualnej. |
> | Działanie | Microsoft. ClassicStorage/storageAccounts/Write | Dodaje nowe konto magazynu. |
> | Działanie | Microsoft. ClassicStorage/vmImages/odczyt | Wyświetla listę obrazów maszyn wirtualnych. |

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
> | Działanie | Microsoft. CognitiveServices/accounts/Delete | Usuwa konta interfejsu API |
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
> | Działanie | Microsoft. CognitiveServices/accounts/listKeys/Action | Klucze list |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/LUIS/przewidywania/akcja | Pobiera opublikowaną prognozę punktów końcowych dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/categorysearch/Action | Zwraca wiadomości dla podanej kategorii. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/Search/Action | Uzyskaj artykuły z wiadomościami istotnymi dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/Trendingtopics/Action | Poznaj tematy trendów identyfikowane przez usługę Bing. Są to te same tematy widoczne na transparencie u dołu strony głównej usługi Bing. |
> | Działanie | Microsoft. CognitiveServices/accounts/Read | Odczytuje konta interfejsu API. |
> | Działanie | Microsoft. CognitiveServices/accounts/regenerateKey/Action | Wygeneruj ponownie klucz |
> | Działanie | Microsoft. CognitiveServices/accounts/SKU/Read | Odczytuje dostępne jednostki SKU dla istniejącego zasobu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/sprawdzanie pisowni/sprawdzanie pisowni/akcja | Pobierz wynik zapytania sprawdzania pisowni za pomocą GET lub POST. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/textanalytics/Entities/akcja | Interfejs API zwraca listę znanych jednostek i ogólnych nazwanych jednostek (\"osoby\", \"lokalizację\", \"organizacji\" itp.) w danym dokumencie. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/textanalytics/frazy kluczowe/akcja | Interfejs API zwraca listę ciągów wskazujących najważniejsze punkty w tekście wejściowym. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/textanalytics/Języki/akcja | Interfejs API zwraca wykryty język i wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 oznaczają 100% pewności, że określony język jest poprawny. Obsługiwanych jest 120 języków. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/textanalytics/tonacji/Action | Interfejs API zwraca wynik liczbowy z zakresu od 0 do 1.<br>Wyniki zbliżone do 1 oznaczają pozytywne tonacji, podczas gdy wyniki zbliżone do 0 oznaczają ujemną tonacji.<br>Wynik 0,5 wskazuje brak tonacji (np.<br>Instrukcja factoid). |
> | Działanie | Microsoft. CognitiveServices/accounts/Usages/Read | Pobierz użycie przydziału dla istniejącego zasobu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/szczegóły/akcja | Uzyskaj wgląd w dane wideo, takie jak powiązane wideo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/Search/Action | Pobierz wideo odpowiednie dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/trendy/akcja | Pobierz obecnie trendy wideo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VisualSearch/Search/Action | Zwraca listę tagów odnoszących się do podanego obrazu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/WebSearch/Search/Action | Pobierz wyniki dla danego zapytania z Internetu, obrazu, wiadomości & wideo. |
> | Działanie | Microsoft. CognitiveServices/accounts/Write | Zapisuje konta interfejsu API. |
> | Działanie | Microsoft. CognitiveServices/checkDomainAvailability/Action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Działanie | Microsoft. CognitiveServices/Locations/checkSkuAvailability/Action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Działanie | Microsoft. CognitiveServices/Locations/checkSkuAvailability/Action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Działanie | Microsoft. CognitiveServices/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadomienie z firmy Microsoft. Network usuwającej VirtualNetworks lub podsieci. |
> | Działanie | Microsoft. CognitiveServices/Operations/Read | Wyświetl wszystkie dostępne operacje |
> | Działanie | Microsoft. CognitiveServices/Register/Action | Rejestruje subskrypcję dla Cognitive Services |
> | Działanie | Microsoft. CognitiveServices/Register/Action | Rejestruje subskrypcję dla Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Commerce/RateCard/odczyt | Zwraca dane oferty, metadane zasobów/licznika i stawki dla danej subskrypcji. |
> | Działanie | Microsoft. Commerce/UsageAggregates/odczyt | Pobiera użycie Microsoft Azure przez subskrypcję. Wynik zawiera zagregowane dane użycia, informacje dotyczące subskrypcji i zasobów, w określonym przedziale czasu. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. COMPUTE/availabilitySets/Delete | Usuwa zestaw dostępności |
> | Działanie | Microsoft. COMPUTE/availabilitySets/Read | Pobierz właściwości zestawu dostępności |
> | Działanie | Microsoft. COMPUTE/availabilitySets/vmSizes/odczyt | Wyświetl dostępne rozmiary dla tworzenia lub aktualizowania maszyny wirtualnej w zestawie dostępności |
> | Działanie | Microsoft. COMPUTE/availabilitySets/Write | Tworzy nowy zestaw dostępności lub aktualizuje istniejący |
> | Działanie | Microsoft. COMPUTE/diskEncryptionSets/Delete | Usuń zestaw szyfrowania dysków |
> | Działanie | Microsoft. COMPUTE/diskEncryptionSets/Read | Pobierz właściwości zestawu szyfrowania dysków |
> | Działanie | Microsoft. COMPUTE/diskEncryptionSets/Write | Utwórz nowy zestaw szyfrowania dysków lub zaktualizuj istniejący |
> | Działanie | Microsoft. COMPUTE/disks/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dysku na potrzeby usługi dostęp do obiektów BLOB |
> | Działanie | Microsoft. COMPUTE/disks/Delete | Usuwa dysk |
> | Działanie | Microsoft. COMPUTE/disks/endGetAccess/Action | Odwołaj identyfikator URI sygnatury dostępu współdzielonego dysku |
> | Działanie | Microsoft. COMPUTE/disks/Read | Pobieranie właściwości dysku |
> | Działanie | Microsoft. COMPUTE/disks/Write | Tworzy nowy dysk lub aktualizuje istniejący |
> | Działanie | Microsoft. COMPUTE/Galerie/Delete | Usuwa galerię |
> | Działanie | Microsoft. COMPUTE/Galerie/obrazy/usuwanie | Usuwa obraz galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/images/Read | Pobiera właściwości obrazu galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/obrazy/wersje/usuwanie | Usuwa wersję obrazu galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/obrazy/wersje/odczyt | Pobiera właściwości wersji obrazu galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/obrazy/wersje/zapis | Tworzy nową wersję obrazu galerii lub aktualizuje istniejącą |
> | Działanie | Microsoft. COMPUTE/Galerie/images/Write | Tworzy nowy obraz galerii lub aktualizuje istniejący |
> | Działanie | Microsoft. COMPUTE/Galerie/odczyt | Pobiera właściwości galerii |
> | Działanie | Microsoft. COMPUTE/Galerie/Write | Tworzy nową galerię lub aktualizuje istniejącą. |
> | Działanie | Microsoft. COMPUTE/hostGroups/Delete | Usuwa grupę hostów |
> | Działanie | Microsoft. COMPUTE/hostGroups/hosts/Delete | Usuwa hosta |
> | Działanie | Microsoft. COMPUTE/hostGroups/hosty/odczyt | Pobierz właściwości hosta |
> | Działanie | Microsoft. COMPUTE/hostGroups/hosts/Write | Tworzy nowy host lub aktualizuje istniejącego hosta |
> | Działanie | Microsoft. COMPUTE/hostGroups/Read | Pobierz właściwości grupy hostów |
> | Działanie | Microsoft. COMPUTE/hostGroups/Write | Tworzy nową grupę hostów lub aktualizuje istniejącą grupę hostów |
> | Działanie | Microsoft. COMPUTE/images/Delete | Usuwa obraz |
> | Działanie | Microsoft. COMPUTE/images/Read | Pobierz właściwości obrazu |
> | Działanie | Microsoft. COMPUTE/images/Write | Tworzy nowy obraz lub aktualizuje istniejący |
> | Działanie | Microsoft. COMPUTE/Locations/capsOperations/Read | Pobiera stan operacji asynchronicznej Cap |
> | Działanie | Microsoft. COMPUTE/Locations/diskOperations/Read | Pobiera stan operacji na dysku asynchronicznym |
> | Działanie | Microsoft. COMPUTE/Locations/logAnalytics/getRequestRateByInterval/Action | Utwórz dzienniki, aby wyświetlić łączną liczbę żądań według przedziałów czasu, aby ułatwić diagnostykę. |
> | Działanie | Microsoft. COMPUTE/Locations/logAnalytics/getThrottledRequests/Action | Utwórz dzienniki, aby wyświetlić zagregowane żądania ograniczane pogrupowane według resourceName, OperationName lub stosowanych zasad ograniczania. |
> | Działanie | Microsoft. COMPUTE/Locations/Operations/Read | Pobiera stan operacji asynchronicznej |
> | Działanie | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/offers/Read | Pobierz właściwości oferty obrazu platformy |
> | Działanie | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/offers/SKU/Read | Pobierz właściwości jednostki SKU obrazu platformy |
> | Działanie | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/offers/SKU/wersje/odczyt | Pobierz właściwości wersji obrazu platformy |
> | Działanie | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/Types/Read | Pobierz właściwości typu VMExtension |
> | Działanie | Microsoft. COMPUTE/Locations/Wydawcas/artifacttypes/Types/wersje/odczyt | Pobierz właściwości wersji VMExtension |
> | Działanie | Microsoft. COMPUTE/Locations/Wydawcas/Read | Pobierz właściwości wydawcy |
> | Działanie | Microsoft. COMPUTE/Locations/runCommands/Read | Wyświetla dostępne polecenia uruchamiania w lokalizacji |
> | Działanie | Microsoft. COMPUTE/lokalizacje/użycia/odczyt | Pobiera limity usługi i bieżące ilości użycia dla zasobów obliczeniowych subskrypcji w lokalizacji |
> | Działanie | Microsoft. COMPUTE/Locations/vmSizes/Read | Wyświetla dostępne rozmiary maszyn wirtualnych w lokalizacji |
> | Działanie | Microsoft. COMPUTE/Operations/Read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. COMPUTE |
> | Działanie | Microsoft. COMPUTE/proximityPlacementGroups/Delete | Usuwa grupę umieszczania zbliżeniowego |
> | Działanie | Microsoft. COMPUTE/proximityPlacementGroups/Read | Pobierz właściwości grupy położenia zbliżeniowe |
> | Działanie | Microsoft. COMPUTE/proximityPlacementGroups/Write | Tworzy nową grupę umieszczania w sąsiedztwie lub aktualizuje istniejącą |
> | Działanie | Microsoft. COMPUTE/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. COMPUTE |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/Delete | Usuwa kolekcję punktów przywracania i zawiera punkty przywracania |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/Read | Pobierz właściwości kolekcji punktów przywracania |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/restorePoints/Delete | Usuwa punkt przywracania |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/restorePoints/odczyt | Pobierz właściwości punktu przywracania |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/restorePoints/retrieveSasUris/Action | Pobierz właściwości punktu przywracania wraz z identyfikatorami URI SAS obiektów BLOB |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/restorePoints/Write | Tworzy nowy punkt przywracania |
> | Działanie | Microsoft. COMPUTE/restorePointCollections/Write | Tworzy nową kolekcję punktów przywracania lub aktualizuje istniejącą. |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/Delete | Usuwa element sharedvmimage |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/Read | Pobierz właściwości elementu element sharedvmimage |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/wersje/usuwanie | Usuń element sharedvmimageversion |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/wersje/odczyt | Pobierz właściwości elementu element sharedvmimageversion |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/Versions/Replikacja/Akcja | Replikowanie element sharedvmimageversion do regionów docelowych |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/wersje/zapis | Utwórz nowy element sharedvmimageversion lub zaktualizuj istniejący |
> | Działanie | Microsoft. COMPUTE/sharedVMImages/Write | Tworzy nowy element sharedvmimage lub aktualizuje istniejący |
> | Działanie | Microsoft. COMPUTE/SKU/odczyt | Pobiera listę jednostek SKU Microsoft. COMPUTE dostępnych dla Twojej subskrypcji |
> | Działanie | Microsoft. COMPUTE/snapshots/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dla migawki do obiektu BLOB |
> | Działanie | Microsoft. COMPUTE/snapshots/Delete | Usuń migawkę |
> | Działanie | Microsoft. COMPUTE/snapshots/endGetAccess/Action | Odwołaj identyfikator URI sygnatury dostępu współdzielonego dla migawki |
> | Działanie | Microsoft. COMPUTE/snapshots/Read | Pobierz właściwości migawki |
> | Działanie | Microsoft. COMPUTE/snapshots/Write | Utwórz nową migawkę lub zaktualizuj istniejącą |
> | Działanie | Microsoft. COMPUTE/Unregister/Action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. COMPUTE |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Capture/Action | Przechwytuje maszynę wirtualną przez kopiowanie wirtualnych dysków twardych i generuje szablon, którego można użyć do tworzenia podobnych maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachines/convertToManagedDisks/akcja | Konwertuje dyski oparte na obiektach Blob maszyny wirtualnej na dyski zarządzane |
> | Działanie | Microsoft. COMPUTE/virtualMachines/deallocate/Action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Delete | Usuwa maszynę wirtualną |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Extensions/Delete | Usuwa rozszerzenie maszyny wirtualnej |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Extensions/Read | Pobierz właściwości rozszerzenia maszyny wirtualnej |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Extensions/Write | Tworzy nowe rozszerzenie maszyny wirtualnej lub aktualizuje istniejące |
> | Działanie | Microsoft. COMPUTE/virtualMachines/generalize/akcja | Ustawia stan maszyny wirtualnej na uogólnione i przygotowuje maszynę wirtualną do przechwycenia |
> | Działanie | Microsoft. COMPUTE/virtualMachines/instanceView/odczyt | Pobiera szczegółowy stan środowiska uruchomieniowego maszyny wirtualnej i jej zasobów |
> | Akcja dataaction | Microsoft. COMPUTE/virtualMachines/login/akcja | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | Akcja dataaction | Microsoft. COMPUTE/virtualMachines/loginAsAdmin/akcja | Logowanie się do maszyny wirtualnej przy użyciu uprawnień administratora systemu Windows lub użytkownika root w systemie Linux |
> | Działanie | Microsoft. COMPUTE/virtualMachines/performMaintenance/akcja | Wykonuje operację konserwacji na maszynie wirtualnej. |
> | Działanie | Microsoft. COMPUTE/virtualMachines/wyłączenie/akcja | Wyłącza maszynę wirtualną. Pamiętaj, że opłaty za maszynę wirtualną będą kontynuowane. |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Read | Pobierz właściwości maszyny wirtualnej |
> | Działanie | Microsoft. COMPUTE/virtualMachines/redeploy/Action | Ponownie wdraża maszynę wirtualną |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Reimage/Action | Odtwarza z obrazu maszynę wirtualną, która korzysta z dysku różnicowego. |
> | Działanie | Microsoft. COMPUTE/virtualMachines/restart/akcja | Uruchamia ponownie maszynę wirtualną |
> | Działanie | Microsoft. COMPUTE/virtualMachines/runCommand/akcja | Wykonuje wstępnie zdefiniowany skrypt na maszynie wirtualnej |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Start/akcja | Uruchamia maszynę wirtualną |
> | Działanie | Microsoft. COMPUTE/virtualMachines/vmSizes/odczyt | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
> | Działanie | Microsoft. COMPUTE/virtualMachines/Write | Tworzy nową maszynę wirtualną lub aktualizuje istniejącą maszynę wirtualną |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/deallocate/Action | Wyłącza i zwalnia zasoby obliczeniowe dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Delete | Usuwa zestaw skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Delete/Action | Usuwa wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Delete | Usuwa rozszerzenie zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Read | Pobiera właściwości rozszerzenia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write | Tworzy nowe rozszerzenie zestawu skalowania maszyn wirtualnych lub aktualizuje istniejące |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/akcja | Ręcznie przechodzenie do domeny aktualizacji platformy zestawu skalowania maszyn wirtualnych usługi Service Fabric w celu zakończenia oczekującej aktualizacji, która jest zablokowana |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/instanceView/odczyt | Pobiera widok wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/manualUpgrade/akcja | Ręcznie aktualizuje wystąpienia do najnowszego modelu zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/networkInterfaces/odczyt | Pobierz właściwości wszystkich interfejsów sieciowych zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/osRollingUpgrade/akcja | Uruchamia uaktualnienie stopniowe, aby przenieść wszystkie wystąpienia zestawu skalowania maszyn wirtualnych do najnowszej dostępnej wersji systemu operacyjnego obrazu platformy. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/osUpgradeHistory/odczyt | Pobiera historię uaktualnień systemu operacyjnego dla zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/performMaintenance/akcja | Przeprowadza planowaną konserwację na wystąpieniach zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/wyłączenie/akcja | Wyłącza wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/adresów publicipaddress/odczyt | Pobierz właściwości wszystkich publicznych adresów IP zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Read | Pobierz właściwości zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/redeploy/Action | Wdróż ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Reimage/Action | Odtwarza z obrazu wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/reimageAll/akcja | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/restart/akcja | Uruchamia ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/rollingUpgrades/Cancel/Action | Anuluje stopniowe uaktualnianie zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/rollingUpgrades/odczyt | Pobierz najnowszy stan stopniowego uaktualniania dla zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Skala/akcja | Sprawdź, czy istniejący zestaw skalowania maszyn wirtualnych może skalować w poziomie/skalować do określonej liczby wystąpień |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/SKU/odczyt | Wyświetla prawidłowe jednostki SKU dla istniejącego zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Start/akcja | Uruchamia wystąpienia zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/unallocate/Action | Wyłącza i zwalnia zasoby obliczeniowe dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Delete | Usuń konkretną maszynę wirtualną w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/instanceView/Read | Pobiera widok wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/Ipconfiguration/adresów publicipaddress/Read | Pobierz właściwości publicznego adresu IP utworzonego przy użyciu zestawu skalowania maszyn wirtualnych. Zestaw skalowania maszyn wirtualnych może utworzyć maksymalnie jeden publiczny adres IP na element ipconfiguration (prywatny adres IP) |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/Ipconfiguration/odczyt | Pobierz właściwości jednej lub wszystkich konfiguracji protokołu IP interfejsu sieciowego utworzonego przy użyciu zestawu skalowania maszyn wirtualnych. Konfiguracje adresów IP reprezentują prywatne adresy IP |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/Read | Pobierz właściwości jednego lub wszystkich interfejsów sieciowych maszyny wirtualnej utworzonej przy użyciu zestawu skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/performMaintenance/Action | Przeprowadza planowaną konserwację na wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/wyłączenie/Action | Wyłącza wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/odczyt | Pobiera właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/redeploy/Action | Ponownie wdraża wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Reimage/Action | Odtwarza z obrazu wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/reimageAll/Action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) dla wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/restart/akcja | Uruchamia ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/UruchomPolecenie/Action | Wykonuje wstępnie zdefiniowany skrypt w wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Start/akcja | Uruchamia wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Write | Aktualizuje właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/vmSizes/odczyt | Wyświetl dostępne rozmiary dla tworzenia lub aktualizowania maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Działanie | Microsoft. COMPUTE/virtualMachineScaleSets/Write | Tworzy nowy zestaw skalowania maszyn wirtualnych lub aktualizuje istniejący |

## <a name="microsoftconsumption"></a>Microsoft. zużycie

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. zużycie/salda/odczyt | Wyświetl podsumowanie użycia dla okresu rozliczeniowego dla grupy zarządzania. |
> | Działanie | Microsoft. zużycie/budżety/usuwanie | Usuń budżety przez subskrypcję lub grupę zarządzania. |
> | Działanie | Microsoft. zużycie/budżety/odczyt | Wystaw budżety według subskrypcji lub grupy zarządzania. |
> | Działanie | Microsoft. zużycie/budżety/zapis | Tworzy i aktualizuje budżety za pomocą subskrypcji lub grupy zarządzania. |
> | Działanie | Microsoft. zużycie/opłaty/odczyt | Wystaw opłaty |
> | Działanie | Microsoft. zużycie/kredyty/odczyt | Wystaw kredyty |
> | Działanie | Microsoft. użycie/zdarzenia/odczyt | Wyświetl listę zdarzeń |
> | Działanie | Microsoft. użycie/prognozy/odczyt | Prognozowanie list |
> | Działanie | Microsoft. zużycie/partie/odczyt | Lista partii |
> | Działanie | Microsoft. zużycie/Marketplace/odczyt | Wyświetl listę szczegółów użycia zasobów portalu Marketplace dla zakresu subskrypcji EA i WebDirect. |
> | Działanie | Microsoft. zużycie/operationresults/odczyt | Operationresults listy |
> | Działanie | Microsoft. użycie/operacje/odczyt | Wyświetl listę wszystkich obsługiwanych operacji przez dostawcę zasobów Microsoft. zużycie. |
> | Działanie | Microsoft. zużycie/operationstatus/odczyt | Operationstatus listy |
> | Działanie | Microsoft. zużycie/pricesheets/odczyt | Wyświetl listę danych Pricesheets dla subskrypcji lub grupy zarządzania. |
> | Działanie | Microsoft. użycie/rejestrowanie/akcja | Zarejestruj się w celu użycia RP |
> | Działanie | Microsoft. zużycie/reservationDetails/odczyt | Wyświetl listę szczegółów użycia dla wystąpień zarezerwowanych według kolejności rezerwacji lub grup zarządzania. Dane szczegółowe są na poziomie każdego wystąpienia dziennie. |
> | Działanie | Microsoft. zużycie/reservationRecommendations/odczyt | Wyświetl listę pojedynczych lub udostępnionych zaleceń dla wystąpień zarezerwowanych dla subskrypcji. |
> | Działanie | Microsoft. zużycie/reservationSummaries/odczyt | Wyświetl listę podsumowań użycia dla wystąpień zarezerwowanych według kolejności rezerwacji lub grup zarządzania. Dane podsumowujące są na poziomie co miesiąc lub codziennie. |
> | Działanie | Microsoft. zużycie/reservationTransactions/odczyt | Wyświetl listę historii transakcji dla wystąpień zarezerwowanych według grup zarządzania. |
> | Działanie | Microsoft. użycie/Tagi/odczyt | Lista tagów dla umów EA i Subscriptions. |
> | Działanie | Microsoft. użycie/dzierżawy/odczyt | Wyświetlanie listy dzierżawców |
> | Działanie | Microsoft. użycie/dzierżawcy/rejestr/akcja | Zarejestruj akcję dla zakresu Microsoft. zużycie przez dzierżawcę. |
> | Działanie | Microsoft. zużycie/warunki/odczyt | Wyświetl listę warunków subskrypcji lub grupy zarządzania. |
> | Działanie | Microsoft. zużycie/usageDetails/odczyt | Wyświetl listę szczegółów użycia zakresu dla subskrypcji EA i WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Containers/exec/akcja | Exec do określonego kontenera. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Containers/Logs/odczyt | Pobierz dzienniki dla określonego kontenera. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Delete | Usuń określoną grupę kontenerów. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Pobierz wynik operacji asynchronicznej |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla grupy kontenerów. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla grupy kontenerów. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla grupy kontenerów. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/odczyt | Pobierz wszystkie grupy kontenerów. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/restart/akcja | Uruchamia ponownie określoną grupę kontenerów. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Start/akcja | Uruchamia określoną grupę kontenerów. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Stop/akcja | Kończy określoną grupę kontenerów. Zasoby obliczeniowe zostaną cofnięte i rozliczenia zostaną zatrzymane. |
> | Działanie | Microsoft. ContainerInstance/containerGroups/Write | Utwórz lub zaktualizuj określoną grupę kontenerów. |
> | Działanie | Microsoft. ContainerInstance/Locations/cachedImages/Read | Pobiera buforowane obrazy dla subskrypcji w regionie. |
> | Działanie | Microsoft. ContainerInstance/lokalizacje/możliwości/odczyt | Uzyskaj możliwości dla regionu. |
> | Działanie | Microsoft. ContainerInstance/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadamia Microsoft. ContainerInstance, że sieć wirtualna lub podsieć jest usuwana. |
> | Działanie | Microsoft. ContainerInstance/Locations/Operations/Read | Wyświetl listę operacji usługi wystąpienia kontenera platformy Azure. |
> | Działanie | Microsoft. ContainerInstance/lokalizacje/użycia/odczyt | Pobierz użycie dla określonego regionu. |
> | Działanie | Microsoft. ContainerInstance/Operations/Read | Wyświetl listę operacji usługi wystąpienia kontenera platformy Azure. |
> | Działanie | Microsoft. ContainerInstance/Register/Action | Rejestruje subskrypcję dostawcy zasobów wystąpienia kontenera i umożliwia tworzenie grup kontenerów. |
> | Działanie | Microsoft. ContainerInstance/serviceassociationlinks/Delete | Usuń link skojarzenia usługi utworzony przez dostawcę zasobów wystąpienia kontenera platformy Azure w podsieci. |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ContainerRegistry/checkNameAvailability/odczyt | Sprawdza, czy nazwa rejestru kontenerów jest dostępna do użycia. |
> | Działanie | Microsoft. ContainerRegistry/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadamia Microsoft. ContainerRegistry, że sieć wirtualna lub podsieć jest usuwana |
> | Działanie | Microsoft. ContainerRegistry/Locations/operationResults/Read | Pobiera wynik operacji asynchronicznej |
> | Działanie | Microsoft. ContainerRegistry/Operations/Read | Wyświetla wszystkie dostępne Azure Container Registry operacje interfejsu API REST |
> | Działanie | Microsoft. ContainerRegistry/Register/Action | Rejestruje subskrypcję dostawcy zasobów rejestru kontenerów i umożliwia tworzenie rejestrów kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/artefakty/usuwanie | Usuń artefakt w rejestrze kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/kompilacje/akcja | Anuluje istniejącą kompilację. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/kompilacje/getLogLink/akcja | Pobiera link umożliwiający pobranie dzienników kompilacji. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/kompilacje/odczyty | Pobiera właściwości określonej kompilacji lub wyświetla wszystkie kompilacje dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/kompilacje/zapisywanie | Aktualizuje kompilację dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/buildTasks/usuwanie | Usuwa zadanie kompilacji z rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/buildTasks/listSourceRepositoryProperties/akcja | Wyświetla listę właściwości kontroli źródła dla zadania kompilacji. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/buildTasks/odczyt | Pobiera właściwości określonego zadania kompilacji lub wyświetla wszystkie zadania kompilacji dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/buildTasks/kroki/Usuwanie | Usuwa krok kompilacji z zadania kompilacji. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/buildTasks/etapy/listBuildArguments/akcja | Wyświetla listę argumentów kompilacji dla kroku kompilacji, w tym argumentów tajnych. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/buildTasks/kroki/odczyt | Pobiera właściwości określonego kroku kompilacji lub wyświetla listę wszystkich kroków kompilacji dla określonego zadania kompilacji. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/buildTasks/kroki/zapis | Tworzy lub aktualizuje krok kompilacji dla zadania kompilacji o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/buildTasks/zapis | Tworzy lub aktualizuje zadanie kompilacji dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/usuwanie | Usuwa rejestr kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/eventGridFilters/usuwanie | Usuwa filtr usługi Event Grid z rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/eventGridFilters/odczyt | Pobiera właściwości określonego filtru usługi Event Grid lub wyświetla listę wszystkich filtrów usługi Event Grid dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/eventGridFilters/zapis | Tworzy lub aktualizuje filtr usługi Event Grid dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/getBuildSourceUploadUrl/akcja | Pobiera lokalizację przekazywania dla użytkownika, aby mógł przekazać źródło. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/importImage/akcja | Zaimportuj obraz do rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/listBuildSourceUploadUrl/akcja | Pobierz lokalizację adresu URL przekazywania źródła dla rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/listCredentials/akcja | Wyświetla listę poświadczeń logowania dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/listPolicies/odczyt | Wyświetla listę zasad dla określonego rejestru kontenerów |
> | Działanie | Microsoft. ContainerRegistry/rejestry/listUsages/odczyt | Wyświetla listę użycia przydziału dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/kancelarie/metadane/odczyt | Pobiera metadane określonego repozytorium dla rejestru kontenerów |
> | Działanie | Microsoft. ContainerRegistry/kancelarie/metadane/zapis | Aktualizuje metadane repozytorium dla rejestru kontenerów |
> | Działanie | Microsoft. ContainerRegistry/rejestry/operationStatuses/odczyt | Pobiera stan operacji asynchronicznej rejestru |
> | Działanie | Microsoft. ContainerRegistry/rejestry/ściąganie/odczyt | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/wypychanie/zapis | Wypchnij lub Zapisz obrazy do rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/quarantineRead/odczyt | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
> | Działanie | Microsoft. ContainerRegistry/rejestry/quarantineWrite/zapis | Zapisywanie/modyfikowanie stanu kwarantanny obrazów z kwarantanny |
> | Działanie | Microsoft. ContainerRegistry/rejestry/queueBuild/akcja | Tworzy nową kompilację opartą na parametrach żądania i Dodaj ją do kolejki kompilacji. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/odczyty | Pobiera właściwości określonego rejestru kontenerów lub wyświetla listę wszystkich rejestrów kontenerów w ramach określonej grupy zasobów lub subskrypcji. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/regenerateCredential/akcja | Generuje ponownie jedno z poświadczeń logowania dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/replikacje/usuwanie | Usuwa replikację z rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/replikacje/operationStatuses/odczyt | Pobiera stan operacji asynchronicznej replikacji |
> | Działanie | Microsoft. ContainerRegistry/rejestry/replikacje/odczyt | Pobiera właściwości określonej replikacji lub wyświetla wszystkie replikacje dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/replikacje/zapis | Tworzy lub aktualizuje replikację dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/uruchomienia/anulowanie/akcja | Anuluj istniejące uruchomienie. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/uruchomienia/listLogSasUrl/akcja | Pobiera adres URL sygnatury dostępu współdzielonego dziennika dla uruchomienia. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/uruchomienia/odczyt | Pobiera właściwości przebiegu dotyczącego przebiegu rejestru lub listy kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/uruchomienia/zapis | Aktualizuje przebieg. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/scheduleRun/akcja | Zaplanuj przebieg dla rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/rejestrowanie/zapisywanie | Wypychanie/Ściąganie metadanych zaufania zawartości dla rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/zadania/usuwanie | Usuwa zadanie dla rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/zadania/listDetails/akcja | Wyświetl listę wszystkich szczegółów zadania dla rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/zadania/odczyt | Pobiera zadanie dla rejestru kontenerów lub wyświetla listę wszystkich zadań. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/zadania/zapis | Tworzy lub aktualizuje zadanie dla rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/updatePolicies/zapis | Aktualizuje zasady dla określonego rejestru kontenerów |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/usuwanie | Usuwa element webhook z rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/getCallbackConfig/akcja | Pobiera konfigurację identyfikatora URI usługi i nagłówków niestandardowych dla elementu webhook. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/listEvents/akcja | Wyświetla listę ostatnich zdarzeń dla określonego elementu webhook. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/operationStatuses/odczyt | Pobiera stan operacji asynchronicznej elementu webhook |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/polecenie ping/akcja | Wyzwala zdarzenie ping do wysłania do elementu webhook. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/odczyt | Pobiera właściwości określonego elementu webhook lub wyświetla listę wszystkich elementów webhook dla określonego rejestru kontenerów. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/elementy webhook/zapis | Tworzy lub aktualizuje element webhook dla rejestru kontenerów o określonych parametrach. |
> | Działanie | Microsoft. ContainerRegistry/rejestry/zapis | Tworzy lub aktualizuje rejestr kontenerów o określonych parametrach. |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ContainerService/containerServices/Delete | Usuwa usługę kontenera |
> | Działanie | Microsoft. ContainerService/containerServices/odczyt | Pobierz usługę kontenera |
> | Działanie | Microsoft. ContainerService/containerServices/Write | Tworzy nową usługę kontenera lub aktualizuje istniejącą |
> | Działanie | Microsoft. ContainerService/Locations/operationresults/Read | Pobiera stan wyniku operacji asynchronicznej |
> | Działanie | Microsoft. ContainerService/Locations/Operations/Read | Pobiera stan operacji asynchronicznej |
> | Działanie | Microsoft. ContainerService/Locations/orchestrators/Read | Wyświetla listę obsługiwanych programów Orchestrator |
> | Działanie | Microsoft. ContainerService/managedClusters/accessProfiles/listCredential/Action | Uzyskiwanie zarządzanego profilu dostępu do klastra według nazwy roli przy użyciu poświadczeń listy |
> | Działanie | Microsoft. ContainerService/managedClusters/accessProfiles/Read | Uzyskaj zarządzany Profil dostępu do klastra według nazwy roli |
> | Działanie | Microsoft. ContainerService/managedClusters/agentPools/Delete | Usuwa pulę agentów |
> | Działanie | Microsoft. ContainerService/managedClusters/agentPools/Read | Pobiera pulę agentów |
> | Działanie | Microsoft. ContainerService/managedClusters/agentPools/upgradeProfiles/Read | Pobiera profil uaktualnienia puli agentów |
> | Działanie | Microsoft. ContainerService/managedClusters/agentPools/Write | Tworzy nową pulę agentów lub aktualizuje istniejącą. |
> | Działanie | Microsoft. ContainerService/managedClusters/Delete | Usuwa zarządzany klaster |
> | Działanie | Microsoft. ContainerService/managedClusters/detektory/odczyt | Pobierz detektor zarządzanego klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/diagnosticsState/Read | Pobiera stan diagnostyki klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/listClusterAdminCredential/akcja | Wyświetlanie clusterAdmin poświadczenia zarządzanego klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/listClusterUserCredential/akcja | Wyświetlanie clusterUser poświadczenia zarządzanego klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. ContainerService/managedClusters/odczyt | Pobierz zarządzany klaster |
> | Działanie | Microsoft. ContainerService/managedClusters/resetAADProfile/akcja | Resetowanie profilu usługi AAD w zarządzanym klastrze |
> | Działanie | Microsoft. ContainerService/managedClusters/resetServicePrincipalProfile/akcja | Resetowanie profilu jednostki usługi zarządzanego klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/upgradeProfiles/Read | Pobiera profil uaktualnienia klastra |
> | Działanie | Microsoft. ContainerService/managedClusters/Write | Tworzy nowy zarządzany klaster lub aktualizuje istniejący. |
> | Działanie | Microsoft. ContainerService/openShiftClusters/Delete | Usuń otwarty klaster przesunięcia |
> | Działanie | Microsoft. ContainerService/openShiftClusters/odczyt | Pobierz otwarty klaster przesunięcia |
> | Działanie | Microsoft. ContainerService/openShiftClusters/Write | Tworzy nowy otwarty klaster przesunięcia lub aktualizuje istniejący |
> | Działanie | Microsoft. ContainerService/openShiftManagedClusters/Delete | Usuń otwarty klaster z zarządzaną przesunięciami |
> | Działanie | Microsoft. ContainerService/openShiftManagedClusters/odczyt | Pobierz klaster zarządzany za otwartym przesunięciami |
> | Działanie | Microsoft. ContainerService/openShiftManagedClusters/Write | Tworzy nowy klaster zarządzany przez przesunięcia lub aktualizuje istniejący |
> | Działanie | Microsoft. ContainerService/Operations/Read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. ContainerService |
> | Działanie | Microsoft. ContainerService/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. ContainerService |
> | Działanie | Microsoft. ContainerService/Unregister/Action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ContentModerator/aplikacje/usuwanie | Operacja usuwania |
> | Działanie | Microsoft. ContentModerator/Applications/listSecrets/Action | Wyświetl wpisy tajne |
> | Działanie | Microsoft. ContentModerator/Applications/listSingleSignOnToken/Action | Odczytaj tokeny logowania jednokrotnego |
> | Działanie | Microsoft. ContentModerator/Applications/Read | Operacja odczytu |
> | Działanie | Microsoft. ContentModerator/Applications/Write | Operacja zapisu |
> | Działanie | Microsoft. ContentModerator/Applications/Write | Operacja zapisu |
> | Działanie | Microsoft. ContentModerator/listCommunicationPreference/Action | Wyświetl preferencję komunikacji |
> | Działanie | Microsoft. ContentModerator/Operations/Read | operacje odczytu |
> | Działanie | Microsoft. ContentModerator/updateCommunicationPreference/Action | Aktualizuj preferencję komunikacji |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. CostManagement/cloudConnectors/Delete | Usuń określony cloudConnector. |
> | Działanie | Microsoft. CostManagement/cloudConnectors/odczyt | Wyświetl listę cloudConnectors dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft. CostManagement/cloudConnectors/Write | Utwórz lub zaktualizuj określony cloudConnector. |
> | Działanie | Microsoft. CostManagement/Dimensions/Read | Wyświetl listę wszystkich obsługiwanych wymiarów według zakresu. |
> | Działanie | Microsoft. CostManagement/eksporty/akcja | Uruchom określony eksport. |
> | Działanie | Microsoft. CostManagement/exports/Delete | Usuń określony eksport. |
> | Działanie | Microsoft. CostManagement/exports/Read | Wyświetl listę eksportów według zakresu. |
> | Działanie | Microsoft. CostManagement/exports/Run/Action | Uruchom eksporty. |
> | Działanie | Microsoft. CostManagement/exports/Write | Utwórz lub zaktualizuj określony eksport. |
> | Działanie | Microsoft. CostManagement/externalBillingAccounts/externalSubscriptions/Read | Wyświetl listę externalSubscriptions w externalBillingAccount dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft. CostManagement/externalBillingAccounts/odczyt | Wyświetl listę externalBillingAccounts dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft. CostManagement/externalSubscriptions/odczyt | Wyświetl listę externalSubscriptions dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft. CostManagement/externalSubscriptions/Write | Aktualizuj skojarzoną grupę zarządzania externalSubscription |
> | Działanie | Microsoft. CostManagement/kwerenda/akcja | Wykonywanie zapytań o dane użycia według zakresu. |
> | Działanie | Microsoft. CostManagement/Query/Read | Wykonywanie zapytań o dane użycia według zakresu. |
> | Działanie | Microsoft. CostManagement/Register/Action | Zarejestruj akcję dla zakresu Microsoft. CostManagement przez subskrypcję. |
> | Działanie | Microsoft. CostManagement/raporty/akcja | Zaplanuj raporty dotyczące danych użycia według zakresu. |
> | Działanie | Microsoft. CostManagement/Reports/Read | Zaplanuj raporty dotyczące danych użycia według zakresu. |
> | Działanie | Microsoft. CostManagement/dzierżawcy/rejestr/akcja | Zarejestruj akcję dla zakresu Microsoft. CostManagement przez dzierżawcę. |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DataBox/Jobs/bookShipmentPickUp/akcja | Umożliwia zaksięgowanie pobrania dla wysyłek zwrotnych. |
> | Działanie | Microsoft. DataBox/Jobs/Cancel/Action | Anuluje zamówienie w toku. |
> | Działanie | Microsoft. DataBox/Jobs/Delete | Usuwanie zamówień |
> | Działanie | Microsoft. DataBox/Jobs/listCredentials/akcja | Wyświetla listę nieszyfrowanych poświadczeń związanych z kolejnością. |
> | Działanie | Microsoft. DataBox/Jobs/Read | Wyświetl lub Pobierz zamówienia |
> | Działanie | Microsoft. DataBox/Jobs/Write | Utwórz lub zaktualizuj zamówienia |
> | Działanie | Microsoft. DataBox/Locations/availableSkus/Action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | Działanie | Microsoft. DataBox/Locations/availableSkus/Read | Wyświetl lub uzyskaj dostępne jednostki SKU |
> | Działanie | Microsoft. DataBox/Locations/operationResults/Read | Wyświetl lub Pobierz wyniki operacji |
> | Działanie | Microsoft. DataBox/Locations/regionConfiguration/Action | Ta metoda zwraca konfiguracje dla regionu. |
> | Działanie | Microsoft. DataBox/Locations/validateAddress/Action | Sprawdza poprawność adresu wysyłkowego i udostępnia alternatywne adresy, jeśli istnieją. |
> | Działanie | Microsoft. DataBox/Locations/validateInputs/Action | Ta metoda wykonuje wszystkie typy walidacji. |
> | Działanie | Microsoft. DataBox/Operations/Read | Wyświetl lub Pobierz operacje |
> | Działanie | Microsoft. DataBox/Register/Action | Zarejestruj dostawcę Microsoft. DATAbox |
> | Działanie | Microsoft. DataBox/subscriptions/resourceGroups/moveResources/Action | Ta metoda służy do przenoszenia zasobu. |
> | Działanie | Microsoft. DataBox/subscriptions/resourceGroups/validateMoveResources/Action | Ta metoda sprawdza, czy przenoszenie zasobów jest dozwolone. |
> | Działanie | Microsoft. DataBox/Unregister/Action | Wyrejestruj dostawcę Microsoft. DATAbox |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Alerts/Read | Wyświetla lub pobiera alerty |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Alerts/Read | Wyświetla lub pobiera alerty |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Delete | Usuwa harmonogramy przepustowości |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Wyświetla lub pobiera harmonogramy przepustowości |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Wyświetla lub pobiera harmonogramy przepustowości |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Write | Tworzy lub aktualizuje harmonogramy przepustowości |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Delete | Usuwa urządzenia Data Box Edge |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/akcja | Pobierz aktualizacje w urządzeniu |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/akcja | Pobiera rozszerzone informacje o zasobach |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/installUpdates/akcja | Zainstaluj aktualizacje na urządzeniu |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Jobs/odczyt | Wyświetla lub pobiera zadania |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/networkSettings/Read | Wyświetla lub pobiera ustawienia sieci urządzenia |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/nodes/Read | Wyświetla lub pobiera węzły |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationsStatus/Read | Wyświetla lub Pobiera stan operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Delete | Usuwa zamówienia |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Wyświetla lub pobiera zamówienia |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Wyświetla lub pobiera zamówienia |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Write | Tworzy lub aktualizuje zamówienia |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/odczyt | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/odczyt | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/odczyt | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/usuwanie | Usuwa role |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/odczyt | Wyświetla lub pobiera role |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/odczyt | Wyświetla lub pobiera role |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/role/Write | Tworzy lub aktualizuje role |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/akcja | Skanuj w poszukiwaniu aktualizacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/Update/Action | Aktualizowanie ustawień zabezpieczeń |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/udziały/usuwanie | Usuwa udziały |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/Refresh/Action | Odświeżanie metadanych udziału przy użyciu danych z chmury |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/udziały/zapis | Tworzy lub aktualizuje udziały |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Delete | Usuwa poświadczenia konta magazynu |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Write | Tworzy lub aktualizuje poświadczenia konta magazynu |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/Delete | Usuwa wyzwalacze |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/Read | Wyświetla lub pobiera wyzwalacze |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/Read | Wyświetla lub pobiera wyzwalacze |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Triggers/Write | Tworzy lub aktualizuje wyzwalacze |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/updateSummary/Read | Wyświetla lub pobiera podsumowanie aktualizacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/akcja | Przekaż certyfikat do rejestracji urządzenia |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/usuwanie | Usuwa użytkowników udziału |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/operationResults/Read | Wyświetla lub pobiera wynik operacji |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/odczyt | Wyświetla lub pobiera użytkowników udziału |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/odczyt | Wyświetla lub pobiera użytkowników udziału |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/users/zapis | Tworzy lub aktualizuje udział użytkowników |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Tworzy lub aktualizuje urządzenia Data Box Edge |
> | Działanie | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Tworzy lub aktualizuje urządzenia Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft. datacegły

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. datacegły/lokalizacje/getNetworkPolicies/akcja | Pobierz zasady dotyczące zastosowania sieci dla podsieci na podstawie lokalizacji używanej przez dostawcy NRP |
> | Działanie | Microsoft. datacegły/rejestr/akcja | Zarejestruj się w kostkach. |
> | Działanie | Microsoft. datacegły/obszary robocze/usuwanie | Usuwa obszar roboczy datakosteks. |
> | Działanie | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Ustawia dostępne ustawienia diagnostyczne dla obszaru roboczego datakosteks |
> | Działanie | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Działanie | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne definicje dzienników dla obszaru roboczego datakosteks |
> | Działanie | Microsoft. datacegły/obszary robocze/odczyt | Pobiera listę obszarów roboczych elementów datakosteks. |
> | Działanie | Microsoft. datacegły/obszary robocze/zapis | Tworzy obszar roboczy datakosteks. |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. datacatalog/wykazy/usuwanie | Usuń zasób wykazów dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft. datacatalog/wykazy/odczyt | Zasób wykazów odczytu dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft. datacatalog/wykazy/zapis | Zasób katalogu zapisu dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft. datacatalog/checkNameAvailability/Read | Sprawdź dostępność nazwy wykazu dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft. datacatalog/datacatalogs/Delete | Usuń zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft. datacatalog/datacatalogs/Read | Odczytaj zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft. datacatalog/datacatalogs/Write | Zapisz zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Działanie | Microsoft. datacatalog/Operations/Read | Odczytuje wszystkie dostępne operacje w Data Catalog dostawcy zasobów. |
> | Działanie | Microsoft. datacatalog/Register/Action | Zarejestruj subskrypcję dla dostawcy zasobów Data Catalog |
> | Działanie | Microsoft. datacatalog/Unregister/Action | Wyrejestrowywanie subskrypcji dla dostawcy zasobów Data Catalog |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DataFactory/checkazuredatafactorynameavailability/Read | Sprawdza, czy nazwa Data Factory jest dostępna do użycia. |
> | Działanie | Microsoft. DataFactory/datafactors/activitywindows/Read | Odczytuje okna działania w Data Factory z określonymi parametrami. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Activities/activitywindows/Read | Odczytuje okna aktywności dla działania potoku z określonymi parametrami. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/activitywindows/Read | Odczytuje okna działań dla potoku z określonymi parametrami. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Delete | Usuwa każdy potok. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Pause/Action | Wstrzymuje wszystkie potoki. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Read | Odczytuje wszystkie potoki. |
> | Działanie | Microsoft. DataFactory/datafactors///Resume/akcja | Wznawia wszystkie potoki. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Update/Action | Aktualizuje każdy potok. |
> | Działanie | Microsoft. DataFactory/datafactors/datapipelines/Write | Tworzy lub aktualizuje każdy potok. |
> | Działanie | Microsoft. DataFactory/datafactors/dataactivitywindowss/Read | Odczytuje okna działań dla zestawu danych z określonymi parametrami. |
> | Działanie | Microsoft. DataFactory/datafabrykas/datafactors/Delete | Usuwa dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/datafactors/datadatasets/Read | Odczytuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/datafactors/dataslicerunss/Read | Odczytuje wycinek danych uruchomienia dla danego zestawu danych z danym czasem rozpoczęcia. |
> | Działanie | Microsoft. DataFactory/datafactors/datadatasets/odczytane | Pobiera wycinki danych w danym okresie. |
> | Działanie | Microsoft. DataFactory/datafactorers/zestawy danych/wycinki/zapis | Zaktualizuj stan wycinka danych. |
> | Działanie | Microsoft. DataFactory/datafactors/DataSets/Write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/datafactors/Delete | Usuwa Data Factory. |
> | Działanie | Microsoft. DataFactory/datafactors/Gateways/ConnectionInfo/Action | Odczytuje informacje o połączeniu dla każdej bramy. |
> | Działanie | Microsoft. DataFactory/datafactors/Gateways/Delete | Usuwa wszystkie bramy. |
> | Działanie | Microsoft. DataFactory/datafactors/Gateways/listauthkeys/Action | Wyświetla listę kluczy uwierzytelniania dla każdej bramy. |
> | Działanie | Microsoft. DataFactory/datafactors/Gateways/Read | Odczytuje każdą bramę. |
> | Działanie | Microsoft. DataFactory/datafactors/Gateways/regenerateauthkey/Action | Generuje ponownie klucze uwierzytelniania dla każdej bramy. |
> | Działanie | Microsoft. DataFactory/datafabrykas/Gateways/Write | Tworzy lub aktualizuje bramę. |
> | Działanie | Microsoft. DataFactory/datafactors/linkedServices/Delete | Usuwa wszystkie połączone usługi. |
> | Działanie | Microsoft. DataFactory/datafactors/linkedServices/Read | Odczytuje wszystkie połączone usługi. |
> | Działanie | Microsoft. DataFactory/datafactors/linkedServices/Write | Tworzy lub aktualizuje każdą połączoną usługę. |
> | Działanie | Microsoft. DataFactory/datafactors/Read | Odczytuje Data Factory. |
> | Działanie | Microsoft. DataFactory/datafactors/LogInfo/Read | Odczytuje identyfikator URI sygnatury dostępu współdzielonego do kontenera obiektów BLOB zawierającego dzienniki. |
> | Działanie | Microsoft. DataFactory/datafactors/Tables/Delete | Usuwa dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/datafactors/Tables/Read | Odczytuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/datafactors/Tables/Write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/datafactors/Write | Tworzy lub aktualizuje Data Factory. |
> | Działanie | Microsoft. DataFactory/Factors/cancelpipelinerun/Action | Anuluje uruchomienie potoku określone przez identyfikator uruchomienia. |
> | Działanie | Microsoft. DataFactory/Factors/cancelSandboxPipelineRun/Action | Anuluje uruchomienie debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/Factors/createdataflowdebugsession/Action | Tworzy sesję debugowania przepływu danych. |
> | Działanie | Microsoft. DataFactory/Factors/dataflows/Delete | Usuwa przepływ danych. |
> | Działanie | Microsoft. DataFactory/Factors/dataflows/Read | Odczytuje przepływ danych. |
> | Działanie | Microsoft. datafabryka/fabryki/dataflows/Write | Utwórz lub zaktualizuj przepływ danych |
> | Działanie | Microsoft. datafabryka/fabryki/zestawy danych/usuwanie | Usuwa dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/Factors/DataSets/odczyt | Odczytuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/Factors/DataSets/Write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Działanie | Microsoft. DataFactory/Factors/debugpipelineruns/Cancel/Action | Anuluje uruchomienie debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/Factors/Delete | Usuwa Data Factory. |
> | Działanie | Microsoft. DataFactory/Factors/deletedataflowdebugsession/Action | Usuwa sesję debugowania przepływu danych. |
> | Działanie | Microsoft. DataFactory/Factors/getDataPlaneAccess/Action | Uzyskuje dostęp do usługi dataplaning ADF. |
> | Działanie | Microsoft. DataFactory/Factors/getDataPlaneAccess/Read | Odczytuje dostęp do usługi dataplaning ADF. |
> | Działanie | Microsoft. DataFactory/Factors/getFeatureValue/Action | Wartość funkcji kontroli ekspozycji dla określonej lokalizacji. |
> | Działanie | Microsoft. DataFactory/Factors/getFeatureValue/Read | Odczytuje wartość funkcji kontroli zagrożeń dla określonej lokalizacji. |
> | Działanie | Microsoft. DataFactory/Factors/getGitHubAccessToken/Action | Pobiera token dostępu usługi GitHub. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Delete | Usuwa wszystkie Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/GetConnectionInfo/Read | Odczytuje Integration Runtime informacji o połączeniu. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/getObjectMetadata/Action | Pobierz metadane usług SSIS Integration Runtime dla określonego Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/GetStatus/Read | Odczytuje Integration Runtime stanu. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/linkedIntegrationRuntime/Action | Utwórz połączone odwołanie Integration Runtime w określonym Integration Runtime udostępnionym. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/listauthkeys/Read | Wyświetla listę kluczy uwierzytelniania dla każdej Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/monitoringdata/Read | Pobiera dane monitorowania dla dowolnych Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Delete | Usuwa węzeł dla podanej Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/nodes/ipAddress/Action | Zwraca adres IP dla określonego węzła Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Read | Odczytuje węzeł dla określonego Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Write | Aktualizuje węzeł Integration Runtime samoobsługowego. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Read | Odczytuje wszystkie Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/refreshObjectMetadata/Action | Odśwież Integration Runtime metadanych dla określonego Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/regenerateauthkey/Action | Generuje ponownie klucze uwierzytelniania dla podanej Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/removelinks/Action | Usuwa połączone Integration Runtime odwołania z określonego Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Start/Action | Uruchamia dowolne Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Stop/Action | Powoduje zatrzymanie wszelkich Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/synccredentials/Action | Synchronizuje poświadczenia dla określonego Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/upgrade/Action | Uaktualnia określony Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/integrationruntimes/Write | Tworzy lub aktualizuje wszystkie Integration Runtime. |
> | Działanie | Microsoft. DataFactory/Factors/linkedServices/Delete | Usuwa połączoną usługę. |
> | Działanie | Microsoft. DataFactory/Factors/linkedServices/Read | Odczytuje połączoną usługę. |
> | Działanie | Microsoft. DataFactory/Factors/linkedServices/Write | Utwórz lub zaktualizuj połączoną usługę |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/activityruns/Read | Odczytuje uruchomienia działania dla określonego identyfikatora uruchomienia potoku. |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/Cancel/Action | Anuluje uruchomienie potoku określone przez identyfikator uruchomienia. |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/queryactivityruns/Action | Wysyła zapytanie do przebiegów działania dla określonego identyfikatora uruchomienia potoku. |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/queryactivityruns/Read | Odczytuje wynik przebiegu działania zapytania dla określonego identyfikatora uruchomienia potoku. |
> | Działanie | Microsoft. DataFactory/Factors/pipelineruns/Read | Odczytuje uruchomienia potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/createrun/akcja | Tworzy przebieg dla potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/usuwanie | Usuwa potok. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/pipelineruns/activityruns/postęp/odczyt | Pobiera postęp uruchomienia działania. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/pipelineruns/odczyt | Odczytuje uruchomienie potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/odczyt | Odczytuje potok. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/piaskownica/akcja | Tworzy środowisko uruchamiania debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/piaskownica/Utwórz/akcję | Tworzy środowisko uruchamiania debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/piaskownica/Uruchom/akcja | Tworzy przebieg debugowania dla potoku. |
> | Działanie | Microsoft. DataFactory/fabryki/potoki/zapis | Utwórz lub zaktualizuj potok |
> | Działanie | Microsoft. DataFactory/Factors/querydebugpipelineruns/Action | Wysyła zapytanie do przebiegów potoku debugowania. |
> | Działanie | Microsoft. DataFactory/Factors/querypipelineruns/Action | Wykonuje zapytanie o uruchomienia potoku. |
> | Działanie | Microsoft. DataFactory/Factors/querypipelineruns/Read | Odczytuje wynik uruchomienia potoku zapytania. |
> | Działanie | Microsoft. DataFactory/Factors/querytriggerruns/Action | Wysyła zapytanie do uruchomienia wyzwalacza. |
> | Działanie | Microsoft. DataFactory/Factors/querytriggerruns/Read | Odczytuje wynik uruchomienia wyzwalacza. |
> | Działanie | Microsoft. DataFactory/Factors/Read | Odczytuje Data Factory. |
> | Działanie | Microsoft. DataFactory/Factors/sandboxpipelineruns/sandboxActivityRuns/Read | Pobiera informacje o uruchamianiu debugowania dla działania. |
> | Działanie | Microsoft. DataFactory/Factors/startdataflowdebugsession/Action | Uruchamia sesję debugowania przepływu danych. |
> | Działanie | Microsoft. DataFactory/Factors/submitDataFlowForPreview/Action | Prześlij przepływ danych, aby uzyskać podgląd danych przy użyciu sesji debugowania. |
> | Działanie | Microsoft. DataFactory/Factors/triggerruns/Read | Odczytuje uruchomienia wyzwalacza. |
> | Działanie | Microsoft. DataFactory/fabryki/wyzwalacze/usuwanie | Usuwa każdy wyzwalacz. |
> | Działanie | Microsoft. DataFactory/fabryki/wyzwalacze/odczyt | Odczytuje dowolny wyzwalacz. |
> | Działanie | Microsoft. DataFactory/Factors/Triggers/Start/Action | Uruchamia dowolny wyzwalacz. |
> | Działanie | Microsoft. DataFactory/Factors/Triggers/akcja | Powoduje zatrzymanie dowolnego wyzwalacza. |
> | Działanie | Microsoft. DataFactory/Factors/Triggers/triggerruns/Read | Odczytuje uruchomienia wyzwalacza. |
> | Działanie | Microsoft. DataFactory/fabryki/wyzwalacze/zapis | Tworzy lub aktualizuje dowolny wyzwalacz. |
> | Działanie | Microsoft. DataFactory/Factors/Write | Utwórz lub zaktualizuj Data Factory |
> | Działanie | Microsoft. DataFactory/Locations/configureFactoryRepo/Action | Konfiguruje repozytorium dla fabryki. |
> | Działanie | Microsoft. DataFactory/Locations/getFeatureValue/Action | Wartość funkcji kontroli ekspozycji dla określonej lokalizacji. |
> | Działanie | Microsoft. DataFactory/Locations/getFeatureValue/Read | Odczytuje wartość funkcji kontroli zagrożeń dla określonej lokalizacji. |
> | Działanie | Microsoft. DataFactory/Operations/Read | Odczytuje wszystkie operacje w programie Microsoft Data Factory Provider. |
> | Działanie | Microsoft. DataFactory/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów Data Factory. |
> | Działanie | Microsoft. DataFactory/Unregister/Action | Wyrejestrowuje subskrypcję dla dostawcy zasobów Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/computePolicies/Delete | Usuń zasady obliczeń. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/computePolicies/Read | Uzyskaj informacje na temat zasad obliczeniowych. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/computePolicies/Write | Utwórz lub zaktualizuj zasady obliczeniowe. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Odłączanie konta kontach datalakestore z konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Read | Uzyskaj informacje o połączonym koncie kontach datalakestore konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Utwórz lub zaktualizuj połączone konto kontach datalakestore konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/Delete | Usuń konto DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/firewallRules/Read | Uzyskaj informacje na temat reguły zapory. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/operationResults/Read | Pobierz wynik operacji konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/Read | Pobierz informacje o istniejącym koncie DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/Action | Wyświetl listę tokenów sygnatury dostępu współdzielonego dla kontenerów magazynu połączonego konta magazynu konta usługi DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Containers/Read | Pobierz kontenery połączonego konta magazynu dla konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Delete | Odłączanie konta magazynu od konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Read | Uzyskaj informacje o połączonym koncie magazynu konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Write | Utwórz lub zaktualizuj połączone konto magazynu dla konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/TakeOwnership/Action | Udziel uprawnień do anulowania zadań przesłanych przez innych użytkowników. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/transferAnalyticsUnits/Action | Przenoszenie SystemMaxAnalyticsUnits między kontami DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Delete | Usuń regułę sieci wirtualnej. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Read | Uzyskaj informacje na temat reguły sieci wirtualnej. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Działanie | Microsoft. DataLakeAnalytics/accounts/Write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/lokalizacje/możliwości/odczyt | Uzyskaj informacje o możliwościach subskrypcji dotyczące korzystania z DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/Locations/checkNameAvailability/Action | Sprawdź dostępność nazwy konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/Locations/operationResults/Read | Pobierz wynik operacji konta DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/lokalizacje/użycia/odczyt | Uzyskaj informacje dotyczące użycia przydziału dla subskrypcji dotyczącej korzystania z DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/Operations/Read | Pobierz dostępne operacje DataLakeAnalytics. |
> | Działanie | Microsoft. DataLakeAnalytics/Register/Action | Zarejestruj subskrypcję usługi DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft. kontach datalakestore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. kontach datalakestore/accounts/Delete | Usuń konto kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/accounts/enableKeyVault/Action | Włącz Magazyn kluczy dla konta kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/accounts/eventGridFilters/Delete | Usuń filtr EventGrid. |
> | Działanie | Microsoft. kontach datalakestore/accounts/eventGridFilters/Read | Pobierz filtr EventGrid. |
> | Działanie | Microsoft. kontach datalakestore/accounts/eventGridFilters/Write | Utwórz lub zaktualizuj filtr EventGrid. |
> | Działanie | Microsoft. kontach datalakestore/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | Działanie | Microsoft. kontach datalakestore/accounts/firewallRules/Read | Uzyskaj informacje na temat reguły zapory. |
> | Działanie | Microsoft. kontach datalakestore/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | Działanie | Microsoft. kontach datalakestore/accounts/operationResults/Read | Pobierz wynik operacji konta kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/accounts/Read | Pobierz informacje o istniejącym koncie kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/accounts/shares/Delete | Usuń udział. |
> | Działanie | Microsoft. kontach datalakestore/accounts/shares/Read | Pobierz informacje o udziale. |
> | Działanie | Microsoft. kontach datalakestore/accounts/shares/Write | Utwórz lub zaktualizuj udział. |
> | Działanie | Microsoft.DataLakeStore/accounts/Superuser/action | Przyznaj administratora na Data Lake Store po udzieleniu z firmą Microsoft. Authorization/roleAssignments/Write. |
> | Działanie | Microsoft. kontach datalakestore/accounts/trustedIdProviders/Delete | Usuń zaufanego dostawcę tożsamości. |
> | Działanie | Microsoft. kontach datalakestore/accounts/trustedIdProviders/Read | Uzyskaj informacje na temat zaufanego dostawcy tożsamości. |
> | Działanie | Microsoft. kontach datalakestore/accounts/trustedIdProviders/Write | Utwórz lub zaktualizuj zaufanego dostawcę tożsamości. |
> | Działanie | Microsoft. kontach datalakestore/accounts/virtualNetworkRules/Delete | Usuń regułę sieci wirtualnej. |
> | Działanie | Microsoft. kontach datalakestore/accounts/virtualNetworkRules/Read | Uzyskaj informacje na temat reguły sieci wirtualnej. |
> | Działanie | Microsoft. kontach datalakestore/accounts/virtualNetworkRules/Write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Działanie | Microsoft. kontach datalakestore/accounts/Write | Utwórz lub zaktualizuj konto kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/lokalizacje/możliwości/odczyt | Uzyskaj informacje o możliwościach subskrypcji dotyczące korzystania z kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/Locations/checkNameAvailability/Action | Sprawdź dostępność nazwy konta kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/Locations/operationResults/Read | Pobierz wynik operacji konta kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/lokalizacje/użycia/odczyt | Uzyskaj informacje dotyczące użycia przydziału dla subskrypcji dotyczącej korzystania z kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/Operations/Read | Pobierz dostępne operacje kontach datalakestore. |
> | Działanie | Microsoft. kontach datalakestore/Register/Action | Zarejestruj subskrypcję usługi kontach datalakestore. |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. datamigration/Locations/operationResults/Read | Pobieranie stanu długotrwałej operacji związanej z zaakceptowaną odpowiedzią 202 |
> | Działanie | Microsoft. datamigration/Locations/operationStatuses/Read | Pobieranie stanu długotrwałej operacji związanej z zaakceptowaną odpowiedzią 202 |
> | Działanie | Microsoft. datamigration/Register/Action | Rejestruje subskrypcję za pomocą dostawcy Azure Database Migration Service |
> | Działanie | Microsoft. datamigration/Services/addworker/Action | Dodaje proces roboczy DMS do dostępny procesów roboczych usługi |
> | Działanie | Microsoft. datamigration/Services/checkStatus/Action | Sprawdź, czy usługa jest wdrożona i uruchomiona |
> | Działanie | Microsoft. datamigration/Services/configureWorker/Action | Konfiguruje proces roboczy DMS dla procesów roboczych dostępny usługi |
> | Działanie | Microsoft. datamigration/Services/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. datamigration/Services/projects/accessArtifacts/Action | Generowanie adresu URL, którego można użyć do pobierania lub UMIESZCZAnia artefaktów projektu |
> | Działanie | Microsoft. datamigration/Services/projects/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. datamigration/Services/projects/Files/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. datamigration/Services/projects/Files/Read | Przeczytaj informacje o zasobach |
> | Działanie | Microsoft. datamigration/Services/projects/Files/Read/Action | Uzyskaj adres URL, za pomocą którego można odczytać zawartość pliku |
> | Działanie | Microsoft. datamigration/Services/projects/Files/readWrite/Action | Uzyskaj adres URL, za pomocą którego można odczytywać lub zapisywać zawartość pliku |
> | Działanie | Microsoft. datamigration/Services/projects/Files/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Działanie | Microsoft. datamigration/Services/projects/Read | Przeczytaj informacje o zasobach |
> | Działanie | Microsoft. datamigration/Services/projects/Tasks/Cancel/Action | Anuluj zadanie, jeśli jest aktualnie uruchomione |
> | Działanie | Microsoft. datamigration/Services/projects/Tasks/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. datamigration/Services/projects/Tasks/Read | Przeczytaj informacje o zasobach |
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
> | Działanie | Microsoft. datamigration/Services/updateAgentConfig/Action | Aktualizuje konfigurację agenta DMS przy użyciu podanych wartości. |
> | Działanie | Microsoft. datamigration/Services/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Działanie | Microsoft. datamigration/SKU/Read | Pobierz listę jednostek SKU obsługiwanych przez zasoby DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DBforMariaDB/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Działanie | Microsoft. DBforMariaDB/Locations/azureAsyncOperation/Read | Zwróć wyniki operacji serwera MariaDB |
> | Działanie | Microsoft. DBforMariaDB/Locations/operationResults/Read | Zwróć wyniki operacji serwera MariaDB opartego na źródle zasobów |
> | Działanie | Microsoft. DBforMariaDB/Locations/operationResults/Read | Zwróć wyniki operacji serwera MariaDB |
> | Działanie | Microsoft. DBforMariaDB/Locations/performanceTiers/Read | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesAzureAsyncOperation/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesOperationResults/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft. DBforMariaDB/Operations/Read | Zwróć listę operacji MariaDB. |
> | Działanie | Microsoft. DBforMariaDB/performanceTiers/odczyt | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforMariaDB/Register/Action | Zarejestruj dostawcę zasobów MariaDB |
> | Działanie | Microsoft. DBforMariaDB/serwery/Administratorzy/usuwanie | Usuwa istniejącego administratora serwera MariaDB. |
> | Działanie | Microsoft. DBforMariaDB/serwery/Administratorzy/odczyt | Pobiera listę administratorów serwera MariaDB. |
> | Działanie | Microsoft. DBforMariaDB/serwery/Administratorzy/zapis | Tworzy lub aktualizuje administratora serwera MariaDB o określonych parametrach. |
> | Działanie | Microsoft. DBforMariaDB/serwery/doradcy/createRecommendedActionSession/akcja | Utwórz nową sesję akcji rekomendacji |
> | Działanie | Microsoft. DBforMariaDB/serwery/doradcy/odczyt | Zwróć listę doradców |
> | Działanie | Microsoft. DBforMariaDB/serwery/doradcy/odczyt | Zwracanie klasyfikatora |
> | Działanie | Microsoft. DBforMariaDB/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft. DBforMariaDB/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft. DBforMariaDB/Servers/Advisors/recommendedActions/Read | Zwróć zalecaną akcję |
> | Działanie | Microsoft. DBforMariaDB/serwery/konfiguracje/odczyt | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Działanie | Microsoft. DBforMariaDB/serwery/konfiguracje/zapis | Zaktualizuj wartość dla określonej konfiguracji |
> | Działanie | Microsoft. DBforMariaDB/serwery/bazy danych/usuwanie | Usuwa istniejącą bazę danych MariaDB. |
> | Działanie | Microsoft. DBforMariaDB/serwery/bazy danych/Odczyt | Zwróć listę baz danych MariaDB lub pobiera właściwości dla określonej bazy danych. |
> | Działanie | Microsoft. DBforMariaDB/serwery/bazy danych/zapis | Tworzy bazę danych MariaDB z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Działanie | Microsoft. DBforMariaDB/serwery/usuwanie | Usuwa istniejący serwer. |
> | Działanie | Microsoft. DBforMariaDB/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Działanie | Microsoft. DBforMariaDB/serwery/firewallRules/odczyt | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Działanie | Microsoft. DBforMariaDB/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Działanie | Microsoft. DBforMariaDB/serwery/logFiles/odczyt | Zwróć listę plików dziennika MariaDB. |
> | Działanie | Microsoft. DBforMariaDB/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie disagnostic dla zasobu |
> | Działanie | Microsoft. DBforMariaDB/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. DBforMariaDB/serwery/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla serwerów MariaDB |
> | Działanie | Microsoft. DBforMariaDB/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft. DBforMariaDB/serwery/queryTexts/akcja | Zwróć teksty dla listy zapytań |
> | Działanie | Microsoft. DBforMariaDB/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Działanie | Microsoft. DBforMariaDB/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft. DBforMariaDB/serwery/recoverableServers/odczyt | Zwróć informacje o odzyskiwanym serwerze MariaDB |
> | Działanie | Microsoft. DBforMariaDB/serwery/repliki/odczyt | Pobierz repliki serwera MariaDB |
> | Działanie | Microsoft. DBforMariaDB/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Działanie | Microsoft. DBforMariaDB/serwery/securityAlertPolicies/odczyt | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft. DBforMariaDB/serwery/securityAlertPolicies/zapis | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Działanie | Microsoft. DBforMariaDB/serwery/topQueryStatistics/odczyt | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Działanie | Microsoft. DBforMariaDB/serwery/topQueryStatistics/odczyt | Zwróć statystykę zapytania |
> | Działanie | Microsoft. DBforMariaDB/serwery/updateConfigurations/akcja | Zaktualizuj konfiguracje dla określonego serwera |
> | Działanie | Microsoft. DBforMariaDB/serwery/virtualNetworkRules/usuwanie | Usuwa istniejącą regułę Virtual Network |
> | Działanie | Microsoft. DBforMariaDB/serwery/virtualNetworkRules/odczyt | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft. DBforMariaDB/serwery/virtualNetworkRules/zapis | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft. DBforMariaDB/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania dla wystąpienia |
> | Działanie | Microsoft. DBforMariaDB/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania |
> | Działanie | Microsoft. DBforMariaDB/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DBforMySQL/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Działanie | Microsoft. DBforMySQL/Locations/azureAsyncOperation/Read | Zwróć wyniki operacji serwera MySQL |
> | Działanie | Microsoft. DBforMySQL/Locations/operationResults/Read | Zwróć wyniki operacji serwera MySQL na podstawie zasobów |
> | Działanie | Microsoft. DBforMySQL/Locations/operationResults/Read | Zwróć wyniki operacji serwera MySQL |
> | Działanie | Microsoft. DBforMySQL/Locations/performanceTiers/Read | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforMySQL/Locations/securityAlertPoliciesAzureAsyncOperation/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft. DBforMySQL/Locations/securityAlertPoliciesOperationResults/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft. DBforMySQL/Operations/Read | Zwróć listę operacji programu MySQL. |
> | Działanie | Microsoft. DBforMySQL/performanceTiers/odczyt | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforMySQL/Register/Action | Zarejestruj dostawcę zasobów MySQL |
> | Działanie | Microsoft. DBforMySQL/serwery/Administratorzy/usuwanie | Usuwa istniejącego administratora serwera MySQL. |
> | Działanie | Microsoft. DBforMySQL/serwery/Administratorzy/odczyt | Pobiera listę administratorów serwera MySQL. |
> | Działanie | Microsoft. DBforMySQL/serwery/Administratorzy/zapis | Tworzy lub aktualizuje administratora serwera MySQL o określonych parametrach. |
> | Działanie | Microsoft. DBforMySQL/serwery/doradcy/createRecommendedActionSession/akcja | Utwórz nową sesję akcji rekomendacji |
> | Działanie | Microsoft. DBforMySQL/serwery/doradcy/odczyt | Zwróć listę doradców |
> | Działanie | Microsoft. DBforMySQL/serwery/doradcy/odczyt | Zwracanie klasyfikatora |
> | Działanie | Microsoft. DBforMySQL/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft. DBforMySQL/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft. DBforMySQL/Servers/Advisors/recommendedActions/Read | Zwróć zalecaną akcję |
> | Działanie | Microsoft. DBforMySQL/serwery/konfiguracje/odczyt | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Działanie | Microsoft. DBforMySQL/serwery/konfiguracje/zapis | Zaktualizuj wartość dla określonej konfiguracji |
> | Działanie | Microsoft. DBforMySQL/serwery/bazy danych/usuwanie | Usuwa istniejącą bazę danych MySQL. |
> | Działanie | Microsoft. DBforMySQL/serwery/bazy danych/Odczyt | Zwróć listę baz danych MySQL lub pobiera właściwości dla określonej bazy danych. |
> | Działanie | Microsoft. DBforMySQL/serwery/bazy danych/zapis | Tworzy bazę danych MySQL z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Działanie | Microsoft. DBforMySQL/serwery/usuwanie | Usuwa istniejący serwer. |
> | Działanie | Microsoft. DBforMySQL/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Działanie | Microsoft. DBforMySQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Działanie | Microsoft. DBforMySQL/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Działanie | Microsoft. DBforMySQL/serwery/logFiles/odczyt | Zwróć listę plików dziennika PostgreSQL. |
> | Działanie | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie disagnostic dla zasobu |
> | Działanie | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla serwerów MySQL |
> | Działanie | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft. DBforMySQL/serwery/queryTexts/akcja | Zwróć teksty dla listy zapytań |
> | Działanie | Microsoft. DBforMySQL/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Działanie | Microsoft. DBforMySQL/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft. DBforMySQL/serwery/recoverableServers/odczyt | Zwróć informacje o odzyskiwanym serwerze MySQL |
> | Działanie | Microsoft. DBforMySQL/serwery/repliki/odczyt | Uzyskaj odczyt replik serwera MySQL |
> | Działanie | Microsoft. DBforMySQL/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Działanie | Microsoft. DBforMySQL/serwery/securityAlertPolicies/odczyt | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft. DBforMySQL/serwery/securityAlertPolicies/zapis | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Działanie | Microsoft. DBforMySQL/serwery/topQueryStatistics/odczyt | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Działanie | Microsoft. DBforMySQL/serwery/topQueryStatistics/odczyt | Zwróć statystykę zapytania |
> | Działanie | Microsoft. DBforMySQL/serwery/updateConfigurations/akcja | Zaktualizuj konfiguracje dla określonego serwera |
> | Działanie | Microsoft. DBforMySQL/serwery/virtualNetworkRules/usuwanie | Usuwa istniejącą regułę Virtual Network |
> | Działanie | Microsoft. DBforMySQL/serwery/virtualNetworkRules/odczyt | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft. DBforMySQL/serwery/virtualNetworkRules/zapis | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft. DBforMySQL/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania dla wystąpienia |
> | Działanie | Microsoft. DBforMySQL/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania |
> | Działanie | Microsoft. DBforMySQL/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DBforPostgreSQL/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/azureAsyncOperation/Read | Zwróć wyniki operacji serwera PostgreSQL |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/operationResults/Read | Zwróć wyniki operacji serwera PostgreSQL opartego na źródle zasobów |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/operationResults/Read | Zwróć wyniki operacji serwera PostgreSQL |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/performanceTiers/Read | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesAzureAsyncOperation/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesOperationResults/Read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Działanie | Microsoft. DBforPostgreSQL/Operations/Read | Zwróć listę operacji PostgreSQL. |
> | Działanie | Microsoft. DBforPostgreSQL/performanceTiers/odczyt | Zwraca listę dostępnych warstw wydajności. |
> | Działanie | Microsoft. DBforPostgreSQL/Register/Action | Zarejestruj dostawcę zasobów PostgreSQL |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/Administratorzy/usuwanie | Usuwa istniejącego administratora serwera PostgreSQL. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/Administratorzy/odczyt | Pobiera listę administratorów serwera PostgreSQL. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/Administratorzy/zapis | Tworzy lub aktualizuje administratora serwera PostgreSQL o określonych parametrach. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/doradcy/odczyt | Zwróć listę doradców |
> | Działanie | Microsoft. DBforPostgreSQL/Servers/Advisors/recommendedActions/Read | Zwróć listę zalecanych akcji |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/doradcy/recommendedActionSessions/akcja | Wykonaj zalecenia |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/konfiguracje/odczyt | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/konfiguracje/zapis | Zaktualizuj wartość dla określonej konfiguracji |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/bazy danych/usuwanie | Usuwa istniejącą bazę danych PostgreSQL. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/bazy danych/Odczyt | Zwróć listę baz danych PostgreSQL lub pobiera właściwości dla określonej bazy danych. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/bazy danych/zapis | Tworzy bazę danych PostgreSQL z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/usuwanie | Usuwa istniejący serwer. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/logFiles/odczyt | Zwróć listę plików dziennika PostgreSQL. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/usuwanie | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/odczyt | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Validate/Action | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/zapis | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/usuwanie | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/odczyt | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/zapis | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/privateLinkResources/odczyt | Pobierz zasoby linku prywatnego dla odpowiedniego serwera PostgreSQL |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie disagnostic dla zasobu |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/queryTexts/odczyt | Zwróć teksty dla listy zapytań |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/recoverableServers/odczyt | Zwróć informacje o odzyskiwanym serwerze PostgreSQL |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/repliki/odczyt | Pobierz repliki serwera PostgreSQL |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/securityAlertPolicies/odczyt | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/securityAlertPolicies/zapis | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/topQueryStatistics/odczyt | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/updateConfigurations/akcja | Zaktualizuj konfiguracje dla określonego serwera |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/virtualNetworkRules/usuwanie | Usuwa istniejącą regułę Virtual Network |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/virtualNetworkRules/odczyt | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/virtualNetworkRules/zapis | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/waitStatistics/odczyt | Zwróć statystykę oczekiwania dla wystąpienia |
> | Działanie | Microsoft. DBforPostgreSQL/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/konfiguracje/odczyt | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/Configurations/Write | Zaktualizuj wartość dla określonej konfiguracji |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/Delete | Usuwa istniejący serwer. |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Delete | Usuwa istniejącą regułę zapory. |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Read | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Write | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie disagnostic dla zasobu |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/updateConfigurations/akcja | Zaktualizuj konfiguracje dla określonego serwera |
> | Działanie | Microsoft. DBforPostgreSQL/serversv2/Write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdevices"></a>Microsoft. urządzenia

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Devices/Account/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/Account/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/Account/logDefinitions/Read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Działanie | Microsoft. Devices/Account/metricDefinitions/Read | Pobiera dostępne metryki dla usługi IotHub |
> | Działanie | Microsoft. Devices/checkNameAvailability/akcja | Sprawdź, czy nazwa IotHub jest dostępna |
> | Działanie | Microsoft. Devices/checkNameAvailability/akcja | Sprawdź, czy nazwa IotHub jest dostępna |
> | Działanie | Microsoft. Devices/checkProvisioningServiceNameAvailability/akcja | Sprawdź, czy nazwa usługi aprowizacji jest dostępna |
> | Działanie | Microsoft. Devices/checkProvisioningServiceNameAvailability/akcja | Sprawdź, czy nazwa usługi aprowizacji jest dostępna |
> | Działanie | Microsoft. Devices/digitalTwins/Delete | Usuń istniejące konto bliźniaczych reprezentacji Digital i wszystkie jego elementy podrzędne |
> | Działanie | Microsoft. Devices/digitalTwins/operationresults/odczyt | Pobieranie stanu operacji przy użyciu konta bliźniaczych reprezentacji Digital |
> | Działanie | Microsoft. Devices/digitalTwins/odczyt | Pobiera listę kont bliźniaczych reprezentacji cyfrowych skojarzonych z subskrypcją |
> | Działanie | Microsoft. Devices/digitalTwins/SKU/odczyt | Pobierz listę prawidłowych jednostek SKU dla kont Digital bliźniaczych reprezentacji |
> | Działanie | Microsoft. Devices/digitalTwins/Write | Utwórz nowe konto bliźniaczych reprezentacji z cyframi |
> | Działanie | Microsoft. Devices/ElasticPools/diagnosticSettings/odczyt | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/ElasticPools/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Delete | Usuwa certyfikat |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Read | Pobiera certyfikat |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Delete | Usuwanie zasobu dzierżawy IotHub |
> | Działanie | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Usuń grupę odbiorców EventHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Utwórz grupę odbiorców EventHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/exportDevices/Action | Eksportuj urządzenia |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/get\field/Read | Pobiera zasób statystyk dzierżawy IotHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/importDevices/Action | Importuj urządzenia |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/iotHubKeys/ListKeys/Action | Pobiera klucz dzierżawy IotHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Jobs/Read | Pobierz szczegóły zadań przesłane w danym IotHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/listKeys/Action | Pobiera klucze dzierżawy IotHub |
> | Działanie | Microsoft. Devices/ElasticPools/IotHubTenants/logDefinitions/Read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Działanie | Microsoft. Devices/ElasticPools/IotHubTenants/metricDefinitions/Read | Pobiera dostępne metryki dla usługi IotHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Pobierz metryki przydziału |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/odczyt | Pobiera zasób dzierżawy IotHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Routing/trasy/$testall/Action | Przetestuj komunikat dla wszystkich istniejących tras |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Routing/trasy/$testnew/Action | Przetestuj komunikat w odniesieniu do podanej trasy testowej |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla IotHub |
> | Działanie | Microsoft. Devices/elasticPools/iotHubTenants/Write | Utwórz lub zaktualizuj zasób dzierżawy IotHub |
> | Działanie | Microsoft. Devices/ElasticPools/metricDefinitions/odczyt | Pobiera dostępne metryki dla usługi IotHub |
> | Działanie | Microsoft. Devices/iotHubs/Certificates/Delete | Usuwa certyfikat |
> | Działanie | Microsoft. Devices/iotHubs/Certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Działanie | Microsoft. Devices/iotHubs/Certificates/Read | Pobiera certyfikat |
> | Działanie | Microsoft. Devices/iotHubs/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Działanie | Microsoft. Devices/iotHubs/Certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Działanie | Microsoft. Devices/iotHubs/Delete | Usuń zasób IotHub |
> | Działanie | Microsoft. Devices/IotHubs/diagnosticSettings/odczyt | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/IotHubs/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/iotHubs/eventGridFilters/Delete | Usuwa filtr Event Grid |
> | Działanie | Microsoft. Devices/iotHubs/eventGridFilters/odczyt | Pobiera Filtr Event Grid |
> | Działanie | Microsoft. Devices/iotHubs/eventGridFilters/Write | Utwórz nowy lub zaktualizuj istniejący filtr Event Grid |
> | Działanie | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Usuń grupę odbiorców EventHub |
> | Działanie | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Działanie | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Utwórz grupę odbiorców EventHub |
> | Działanie | Microsoft. Devices/iotHubs/exportDevices/akcja | Eksportuj urządzenia |
> | Działanie | Microsoft. Devices/iotHubs/importDevices/akcja | Importuj urządzenia |
> | Działanie | Microsoft. Devices/iotHubs/iotHubKeys/ListKeys/Action | Pobierz klucz IotHub dla podaną nazwę |
> | Działanie | Microsoft. Devices/iotHubs/iotHubStats/odczyt | Pobierz statystyki IotHub |
> | Działanie | Microsoft. Devices/iotHubs/Jobs/Read | Pobierz szczegóły zadań przesłane w danym IotHub |
> | Działanie | Microsoft. Devices/iotHubs/ListKeys/akcja | Pobierz wszystkie klucze IotHub |
> | Działanie | Microsoft. Devices/IotHubs/logDefinitions/odczyt | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Działanie | Microsoft. Devices/IotHubs/metricDefinitions/odczyt | Pobiera dostępne metryki dla usługi IotHub |
> | Działanie | Microsoft. Devices/iotHubs/operationresults/odczyt | Pobierz wynik operacji (przestarzały interfejs API) |
> | Działanie | Microsoft. Devices/iotHubs/quotaMetrics/odczyt | Pobierz metryki przydziału |
> | Działanie | Microsoft. Devices/iotHubs/odczyt | Pobiera zasoby IotHub |
> | Działanie | Microsoft. Devices/iotHubs/Routing/$testall/Action | Przetestuj komunikat dla wszystkich istniejących tras |
> | Działanie | Microsoft. Devices/iotHubs/Routing/$testnew/Action | Przetestuj komunikat w odniesieniu do podanej trasy testowej |
> | Działanie | Microsoft. Devices/iotHubs/routingEndpointsHealth/odczyt | Pobiera kondycję wszystkich punktów końcowych routingu dla IotHub |
> | Działanie | Microsoft. Devices/iotHubs/SKU/odczyt | Pobieranie prawidłowych jednostek SKU IotHub |
> | Działanie | Microsoft. Devices/iotHubs/Write | Utwórz lub zaktualizuj zasób IotHub |
> | Działanie | Microsoft. Devices/Locations/operationresults/Read | Pobierz wynik operacji na podstawie lokalizacji |
> | Działanie | Microsoft. Devices/operationresults/odczyt | Pobierz wynik operacji |
> | Działanie | Microsoft. Devices/Operations/Read | Pobierz wszystkie operacje ResourceProvider |
> | Działanie | Microsoft. Devices/provisioningServices/Certificates/Delete | Usuwa certyfikat |
> | Działanie | Microsoft. Devices/provisioningServices/Certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Działanie | Microsoft. Devices/provisioningServices/Certificates/Read | Pobiera certyfikat |
> | Działanie | Microsoft. Devices/provisioningServices/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Działanie | Microsoft. Devices/provisioningServices/Certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Działanie | Microsoft. Devices/provisioningServices/Delete | Usuń zasób IotDps |
> | Działanie | Microsoft. Devices/provisioningServices/diagnosticSettings/odczyt | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/provisioningServices/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. Devices/provisioningServices/Keys/ListKeys/akcja | Pobierz klucze IotDps dla nazwy klucza |
> | Działanie | Microsoft. Devices/provisioningServices/ListKeys/akcja | Pobierz wszystkie klucze IotDps |
> | Działanie | Microsoft. Devices/provisioningServices/logDefinitions/odczyt | Pobiera dostępne definicje dzienników dla usługi aprowizacji |
> | Działanie | Microsoft. Devices/provisioningServices/metricDefinitions/odczyt | Pobiera dostępne metryki dla usługi aprowizacji |
> | Działanie | Microsoft. Devices/provisioningServices/operationresults/odczyt | Pobierz wynik operacji DPS |
> | Działanie | Microsoft. Devices/provisioningServices/odczyt | Pobierz zasób IotDps |
> | Działanie | Microsoft. Devices/provisioningServices/SKU/odczyt | Pobieranie prawidłowych jednostek SKU IotDps |
> | Działanie | Microsoft. Devices/provisioningServices/Write | Utwórz zasób IotDps |
> | Działanie | Microsoft. Devices/Register/Action | Zarejestruj subskrypcję dostawcy zasobów IotHub i Włącz tworzenie zasobów IotHub |
> | Działanie | Microsoft. urządzenia/użycia/odczyt | Pobierz szczegóły użycia subskrypcji dla tego dostawcy. |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DevSpaces/controllers/Delete | Usuwanie kontrolera Azure Dev Spaces i usług dataplaning |
> | Działanie | Microsoft. DevSpaces/controllers/listConnectionDetails/Action | Wyświetl szczegóły połączenia dla infrastruktury kontrolera Azure Dev Spaces |
> | Działanie | Microsoft. DevSpaces/controllers/Read | Odczytaj właściwości kontrolera Azure Dev Spaces |
> | Działanie | Microsoft. DevSpaces/controllers/Write | Utwórz lub zaktualizuj właściwości kontrolera Azure Dev Spaces |
> | Działanie | Microsoft. DevSpaces/Locations/checkContainerHostMapping/Action | Sprawdź istniejące mapowanie kontrolera dla hosta kontenera |
> | Działanie | Microsoft. DevSpaces/Locations/operationresults/Read | Stan odczytu operacji asynchronicznej |
> | Działanie | Microsoft. DevSpaces/Register/Action | Rejestrowanie dostawcy zasobów Microsoft dev Spaces z subskrypcją |

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. wspólny/labCenters/Delete | Usuń centra laboratoryjne. |
> | Działanie | Microsoft. wspólny/labCenters/odczyt | Odczytywanie centrów laboratoryjnych. |
> | Działanie | Microsoft. wspólny/labCenters/Write | Dodawanie lub modyfikowanie centrów laboratoryjnych. |
> | Działanie | Microsoft. wspólny/Labs/artifactSources/armTemplates/Read | Odczytywanie szablonów usługi Azure Resource Manager. |
> | Działanie | Microsoft. wspólny/Labs/artifactSources/artefakty/GenerateArmTemplate/akcja | Generuje szablon ARM dla danego artefaktu, przekazuje wymagane pliki na konto magazynu i sprawdza poprawność wygenerowanego artefaktu. |
> | Działanie | Microsoft. wspólny/Labs/artifactSources/artefakty/odczyt | Odczytaj artefakty. |
> | Działanie | Microsoft. wspólny/Labs/artifactSources/usuwanie | Usuń źródła artefaktów. |
> | Działanie | Microsoft. wspólny/Labs/artifactSources/odczyt | Odczytaj źródła artefaktów. |
> | Działanie | Microsoft. wspólny/Labs/artifactSources/zapis | Dodaj lub zmodyfikuj źródła artefaktów. |
> | Działanie | Microsoft. wspólny/Labs/ClaimAnyVm/akcja | Zarezerwuj losową maszynę wirtualną z dodaną odpowiedzialnością w laboratorium. |
> | Działanie | Microsoft. wspólny/Labs/koszty/odczyt | Zapoznaj się z kosztami. |
> | Działanie | Microsoft. wspólny/Labs/koszty/zapis | Dodawanie lub modyfikowanie kosztów. |
> | Działanie | Microsoft. wspólny/Labs/środowisko/akcja | Twórz maszyny wirtualne w laboratorium. |
> | Działanie | Microsoft. wspólny/Labs/customImages/usuwanie | Usuń obrazy niestandardowe. |
> | Działanie | Microsoft. wspólny/Labs/customImages/odczyt | Odczytaj obrazy niestandardowe. |
> | Działanie | Microsoft. wspólny/Labs/customImages/zapis | Dodawanie lub modyfikowanie obrazów niestandardowych. |
> | Działanie | Microsoft. wspólny/Labs/usuwanie | Usuwanie laboratoriów. |
> | Działanie | Microsoft. wspólny/Labs/EnsureCurrentUserProfile/akcja | Upewnij się, że bieżący użytkownik ma prawidłowy profil w laboratorium. |
> | Działanie | Microsoft. wspólny/Labs/ExportResourceUsage/akcja | Eksportuje użycie zasobów laboratorium do konta magazynu |
> | Działanie | Microsoft. wspólny/Labs/formuły/usuwanie | Usuń formuły. |
> | Działanie | Microsoft. wspólny/Labs/formuły/odczyt | Odczytaj formuły. |
> | Działanie | Microsoft. wspólny/Labs/formuły/zapis | Dodaj lub zmodyfikuj formuły. |
> | Działanie | Microsoft. wspólny/Labs/galleryImages/odczyt | Odczytuj obrazy galerii. |
> | Działanie | Microsoft. wspólny/Labs/GenerateUploadUri/akcja | Generowanie identyfikatora URI na potrzeby przekazywania niestandardowych obrazów dysków do laboratorium. |
> | Działanie | Microsoft. wspólny/Labs/ImportVirtualMachine/akcja | Zaimportuj maszynę wirtualną do innego laboratorium. |
> | Działanie | Microsoft. wspólny/Labs/ListVhds/akcja | Wyświetlanie listy obrazów dysków dostępnych do tworzenia obrazów niestandardowych. |
> | Działanie | Microsoft. wspólny/Labs/notificationChannels/usuwanie | Usuwanie kanałów powiadomień. |
> | Działanie | Microsoft. wspólny/Labs/notificationChannels/powiadamianie/akcja | Wyślij powiadomienie do podanego kanału. |
> | Działanie | Microsoft. wspólny/Labs/notificationChannels/odczyt | Odczytaj kanały powiadomień. |
> | Działanie | Microsoft. wspólny/Labs/notificationChannels/zapis | Dodawanie lub modyfikowanie kanałów powiadomień. |
> | Działanie | Microsoft. wspólny/Labs/policySets/EvaluatePolicies/Action | Szacuje zasady laboratorium. |
> | Działanie | Microsoft. wspólny/Labs/policySets/zasady/usuwanie | Usuń zasady. |
> | Działanie | Microsoft. wspólny/Labs/policySets/zasady/odczyt | Odczytaj zasady. |
> | Działanie | Microsoft. wspólny/Labs/policySets/zasady/zapis | Dodawanie lub modyfikowanie zasad. |
> | Działanie | Microsoft. wspólny/Labs/policySets/odczyt | Odczytaj zestawy zasad. |
> | Działanie | Microsoft. wspólny/Labs/odczyt | Odczytywanie laboratoriów. |
> | Działanie | Microsoft. wspólny/Labs/harmonogramy/usuwanie | Usuwanie harmonogramów. |
> | Działanie | Microsoft. wspólny/Labs/harmonogramy/wykonywanie/akcja | Wykonaj harmonogram. |
> | Działanie | Microsoft. wspólny/Labs/Schedules/ListApplicable/Action | Wyświetla wszystkie odpowiednie harmonogramy |
> | Działanie | Microsoft. wspólny/Labs/harmonogramy/odczyt | Odczytaj harmonogramy. |
> | Działanie | Microsoft. wspólny/Labs/harmonogramy/zapis | Dodawanie lub modyfikowanie harmonogramów. |
> | Działanie | Microsoft. wspólny/Labs/servicerunners/Delete | Usuwanie modułu uruchamiającego usługi. |
> | Działanie | Microsoft. wspólny/Labs/servicerunners/Read | Odczytywanie modułu uruchamiającego usługi. |
> | Działanie | Microsoft. wspólny/Labs/servicerunners/Write | Dodawanie lub modyfikowanie modułu uruchamiającego usługi. |
> | Działanie | Microsoft. wspólny/Labs/sharedGalleries/usuwanie | Usuń udostępnione galerie. |
> | Działanie | Microsoft. wspólny/Labs/sharedGalleries/odczyt | Odczytuj udostępnione galerie. |
> | Działanie | Microsoft. wspólny/Labs/sharedGalleries/sharedImages/Delete | Usuń obrazy udostępnione. |
> | Działanie | Microsoft. wspólny/Labs/sharedGalleries/sharedImages/Read | Odczytuj obrazy udostępnione. |
> | Działanie | Microsoft. wspólny/Labs/sharedGalleries/sharedImages/Write | Dodaj lub Modyfikuj obrazy udostępnione. |
> | Działanie | Microsoft. wspólny/Labs/sharedGalleries/zapis | Dodaj lub zmodyfikuj udostępnione galerie. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/usuwanie | Usuń profile użytkowników. |
> | Działanie | Microsoft. wspólny/Labs/users/disks/Attach/Action | Dołącz i Utwórz dzierżawę dysku dla maszyny wirtualnej. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/dyski/usuwanie | Usuń dyski. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/dyski/odłączenie/akcja | Odłącz i Przerwij dzierżawę dysku dołączonego do maszyny wirtualnej. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/dyski/odczyt | Odczytywanie dysków. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/dyski/zapis | Dodawanie lub modyfikowanie dysków. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/środowiska/usuwanie | Usuń środowiska. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/środowiska/odczyt | Odczytuj środowiska. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/środowiska/zapis | Dodaj lub zmodyfikuj środowiska. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/odczyt | Odczytaj profile użytkowników. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/wpisy tajne/usuwanie | Usuń wpisy tajne. |
> | Działanie | Microsoft. wspólny/Labs/users/wpisy tajne/odczyt | Odczytaj wpisy tajne. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/wpisy tajne/zapis | Dodaj lub zmodyfikuj wpisy tajne. |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/Delete | Usuwanie sieci szkieletowej usług. |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/ListApplicableSchedules/Action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/Read | Odczytaj sieci szkieletowe usług. |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Delete | Usuwanie harmonogramów. |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Execute/Action | Wykonaj harmonogram. |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Read | Odczytaj harmonogramy. |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Write | Dodawanie lub modyfikowanie harmonogramów. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/servicefabrics/Start/akcja | Uruchom sieć szkieletową usług. |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/Stop/Action | Zatrzymaj sieć szkieletową usług |
> | Działanie | Microsoft. wspólny/Labs/users/servicefabrics/Write | Dodawanie lub modyfikowanie sieci szkieletowej usług. |
> | Działanie | Microsoft. wspólny/Labs/użytkownicy/zapis | Dodaj lub zmodyfikuj profile użytkowników. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/AddDataDisk/Action | Dołącz nowy lub istniejący dysk danych do maszyny wirtualnej. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/ApplyArtifacts/Action | Zastosuj artefakty do maszyny wirtualnej. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/roszczeń/akcja | Przejmowanie na własność istniejącej maszyny wirtualnej |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/usuwanie | Usuwanie maszyn wirtualnych. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/DetachDataDisk/Action | Odłącz określony dysk od maszyny wirtualnej. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/GetRdpFileContents/Action | Pobiera ciąg, który reprezentuje zawartość pliku RDP dla maszyny wirtualnej |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/ListApplicableSchedules/Action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/odczyt | Odczytaj maszyny wirtualne. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/ponowna instalacja/akcja | Ponowne wdrażanie maszyny wirtualnej |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/Zmień rozmiar/akcję | Zmień rozmiar maszyny wirtualnej. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/restart/akcja | Uruchom ponownie maszynę wirtualną. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/Schedules/Delete | Usuwanie harmonogramów. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/Schedules/Execute/Action | Wykonaj harmonogram. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/Schedules/Read | Odczytaj harmonogramy. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/Schedules/Write | Dodawanie lub modyfikowanie harmonogramów. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/Start/akcja | Uruchom maszynę wirtualną. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/Stop/akcja | Zatrzymaj maszynę wirtualną |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/TransferDisks/Action | Przenosi wszystkie dyski z danymi dołączone do maszyny wirtualnej, które mają należeć do bieżącego użytkownika. |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/unclaim/Action | Zwolnij własność istniejącej maszyny wirtualnej |
> | Działanie | Microsoft. wspólny/Labs/virtualMachines/zapis | Dodaj lub zmodyfikuj maszyny wirtualne. |
> | Działanie | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Delete | Usuń bastionhosts. |
> | Działanie | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Read | Odczytaj bastionhosts. |
> | Działanie | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Write | Dodaj lub zmodyfikuj bastionhosts. |
> | Działanie | Microsoft. wspólny/Labs/virtualNetworks/usuwanie | Usuń sieci wirtualne. |
> | Działanie | Microsoft. wspólny/Labs/virtualNetworks/odczyt | Odczytaj sieci wirtualne. |
> | Działanie | Microsoft. wspólny/Labs/virtualNetworks/zapis | Dodaj lub zmodyfikuj sieci wirtualne. |
> | Działanie | Microsoft. wspólny/Labs/vmPools/usuwanie | Usuń pule maszyn wirtualnych. |
> | Działanie | Microsoft. wspólny/Labs/vmPools/odczyt | Odczytaj pule maszyn wirtualnych. |
> | Działanie | Microsoft. wspólny/Labs/vmPools/zapis | Dodaj lub zmodyfikuj pule maszyn wirtualnych. |
> | Działanie | Microsoft. wspólny/Labs/zapis | Dodawanie lub modyfikowanie laboratoriów. |
> | Działanie | Microsoft. wspólny/Locations/Operations/Read | Operacje odczytu. |
> | Działanie | Microsoft. wspólny/Register/Action | Rejestruje subskrypcję |
> | Działanie | Microsoft. wspólny/harmonogramy/usuwanie | Usuwanie harmonogramów. |
> | Działanie | Microsoft. wspólny/harmonogramy/wykonywanie/akcja | Wykonaj harmonogram. |
> | Działanie | Microsoft. wspólny/Schedules/Read | Odczytaj harmonogramy. |
> | Działanie | Microsoft. wspólny/harmonogramy/Przekieruj/akcję | Aktualizuje identyfikator zasobu docelowego harmonogramu. |
> | Działanie | Microsoft. wspólny/Schedules/Write | Dodawanie lub modyfikowanie harmonogramów. |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. DocumentDB/databaseAccountNames/odczyt | Sprawdza dostępność nazw. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Delete | Usuwanie kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Read | Odczytaj kolekcję lub Wyświetl listę wszystkich kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Settings/Read | Odczytaj przepływność kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Settings/Write | Aktualizowanie przepływności kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Write | Utwórz lub zaktualizuj kolekcję. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Delete | Usuń kontener. Dotyczy tylko typów interfejsu API: "SQL". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Read | Odczytaj kontener lub Wyświetl listę wszystkich kontenerów. Dotyczy tylko typów interfejsu API: "SQL". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Settings/Read | Odczytaj przepływność kontenera. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Settings/Write | Zaktualizuj przepływność kontenera. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Write | Utwórz lub zaktualizuj kontener. Dotyczy tylko typów interfejsu API: "SQL". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Delete | Usuń bazę danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/bazy danych/wykresy/usuwanie | Usuwanie grafu. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/graphs/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/bazy danych/wykresy/odczyt | Odczytaj wykres lub Wyświetl wszystkie wykresy. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/graphs/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/graphs/Settings/Read | Odczytaj przepływność grafu. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/wykresy/ustawienia/zapis | Aktualizowanie przepływności grafu. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/bazy danych/wykresy/zapis | Utwórz lub zaktualizuj Graf. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Read | Odczytaj bazę danych lub listę wszystkich baz danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Settings/Read | Odczytaj przepływność bazy danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Settings/Write | Aktualizowanie przepływności bazy danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Databases/Write | Tworzenie bazy danych Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/usuwanie | Usuń przestrzeń kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Spaces/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Spaces/Read | Odczytaj miejsce na dysku lub Wyświetl wszystkie spacje. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/dataspaces/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/miejsca/ustawienia/odczyt | Odczytaj przepustowość przestrzeni kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/ustawienia/zapis | Aktualizowanie przepływności przestrzeni kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/tabele/usuwanie | Usuń tabelę. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Spaces/tabless/Tables/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/tabele/odczyt | Odczytaj tabelę lub listę wszystkich tabel. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Spaces/Tables/Tables/Settings/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Spaces/Tables/Tables/Settings/Read | Odczytaj tabelę przepływność. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Spaces/Tables/Tables/Settings/Write | Aktualizowanie przepływności tabeli. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/przestrzenie kluczy/tabele/zapis | Utwórz lub zaktualizuj tabelę. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/miejsca do zapisu/zapis | Utwórz miejsce na dysku. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/usuwanie | Usuń tabelę. Dotyczy tylko typów interfejsu API: "Table". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/API/Tables/operationResults/Read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Table". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/odczyt | Odczytaj tabelę lub listę wszystkich tabel. Dotyczy tylko typów interfejsu API: "Table". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/ustawienia/operationResults/odczyt | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/ustawienia/odczyt | Odczytaj tabelę przepływność. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/ustawienia/zapis | Aktualizowanie przepływności tabeli. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/zapis | Utwórz lub zaktualizuj tabelę. Dotyczy tylko typów interfejsu API: "Table". |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/kopia zapasowa/akcja | Prześlij żądanie skonfigurowania kopii zapasowej |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/changeResourceGroup/akcja | Zmień grupę zasobów konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Databases/Collections/metricDefinitions/Read | Odczytuje definicje metryk kolekcji. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Databases/Collections/Metrics/Read | Odczytuje metryki kolekcji. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Databases/Collections/partitionKeyRangeId/Metrics/Read | Odczytaj metryki na poziomie klucza partycji konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/bazy danych/kolekcje/partycje/metryki/odczyt | Odczytaj metryki na poziomie partycji konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Databases/Collections/Partitions/Read | Odczytywanie partycji konta bazy danych w kolekcji |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/bazy danych/kolekcje/partycje/użycia/odczyt | Odczytaj użycie poziomów partycji konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/bazy danych/kolekcje/użycia/odczyt | Odczytuje użycie kolekcji. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Databases/metricDefinitions/Read | Odczytuje definicje metryk bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Databases/Metrics/Read | Odczytuje metryki bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/bazy danych/użycia/odczyt | Odczytuje użycie bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Delete | Usuwa konta bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/failoverPriorityChange/akcja | Zmień priorytety trybu failover regionów konta bazy danych. Służy do wykonywania ręcznej operacji przełączania do trybu failover |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/akcja | Pobieranie zasad tworzenia kopii zapasowej konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/akcja | Pobieranie parametrów połączenia dla konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/listKeys/akcja | Lista kluczy konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/metricDefinitions/Read | Odczytuje definicje metryk konta bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Metrics/odczyt | Odczytuje metryki konta bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/offlineRegion/akcja | Offline w regionie konta bazy danych.  |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/onlineRegion/akcja | Online region konta bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/operationResults/Read | Stan odczytu operacji asynchronicznej |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/percentyl/metryki/odczyt | Metryki opóźnienia odczytu |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/percentyl/odczyt | Odczytaj percentyle opóźnień replikacji |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/percentyl/sourceRegion/targetRegion/Metrics/Read | Odczytaj metryki opóźnienia dla określonego źródła i regionu docelowego |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/percentyl/targetRegion/Metrics/Read | Odczytaj metryki opóźnienia dla określonego regionu docelowego |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Delete | Usuń serwer proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Read | Odczytaj prywatny serwer proxy połączenia dla konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Validate/akcja | Weryfikowanie serwera proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Write | Utwórz lub zaktualizuj serwer proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/odczyt | Odczytuje konto bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/readonlykeys/akcja | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Read | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/regenerateKey/akcja | Obróć klucze konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/region/bazy danych/kolekcje/metryki/odczyt | Odczytuje metryki kolekcji regionalnej. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/region/Databases/Collections/partitionKeyRangeId/Metrics/Read | Odczytaj metryki na poziomie klucza partycji konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/region/bazy danych/kolekcje/partycje/metryki/odczyt | Odczytaj metryki na poziomie partycji konta bazy danych |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/region/bazy danych/kolekcje/partycje/odczyt | Odczytywanie partycji konta bazy danych regionalnych w kolekcji |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/region/metryki/odczyt | Odczytuje metryki konta regionu i bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Prześlij żądanie przywrócenia |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Usages/Read | Odczytuje użycie konta bazy danych. |
> | Działanie | Microsoft. DocumentDB/databaseAccounts/Write | Zaktualizuj konta bazy danych. |
> | Działanie | Microsoft. DocumentDB/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadamia Microsoft. DocumentDB, że VirtualNetwork lub podsieć jest usuwana |
> | Działanie | Microsoft. DocumentDB/Locations/deleteVirtualNetworkOrSubnets/operationResults/Read | Odczytaj stan operacji asynchronicznej deleteVirtualNetworkOrSubnets |
> | Działanie | Microsoft. DocumentDB/Locations/operationsStatus/Read | Odczytuje stan operacji asynchronicznych |
> | Działanie | Microsoft. DocumentDB/operationResults/odczyt | Stan odczytu operacji asynchronicznej |
> | Działanie | Microsoft. DocumentDB/Operations/Read | Operacje odczytu dostępne dla programu Microsoft DocumentDBe  |
> | Działanie | Microsoft. DocumentDB/Register/Action |  Zarejestruj dostawcę zasobów Microsoft DocumentDB dla subskrypcji |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

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
> | Działanie | Microsoft. DomainRegistration/topLevelDomains/listAgreements/akcja | Lista akcja umowy |
> | Działanie | Microsoft. DomainRegistration/topLevelDomains/odczyt | Pobierz domeny Toplevel |
> | Działanie | Microsoft. DomainRegistration/topLevelDomains/odczyt | Pobierz domenę Toplevel |
> | Działanie | Microsoft. DomainRegistration/validateDomainRegistrationInformation/Action | Sprawdzanie poprawności obiektu zakupu domeny bez jego przesyłania |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. EventGrid/domeny/usuwanie | Usuwanie domeny |
> | Działanie | Microsoft. EventGrid/domeny/listKeys/akcja | Wyświetl klucze dla domeny |
> | Działanie | Microsoft. EventGrid/domeny/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla domen |
> | Działanie | Microsoft. EventGrid/domeny/odczyt | Odczytaj domenę |
> | Działanie | Microsoft. EventGrid/domeny/regenerateKey/akcja | Wygeneruj ponownie klucz dla domeny |
> | Działanie | Microsoft. EventGrid/domen/tematów/tematy/Usuń | Usuwanie tematu domeny |
> | Działanie | Microsoft. EventGrid/domeny/tematy/Odczytaj | Odczytaj temat domeny |
> | Działanie | Microsoft. EventGrid/domen/tematy/Zapisz | Utwórz lub zaktualizuj temat domeny |
> | Działanie | Microsoft. EventGrid/domeny/zapis | Utwórz lub zaktualizuj domenę |
> | Działanie | Microsoft. EventGrid/eventSubscriptions/Delete | Usuń eventSubscription |
> | Działanie | Microsoft. EventGrid/eventSubscriptions/getFullUrl/akcja | Pobierz pełny adres URL dla subskrypcji zdarzeń |
> | Działanie | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Działanie | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Działanie | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla eventSubscriptions |
> | Działanie | Microsoft. EventGrid/eventSubscriptions/odczyt | Odczytaj eventSubscription |
> | Działanie | Microsoft. EventGrid/eventSubscriptions/Write | Utwórz lub zaktualizuj eventSubscription |
> | Działanie | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla tematów |
> | Działanie | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Działanie | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla tematów |
> | Działanie | Microsoft. EventGrid/extensionTopics/odczyt | Odczytaj extensionTopic. |
> | Działanie | Microsoft. EventGrid/Locations/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | Działanie | Microsoft. EventGrid/Locations/operationResults/Read | Przeczytaj wynik operacji regionalnej |
> | Działanie | Microsoft. EventGrid/Locations/operationsStatus/Read | Odczytywanie stanu operacji regionalnej |
> | Działanie | Microsoft. EventGrid/Locations/topictypes/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | Działanie | Microsoft. EventGrid/operationResults/odczyt | Odczytaj wynik operacji |
> | Działanie | Microsoft. EventGrid/Operations/Read | Utwórz listę operacji EventGrid. |
> | Działanie | Microsoft. EventGrid/operationsStatus/odczyt | Odczytywanie stanu operacji |
> | Działanie | Microsoft. EventGrid/Register/Action | Rejestruje subskrypcję dostawcy zasobów EventGrid. |
> | Działanie | Microsoft. EventGrid/tematy/Usuń | Usuwanie tematu |
> | Działanie | Microsoft. EventGrid/tematy/listKeys/akcja | Lista kluczy tematu |
> | Działanie | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla tematów |
> | Działanie | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Działanie | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla tematów |
> | Działanie | Microsoft. EventGrid/tematy/odczyt | Zapoznaj się z tematem |
> | Działanie | Microsoft. EventGrid/tematy/regenerateKey/akcja | Wygeneruj ponownie klucz dla tematu |
> | Działanie | Microsoft. EventGrid/tematy/zapis | Utwórz lub zaktualizuj temat |
> | Działanie | Microsoft. EventGrid/topictypes/eventSubscriptions/Read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | Działanie | Microsoft. EventGrid/topictypes/eventtypes/Read | Odczytaj zdarzenia typu obsługiwane przez element temattype |
> | Działanie | Microsoft. EventGrid/topictypes/odczyt | Odczytaj element tematu |
> | Działanie | Microsoft. EventGrid/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy zasobów EventGrid. |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. EventHub/availableClusterRegions/odczyt | Operacja odczytu, aby wyświetlić listę dostępnych wstępnie inicjowanych klastrów według regionów platformy Azure. |
> | Działanie | Microsoft. EventHub/checkNameAvailability/akcja | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft. EventHub/checkNamespaceAvailability/akcja | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Działanie | Microsoft. EventHub/klastry/usuwanie | Usuwa istniejący zasób klastra. |
> | Działanie | Microsoft. EventHub/klastry/przestrzenie nazw/odczyt | Wyświetl listę identyfikatorów ARM obszaru nazw dla przestrzeni nazw w klastrze. |
> | Działanie | Microsoft. EventHub/klastry/operationresults/odczyt | Pobierz stan asynchronicznej operacji klastra. |
> | Działanie | Microsoft. EventHub/klastry/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk klastra |
> | Działanie | Microsoft. EventHub/klastry/odczyt | Pobiera opis zasobu klastra |
> | Działanie | Microsoft. EventHub/klastry/zapis | Tworzy lub modyfikuje istniejący zasób klastra. |
> | Działanie | Microsoft. EventHub/lokalizacje/deleteVirtualNetworkOrSubnets/akcja | Usuwa reguły sieci wirtualnej w dostawcy zasobów EventHub dla określonej sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/reguł autoryzacji/ListKeys/Action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/reguł autoryzacji/odczyt | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/breakPairing/Action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/disasterrecoveryconfigs/checkNameAvailability/Action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/Delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/tryb failover/akcja | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/Read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/Write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Action | Operacja aktualizowania centrum EventHub. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji EventHub |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do centrum EventHub |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/odczyt |  Pobierz listę reguł autoryzacji EventHub |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Write | Utwórz reguły autoryzacji EventHub i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventHubs/consumergroups/Delete | Operacja usuwania zasobu w ramach odbiorcy |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventHubs/consumergroups/odczyt | Pobierz listę opisów zasobów dla odbiorców |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventHubs/consumergroups/Write | Utwórz lub zaktualizuj właściwości odbiorcy. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/Delete | Operacja usuwania zasobu EventHub |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/Read | Pobierz listę opisów zasobów EventHub |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/eventhubs/Write | Utwórz lub zaktualizuj właściwości EventHub. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Delete | Usuń zasób filtru IP |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Read | Pobierz zasób filtru IP |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Write | Utwórz zasób filtru IP |
> | Akcja dataaction | Microsoft. EventHub/przestrzenie nazw/komunikaty/odbieranie/akcja | Odbieranie komunikatów |
> | Akcja dataaction | Microsoft. EventHub/przestrzenie nazw/komunikaty/wysyłanie/akcja | Wysyłanie komunikatów |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/networkruleset/Delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/networkruleset/Read | Pobiera zasób NetworkRuleSet |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/networkruleset/Write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/networkrulesets/Delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/networkrulesets/Read | Pobiera zasób NetworkRuleSet |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/networkrulesets/Write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/operationresults/Read | Pobierz stan operacji przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobierz listę opisów zasobów dzienników przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/removeAcsNamepsace/Action | Usuń przestrzeń nazw ACS |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/virtualNetworkRules/Delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/virtualNetworkRules/Read | Pobiera zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/virtualNetworkRules/Write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. EventHub/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Działanie | Microsoft. EventHub/Operations/Read | Pobierz operacje |
> | Działanie | Microsoft. EventHub/rejestr/akcja | Rejestruje subskrypcję dostawcy zasobów centrum zdarzeń i umożliwia tworzenie zasobów EventHub |
> | Działanie | Microsoft. EventHub/SKU/odczyt | Pobierz listę opisów zasobów jednostki SKU |
> | Działanie | Microsoft. EventHub/SKU/regiony/odczyt | Pobierz listę opisów zasobów SkuRegions |
> | Działanie | Microsoft. EventHub/Wyrejestruj/akcja | Rejestruje dostawcę zasobów centrum zdarzeń |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Features/Features/Read | Pobiera funkcje subskrypcji. |
> | Działanie | Microsoft. Features/Operations/Read | Pobiera listę operacji. |
> | Działanie | Microsoft. Features/Providers/Features/Read | Pobiera funkcję subskrypcji dla danego dostawcy zasobów. |
> | Działanie | Microsoft. Features/Providers/Features/Register/Action | Rejestruje funkcję subskrypcji dla danego dostawcy zasobów. |
> | Działanie | Microsoft. Features/Providers/Features/Unregister/Action | Wyrejestrowuje funkcję subskrypcji w ramach danego dostawcy zasobów. |
> | Działanie | Microsoft. Features/Register/Action | Rejestruje funkcję subskrypcji. |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. GuestConfiguration/guestConfigurationAssignments/Delete | Usuń przypisanie konfiguracji gościa. |
> | Działanie | Microsoft. GuestConfiguration/guestConfigurationAssignments/odczyt | Pobierz przypisanie konfiguracji gościa. |
> | Działanie | Microsoft. GuestConfiguration/guestConfigurationAssignments/Reports/Read | Pobierz raport dotyczący przypisywania konfiguracji gościa. |
> | Działanie | Microsoft. GuestConfiguration/guestConfigurationAssignments/Write | Utwórz nowe przypisanie konfiguracji gościa. |
> | Działanie | Microsoft. GuestConfiguration/Operations/Read | Pobiera operacje dla dostawcy zasobów Microsoft. GuestConfiguration |
> | Działanie | Microsoft. GuestConfiguration/Register/Action | Rejestruje subskrypcję dostawcy zasobów Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

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

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ImportExport/Jobs/Delete | Usuwa istniejące zadanie. |
> | Działanie | Microsoft. ImportExport/Jobs/listBitLockerKeys/akcja | Pobiera klucze funkcji BitLocker dla określonego zadania. |
> | Działanie | Microsoft. ImportExport/Jobs/Read | Pobiera właściwości określonego zadania lub zwraca listę zadań. |
> | Działanie | Microsoft. ImportExport/Jobs/Write | Tworzy zadanie z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego zadania. |
> | Działanie | Microsoft. ImportExport/lokalizacje/odczyt | Pobiera właściwości dla określonej lokalizacji lub zwraca listę lokalizacji. |
> | Działanie | Microsoft. ImportExport/Operations/Read | Pobiera operacje obsługiwane przez dostawcę zasobów. |
> | Działanie | Microsoft. ImportExport/Register/Action | Rejestruje subskrypcję dostawcy zasobów importu/eksportu i umożliwia tworzenie zadań importu/eksportu. |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Insights/ActionGroups/Delete | Usuwanie grupy akcji |
> | Działanie | Microsoft. Insights/ActionGroups/Read | Odczytaj grupę akcji |
> | Działanie | Microsoft. Insights/ActionGroups/Write | Utwórz lub Zaktualizuj grupę akcji |
> | Działanie | Microsoft. Insights/ActivityLogAlerts/aktywowany/akcja | Uaktywniono alert dziennika aktywności |
> | Działanie | Microsoft. Insights/ActivityLogAlerts/Delete | Usuwanie alertu dziennika aktywności |
> | Działanie | Microsoft. Insights/ActivityLogAlerts/Read | Odczytaj alert dotyczący dziennika aktywności |
> | Działanie | Microsoft. Insights/ActivityLogAlerts/Write | Utwórz lub zaktualizuj alert dotyczący dziennika aktywności |
> | Działanie | Microsoft. Insights/AlertRules/aktywowany/akcja | Uaktywniono alert dotyczący metryki klasycznej |
> | Działanie | Microsoft. Insights/AlertRules/Delete | Usuwanie alertu dotyczącego klasycznej metryki |
> | Działanie | Microsoft. Insights/AlertRules/incydenty/odczyt | Odczytaj zdarzenie klasycznego alertu dotyczącego metryki |
> | Działanie | Microsoft. Insights/AlertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | Działanie | Microsoft. Insights/AlertRules/rozwiązany/akcja | Rozwiązano alert dotyczący metryki klasycznej |
> | Działanie | Microsoft. Insights/AlertRules/z ograniczeniami/akcją | Ograniczona reguła alertu dotyczącego metryki klasycznej |
> | Działanie | Microsoft. Insights/AlertRules/Write | Utwórz lub zaktualizuj klasyczny alert dotyczący metryki |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Delete | Usuń ustawienie automatycznego skalowania |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/logDefinitions/Read | Odczytaj definicje dzienników |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Read | Odczytaj ustawienie automatycznego skalowania |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Scaledown/Action | Inicjowano skalowanie w poziomie automatycznego skalowania |
> | Działanie | Microsoft. Insights/AutoscaleSettings/ScaledownResult/Action | Ukończono Skalowanie automatyczne w dół |
> | Działanie | Microsoft. Insights/AutoscaleSettings/Scaleup/Action | Zainicjowanie skalowania automatycznego skalowania w górę |
> | Działanie | Microsoft. Insights/AutoscaleSettings/ScaleupResult/Action | Ukończenie skalowania automatycznego skalowania w górę |
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
> | Działanie | Microsoft. Insights/Components/MyAnalyticsItems/Delete | Usuwanie Application Insights osobistego elementu analizy |
> | Działanie | Microsoft. Insights/Components/MyAnalyticsItems/Read | Odczytywanie Application Insights osobistego elementu analizy |
> | Działanie | Microsoft. Insights/Components/MyAnalyticsItems/Write | Pisanie Application Insights osobistego elementu analizy |
> | Działanie | Microsoft. Insights/Components/webulubionych/odczyt | Odczytywanie Application Insights osobistego elementu ulubionego |
> | Działanie | Microsoft. Insights/Components/Operations/Read | Pobieranie stanu długotrwałych operacji w Application Insights |
> | Działanie | Microsoft. Insights/Components/PricingPlans/Read | Odczytywanie planu cenowego składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/PricingPlans/Write | Pisanie planu cenowego składnika Application Insights |
> | Działanie | Microsoft. Insights/Components/ProactiveDetectionConfigs/Read | Odczytywanie konfiguracji wykrywania proaktywnego Application Insights |
> | Działanie | Microsoft. Insights/Components/ProactiveDetectionConfigs/Write | Pisanie Application Insights konfiguracji wykrywania proaktywnego |
> | Działanie | Microsoft. Insights/Components/Providers/Microsoft. Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft. Insights/Components/Przeczyść/akcja | Przeczyszczanie danych z Application Insights |
> | Działanie | Microsoft. Insights/Components/Query/Read | Uruchamianie zapytań względem dzienników Application Insights |
> | Działanie | Microsoft. Insights/Components/QuotaStatus/Read | Odczytywanie stanu przydziału składników Application Insights |
> | Działanie | Microsoft. Insights/Components/Read | Odczytywanie konfiguracji składnika usługi Application Insights |
> | Działanie | Microsoft. Insights/Components/SyntheticMonitorLocations/Read | Odczytywanie Application Insights lokalizacji WebTest |
> | Działanie | Microsoft. Insights/Components/webtests/Read | Odczytywanie konfiguracji WebTest |
> | Działanie | Microsoft. Insights/Components/WorkItemConfigs/Delete | Usuwanie Application Insights ALM Integration Configuration |
> | Działanie | Microsoft. Insights/Components/WorkItemConfigs/Read | Odczytywanie konfiguracji integracji Application Insights ALM |
> | Działanie | Microsoft. Insights/Components/WorkItemConfigs/Write | Pisanie Application Insights ALM Integration Configuration |
> | Działanie | Microsoft. Insights/Components/Write | Zapisywanie w konfiguracji składnika usługi Application Insights |
> | Działanie | Microsoft. Insights/DiagnosticSettings/Delete | Usuń ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft. Insights/DiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft. Insights/DiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | Działanie | Microsoft. Insights/EventCategories/Read | Odczytaj dostępne kategorie zdarzeń dziennika aktywności |
> | Działanie | Microsoft. Insights/eventtypes/digestevents/Read | Odczytaj podsumowanie typu zdarzenia zarządzania |
> | Działanie | Microsoft. Insights/eventtypes/Values/odczyty | Odczytaj zdarzenia dziennika aktywności |
> | Działanie | Microsoft. Insights/ExtendedDiagnosticSettings/Delete | Usuń ustawienie diagnostyczne dziennika przepływu sieci |
> | Działanie | Microsoft. Insights/ExtendedDiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne dziennika przepływu sieci |
> | Działanie | Microsoft. Insights/ExtendedDiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne dziennika przepływu sieci |
> | Działanie | Microsoft. Insights/ListMigrationDate/Action | Pobierz datę migracji subskrypcji |
> | Działanie | Microsoft. Insights/ListMigrationDate/Read | Pobierz datę migracji subskrypcji |
> | Działanie | Microsoft. Insights/LogDefinitions/Read | Odczytaj definicje dzienników |
> | Działanie | Microsoft. Insights/LogProfiles/Delete | Usuwanie profilu dziennika aktywności |
> | Działanie | Microsoft. Insights/LogProfiles/Read | Odczytaj profil dziennika aktywności |
> | Działanie | Microsoft. Insights/LogProfiles/Write | Utwórz lub zaktualizuj profil dziennika aktywności |
> | Działanie | Microsoft. Insights/Logs/ADAssessmentRecommendation/Read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/ADReplicationResult/Read | Odczytaj dane z tabeli ADReplicationResult |
> | Działanie | Microsoft. Insights/Logs/ADSecurityAssessmentRecommendation/Read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/alerty/odczyt | Odczytaj dane z tabeli alertów |
> | Działanie | Microsoft. Insights/Logs/AlertHistory/Read | Odczytaj dane z tabeli AlertHistory |
> | Działanie | Microsoft. Insights/Logs/ApplicationInsights/Read | Odczytaj dane z tabeli ApplicationInsights |
> | Działanie | Microsoft. Insights/Logs/Azure/odczyt | Odczytywanie danych z tabeli platformy Azure |
> | Działanie | Microsoft. Insights/Logs/AzureMetrics/Read | Odczytaj dane z tabeli AzureMetrics |
> | Działanie | Microsoft. Insights/Logs/BoundPort/Read | Odczytaj dane z tabeli BoundPort |
> | Działanie | Microsoft. Insights/Logs/CommonSecurityLog/Read | Odczytaj dane z tabeli CommonSecurityLog |
> | Działanie | Microsoft. Insights/dzienniki/komputery/odczyt | Odczytywanie danych z tabeli komputerów |
> | Działanie | Microsoft. Insights/Logs/Zmianakonfiguracji/Read | Odczytaj dane z tabeli Zmianakonfiguracji |
> | Działanie | Microsoft. Insights/Logs/ConfigurationData/Read | Odczytaj dane z tabeli ConfigurationData |
> | Działanie | Microsoft. Insights/Logs/ContainerImageInventory/Read | Odczytaj dane z tabeli ContainerImageInventory |
> | Działanie | Microsoft. Insights/Logs/ContainerInventory/Read | Odczytaj dane z tabeli ContainerInventory |
> | Działanie | Microsoft. Insights/Logs/ContainerLog/Read | Odczytaj dane z tabeli ContainerLog |
> | Działanie | Microsoft. Insights/Logs/ContainerServiceLog/Read | Odczytaj dane z tabeli ContainerServiceLog |
> | Działanie | Microsoft. Insights/Logs/DeviceAppCrash/Read | Odczytaj dane z tabeli DeviceAppCrash |
> | Działanie | Microsoft. Insights/Logs/DeviceAppLaunch/Read | Odczytaj dane z tabeli DeviceAppLaunch |
> | Działanie | Microsoft. Insights/Logs/DeviceCalendar/Read | Odczytaj dane z tabeli DeviceCalendar |
> | Działanie | Microsoft. Insights/Logs/DeviceCleanup/Read | Odczytaj dane z tabeli DeviceCleanup |
> | Działanie | Microsoft. Insights/Logs/DeviceConnectSession/Read | Odczytaj dane z tabeli DeviceConnectSession |
> | Działanie | Microsoft. Insights/Logs/DeviceEtw/Read | Odczytaj dane z tabeli DeviceEtw |
> | Działanie | Microsoft. Insights/Logs/DeviceHardwareHealth/Read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | Działanie | Microsoft. Insights/Logs/zameldowaniem/Read | Odczytaj dane z tabeli zameldowaniem |
> | Działanie | Microsoft. Insights/Logs/DeviceHeartbeat/Read | Odczytaj dane z tabeli DeviceHeartbeat |
> | Działanie | Microsoft. Insights/Logs/DeviceSkypeHeartbeat/Read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | Działanie | Microsoft. Insights/Logs/DeviceSkypeSignIn/Read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | Działanie | Microsoft. Insights/Logs/DeviceSleepState/Read | Odczytaj dane z tabeli DeviceSleepState |
> | Działanie | Microsoft. Insights/Logs/DHAppFailure/Read | Odczytaj dane z tabeli DHAppFailure |
> | Działanie | Microsoft. Insights/Logs/DHAppReliability/Read | Odczytaj dane z tabeli DHAppReliability |
> | Działanie | Microsoft. Insights/Logs/DHDriverReliability/Read | Odczytaj dane z tabeli DHDriverReliability |
> | Działanie | Microsoft. Insights/Logs/DHLogonFailures/Read | Odczytaj dane z tabeli DHLogonFailures |
> | Działanie | Microsoft. Insights/Logs/DHLogonMetrics/Read | Odczytaj dane z tabeli DHLogonMetrics |
> | Działanie | Microsoft. Insights/Logs/DHOSCrashData/Read | Odczytaj dane z tabeli DHOSCrashData |
> | Działanie | Microsoft. Insights/Logs/DHOSReliability/Read | Odczytaj dane z tabeli DHOSReliability |
> | Działanie | Microsoft. Insights/Logs/DHWipAppLearning/Read | Odczytaj dane z tabeli DHWipAppLearning |
> | Działanie | Microsoft. Insights/Logs/DnsEvents/Read | Odczytaj dane z tabeli DnsEvents |
> | Działanie | Microsoft. Insights/Logs/DnsInventory/Read | Odczytaj dane z tabeli DnsInventory |
> | Działanie | Microsoft. Insights/Logs/ETWEvent/Read | Odczytaj dane z tabeli ETWEvent |
> | Działanie | Microsoft. Insights/Logs/Event/Read | Odczytaj dane z tabeli zdarzeń |
> | Działanie | Microsoft. Insights/Logs/ExchangeAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | Działanie | Microsoft. Insights/dzienniki/puls/odczyt | Odczytaj dane z tabeli pulsu |
> | Działanie | Microsoft. Insights/Logs/IISAssessmentRecommendation/Read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/InboundConnection/Read | Odczytaj dane z tabeli InboundConnection |
> | Działanie | Microsoft. Insights/Logs/KubeNodeInventory/Read | Odczytaj dane z tabeli KubeNodeInventory |
> | Działanie | Microsoft. Insights/Logs/KubePodInventory/Read | Odczytaj dane z tabeli KubePodInventory |
> | Działanie | Microsoft. Insights/Logs/LinuxAuditLog/Read | Odczytaj dane z tabeli LinuxAuditLog |
> | Działanie | Microsoft. Insights/Logs/MAApplication/Read | Odczytaj dane z tabeli MAApplication |
> | Działanie | Microsoft. Insights/Logs/MAApplicationHealth/Read | Odczytaj dane z tabeli MAApplicationHealth |
> | Działanie | Microsoft. Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | Działanie | Microsoft. Insights/Logs/MAApplicationHealthIssues/Read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | Działanie | Microsoft. Insights/Logs/MAApplicationInstance/Read | Odczytaj dane z tabeli MAApplicationInstance |
> | Działanie | Microsoft. Insights/Logs/MAApplicationInstanceReadiness/Read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | Działanie | Microsoft. Insights/Logs/MAApplicationReadiness/Read | Odczytaj dane z tabeli MAApplicationReadiness |
> | Działanie | Microsoft. Insights/Logs/MADeploymentPlan/Read | Odczytaj dane z tabeli MADeploymentPlan |
> | Działanie | Microsoft. Insights/Logs/MADevice/Read | Odczytaj dane z tabeli MADevice |
> | Działanie | Microsoft. Insights/Logs/MADevicePnPHealth/Read | Odczytaj dane z tabeli MADevicePnPHealth |
> | Działanie | Microsoft. Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | Działanie | Microsoft. Insights/Logs/MADevicePnPHealthIssues/Read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | Działanie | Microsoft. Insights/Logs/MADeviceReadiness/Read | Odczytaj dane z tabeli MADeviceReadiness |
> | Działanie | Microsoft. Insights/Logs/MADriverInstanceReadiness/Read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | Działanie | Microsoft. Insights/Logs/MADriverReadiness/Read | Odczytaj dane z tabeli MADriverReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddin/Read | Odczytaj dane z tabeli MAOfficeAddin |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddinHealth/Read | Odczytaj dane z tabeli MAOfficeAddinHealth |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddinHealthIssues/Read | Odczytaj dane z tabeli MAOfficeAddinHealthIssues |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddinInstance/Read | Odczytaj dane z tabeli MAOfficeAddinInstance |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinInstanceReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAddinReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeApp/Read | Odczytaj dane z tabeli MAOfficeApp |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAppHealth/Read | Odczytaj dane z tabeli MAOfficeAppHealth |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAppInstance/Read | Odczytaj dane z tabeli MAOfficeAppInstance |
> | Działanie | Microsoft. Insights/Logs/MAOfficeAppReadiness/Read | Odczytaj dane z tabeli MAOfficeAppReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeBuildInfo/Read | Odczytaj dane z tabeli MAOfficeBuildInfo |
> | Działanie | Microsoft. Insights/Logs/MAOfficeCurrencyAssessment/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessment |
> | Działanie | Microsoft. Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | Działanie | Microsoft. Insights/Logs/MAOfficeDeploymentStatus/Read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroHealth/Read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroHealthIssues/Read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroIssueReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | Działanie | Microsoft. Insights/Logs/MAOfficeMacroSummary/Read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | Działanie | Microsoft. Insights/Logs/MAOfficeSuite/Read | Odczytaj dane z tabeli MAOfficeSuite |
> | Działanie | Microsoft. Insights/Logs/MAOfficeSuiteInstance/Read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | Działanie | Microsoft. Insights/Logs/MAProposedPilotDevices/Read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | Działanie | Microsoft. Insights/Logs/MAWindowsBuildInfo/Read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | Działanie | Microsoft. Insights/Logs/MAWindowsCurrencyAssessment/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | Działanie | Microsoft. Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Działanie | Microsoft. Insights/Logs/MAWindowsDeploymentStatus/Read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | Działanie | Microsoft. Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | Działanie | Microsoft. Insights/Logs/NetworkMonitoring/Read | Odczytaj dane z tabeli NetworkMonitoring |
> | Działanie | Microsoft. Insights/Logs/dzienniki/Office/odczyt | Odczytaj dane z tabeli pakietu Office |
> | Działanie | Microsoft. Insights/Logs/dzienników/operacji/odczytu | Odczytaj dane z tabeli operacji |
> | Działanie | Microsoft. Insights/Logs/OutboundConnection/Read | Odczytaj dane z tabeli OutboundConnection |
> | Działanie | Microsoft. Insights/Logs/perf/Read | Odczytaj dane z tabeli wydajności |
> | Działanie | Microsoft. Insights/Logs/ProtectionStatus/Read | Odczytaj dane z tabeli ProtectionStatus |
> | Działanie | Microsoft. Insights/Logs/odczyt | Odczytywanie danych ze wszystkich dzienników |
> | Działanie | Microsoft. Insights/Logs/ReservedAzureCommonFields/Read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | Działanie | Microsoft. Insights/Logs/ReservedCommonFields/Read | Odczytaj dane z tabeli ReservedCommonFields |
> | Działanie | Microsoft. Insights/Logs/SCCMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SCOMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SecurityAlert/Read | Odczytaj dane z tabeli SecurityAlert |
> | Działanie | Microsoft. Insights/Logs/SecurityBaseline/Read | Odczytaj dane z tabeli SecurityBaseline |
> | Działanie | Microsoft. Insights/Logs/SecurityBaselineSummary/Read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | Działanie | Microsoft. Insights/Logs/SecurityDetection/Read | Odczytaj dane z tabeli SecurityDetection |
> | Działanie | Microsoft. Insights/Logs/SecurityEvent/Read | Odczytaj dane z tabeli SecurityEvent |
> | Działanie | Microsoft. Insights/Logs/ServiceFabricOperationalEvent/Read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | Działanie | Microsoft. Insights/Logs/ServiceFabricReliableActorEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | Działanie | Microsoft. Insights/Logs/ServiceFabricReliableServiceEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | Działanie | Microsoft. Insights/Logs/SfBAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SPAssessmentRecommendation/Read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SQLAssessmentRecommendation/Read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/SQLQueryPerformance/Read | Odczytaj dane z tabeli SQLQueryPerformance |
> | Działanie | Microsoft. Insights/Logs/dziennik systemowy/odczyt | Odczytaj dane z tabeli dziennika systemowego |
> | Działanie | Microsoft. Insights/Logs/SysmonEvent/Read | Odczytaj dane z tabeli SysmonEvent |
> | Działanie | Microsoft. Insights/dzienniki/tabele. Custom/Read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | Działanie | Microsoft. Insights/Logs/UAApp/Read | Odczytaj dane z tabeli UAApp |
> | Działanie | Microsoft. Insights/Logs/UAComputer/Read | Odczytaj dane z tabeli UAComputer |
> | Działanie | Microsoft. Insights/Logs/UAComputerRank/Read | Odczytaj dane z tabeli UAComputerRank |
> | Działanie | Microsoft. Insights/Logs/UADriver/Read | Odczytaj dane z tabeli UADriver |
> | Działanie | Microsoft. Insights/Logs/UADriverProblemCodes/Read | Odczytaj dane z tabeli UADriverProblemCodes |
> | Działanie | Microsoft. Insights/Logs/UAFeedback/Read | Odczytaj dane z tabeli UAFeedback |
> | Działanie | Microsoft. Insights/Logs/UAHardwareSecurity/Read | Odczytaj dane z tabeli UAHardwareSecurity |
> | Działanie | Microsoft. Insights/Logs/UAIESiteDiscovery/Read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | Działanie | Microsoft. Insights/Logs/UAOfficeAddIn/Read | Odczytaj dane z tabeli UAOfficeAddIn |
> | Działanie | Microsoft. Insights/Logs/UAProposedActionPlan/Read | Odczytaj dane z tabeli UAProposedActionPlan |
> | Działanie | Microsoft. Insights/Logs/UASysReqIssue/Read | Odczytaj dane z tabeli UASysReqIssue |
> | Działanie | Microsoft. Insights/Logs/UAUpgradedComputer/Read | Odczytaj dane z tabeli UAUpgradedComputer |
> | Działanie | Microsoft. Insights/Logs/Update/Read | Odczytaj dane z tabeli aktualizacji |
> | Działanie | Microsoft. Insights/Logs/UpdateRunProgress/Read | Odczytaj dane z tabeli UpdateRunProgress |
> | Działanie | Microsoft. Insights/Logs/UpdateSummary/Read | Odczytaj dane z tabeli UpdateSummary |
> | Działanie | Microsoft. Insights/Logs/dzienniki/użycie/odczyt | Odczytywanie danych z tabeli użycia |
> | Działanie | Microsoft. Insights/Logs/W3CIISLog/Read | Odczytaj dane z tabeli W3CIISLog |
> | Działanie | Microsoft. Insights/Logs/WaaSDeploymentStatus/Read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | Działanie | Microsoft. Insights/Logs/WaaSInsiderStatus/Read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | Działanie | Microsoft. Insights/Logs/WaaSUpdateStatus/Read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | Działanie | Microsoft. Insights/Logs/WDAVStatus/Read | Odczytaj dane z tabeli WDAVStatus |
> | Działanie | Microsoft. Insights/Logs/WDAVThreat/Read | Odczytaj dane z tabeli WDAVThreat |
> | Działanie | Microsoft. Insights/Logs/WindowsClientAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/WindowsFirewall/Read | Odczytaj dane z tabeli WindowsFirewall |
> | Działanie | Microsoft. Insights/Logs/WindowsServerAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | Działanie | Microsoft. Insights/Logs/typowe/Read | Odczytaj dane z tabeli typowe |
> | Działanie | Microsoft. Insights/Logs/WUDOAggregatedStatus/Read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | Działanie | Microsoft. Insights/Logs/WUDOStatus/Read | Odczytaj dane z tabeli WUDOStatus |
> | Działanie | Microsoft. Insights/MetricAlerts/Delete | Usuwanie alertu metryki |
> | Działanie | Microsoft. Insights/MetricAlerts/Read | Odczytaj alert dotyczący metryki |
> | Działanie | Microsoft. Insights/MetricAlerts/status/Read | Odczytaj stan alertu metryki |
> | Działanie | Microsoft. Insights/MetricAlerts/Write | Utwórz lub zaktualizuj alert dotyczący metryki |
> | Działanie | Microsoft. Insights/MetricBaselines/Read | Odczytaj linie bazowe metryki |
> | Działanie | Microsoft. Insights/MetricDefinitions/Microsoft. Insights/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft. Insights/MetricDefinitions/Providers/Microsoft. Insights/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft. Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft. Insights/Metricnamespaces/Read | Odczytaj przestrzenie nazw metryk |
> | Działanie | Microsoft. Insights/Metrics/Action | Akcja metryki |
> | Działanie | Microsoft. Insights/Metrics/Microsoft. Insights/Read | Odczytaj metryki |
> | Działanie | Microsoft. Insights/Metrics/Providers/Metrics/Read | Odczytaj metryki |
> | Działanie | Microsoft. Insights/Metrics/Read | Odczytaj metryki |
> | Akcja dataaction | Microsoft. Insights/Metrics/Write | Metryki zapisu |
> | Działanie | Microsoft. Insights/MigrateToNewpricingModel/Action | Migruj subskrypcję do nowego modelu cen |
> | Działanie | Microsoft. Insights/Operations/Read | Operacje odczytu |
> | Działanie | Microsoft. Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Działanie | Microsoft. Insights/RollbackToLegacyPricingModel/Action | Wycofywanie subskrypcji do starszego modelu cen |
> | Działanie | Microsoft. Insights/ScheduledQueryRules/Delete | Usuwanie reguły zaplanowanego zapytania |
> | Działanie | Microsoft. Insights/ScheduledQueryRules/Read | Odczytywanie reguły zaplanowanego zapytania |
> | Działanie | Microsoft. Insights/ScheduledQueryRules/Write | Pisanie reguły zaplanowanego zapytania |
> | Działanie | Microsoft. Insights/dzierżawcy/rejestr/akcja | Inicjuje dostawcę usługi Microsoft Insights |
> | Działanie | Microsoft. Insights/Unregister/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Działanie | Microsoft. Insights/webtests/Delete | Usuwanie konfiguracji WebTest |
> | Działanie | Microsoft. Insights/webtests/GetToken/Read | Odczytywanie tokenu WebTest |
> | Działanie | Microsoft. Insights/webtests/MetricDefinitions/Read | Odczytywanie definicji metryk WebTest |
> | Działanie | Microsoft. Insights/webtests/Metrics/Read | Odczytywanie metryk WebTest |
> | Działanie | Microsoft. Insights/webtests/Read | Odczytywanie konfiguracji WebTest |
> | Działanie | Microsoft. Insights/webtests/Write | Zapisywanie w konfiguracji WebTest |
> | Działanie | Microsoft. Insights/skoroszyty/usuwanie | Usuwanie skoroszytu |
> | Działanie | Microsoft. Insights/skoroszyty/przeczytane | Odczytaj skoroszyt |
> | Działanie | Microsoft. Insights/skoroszyty/zapis | Utwórz lub zaktualizuj skoroszyt |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Intune/diagnosticsettings/Delete | Usuwanie ustawień diagnostycznych |
> | Działanie | Microsoft. Intune/diagnosticsettings/odczyt | Odczytywanie ustawień diagnostycznych |
> | Działanie | Microsoft. Intune/diagnosticsettings/Write | Zapisywanie ustawień diagnostycznych |
> | Działanie | Microsoft. Intune/diagnosticsettingscategories/odczyt | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. IoTCentral/appTemplates/Action | Pobiera wszystkie dostępne szablony aplikacji na platformie Azure IoT Central |
> | Działanie | Microsoft. IoTCentral/checkNameAvailability/Action | Sprawdza, czy nazwa aplikacji IoT Central jest dostępna |
> | Działanie | Microsoft. IoTCentral/checkSubdomainAvailability/Action | Sprawdza, czy IoT Central poddomena aplikacji jest dostępna |
> | Działanie | Microsoft. IoTCentral/IoTApps/Delete | Usuwa IoT Central aplikacje |
> | Działanie | Microsoft. IoTCentral/IoTApps/odczyt | Pobiera pojedynczą aplikację IoT Central |
> | Działanie | Microsoft. IoTCentral/IoTApps/Write | Tworzy lub aktualizuje aplikacje IoT Central |
> | Działanie | Microsoft. IoTCentral/Operations/Read | Pobiera wszystkie dostępne operacje dotyczące IoT Central aplikacji |
> | Działanie | Microsoft. IoTCentral/Register/Action | Zarejestruj subskrypcję dostawcy zasobów usługi Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. IoTSpaces/Graph/Delete | Usuwa zasób programu Microsoft. IoTSpaces Graph |
> | Działanie | Microsoft. IoTSpaces/Graph/odczyt | Pobiera zasoby programu Microsoft. IoTSpaces Graph |
> | Działanie | Microsoft. IoTSpaces/Graph/Write | Utwórz zasób programu Microsoft. IoTSpaces Graph |
> | Działanie | Microsoft. IoTSpaces/Register/Action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. IoTSpaces Graph, aby umożliwić tworzenie zasobów |

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft./Magazyn kluczy/checkNameAvailability/odczyt | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i czy nie jest używana |
> | Działanie | Microsoft./Magazyn kluczy/deletedVaults/odczyt | Wyświetl właściwości usuniętych nietrwałych magazynów kluczy |
> | Działanie | Microsoft./Magazyn kluczy/hsmPools/usuwanie | Usuwanie puli modułów HSM |
> | Działanie | Microsoft. kluczy/hsmPools/joinVault/Action | Dołącz Magazyn kluczy do puli modułów HSM |
> | Działanie | Microsoft./Magazyn kluczy/hsmPools/odczyt | Wyświetlanie właściwości puli modułów HSM |
> | Działanie | Microsoft./Magazyn kluczy/hsmPools/zapis | Utwórz nową pulę modułów HSM, aby zaktualizować właściwości istniejącej puli modułów HSM |
> | Działanie | Microsoft./Magazyn kluczy/lokalizacji/deletedVaults/przeczyszczanie/akcja | Przeczyść usunięty nietrwale Magazyn kluczy |
> | Działanie | Microsoft./Magazyn kluczy/lokalizacji/deletedVaults/odczyt | Wyświetl właściwości usuniętego nietrwałego magazynu kluczy |
> | Działanie | Microsoft./Magazyn kluczy/lokalizacje/deleteVirtualNetworkOrSubnets/akcja | Powiadamia Microsoft. kluczy, że sieć wirtualna lub podsieć jest usuwana |
> | Działanie | Microsoft./Magazyn kluczy/lokalizacji/operationResults/odczyt | Sprawdź wynik długotrwałej operacji uruchamiania |
> | Działanie | Microsoft./Magazyn kluczy/operacje/odczyt | Wyświetla listę operacji dostępnych w ramach dostawcy zasobów Microsoft. |
> | Działanie | Microsoft.//Rejestr/Rejestracja/akcja | Rejestruje subskrypcję |
> | Działanie | Microsoft.//Unregister/akcja | Wyrejestrowuje subskrypcję |
> | Działanie | Microsoft./magazyny kluczy/magazynu/accessPolicies/zapis | Aktualizowanie istniejących zasad dostępu przez scalanie lub zastępowanie lub Dodawanie nowych zasad dostępu do magazynu. |
> | Działanie | Microsoft./magazyny/magazynu/usuwanie | Usuń magazyn kluczy |
> | Działanie | Microsoft./magazyny/magazynu/wdrożenie/akcja | Zapewnia dostęp do wpisów tajnych w magazynie kluczy podczas wdrażania zasobów platformy Azure |
> | Działanie | Microsoft./magazyny/magazynu/eventGridFilters/usuwanie | Powiadamia Microsoft. EventGrid, że jest usuwana subskrypcja usługi dla Key Vault |
> | Działanie | Microsoft./magazyny/magazynu/eventGridFilters/odczyt | Powiadamia Microsoft. EventGrid, że jest wyświetlana subskrypcja usługi dla Key Vault |
> | Działanie | Microsoft./magazyny kluczy/magazynu/eventGridFilters/zapis | Powiadamia Microsoft. kluczy, że jest tworzona nowa subskrypcja EventGrid dla Key Vault |
> | Działanie | Microsoft./magazyny kluczy/magazynu/odczyt | Wyświetlanie właściwości magazynu kluczy |
> | Działanie | Microsoft./magazyny/magazynu/wpisy tajne/odczyty | Wyświetl właściwości wpisu tajnego, ale nie jego wartość. |
> | Działanie | Microsoft./magazyny/magazynu/wpisy tajne/zapis | Utwórz nowy wpis tajny lub zaktualizuj wartość istniejącego klucza tajnego. |
> | Działanie | Microsoft./magazyny kluczy/magazynu/zapis | Utwórz nowy magazyn kluczy lub zaktualizuj właściwości istniejącego magazynu kluczy |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Kusto/klastry/aktywacja/akcja | Uruchamia klaster. |
> | Działanie | Microsoft. Kusto/klastry/AttachedDatabaseConfigurations/usuwanie | Usuwa dołączoną resourceCopy konfiguracji bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/AttachedDatabaseConfigurations/odczyt | Odczytuje załączoną konfigurację bazy danych resourceCopy. |
> | Działanie | Microsoft. Kusto/klastry/AttachedDatabaseConfigurations/zapis | Zapisuje dołączoną resourceCopy konfiguracji bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/CheckNameAvailability/akcja | Sprawdza dostępność nazwy klastra. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/addprincipals/Action | Dodaje podmioty zabezpieczeń bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/CheckNameAvailability/akcja | Sprawdza dostępność nazwy dla danego typu. |
> | Działanie | Microsoft. Kusto/klastry/Databases/dataconnections/Delete | Usuwa resourceCopy połączeń danych. |
> | Działanie | Microsoft. Kusto/klastry/Databases/dataconnections/Read | Odczytuje resourceCopy połączeń danych. |
> | Działanie | Microsoft. Kusto/klastry/Databases/dataconnections/Write | Zapisuje resourceCopy połączeń danych. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/DataConnectionValidation/akcja | Sprawdza poprawność połączenia danych bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/usuwanie | Usuwa resourceCopy bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/EventHubConnections/usuwanie | Usuwa resourceCopy połączeń centrum zdarzeń. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/EventHubConnections/odczyt | Odczytuje resourceCopy połączeń centrum zdarzeń. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/EventHubConnections/zapis | Zapisuje resourceCopy połączeń centrum zdarzeń. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/EventHubConnectionValidation/akcja | Sprawdza poprawność połączenia centrum zdarzeń bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/ListPrincipals/akcja | Wyświetla listę podmiotów zabezpieczeń bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/Odczyt | Odczytuje resourceCopy bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/RemovePrincipals/akcja | Usuwa podmioty zabezpieczeń bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/bazy danych/zapis | Zapisuje resourceCopy bazy danych. |
> | Działanie | Microsoft. Kusto/klastry/dezaktywowanie/akcja | Zamyka klaster. |
> | Działanie | Microsoft. Kusto/klastry/usuwanie | Usuwa klaster resourceCopy. |
> | Działanie | Microsoft. Kusto/klastry/odczyt | Odczytuje resourceCopy klastra. |
> | Działanie | Microsoft. Kusto/klastry/jednostki SKU/odczyt | Odczytuje jednostkę SKU klastra resourceCopy. |
> | Działanie | Microsoft. Kusto/klastry/uruchamianie/akcja | Uruchamia klaster. |
> | Działanie | Microsoft. Kusto/klastry/zatrzymanie/akcja | Zamyka klaster. |
> | Działanie | Microsoft. Kusto/klastry/zapis | Zapisuje resourceCopy klastra. |
> | Działanie | Microsoft. Kusto/DetachFollowerDatabases/Action | Odłącza bazy danych programu. |
> | Działanie | Microsoft. Kusto/ListFollowerDatabases/Action | Wyświetla listę baz danych programu. |
> | Działanie | Microsoft. Kusto/Locations/CheckNameAvailability/Action | Sprawdza dostępność nazwy resourceCopy. |
> | Działanie | Microsoft. Kusto/Locations/operationresults/Read | Odczytuje operacje resourceCopys |
> | Działanie | Microsoft. Kusto/Operations/Read | Odczytuje operacje resourceCopys |
> | Działanie | Microsoft. Kusto/Register/Action | Akcja rejestracji subskrypcji |
> | Działanie | Microsoft. Kusto/Register/Action | Rejestruje subskrypcję u dostawcy zasobów Kusto. |
> | Działanie | Microsoft. Kusto/jednostki SKU/odczyt | Odczytuje resourceCopy jednostki SKU. |
> | Działanie | Microsoft. Kusto/Unregister/Action | Wyrejestrowuje subskrypcję do dostawcy zasobów Kusto. |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. LabServices/labAccounts/CreateLab/akcja | Utwórz laboratorium na koncie laboratorium. |
> | Działanie | Microsoft. LabServices/labAccounts/Delete | Usuń konta laboratorium. |
> | Działanie | Microsoft. LabServices/labAccounts/galleryImages/Delete | Usuń obrazy galerii. |
> | Działanie | Microsoft. LabServices/labAccounts/galleryImages/Read | Odczytuj obrazy galerii. |
> | Działanie | Microsoft. LabServices/labAccounts/galleryImages/Write | Dodawanie lub modyfikowanie obrazów galerii. |
> | Działanie | Microsoft. LabServices/labAccounts/GetRegionalAvailability/akcja | Uzyskaj informacje o dostępności regionalnej dla każdej kategorii rozmiaru skonfigurowanej w ramach konta laboratorium |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/ADDUSERS/Action | Dodawanie użytkowników do laboratorium |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/usuwanie | Usuwanie laboratoriów. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Delete | Usuń ustawienie środowiska. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Delete | Usuń środowiska. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Read | Odczytuj środowiska. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/ResetPassword/Action | Resetuje hasło użytkownika w środowisku |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Start/Action | Uruchamia środowisko przez uruchomienie wszystkich zasobów w środowisku. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/akcja | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Write | Dodaj lub zmodyfikuj środowiska. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Publish/Action | Rezerwy/rezerwowe zasoby wymagane dla ustawienia środowiska na podstawie bieżącego stanu ustawienia laboratorium/środowisko. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Read | Odczytaj ustawienie środowiska. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ResetPassword/Action | Resetuje hasło na maszynie wirtualnej szablonu. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/SaveImage/Action | Zapisuje bieżący obraz szablonu do udostępnionej galerii na koncie laboratorium |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/harmonogramy/usuwanie | Usuwanie harmonogramów. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Schedules/Read | Odczytaj harmonogramy. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Schedules/Write | Dodawanie lub modyfikowanie harmonogramów. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Start/akcja | Uruchamia szablon przez uruchomienie wszystkich zasobów w szablonie. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/akcja | Zatrzymuje szablon przez zatrzymanie wszystkich zasobów w szablonie. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Write | Dodaj lub zmodyfikuj ustawienie środowiska. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/odczyt | Odczytywanie laboratoriów. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/SendEmail/Action | Wyślij wiadomość e-mail z linkiem rejestracji do laboratorium |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/użytkownicy/usuwanie | Usuń użytkowników. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/użytkownicy/odczyt | Odczytuj użytkowników. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/użytkownicy/zapis | Dodawanie lub modyfikowanie użytkowników. |
> | Działanie | Microsoft. LabServices/labAccounts/Labs/zapis | Dodawanie lub modyfikowanie laboratoriów. |
> | Działanie | Microsoft. LabServices/labAccounts/odczyt | Odczytuj konta laboratorium. |
> | Działanie | Microsoft. LabServices/labAccounts/sharedGalleries/Delete | Usuń sharedgalleries. |
> | Działanie | Microsoft. LabServices/labAccounts/sharedGalleries/Read | Odczytaj sharedgalleries. |
> | Działanie | Microsoft. LabServices/labAccounts/sharedGalleries/Write | Dodaj lub zmodyfikuj sharedgalleries. |
> | Działanie | Microsoft. LabServices/labAccounts/sharedImages/Delete | Usuń Sharedimages. |
> | Działanie | Microsoft. LabServices/labAccounts/sharedImages/Read | Odczytaj Sharedimages. |
> | Działanie | Microsoft. LabServices/labAccounts/sharedImages/Write | Dodaj lub zmodyfikuj Sharedimages. |
> | Działanie | Microsoft. LabServices/labAccounts/Write | Dodawanie lub modyfikowanie kont laboratorium. |
> | Działanie | Microsoft. LabServices/Locations/Operations/Read | Operacje odczytu. |
> | Działanie | Microsoft. LabServices/Register/Action | Rejestruje subskrypcję |
> | Działanie | Microsoft. LabServices/users/GetOperationBatchStatus/akcja | Pobierz stan operacji wsadowej |
> | Działanie | Microsoft. LabServices/users/zwróciło/akcja | Pobiera stan długotrwałej operacji |
> | Działanie | Microsoft. LabServices/users/GetPersonalPreferences/akcja | Uzyskaj osobiste preferencje dla użytkownika |
> | Działanie | Microsoft. LabServices/users/ListAllEnvironments/akcja | Wyświetl listę wszystkich środowisk dla użytkownika |
> | Działanie | Microsoft. LabServices/users/rejestr/akcja | Rejestrowanie użytkownika w zarządzanym laboratorium |
> | Działanie | Microsoft. LabServices/users/ResetPassword/akcja | Resetuje hasło użytkownika w środowisku |
> | Działanie | Microsoft. LabServices/users/StartEnvironment/akcja | Uruchamia środowisko przez uruchomienie wszystkich zasobów w środowisku. |
> | Działanie | Microsoft. LabServices/users/StopEnvironment/akcja | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Logic/integrationAccounts/Agreement/Delete | Usuwa umowę na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Agreements/listContentCallbackUrl/Action | Pobiera adres URL wywołania zwrotnego dla zawartości umowy na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Agreement/Read | Odczytuje umowę na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Agreements/Write | Tworzy lub aktualizuje umowę na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/zestawy/usuwanie | Usuwa zestaw na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/zestawy/listContentCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla zawartości zestawu na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/zestawy/odczyt | Odczytuje zestaw na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/zestawy/zapis | Tworzy lub aktualizuje zestaw na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/batchConfigurations/Delete | Usuwa konfigurację partii na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/batchConfigurations/odczyt | Odczytuje konfigurację partii na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/batchConfigurations/Write | Tworzy lub aktualizuje konfigurację partii na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Certificates/Delete | Usuwa certyfikat na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Certificates/Read | Odczytuje certyfikat na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Certificates/Write | Tworzy lub aktualizuje certyfikat na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Delete | Usuwa konto integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Join/Action | Umożliwia dołączenie do konta integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/listCallbackUrl/Action | Pobiera adres URL wywołania zwrotnego dla konta integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/listKeyVaultKeys/Action | Pobiera klucze w magazynie kluczy. |
> | Działanie | Microsoft. Logic/integrationAccounts/logTrackingEvents/Action | Rejestruje zdarzenia śledzenia na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Maps/Delete | Usuwa mapowanie na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Maps/listContentCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla zawartości mapy na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Maps/odczyt | Odczytuje mapę na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Maps/Write | Tworzy lub aktualizuje mapę na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Partners/Delete | Usuwa partnera na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Partners/listContentCallbackUrl/Action | Pobiera adres URL wywołania zwrotnego dla zawartości partnera na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Partners/Read | Odczytuje partnera na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Partners/Write | Tworzy lub aktualizuje partnera na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Providers/Microsoft. Insights/logDefinitions/Read | Odczytuje definicje dzienników konta integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Read | Odczytuje konto integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/regenerateAccessKey/Action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Działanie | Microsoft. Logic/integrationAccounts/schematy/usuwanie | Usuwa schemat na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/schematów/listContentCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla zawartości schematu na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/schematy/odczyt | Odczytuje schemat na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/schematy/zapis | Tworzy lub aktualizuje schemat na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Sessions/Delete | Usuwa sesję na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Sessions/Read | Odczytuje konfigurację partii na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Sessions/Write | Tworzy lub aktualizuje sesję na koncie integracji. |
> | Działanie | Microsoft. Logic/integrationAccounts/Write | Tworzy lub aktualizuje konto integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/Delete | Usuwa środowisko usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/Join/Action | Sprzęga środowisko usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/managedApis/apiOperations/Read | Odczytuje operację zarządzanego interfejsu API środowiska usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/managedApis/odczyt | Odczytuje zarządzany interfejs API środowiska usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/operationStatuses/odczyt | Odczytuje stan operacji środowiska usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/Providers/Microsoft. Insights/metricDefinitions/Read | Odczytuje definicje metryk środowiska usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/Read | Odczytuje środowisko usługi integracji. |
> | Działanie | Microsoft. Logic/integrationServiceEnvironments/Write | Tworzy lub aktualizuje środowisko usługi integracji. |
> | Działanie | Microsoft. Logic/Locations/Workflows/recommendOperationGroups/Action | Pobiera grupy operacji zalecane dla przepływu pracy. |
> | Działanie | Microsoft. Logic/lokalizacje/przepływy pracy/Walidacja/akcja | Sprawdza poprawność przepływu pracy. |
> | Działanie | Microsoft. Logic/Operations/Read | Pobiera operację. |
> | Działanie | Microsoft. Logic/Register/Action | Rejestruje dostawcę zasobów Microsoft. Logic dla danej subskrypcji. |
> | Działanie | Microsoft. Logic/Workflows/accessKeys/Delete | Usuwa klucz dostępu. |
> | Działanie | Microsoft. Logic/Workflows/accessKeys/list/Action | Wyświetla wpisy tajne klucza dostępu. |
> | Działanie | Microsoft. Logic/Workflows/accessKeys/Read | Odczytuje klucz dostępu. |
> | Działanie | Microsoft. Logic/Workflows/accessKeys/Regenerate/Action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Działanie | Microsoft. Logic/Workflows/accessKeys/Write | Tworzy lub aktualizuje klucz dostępu. |
> | Działanie | Microsoft. Logic/przepływy pracy/usuwanie | Usuwa przepływ pracy. |
> | Działanie | Microsoft. Logic/Workflows/Disable/Action | Wyłącza przepływ pracy. |
> | Działanie | Microsoft. Logic/Workflows/Enable/Action | Włącza przepływ pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/listCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla przepływu pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/listSwagger/akcja | Pobiera definicje struktury Swagger przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/przenoszenie/akcja | Przenosi przepływ pracy z istniejącego identyfikatora subskrypcji, grupy zasobów i/lub nazwy do innego identyfikatora subskrypcji, grupy zasobów i/lub nazwy. |
> | Działanie | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/diagnosticSettings/Read | Odczytuje ustawienia diagnostyki przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyki przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/logDefinitions/Read | Odczytuje definicje dziennika przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/metricDefinitions/Read | Odczytuje definicje metryk przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/Read | Odczytuje przepływ pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/regenerateAccessKey/akcja | Generuje ponownie wpisy tajne klucza dostępu. |
> | Działanie | Microsoft. Logic/Workflows/Run/Action | Uruchamia przebieg przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/listExpressionTraces/akcja | Pobiera ślady wyrażenia akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/odczyt | Odczytuje akcję przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia/listExpressionTraces/akcja | Pobiera ślady wyrażenia powtórzenia akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia/odczyt | Odczytuje powtórzenie akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia/requestHistories/odczyt | Odczytuje historię żądań akcji powtórzenia przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/uruchomienia/Actions/requestHistories/Read | Odczytuje historię żądań akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/uruchomienia/Actions/scoperepetitions/Read | Odczytuje powtórzenie zakresu akcji przebiegu przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/uruchomienia/Anuluj/akcja | Anuluje uruchomienie przepływu pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/uruchomienia/Usuń | Usuwa przebieg przepływu pracy. |
> | Działanie | Microsoft. Logic/Workflows/uruchomienia/Operations/Read | Odczytuje stan operacji przebiegu przepływu pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/uruchomienia/odczyt | Odczytuje przebieg przepływu pracy. |
> | Działanie | Microsoft. Logic/przepływy pracy/wstrzymywanie/akcja | Wstrzymuje przepływ pracy. |
> | Działanie | Microsoft. Logic/przepływy/wyzwalacze/historie/odczyt | Odczytuje historie wyzwalacza. |
> | Działanie | Microsoft. Logic/przepływy/wyzwalacze/historie/ponowne przesyłanie/akcja | Przesyła ponownie wyzwalacz przepływu pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/wyzwalacze/listCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Działanie | Microsoft. Logic/Workflows/Triggers/Read | Odczytuje wyzwalacz. |
> | Działanie | Microsoft. Logic/przepływy pracy/wyzwalacze/Reset/akcja | Resetuje wyzwalacz. |
> | Działanie | Microsoft. Logic/Workflows/Triggers/Run/Action | Wykonuje wyzwalacz. |
> | Działanie | Microsoft. Logic/Workflows/Triggers/setstate/Action | Ustawia stan wyzwalacza. |
> | Działanie | Microsoft. Logic/Workflows/Validate/Action | Sprawdza poprawność przepływu pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/wersje/odczyt | Odczytuje wersję przepływu pracy. |
> | Działanie | Microsoft. logiki/przepływy pracy/wersje/wyzwalacze/listCallbackUrl/akcja | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Działanie | Microsoft. Logic/przepływy pracy/zapis | Tworzy lub aktualizuje przepływ pracy. |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

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
> | Działanie | Microsoft. MachineLearning/jednostki SKU/odczyt | Pobierz jednostki SKU planu zobowiązania Machine Learning |
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
> | Działanie | Microsoft. MachineLearningServices/lokalizacje/użycia/odczyt | Raport użycia dla zasobów obliczeniowych AML w ramach subskrypcji |
> | Działanie | Microsoft. MachineLearningServices/Locations/vmsizes/Read | Pobierz obsługiwane rozmiary maszyn wirtualnych |
> | Działanie | Microsoft. MachineLearningServices/Locations/workspaceOperationsStatus/Read | Pobiera stan określonej operacji obszaru roboczego |
> | Działanie | Microsoft. MachineLearningServices/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów Machine Learning Services |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/obliczenia/usuwanie | Usuwa zasoby obliczeniowe w Machine Learning Services obszarach roboczych |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/obliczenia/listKeys/akcja | Wyświetlanie wpisów tajnych dla zasobów obliczeniowych w obszarze roboczym Machine Learning Services |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/obliczenia/listNodes/akcja | Wyświetlanie listy węzłów dla zasobu obliczeniowego w obszarze roboczym Machine Learning Services |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/obliczenia/odczyt | Pobiera zasoby obliczeniowe w Machine Learning Services obszarach roboczych |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/obliczenia/zapis | Tworzy lub aktualizuje zasoby obliczeniowe w obszarze roboczym Machine Learning Services |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/usuwanie | Usuwa obszary robocze Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/usuwanie | Usuwa eksperymenty w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/odczyt | Pobiera eksperymenty w Machine Learning Servicesych obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/zapis | Tworzy lub aktualizuje eksperymenty w Machine Learning Services obszarach roboczych |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/listKeys/akcja | Wyświetlanie wpisów tajnych dla obszaru roboczego Machine Learning Services |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/odczyt | Pobiera Machine Learning Services obszary robocze |
> | Działanie | Microsoft. MachineLearningServices/obszary robocze/zapis | Tworzy lub aktualizuje Machine Learning Services obszary robocze |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ManagedIdentity/tożsamości/odczyt | Pobiera istniejącą tożsamość przypisaną do systemu |
> | Działanie | Microsoft. ManagedIdentity/Register/Action | Rejestruje subskrypcję dostawcy zasobów tożsamości zarządzanej |
> | Działanie | Microsoft. ManagedIdentity/Resourceidentity/Assign/Action | Akcja RBAC do przypisywania istniejącej przypisanej użytkownikowi tożsamości do zasobu |
> | Działanie | Microsoft. ManagedIdentity/Resourceidentity/Delete | Usuwa istniejącą tożsamość przypisanego użytkownika |
> | Działanie | Microsoft. ManagedIdentity/Resourceidentity/odczyt | Pobiera istniejącą tożsamość przypisanego użytkownika |
> | Działanie | Microsoft. ManagedIdentity/Resourceidentity/Write | Tworzy nową tożsamość przypisanego użytkownika lub aktualizuje Tagi skojarzone z istniejącą tożsamością przypisanego użytkownika |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ManagedServices/marketplaceRegistrationDefinitions/odczyt | Pobiera listę definicji rejestracji usług zarządzanych. |
> | Działanie | Microsoft. ManagedServices/Operations/Read | Pobiera listę operacji usług zarządzanych. |
> | Działanie | Microsoft. ManagedServices/operationStatuses/odczyt | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft. ManagedServices/Register/Action | Zarejestruj się w usłudze Managed Services. |
> | Działanie | Microsoft. ManagedServices/registrationAssignments/Delete | Usuwa przypisanie rejestracji usług zarządzanych. |
> | Działanie | Microsoft. ManagedServices/registrationAssignments/odczyt | Pobiera listę przypisań rejestracji usług zarządzanych. |
> | Działanie | Microsoft. ManagedServices/registrationAssignments/Write | Dodaj lub zmodyfikuj przypisanie rejestracji usług zarządzanych. |
> | Działanie | Microsoft. ManagedServices/registrationDefinitions/Delete | Usuwa definicję rejestracji usług zarządzanych. |
> | Działanie | Microsoft. ManagedServices/registrationDefinitions/odczyt | Pobiera listę definicji rejestracji usług zarządzanych. |
> | Działanie | Microsoft. ManagedServices/registrationDefinitions/Write | Dodaj lub zmodyfikuj definicję rejestracji usług zarządzanych. |
> | Działanie | Microsoft. ManagedServices/Unregister/Action | Wyrejestruj z usług zarządzanych. |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Management/checkNameAvailability/akcja | Sprawdza, czy określona nazwa grupy zarządzania jest prawidłowa i unikatowa. |
> | Działanie | Microsoft. Management/getentities/akcja | Wyświetl listę wszystkich jednostek (Grupy zarządzania, subskrypcje itp.) dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft. Management/managementGroups/Delete | Usuń grupę zarządzania. |
> | Działanie | Microsoft. Management/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | Działanie | Microsoft. Management/managementGroups/subskrypcje/usuwanie | Anuluj skojarzenie subskrypcji z grupą zarządzania. |
> | Działanie | Microsoft. Management/managementGroups/subskrypcje/zapis | Kojarzy istniejącą subskrypcję z grupą zarządzania. |
> | Działanie | Microsoft. Management/managementGroups/Write | Utwórz lub Zaktualizuj grupę zarządzania. |
> | Działanie | Microsoft. Management/Register/Action | Zarejestruj określoną subskrypcję w usłudze Microsoft. Management |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja dataaction | Microsoft. Maps/konta/dane/odczyt | Przyznaje dostęp do odczytu do konta Maps. |
> | Działanie | Microsoft. Maps/accounts/Delete | Usuń konto Maps. |
> | Działanie | Microsoft. Maps/accounts/eventGridFilters/Delete | Usuń filtr Event Grid |
> | Działanie | Microsoft. Maps/accounts/eventGridFilters/Read | Pobierz filtr Event Grid |
> | Działanie | Microsoft. Maps/accounts/eventGridFilters/Write | Utwórz lub zaktualizuj filtr Event Grid |
> | Działanie | Microsoft. Maps/accounts/listKeys/Action | Lista kluczy konta map |
> | Działanie | Microsoft. Maps/konta/odczyt | Pobierz konto Maps. |
> | Działanie | Microsoft. Maps/accounts/regenerateKey/Action | Generuj nowy klucz podstawowy lub pomocniczy konta mapy |
> | Działanie | Microsoft. Maps/accounts/Write | Utwórz lub zaktualizuj konto Maps. |
> | Działanie | Microsoft. Maps/Operations/Read | Odczytaj operacje dostawcy |
> | Działanie | Microsoft. Maps/Zarejestruj/akcja | Rejestrowanie dostawcy |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Marketplace/offerTypes/wydawcy/oferty/plany/umowy/odczyt | Zwraca umowę. |
> | Działanie | Microsoft. Marketplace/offerTypes/wydawcy/oferty/plany/umowy/zapis | Akceptuje umowę ze znakiem. |
> | Działanie | Microsoft. Marketplace/offerTypes/Wydawca/oferty/plany/konfiguracje/importImage/akcja | Importuje obraz do ACR użytkownika końcowego. |
> | Działanie | Microsoft. Marketplace/offerTypes/wydawcy/oferty/plany/konfiguracje/odczyt | Zwraca konfigurację. |
> | Działanie | Microsoft. Marketplace/offerTypes/wydawcy/oferty/plany/konfiguracje/zapis | Zapisuje konfigurację. |
> | Działanie | Microsoft. Marketplace/rejestr/akcja | Rejestruje dostawcę zasobów Microsoft. Marketplace w ramach subskrypcji. |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. MarketplaceApps/ClassicDevServices/Delete | Wykonuje operację usuwania dla zasobu klasycznej usługi deweloperskiej. |
> | Działanie | Microsoft. MarketplaceApps/ClassicDevServices/listSecrets/akcja | Pobiera klasyczne klucze zarządzania zasobami usług deweloperskich. |
> | Działanie | Microsoft. MarketplaceApps/ClassicDevServices/listSingleSignOnToken/akcja | Pobiera adres URL logowania jednokrotnego dla klasycznej usługi deweloperskiej. |
> | Działanie | Microsoft. MarketplaceApps/ClassicDevServices/odczyt | Wykonuje operację pobierania na klasycznej usłudze deweloperskiej. |
> | Działanie | Microsoft. MarketplaceApps/ClassicDevServices/regenerateKey/akcja | Generuje klucze zarządzania zasobami klasycznej usługi deweloperskiej. |
> | Działanie | Microsoft. MarketplaceApps/Operations/Read | Przeczytaj operacje dla wszystkich typów zasobów. |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. MarketplaceOrdering/umowy/oferty/plany/anulowanie/akcja | Anulowanie umowy dotyczącej danego elementu portalu Marketplace |
> | Działanie | Microsoft. MarketplaceOrdering/umowy/oferty/plany/odczyt | Zwróć umowę dla danego elementu portalu Marketplace |
> | Działanie | Microsoft. MarketplaceOrdering/umowy/oferty/plany/Podpisz/akcję | Podpisz umowę dotyczącą danego elementu portalu Marketplace |
> | Działanie | Microsoft. MarketplaceOrdering/umowy/odczyt | Zwróć wszystkie umowy w ramach danej subskrypcji |
> | Działanie | Microsoft. MarketplaceOrdering/offertypes/Wydawca/oferty/plany/umowy/odczyt | Pobierz umowę dotyczącą danego elementu maszyny wirtualnej portalu Marketplace |
> | Działanie | Microsoft. MarketplaceOrdering/offertypes/Wydawca/oferty/plany/umowy/zapis | Podpisz lub Anuluj umowę dotyczącą danego elementu maszyny wirtualnej portalu Marketplace |
> | Działanie | Microsoft. MarketplaceOrdering/Operations/Read | Wyświetl listę wszystkich możliwych operacji w interfejsie API |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Media/checknameavailability/Action | Sprawdza, czy nazwa konta Media Services jest dostępna |
> | Działanie | Microsoft. Media/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa konta Media Services jest dostępna |
> | Działanie | Microsoft. Media/MediaServices/accountfilters/Delete | Usuń filtr konta |
> | Działanie | Microsoft. Media/MediaServices/accountfilters/Read | Odczytaj dowolny filtr konta |
> | Działanie | Microsoft. Media/MediaServices/accountfilters/Write | Utwórz lub zaktualizuj dowolny filtr konta |
> | Działanie | Microsoft. Media/MediaServices/Assets/assetfilters/Delete | Usuń dowolny filtr zasobów |
> | Działanie | Microsoft. Media/MediaServices/Assets/assetfilters/Read | Odczytaj dowolny filtr zasobów |
> | Działanie | Microsoft. Media/MediaServices/Assets/assetfilters/Write | Utwórz lub zaktualizuj dowolny filtr zasobów |
> | Działanie | Microsoft. Media/MediaServices/Assets/Delete | Usuń dowolny element zawartości |
> | Działanie | Microsoft. Media/MediaServices/Assets/getEncryptionKey/Action | Pobierz klucz szyfrowania zasobu |
> | Działanie | Microsoft. Media/MediaServices/Assets/listContainerSas/Action | Wyświetl listę adresów URL SAS kontenera zasobów |
> | Działanie | Microsoft. Media/MediaServices/Assets/listStreamingLocators/Action | Wyświetl listę lokalizatorów przesyłania strumieniowego dla elementu zawartości |
> | Działanie | Microsoft. Media/MediaServices/Assets/Read | Odczytaj dowolny zasób |
> | Działanie | Microsoft. Media/MediaServices/Assets/Write | Utwórz lub zaktualizuj dowolny element zawartości |
> | Działanie | Microsoft. Media/MediaServices/contentKeyPolicies/Delete | Usuń wszystkie zasady kluczy zawartości |
> | Działanie | Microsoft. Media/MediaServices/contentKeyPolicies/getPolicyPropertiesWithSecrets/Action | Pobieranie właściwości zasad przy użyciu wpisów tajnych |
> | Działanie | Microsoft. Media/MediaServices/contentKeyPolicies/Read | Odczytaj wszystkie zasady dotyczące klucza zawartości |
> | Działanie | Microsoft. Media/MediaServices/contentKeyPolicies/Write | Tworzenie lub aktualizowanie zasad klucza zawartości |
> | Działanie | Microsoft. Media/MediaServices/Delete | Usuń dowolne konto Media Services |
> | Działanie | Microsoft. Media/MediaServices/eventGridFilters/Delete | Usuń dowolny filtr Event Grid |
> | Działanie | Microsoft. Media/MediaServices/eventGridFilters/Read | Odczytaj dowolny filtr Event Grid |
> | Działanie | Microsoft. Media/MediaServices/eventGridFilters/Write | Utwórz lub zaktualizuj dowolny filtr Event Grid |
> | Działanie | Microsoft. Media/MediaServices/liveEventOperations/Read | Odczytaj wszystkie operacje na żywo dotyczące zdarzeń |
> | Działanie | Microsoft. Media/MediaServices/liveEvents/Delete | Usuń dowolne wydarzenie na żywo |
> | Działanie | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Delete | Usuń wszystkie dane wyjściowe na żywo |
> | Działanie | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Read | Odczytaj wszystkie dane wyjściowe na żywo |
> | Działanie | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Write | Utwórz lub zaktualizuj wszystkie dane wyjściowe na żywo |
> | Działanie | Microsoft. Media/MediaServices/liveEvents/Read | Odczytaj dowolne wydarzenie na żywo |
> | Działanie | Microsoft. Media/MediaServices/liveEvents/Reset/Action | Zresetuj wszystkie operacje na żywo dotyczące zdarzeń |
> | Działanie | Microsoft. Media/MediaServices/liveEvents/Start/akcja | Rozpocznij wszystkie operacje na żywo dotyczące zdarzeń |
> | Działanie | Microsoft. Media/MediaServices/liveEvents/akcja | Zatrzymaj wszystkie operacje na żywo dotyczące zdarzeń |
> | Działanie | Microsoft. Media/MediaServices/liveEvents/Write | Utwórz lub zaktualizuj dowolne wydarzenie na żywo |
> | Działanie | Microsoft. Media/MediaServices/liveOutputOperations/Read | Odczytaj wszystkie operacje wyjścia na żywo |
> | Działanie | Microsoft. Media/MediaServices/Read | Odczytaj dowolne konto Media Services |
> | Działanie | Microsoft. Media/MediaServices/streamingEndpointOperations/Read | Odczytaj wszystkie operacje punktu końcowego przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingEndpoints/Delete | Usuń wszystkie punkty końcowe przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingEndpoints/Read | Odczytaj dowolny punkt końcowy przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingEndpoints/Skala/akcja | Skaluj wszystkie operacje punktu końcowego przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingEndpoints/Start/akcja | Uruchom wszystkie operacje punktu końcowego przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingEndpoints/akcja | Zatrzymaj wszystkie operacje punktu końcowego przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingEndpoints/Write | Utwórz lub zaktualizuj dowolny punkt końcowy przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingLocators/Delete | Usuń wszystkie lokalizatory przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingLocators/listContentKeys/Action | Wyświetl listę kluczy zawartości |
> | Działanie | Microsoft. Media/MediaServices/streamingLocators/listPaths/Action | Wyświetl ścieżki |
> | Działanie | Microsoft. Media/MediaServices/streamingLocators/Read | Odczytaj każdy lokalizator przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingLocators/Write | Utwórz lub zaktualizuj lokalizator przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingPolicies/Delete | Usuń wszystkie zasady przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingPolicies/Read | Odczytaj wszystkie zasady przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/streamingPolicies/Write | Tworzenie lub aktualizowanie wszelkich zasad przesyłania strumieniowego |
> | Działanie | Microsoft. Media/MediaServices/syncStorageKeys/Action | Synchronizuj klucze magazynu dla dołączonego konta usługi Azure Storage |
> | Działanie | Microsoft. Media/MediaServices/transformacje/usuwanie | Usuń dowolne przekształcenie |
> | Działanie | Microsoft. Media/MediaServices/transformacje/zadania/cancelJob/akcja | Anuluj zadanie |
> | Działanie | Microsoft. Media/MediaServices/transformacje/zadania/usuwanie | Usuń dowolne zadanie |
> | Działanie | Microsoft. Media/MediaServices/transformacje/zadania/odczyt | Odczytaj dowolne zadanie |
> | Działanie | Microsoft. Media/MediaServices/transformacje/zadania/zapis | Utwórz lub zaktualizuj dowolne zadanie |
> | Działanie | Microsoft. Media/MediaServices/transformacje/odczyt | Odczytaj dowolne przekształcenia |
> | Działanie | Microsoft. Media/MediaServices/transformacje/zapis | Utwórz lub zaktualizuj dowolne przekształcenie |
> | Działanie | Microsoft. Media/MediaServices/Write | Utwórz lub zaktualizuj dowolne konto Media Services |
> | Działanie | Microsoft. Media/Operations/Read | Pobierz dostępne operacje |
> | Działanie | Microsoft. Media/Register/Action | Rejestruje subskrypcję dostawcy zasobów Media Services i umożliwia tworzenie kont Media Services |
> | Działanie | Microsoft. Media/Unregister/Action | Wyrejestrowuje subskrypcję dla dostawcy zasobów Media Services |

## <a name="microsoftmigrate"></a>Microsoft. Migrowanie

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Migrowanie/assessmentprojects/assessmentOptions/odczyt | Pobiera opcje oceny, które są dostępne w danej lokalizacji |
> | Działanie | Microsoft. Migrowanie/assessmentprojects/oceny/odczyt | Wyświetla listę ocen w projekcie |
> | Działanie | Microsoft. Migruj/assessmentprojects/Usuń | Usuwa projekt oceny |
> | Działanie | Microsoft. migruje/assessmentprojects/Groups/Assessments/assessedmachines/Read | Pobierz właściwości ocenianego komputera |
> | Działanie | Microsoft. Migrowanie/assessmentprojects/grupy/oceny/usuwanie | Usuwa ocenę |
> | Działanie | Microsoft. Migrowanie/assessmentprojects/grupy/oceny/downloadurl/akcja | Pobiera adres URL raportu oceny |
> | Działanie | Microsoft. Migruj/assessmentprojects/grupy/oceny/odczyt | Pobiera właściwości oceny |
> | Działanie | Microsoft. Migruj/assessmentprojects/grupy/oceny/zapis | Tworzy nową ocenę lub aktualizuje istniejącą ocenę |
> | Działanie | Microsoft. Migrowanie/assessmentprojects/grupy/usuwanie | Usuwa grupę |
> | Działanie | Microsoft. migrowana/assessmentprojects/Groups/Read | Pobierz właściwości grupy |
> | Działanie | Microsoft. zmigrować/assessmentprojects/Groups/updateMachines/Action | Aktualizowanie grupy przez dodawanie lub usuwanie maszyn |
> | Działanie | Microsoft. migruje/assessmentprojects/Groups/Write | Tworzy nową grupę lub aktualizuje istniejącą grupę |
> | Działanie | Microsoft. Migrowanie/assessmentprojects/hypervcollectors/usuwanie | Usuwa moduł zbierający HyperV |
> | Działanie | Microsoft. Migrowanie/assessmentprojects/hypervcollectors/odczyt | Pobiera właściwości modułu zbierającego HyperV |
> | Działanie | Microsoft. Migruj/assessmentprojects/hypervcollectors/Write | Tworzy nowy moduł zbierający HyperV lub aktualizuje istniejący moduł zbierający funkcji Hyper-v |
> | Działanie | Microsoft. migruje/assessmentprojects/Machines/Read | Pobiera właściwości maszyny |
> | Działanie | Microsoft. migrowana/assessmentprojects/Read | Pobiera właściwości projektu oceny |
> | Działanie | Microsoft. Migrowanie/assessmentprojects/vmwarecollectors/usuwanie | Usuwa moduł zbierający VMware |
> | Działanie | Microsoft. Migrowanie/assessmentprojects/vmwarecollectors/odczyt | Pobiera właściwości modułu zbierającego VMware |
> | Działanie | Microsoft. Migruj/assessmentprojects/vmwarecollectors/Write | Tworzy nowy moduł zbierający VMware lub aktualizuje istniejący moduł zbierający VMware |
> | Działanie | Microsoft. Migruj/assessmentprojects/Write | Tworzy nowy projekt oceny lub aktualizuje istniejący projekt oceny |
> | Działanie | Microsoft. migrowana/Locations/assessmentOptions/Read | Pobiera opcje oceny, które są dostępne w danej lokalizacji |
> | Działanie | Microsoft. migrowana/Locations/checknameavailability/Action | Sprawdza dostępność nazwy zasobu dla danej subskrypcji w danej lokalizacji |
> | Działanie | Microsoft. Migrowanie/migrateprojects/DatabaseInstances/odczyt | Pobiera właściwości wystąpienia bazy danych |
> | Działanie | Microsoft. migruje/migrateprojects/Databases/Read | Pobiera właściwości bazy danych |
> | Działanie | Microsoft. Migruj/migrateprojects/Usuń | Usuwa projekt migracji |
> | Działanie | Microsoft. migruje/migrateprojects/Machines/Read | Pobiera właściwości maszyny |
> | Działanie | Microsoft. Migrowanie/migrateprojects/MigrateEvents/usuwanie | Usuwa zdarzenie migracji |
> | Działanie | Microsoft. Migrowanie/migrateprojects/MigrateEvents/odczyt | Pobiera właściwości zdarzenia migracji. |
> | Działanie | Microsoft. migrowana/migrateprojects/Read | Pobiera właściwości migracji projektu |
> | Działanie | Microsoft. migrowana/migrateprojects/RefreshSummary/akcja | Odświeża podsumowanie migracji projektu |
> | Działanie | Microsoft. migrowana/migrateprojects/registerTool/akcja | Rejestruje narzędzie do projektu migracji |
> | Działanie | Microsoft. migruje/migrateprojects/Solutions/cleanupData/Action | Wyczyść dane rozwiązania migracji projektu |
> | Działanie | Microsoft. migruje/migrateprojects/Solutions/Delete | Usuwa rozwiązanie migracji projektu |
> | Działanie | Microsoft. zmigrować/migrateprojects/Solutions/GetConfig/Action | Pobiera konfigurację rozwiązania migracji projektu |
> | Działanie | Microsoft. migruje/migrateprojects/Solutions/Read | Pobiera właściwości rozwiązania Migrowanie projektu |
> | Działanie | Microsoft. migruje/migrateprojects/Solutions/Write | Tworzy nowe rozwiązanie migracji projektu lub aktualizuje istniejące rozwiązanie migracji projektu |
> | Działanie | Microsoft. Migruj/migrateprojects/Write | Tworzy nowy projekt migracji lub aktualizuje istniejący projekt migracji |
> | Działanie | Microsoft. migrowana/Operations/Read | Wyświetla listę operacji dostępnych w witrynie Microsoft. Migrowanie dostawcy zasobów |
> | Działanie | Microsoft. Migracja/projekty/oceny/odczyt | Wyświetla listę ocen w projekcie |
> | Działanie | Microsoft. Migracja/projekty/usuwanie | Usuwa projekt |
> | Działanie | Microsoft. Migrowanie/projekty/grupy/oceny/assessedmachines/odczyt | Pobierz właściwości ocenianego komputera |
> | Działanie | Microsoft. Migrowanie/projekty/grupy/oceny/usuwanie | Usuwa ocenę |
> | Działanie | Microsoft. Migracja/projekty/grupy/oceny/downloadurl/akcja | Pobiera adres URL raportu oceny |
> | Działanie | Microsoft. Migracja/projekty/grupy/oceny/odczyt | Pobiera właściwości oceny |
> | Działanie | Microsoft. Migrowanie/projekty/grupy/oceny/zapis | Tworzy nową ocenę lub aktualizuje istniejącą ocenę |
> | Działanie | Microsoft. Migracja/projekty/grupy/usuwanie | Usuwa grupę |
> | Działanie | Microsoft. Migrowanie/projekty/grupy/odczyt | Pobierz właściwości grupy |
> | Działanie | Microsoft. Migrowanie/projekty/grupy/zapis | Tworzy nową grupę lub aktualizuje istniejącą grupę |
> | Działanie | Microsoft. Migruj/projekty/klucze/akcja | Pobiera klucze wspólne dla projektu |
> | Działanie | Microsoft. Migrowanie/projekty/maszyny/odczyt | Pobiera właściwości maszyny |
> | Działanie | Microsoft. Migracja/projekty/odczyt | Pobiera właściwości projektu |
> | Działanie | Microsoft. Migracja/projekty/zapis | Tworzy nowy projekt lub aktualizuje istniejący projekt |
> | Działanie | Microsoft. Migrowanie/rejestrowanie/akcja | Rejestruje subskrypcję za pomocą programu Microsoft. Migrowanie dostawcy zasobów |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. MixedReality/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów rzeczywistości mieszanej. |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Utwórz kotwice przestrzenne |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Usuń kotwice przestrzenne |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/odnajdywanie/odczyt | Odkryj kotwice w pobliżu |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/właściwości/odczyt | Pobierz właściwości kotwic przestrzennych |
> | Działanie | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla Microsoft. MixedReality/spatialAnchorsAccounts |
> | Działanie | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla Microsoft. MixedReality/spatialAnchorsAccounts |
> | Działanie | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla elementu Microsoft. MixedReality/spatialAnchorsAccounts |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | Akcja dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Aktualizuj właściwości kotwic przestrzennych |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. NetApp/Locations/checkfilepathavailability/Action | Sprawdź, czy ścieżka pliku jest dostępna |
> | Działanie | Microsoft. NetApp/Locations/checknameavailability/Action | Sprawdź, czy nazwa zasobu jest dostępna |
> | Działanie | Microsoft. NetApp/Locations/operationresults/Read | Odczytuje zasób wyniku operacji. |
> | Działanie | Microsoft. NetApp/lokalizacje/odczyt | Odczytuje zasób sprawdzania dostępności. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/Delete | Usuwa zasób puli. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/Read | Odczytuje zasób puli. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/Delete | Usuwa zasób woluminu. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/mountTargets/Read | Odczytuje zasób docelowy instalacji. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/Read | Odczytuje zasób woluminu. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy/migawki/usuwanie | Usuwa zasób migawki. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy/migawki/odczyt | Odczytuje zasób migawki. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy/migawki/zapis | Zapisuje zasób migawki. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/Write | Zapisuje zasób woluminu. |
> | Działanie | Microsoft. NetApp/netAppAccounts/capacityPools/Write | Zapisuje zasób puli. |
> | Działanie | Microsoft. NetApp/netAppAccounts/Delete | Usuwa zasób konta. |
> | Działanie | Microsoft. NetApp/netAppAccounts/odczyt | Odczytuje zasób konta. |
> | Działanie | Microsoft. NetApp/netAppAccounts/Write | Zapisuje zasób konta. |
> | Działanie | Microsoft. NetApp/Operations/Read | Odczytuje zasoby operacji. |
> | Działanie | Microsoft. NetApp/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. NetApp |
> | Działanie | Microsoft. NetApp/Unregister/Action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. NetApp |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Network/applicationGatewayAvailableRequestHeaders/odczyt | Pobierz Application Gateway dostępne nagłówki żądania |
> | Działanie | Microsoft. Network/applicationGatewayAvailableResponseHeaders/odczyt | Pobierz Application Gateway dostępny nagłówek odpowiedzi |
> | Działanie | Microsoft. Network/applicationGatewayAvailableServerVariables/odczyt | Pobierz Application Gateway dostępne zmienne serwera |
> | Działanie | Microsoft. Network/applicationGatewayAvailableSslOptions/predefinedPolicies/odczyt | Application Gateway wstępnie zdefiniowanych zasad SSL |
> | Działanie | Microsoft. Network/applicationGatewayAvailableSslOptions/odczyt | Application Gateway dostępne opcje protokołu SSL |
> | Działanie | Microsoft. Network/applicationGatewayAvailableWafRuleSets/odczyt | Pobiera Application Gateway dostępne zestawy reguł WAF |
> | Działanie | Microsoft. Network/applicationGateways/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza bramy aplikacji. Brak alertów. |
> | Działanie | Microsoft. Network/applicationGateways/backendhealth/akcja | Pobiera kondycję zaplecza bramy aplikacji |
> | Działanie | Microsoft. Network/applicationGateways/Delete | Usuwa bramę aplikacji |
> | Działanie | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/akcja | Pobiera kondycję zaplecza bramy aplikacji na żądanie dla danego ustawienia http i puli zaplecza |
> | Działanie | Microsoft. Network/applicationGateways/odczyt | Pobiera bramę aplikacji |
> | Działanie | Microsoft. Network/applicationGateways/Start/akcja | Uruchamia bramę aplikacji |
> | Działanie | Microsoft. Network/applicationGateways/Stop/akcja | Kończy działanie bramy aplikacji |
> | Działanie | Microsoft. Network/applicationGateways/Write | Tworzy bramę aplikacji lub aktualizuje bramę aplikacji |
> | Działanie | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Delete | Usuwa zasady WAF Application Gateway |
> | Działanie | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/odczyt | Pobiera zasady WAF Application Gateway |
> | Działanie | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Write | Application Gateway tworzy zasady WAFymi lub aktualizuje zasady Application Gateway WAF |
> | Działanie | Microsoft. Network/applicationSecurityGroups/Delete | Usuwa grupę zabezpieczeń aplikacji |
> | Działanie | Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/akcja | Przyłącza konfigurację IP do grup zabezpieczeń aplikacji. Brak alertów. |
> | Działanie | Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/akcja | Przyłączanie reguły zabezpieczeń do grup zabezpieczeń aplikacji. Brak alertów. |
> | Działanie | Microsoft. Network/applicationSecurityGroups/listIpConfigurations/akcja | Wyświetla konfiguracje protokołu IP w ApplicationSecurityGroup |
> | Działanie | Microsoft. Network/applicationSecurityGroups/odczyt | Pobiera identyfikator grupy zabezpieczeń aplikacji. |
> | Działanie | Microsoft. Network/applicationSecurityGroups/Write | Tworzy grupę zabezpieczeń aplikacji lub aktualizuje istniejącą grupę zabezpieczeń aplikacji. |
> | Działanie | Microsoft. Network/azureFirewallFqdnTags/odczyt | Pobiera Tagi FQDN zapory platformy Azure |
> | Działanie | Microsoft. Network/azurefirewalls/Delete | Usuwanie zapory platformy Azure |
> | Działanie | Microsoft. Network/azurefirewalls/odczyt | Uzyskiwanie zapory platformy Azure |
> | Działanie | Microsoft. Network/azurefirewalls/Write | Tworzy lub aktualizuje zaporę platformy Azure |
> | Działanie | Microsoft. Network/bastionHosts/Delete | Usuwa hosta bastionu |
> | Działanie | Microsoft. Network/bastionHosts/odczyt | Pobiera hosta bastionu |
> | Działanie | Microsoft. Network/bastionHosts/Write | Tworzenie lub aktualizowanie hosta bastionu |
> | Działanie | Microsoft. Network/bgpServiceCommunities/odczyt | Pobierz społeczności usługi BGP |
> | Działanie | Microsoft. Network/checkFrontDoorNameAvailability/akcja | Sprawdza, czy nazwa drzwi czołowych jest dostępna |
> | Działanie | Microsoft. Network/checkTrafficManagerNameAvailability/akcja | Sprawdza dostępność Traffic Manager względnej nazwy DNS. |
> | Działanie | Microsoft. Network/Connections/Delete | Usuwa VirtualNetworkGatewayConnection |
> | Działanie | Microsoft. Network/Connections/Read | Pobiera VirtualNetworkGatewayConnection |
> | Działanie | Microsoft. Network/Connections/odwoływanie/akcja | Oznacza stan połączenia usługi Express Route jako odwołane |
> | Działanie | Microsoft. Network/Connections/SharedKey/Action | Pobierz VirtualNetworkGatewayConnection SharedKey |
> | Działanie | Microsoft. Network/Connections/sharedKey/Read | Pobiera VirtualNetworkGatewayConnection SharedKey |
> | Działanie | Microsoft. Network/Connections/sharedKey/Write | Tworzy lub aktualizuje istniejące VirtualNetworkGatewayConnection SharedKey |
> | Działanie | Microsoft. Network/Connections/vpndeviceconfigurationscript/Action | Pobiera konfigurację urządzenia sieci VPN VirtualNetworkGatewayConnection |
> | Działanie | Microsoft. Network/Connections/Write | Tworzy lub aktualizuje istniejący VirtualNetworkGatewayConnection |
> | Działanie | Microsoft. Network/ddosCustomPolicies/Delete | Usuwa zasady niestandardowe DDoS |
> | Działanie | Microsoft. Network/ddosCustomPolicies/odczyt | Pobiera DDoS dostosowaną definicję zasad |
> | Działanie | Microsoft. Network/ddosCustomPolicies/Write | Tworzy dostosowane zasady DDoS lub aktualizuje istniejące dostosowane zasady DDoS |
> | Działanie | Microsoft. Network/ddosProtectionPlans/Delete | Usuwa plan DDoS Protection |
> | Działanie | Microsoft. Network/ddosProtectionPlans/Join/Action | Dołącza do planu DDoS Protection. Brak alertów. |
> | Działanie | Microsoft. Network/ddosProtectionPlans/odczyt | Pobiera plan DDoS Protection |
> | Działanie | Microsoft. Network/ddosProtectionPlans/Write | Tworzy plan DDoS Protection lub aktualizuje plan DDoS Protection  |
> | Działanie | Microsoft. Network/dnsoperationresults/odczyt | Pobiera wyniki operacji DNS |
> | Działanie | Microsoft. Network/dnsoperationstatuses/odczyt | Pobiera stan operacji DNS  |
> | Działanie | Microsoft. Network/dnszones/A/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "A" ze strefy DNS. |
> | Działanie | Microsoft. Network/dnszones/A/Odczyt | Pobierz zestaw rekordów typu "A" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/A/Write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/dnszones/AAAA/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "AAAA" ze strefy DNS. |
> | Działanie | Microsoft. Network/dnszones/AAAA/odczyt | Pobierz zestaw rekordów typu "AAAA" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/AAAA/Write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/dnszones/All/Read | Pobiera zestawy rekordów DNS dla różnych typów |
> | Działanie | Microsoft. Network/dnszones/CAA/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "CAA" ze strefy DNS. |
> | Działanie | Microsoft. Network/dnszones/CAA/odczyt | Pobierz zestaw rekordów typu "CAA" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/CAA/Write | Utwórz lub zaktualizuj zestaw rekordów typu "CAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/dnszones/CNAME/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "CNAME" ze strefy DNS. |
> | Działanie | Microsoft. Network/dnszones/CNAME/odczyt | Pobierz zestaw rekordów typu "CNAME" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/CNAME/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/dnszones/Delete | Usuń strefę DNS w formacie JSON. Właściwości strefy obejmują Tagi, ETag, numberOfRecordSets i maxNumberOfRecordSets. |
> | Działanie | Microsoft. Network/dnszones/MX/Delete | Usuń zestaw rekordów o danej nazwie i typie "MX" ze strefy DNS. |
> | Działanie | Microsoft. Network/dnszones/MX/odczyt | Pobierz zestaw rekordów typu "MX" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/MX/Write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/dnszones/NS/Delete | Usuwa zestaw rekordów DNS typu NS |
> | Działanie | Microsoft. Network/dnszones/NS/odczyt | Pobiera zestaw rekordów DNS typu NS |
> | Działanie | Microsoft. Network/dnszones/NS/zapis | Tworzy lub aktualizuje zestaw rekordów DNS typu NS |
> | Działanie | Microsoft. Network/dnszones/PTR/usuwanie | Usuń zestaw rekordów o danej nazwie i typie "PTR" ze strefy DNS. |
> | Działanie | Microsoft. Network/dnszones/PTR/odczyt | Pobierz zestaw rekordów typu "PTR" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/PTR/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/dnszones/odczyt | Pobierz strefę DNS w formacie JSON. Właściwości strefy obejmują Tagi, ETag, numberOfRecordSets i maxNumberOfRecordSets. Należy zauważyć, że to polecenie nie pobiera zestawów rekordów zawartych w strefie. |
> | Działanie | Microsoft. Network/dnszones/zestawy rekordów/odczyt | Pobiera zestawy rekordów DNS dla różnych typów |
> | Działanie | Microsoft. Network/dnszones/SOA/Read | Pobiera zestaw rekordów DNS typu SOA |
> | Działanie | Microsoft. Network/dnszones/SOA/Write | Tworzy lub aktualizuje zestaw rekordów DNS typu SOA |
> | Działanie | Microsoft. Network/dnszones/SRV/Delete | Usuń zestaw rekordów o danej nazwie i typie "SRV" ze strefy DNS. |
> | Działanie | Microsoft. Network/dnszones/SRV/Read | Pobierz zestaw rekordów typu "SRV" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/SRV/Write | Utwórz lub zaktualizuj zestaw rekordów typu SRV |
> | Działanie | Microsoft. Network/dnszones/TXT/Delete | Usuń zestaw rekordów o danej nazwie i typie "TXT" ze strefy DNS. |
> | Działanie | Microsoft. Network/dnszones/TXT/odczyt | Pobierz zestaw rekordów typu "TXT" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/dnszones/TXT/Write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/dnszones/Write | Utwórz lub zaktualizuj strefę DNS w grupie zasobów.  Służy do aktualizowania tagów w zasobie strefy DNS. Należy pamiętać, że tego polecenia nie można użyć do tworzenia lub aktualizowania zestawów rekordów w strefie. |
> | Działanie | Microsoft. Network/expressRouteCircuits/autoryzacje/usuwanie | Usuwa autoryzację ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/authorizations/Read | Pobiera autoryzację ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/autoryzacje/zapis | Tworzy lub aktualizuje istniejącą autoryzację ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Delete | Usuwa ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Join/Action | Sprzęga obwód trasy Express. Brak alertów. |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/arpTables/Read | Pobiera ExpressRoute komunikacji równorzędnej ArpTable |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/Connections/Delete | Usuwa połączenie ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/Connections/Read | Pobiera połączenie ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/Connections/Write | Tworzy lub aktualizuje istniejący zasób połączenia usługi ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna/usuwanie | Usuwa komunikację równorzędną ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/peerConnections/Read | Pobiera połączenie obwodu trasy usługi peer Express |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/Read | Pobiera komunikację równorzędną ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/routeTables/Read | Pobiera trasę komunikacji równorzędnej ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/routeTablesSummary/Read | Pobiera podsumowanie tras komunikacji równorzędnej ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/\field/Read | Pobiera stat ExpressRoute komunikacji równorzędnej |
> | Działanie | Microsoft. Network/expressRouteCircuits/Peers/Write | Tworzy lub aktualizuje istniejącą komunikację równorzędną ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/odczyt | Pobierz ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCircuits/Statystyka/odczyt | Pobiera ExpressRoute stat |
> | Działanie | Microsoft. Network/expressRouteCircuits/Write | Tworzy lub aktualizuje istniejący ExpressRoute |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Join/Action | Sprzęga połączenie krzyżowe usługi Express Route. Brak alertów. |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Peers/arpTables/Read | Pobiera tabelę ARP komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Komunikacja równorzędna/usuwanie | Usuwa komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Peers/Read | Pobiera komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Peers/routeTables/Read | Pobiera tabelę tras komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Peers/routeTableSummary/Read | Pobiera podsumowanie tabeli tras komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/Peers/Write | Tworzy komunikację równorzędną połączenia krzyżowego usługi Express Route lub aktualizuje istniejącą komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteCrossConnections/odczyt | Pobierz połączenie krzyżowe usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteGateways/expressRouteConnections/Delete | Usuwa połączenie usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteGateways/expressRouteConnections/odczyt | Pobiera połączenie usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteGateways/expressRouteConnections/Write | Tworzy połączenie usługi Express Route lub aktualizuje istniejące połączenie usługi Express Route |
> | Działanie | Microsoft. Network/expressRouteGateways/Join/Action | Umożliwia dołączenie do bramy Express Route Gateway. Brak alertów. |
> | Działanie | Microsoft. Network/expressRouteGateways/odczyt | Pobierz bramę Express Route Gateway |
> | Działanie | Microsoft. Network/expressRoutePorts/Delete | Usuwa ExpressRoutePorts |
> | Działanie | Microsoft. Network/expressRoutePorts/Join/Action | Sprzęga porty routingu Express. Brak alertów. |
> | Działanie | Microsoft. Network/expressRoutePorts/Links/Read | Pobiera ExpressRouteLink |
> | Działanie | Microsoft. Network/expressRoutePorts/odczyt | Pobiera ExpressRoutePorts |
> | Działanie | Microsoft. Network/expressRoutePorts/Write | Tworzy lub aktualizuje ExpressRoutePorts |
> | Działanie | Microsoft. Network/expressRoutePortsLocations/odczyt | Pobierz lokalizacje portów Express Route |
> | Działanie | Microsoft. Network/expressRouteServiceProviders/odczyt | Pobiera dostawców Route Service ekspresowych |
> | Działanie | Microsoft. Network/firewallPolicies/Delete | Usuwa zasady zapory |
> | Działanie | Microsoft. Network/firewallPolicies/Join/Action | Przyłącza zasady zapory. Brak alertów. |
> | Działanie | Microsoft. Network/firewallPolicies/odczyt | Pobiera zasady zapory |
> | Działanie | Microsoft. Network/firewallPolicies/ruleGroups/Delete | Usuwa grupę reguł zasad zapory |
> | Działanie | Microsoft. Network/firewallPolicies/ruleGroups/odczyt | Pobiera grupę reguł zasad zapory |
> | Działanie | Microsoft. Network/firewallPolicies/ruleGroups/Write | Tworzy grupę reguł zasad zapory lub aktualizuje istniejącą grupę reguł zasad zapory |
> | Działanie | Microsoft. Network/firewallPolicies/Write | Tworzy zasady zapory lub aktualizuje istniejące zasady zapory |
> | Działanie | Microsoft. Network/usługi frontdoor/backendPools/Delete | Usuwa pulę zaplecza |
> | Działanie | Microsoft. Network/usługi frontdoor/backendPools/odczyt | Pobiera pulę zaplecza |
> | Działanie | Microsoft. Network/usługi frontdoor/backendPools/Write | Tworzy lub aktualizuje pulę zaplecza |
> | Działanie | Microsoft. Network/usługi frontdoor/Delete | Usuwa drzwiczki z przodu |
> | Działanie | Microsoft. Network/usługi frontdoor/frontendEndpoints/Delete | Usuwa punkt końcowy frontonu |
> | Działanie | Microsoft. Network/usługi frontdoor/frontendEndpoints/disableHttps/Action | Wyłącza protokół HTTPS w punkcie końcowym frontonu |
> | Działanie | Microsoft. Network/usługi frontdoor/frontendEndpoints/enableHttps/Action | Włącza protokół HTTPS w punkcie końcowym frontonu |
> | Działanie | Microsoft. Network/usługi frontdoor/frontendEndpoints/odczyt | Pobiera punkt końcowy frontonu |
> | Działanie | Microsoft. Network/usługi frontdoor/frontendEndpoints/Write | Tworzy lub aktualizuje punkt końcowy frontonu |
> | Działanie | Microsoft. Network/usługi frontdoor/healthProbeSettings/Delete | Usuwa ustawienia sondy kondycji |
> | Działanie | Microsoft. Network/usługi frontdoor/healthProbeSettings/odczyt | Pobiera ustawienia sondy kondycji |
> | Działanie | Microsoft. Network/usługi frontdoor/healthProbeSettings/Write | Tworzy lub aktualizuje ustawienia sondy kondycji |
> | Działanie | Microsoft. Network/usługi frontdoor/loadBalancingSettings/Delete | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Działanie | Microsoft. Network/usługi frontdoor/loadBalancingSettings/odczyt | Pobiera ustawienia równoważenia obciążenia |
> | Działanie | Microsoft. Network/usługi frontdoor/loadBalancingSettings/Write | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Działanie | Microsoft. Network/usługi frontdoor/przeczyszczanie/akcja | Przeczyszczanie zawartości w pamięci podręcznej z drzwi przednich |
> | Działanie | Microsoft. Network/usługi frontdoor/odczyt | Pobiera tylne drzwi |
> | Działanie | Microsoft. Network/usługi frontdoor/routingRules/Delete | Usuwa regułę routingu |
> | Działanie | Microsoft. Network/usługi frontdoor/routingRules/odczyt | Pobiera regułę routingu |
> | Działanie | Microsoft. Network/usługi frontdoor/routingRules/Write | Tworzy lub aktualizuje regułę routingu |
> | Działanie | Microsoft. Network/usługi frontdoor/validateCustomDomain/akcja | Sprawdza poprawność punktu końcowego frontonu dla drzwi z przodu |
> | Działanie | Microsoft. Network/usługi frontdoor/Write | Tworzy lub aktualizuje tylne drzwi |
> | Działanie | Microsoft. Network/frontDoorWebApplicationFirewallManagedRuleSets/odczyt | Pobiera zestawy reguł zarządzane przez zaporę aplikacji sieci Web |
> | Działanie | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Delete | Usuwa zasady zapory aplikacji sieci Web |
> | Działanie | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/odczyt | Pobiera zasady zapory aplikacji sieci Web |
> | Działanie | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Write | Tworzy lub aktualizuje zasady zapory aplikacji sieci Web |
> | Działanie | Microsoft. Network/loadBalancers/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | Działanie | Microsoft. Network/loadBalancers/backendAddressPools/odczyt | Pobiera definicję puli adresów zaplecza modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/Delete | Usuwa moduł równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/frontonu używającej/Join/Action | Sprzęga konfigurację adresu IP frontonu Load Balancer. Brak alertów. |
> | Działanie | Microsoft. Network/loadBalancers/frontonu używającej/odczyt | Pobiera definicję konfiguracji adresu IP frontonu modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/inboundNatPools/Join/Action | Przyłącza do puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia. Brak alertów. |
> | Działanie | Microsoft. Network/loadBalancers/inboundNatPools/odczyt | Pobiera definicję puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/inboundNatRules/Delete | Usuwa regułę NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/inboundNatRules/Join/Action | Przyłącza do przychodzącej reguły NAT modułu równoważenia obciążenia. Brak alertów. |
> | Działanie | Microsoft. Network/loadBalancers/inboundNatRules/odczyt | Pobiera definicję reguły NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/inboundNatRules/Write | Tworzy regułę NAT dla ruchu przychodzącego modułu równoważenia obciążenia lub aktualizuje istniejącą regułę NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/loadBalancingRules/odczyt | Pobiera definicję reguły równoważenia obciążenia modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/networkInterfaces/odczyt | Pobiera odwołania do wszystkich interfejsów sieciowych w ramach modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/outboundRules/odczyt | Pobiera definicję reguły ruchu wychodzącego modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/sondy/akcja | Umożliwia korzystanie z sond modułu równoważenia obciążenia. Na przykład za pomocą tej właściwości uprawnienia healthProbe zestawu skalowania maszyn wirtualnych można odwoływać się do sondy. Brak alertów. |
> | Działanie | Microsoft. Network/loadBalancers/sondy/odczyt | Pobiera sondę modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/odczyt | Pobiera definicję modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/virtualMachines/odczyt | Pobiera odwołania do wszystkich maszyn wirtualnych w ramach modułu równoważenia obciążenia |
> | Działanie | Microsoft. Network/loadBalancers/Write | Tworzy moduł równoważenia obciążenia lub aktualizuje istniejący moduł równoważenia obciążenia |
> | Działanie | Microsoft. Network/localnetworkgateways/Delete | Usuwa LocalNetworkGateway |
> | Działanie | Microsoft. Network/localnetworkgateways/odczyt | Pobiera LocalNetworkGateway |
> | Działanie | Microsoft. Network/localnetworkgateways/Write | Tworzy lub aktualizuje istniejący LocalNetworkGateway |
> | Działanie | Microsoft. Network/Locations/autoApprovedPrivateLinkServices/Read | Pobiera usługi linków prywatnych z autozatwierdzeniem |
> | Działanie | Microsoft. Network/Locations/availableDelegations/Read | Pobiera dostępne delegacje |
> | Działanie | Microsoft. Network/Locations/availablePrivateEndpointTypes/Read | Pobiera dostępne zasoby prywatnego punktu końcowego |
> | Działanie | Microsoft. Network/Locations/availableServiceAliases/Read | Pobiera dostępne aliasy usługi |
> | Działanie | Microsoft. Network/Locations/bareMetalTenants/Action | Przydziela lub sprawdza poprawność dzierżawy systemu operacyjnego |
> | Działanie | Microsoft. Network/Locations/checkAcceleratedNetworkingSupport/Action | Sprawdza obsługę przyspieszonej sieci |
> | Działanie | Microsoft. Network/Locations/checkDnsNameAvailability/Read | Sprawdza, czy etykieta DNS jest dostępna w określonej lokalizacji |
> | Działanie | Microsoft. Network/Locations/checkPrivateLinkServiceVisibility/Action | Sprawdza widoczność usługi link prywatny |
> | Działanie | Microsoft. Network/Locations/operationResults/Read | Pobiera wynik operacji asynchronicznej operacji POST lub DELETE |
> | Działanie | Microsoft. Network/Locations/Operations/Read | Pobiera zasób operacji reprezentujący stan operacji asynchronicznej |
> | Działanie | Microsoft. Network/Locations/servicetags/Read | Pobierz Tagi usługi |
> | Działanie | Microsoft. Network/Locations/supportedVirtualMachineSizes/Read | Pobiera obsługiwane rozmiary maszyn wirtualnych |
> | Działanie | Microsoft. Network/lokalizacje/użycia/odczyt | Pobiera metryki użycia zasobów |
> | Działanie | Microsoft. Network/Locations/virtualNetworkAvailableEndpointServices/Read | Pobiera listę dostępnych Virtual Network usług punktu końcowego |
> | Działanie | Microsoft. Network/networkIntentPolicies/Delete | Usuwa zasady dotyczące opcji sieci |
> | Działanie | Microsoft. Network/networkIntentPolicies/odczyt | Pobiera opis zasad przeznaczenie sieci |
> | Działanie | Microsoft. Network/networkIntentPolicies/Write | Tworzy zasady założeń sieciowych lub aktualizuje istniejące zasady założeń sieci |
> | Działanie | Microsoft. Network/networkInterfaces/Delete | Usuwa interfejs sieciowy |
> | Działanie | Microsoft. Network/networkInterfaces/effectiveNetworkSecurityGroups/akcja | Pobieranie sieciowych grup zabezpieczeń skonfigurowanych dla interfejsu sieciowego maszyny wirtualnej |
> | Działanie | Microsoft. Network/networkInterfaces/effectiveRouteTable/akcja | Pobieranie tabeli tras skonfigurowanych dla interfejsu sieciowego maszyny wirtualnej |
> | Działanie | Microsoft. Network/networkInterfaces/ipconfiguration/sprzężenie/akcja | Umożliwia dołączenie do konfiguracji protokołu IP interfejsu sieciowego. Brak alertów. |
> | Działanie | Microsoft. Network/networkInterfaces/ipconfiguration/odczyt | Pobiera definicję konfiguracji protokołu IP interfejsu sieciowego.  |
> | Działanie | Microsoft. Network/networkInterfaces/Join/Action | Przyłączanie maszyny wirtualnej do interfejsu sieciowego. Brak alertów. |
> | Działanie | Microsoft. Network/networkInterfaces/loadBalancers/odczyt | Pobiera wszystkie usługi równoważenia obciążenia, do których należy interfejs sieciowy |
> | Działanie | Microsoft. Network/networkInterfaces/odczyt | Pobiera definicję interfejsu sieciowego.  |
> | Działanie | Microsoft. Network/networkInterfaces/tapConfigurations/Delete | Usuwa konfigurację naciskania interfejsu sieciowego. |
> | Działanie | Microsoft. Network/networkInterfaces/tapConfigurations/odczyt | Pobiera konfigurację naciskania interfejsu sieciowego. |
> | Działanie | Microsoft. Network/networkInterfaces/tapConfigurations/Write | Tworzy interfejs sieciowy naciśnij pozycję Konfiguracja lub aktualizuje istniejący interfejs sieciowy naciśnij pozycję Konfiguracja. |
> | Działanie | Microsoft. Network/networkInterfaces/Write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Działanie | Microsoft. Network/networkProfiles/Delete | Usuwa profil sieciowy |
> | Działanie | Microsoft. Network/networkProfiles/odczyt | Pobiera profil sieciowy |
> | Działanie | Microsoft. Network/networkProfiles/removeContainers/akcja | Usuwa kontenery |
> | Działanie | Microsoft. Network/networkProfiles/setcontainers/Action | Ustawia kontenery |
> | Działanie | Microsoft. Network/networkProfiles/setNetworkInterfaces/akcja | Ustawia interfejsy sieciowe kontenerów |
> | Działanie | Microsoft. Network/networkProfiles/Write | Tworzy lub aktualizuje profil sieciowy |
> | Działanie | Microsoft. Network/networkSecurityGroups/defaultSecurityRules/odczyt | Pobiera domyślną definicję reguły zabezpieczeń |
> | Działanie | Microsoft. Network/networkSecurityGroups/Delete | Usuwa sieciową grupę zabezpieczeń |
> | Działanie | Microsoft. Network/networkSecurityGroups/Join/Action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | Działanie | Microsoft. Network/networkSecurityGroups/odczyt | Pobiera definicję sieciowej grupy zabezpieczeń |
> | Działanie | Microsoft. Network/networkSecurityGroups/securityRules/Delete | Usuwa regułę zabezpieczeń |
> | Działanie | Microsoft. Network/networkSecurityGroups/securityRules/odczyt | Pobiera definicję reguły zabezpieczeń |
> | Działanie | Microsoft. Network/networkSecurityGroups/securityRules/Write | Tworzy regułę zabezpieczeń lub aktualizuje istniejącą regułę zabezpieczeń |
> | Działanie | Microsoft. Network/networkSecurityGroups/Write | Tworzy sieciową grupę zabezpieczeń lub aktualizuje istniejącą sieciową grupę zabezpieczeń |
> | Działanie | Microsoft. Network/networkWatchers/availableProvidersList/akcja | Zwraca wszystkich dostępnych usługodawców internetowych dla określonego regionu świadczenia usługi Azure. |
> | Działanie | Microsoft. Network/networkWatchers/azureReachabilityReport/akcja | Zwraca wynik względnego opóźnienia dla usługodawców internetowych z określonej lokalizacji do regionów świadczenia usługi Azure. |
> | Działanie | Microsoft. Network/networkWatchers/configureFlowLog/akcja | Konfiguruje rejestrowanie przepływu dla zasobu docelowego. |
> | Działanie | Microsoft. Network/networkWatchers/connectionMonitors/Delete | Usuwa monitor połączeń |
> | Działanie | Microsoft. Network/networkWatchers/connectionMonitors/kwerenda/akcja | Zbadaj łączność monitorowania między określonymi punktami końcowymi |
> | Działanie | Microsoft. Network/networkWatchers/connectionMonitors/odczyt | Pobierz szczegóły monitora połączeń |
> | Działanie | Microsoft. Network/networkWatchers/connectionMonitors/Start/akcja | Rozpocznij Monitorowanie łączności między określonymi punktami końcowymi |
> | Działanie | Microsoft. Network/networkWatchers/connectionMonitors/akcja | Zatrzymaj/Wstrzymaj Monitorowanie łączności między określonymi punktami końcowymi |
> | Działanie | Microsoft. Network/networkWatchers/connectionMonitors/Write | Tworzy monitor połączeń |
> | Działanie | Microsoft. Network/networkWatchers/connectivityCheck/akcja | Weryfikuje możliwość ustanowienia bezpośredniego połączenia TCP z maszyny wirtualnej do danego punktu końcowego, w tym innej maszyny wirtualnej lub dowolnego serwera zdalnego. |
> | Działanie | Microsoft. Network/networkWatchers/Delete | Usuwa obserwatora sieciowego |
> | Działanie | Microsoft. Network/networkWatchers/ipFlowVerify/akcja | Zwraca czy pakiet jest dozwolony, czy odrzucany do lub z określonego miejsca docelowego. |
> | Działanie | Microsoft. Network/networkWatchers/soczewki/usuwanie | Usuwa soczewkę |
> | Działanie | Microsoft. Network/networkWatchers/soczewki/kwerenda/akcja | Zbadaj ruch sieciowy monitorowania w określonym punkcie końcowym |
> | Działanie | Microsoft. Network/networkWatchers/soczewki/odczyt | Pobierz szczegóły obiektywu |
> | Działanie | Microsoft. Network/networkWatchers/soczewki/uruchomienie/akcja | Rozpocznij monitorowanie ruchu sieciowego w określonym punkcie końcowym |
> | Działanie | Microsoft. Network/networkWatchers/soczewki/akcja | Zatrzymaj/Wstrzymaj monitorowanie ruchu sieciowego w określonym punkcie końcowym |
> | Działanie | Microsoft. Network/networkWatchers/soczewki/Write | Tworzy soczewkę |
> | Działanie | Microsoft. Network/networkWatchers/networkConfigurationDiagnostic/akcja | Diagnostyka konfiguracji sieci. |
> | Działanie | Microsoft. Network/networkWatchers/skoku/akcja | W przypadku określonego docelowego i docelowego adresu IP Zwróć wartość typu następnego przeskoku i Następny adres IP. |
> | Działanie | Microsoft. Network/networkWatchers/packetCaptures/Delete | Usuwa przechwycenie pakietu |
> | Działanie | Microsoft. Network/networkWatchers/packetCaptures/queryStatus/Action | Pobiera informacje o właściwościach i stanie zasobu przechwytywania pakietów. |
> | Działanie | Microsoft. Network/networkWatchers/packetCaptures/odczyt | Pobierz definicję przechwytywania pakietów |
> | Działanie | Microsoft. Network/networkWatchers/packetCaptures/akcja | Zatrzymaj uruchomioną sesję przechwytywania pakietów. |
> | Działanie | Microsoft. Network/networkWatchers/packetCaptures/Write | Tworzy przechwycenie pakietu |
> | Działanie | Microsoft. Network/networkWatchers/pingMeshes/Delete | Usuwa element PingMesh |
> | Działanie | Microsoft. Network/networkWatchers/pingMeshes/odczyt | Pobierz szczegóły PingMesh |
> | Działanie | Microsoft. Network/networkWatchers/pingMeshes/Start/akcja | Rozpocznij PingMesh między określonymi maszynami wirtualnymi |
> | Działanie | Microsoft. Network/networkWatchers/pingMeshes/akcja | Zatrzymaj PingMesh między określonymi maszynami wirtualnymi |
> | Działanie | Microsoft. Network/networkWatchers/pingMeshes/Write | Tworzy element PingMesh |
> | Działanie | Microsoft. Network/networkWatchers/queryConnectionMonitors/akcja | Połączenie wsadowe monitorowania zapytań między określonymi punktami końcowymi |
> | Działanie | Microsoft. Network/networkWatchers/queryFlowLogStatus/akcja | Pobiera stan rejestrowania przepływu dla zasobu. |
> | Działanie | Microsoft. Network/networkWatchers/queryTroubleshootResult/akcja | Pobiera wynik rozwiązywania problemów z wcześniej uruchomionej lub aktualnie uruchomionej operacji rozwiązywania problemów. |
> | Działanie | Microsoft. Network/networkWatchers/odczyt | Pobierz definicję obserwatora sieciowego |
> | Działanie | Microsoft. Network/networkWatchers/securityGroupView/akcja | Wyświetl skonfigurowane i efektywne reguły sieciowej grupy zabezpieczeń zastosowane na maszynie wirtualnej. |
> | Działanie | Microsoft. Network/networkWatchers/topologia/akcja | Pobiera widok poziomu sieci zasobów i ich relacje z grupą zasobów. |
> | Działanie | Microsoft. Network/networkWatchers/Rozwiązywanie problemów/akcja | Uruchamia rozwiązywanie problemów dotyczących zasobów sieciowych na platformie Azure. |
> | Działanie | Microsoft. Network/networkWatchers/Write | Tworzy obserwatora sieciowego lub aktualizuje istniejący obserwator sieci |
> | Działanie | Microsoft. Network/Operations/Read | Pobierz dostępne operacje |
> | Działanie | Microsoft. Network/p2sVpnGateways/Delete | Usuwa element P2SVpnGateway. |
> | Działanie | Microsoft. Network/p2sVpnGateways/generatevpnprofile/akcja | Generuj profil sieci VPN dla P2SVpnGateway |
> | Działanie | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealth/akcja | Pobiera kondycję połączenia sieci VPN P2S dla P2SVpnGateway |
> | Działanie | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/akcja | Pobiera szczegóły kondycji połączenia sieci VPN P2S dla P2SVpnGateway |
> | Działanie | Microsoft. Network/p2sVpnGateways/odczyt | Pobiera P2SVpnGateway. |
> | Działanie | Microsoft. Network/p2sVpnGateways/Write | P2SVpnGateway. |
> | Działanie | Microsoft. Network/privateDnsOperationResults/odczyt | Pobiera wyniki operacji Prywatna strefa DNS |
> | Działanie | Microsoft. Network/privateDnsOperationStatuses/odczyt | Pobiera stan operacji Prywatna strefa DNS |
> | Działanie | Microsoft. Network/privateDnsZones/A/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "A" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/A/Odczyt | Pobierz zestaw rekordów typu "A" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/privateDnsZones/A/Write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/privateDnsZones/AAAA/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "AAAA" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/AAAA/odczyt | Pobierz zestaw rekordów typu "AAAA" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/privateDnsZones/AAAA/Write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/privateDnsZones/ALL/Read | Pobiera Prywatna strefa DNS zestawy rekordów dla różnych typów |
> | Działanie | Microsoft. Network/privateDnsZones/CNAME/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "CNAME" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/CNAME/odczyt | Pobierz zestaw rekordów typu "CNAME" w ramach strefy Prywatna strefa DNS w formacie JSON. |
> | Działanie | Microsoft. Network/privateDnsZones/CNAME/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w ramach strefy Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/Delete | Usuń strefę Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/MX/Delete | Usuń zestaw rekordów o danej nazwie i typie "MX" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/MX/odczyt | Pobierz zestaw rekordów typu "MX" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/privateDnsZones/MX/Write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/privateDnsZones/PTR/usuwanie | Usuń zestaw rekordów o danej nazwie i wpisz "PTR" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/PTR/odczyt | Pobierz zestaw rekordów typu "PTR" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/privateDnsZones/PTR/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/privateDnsZones/odczyt | Pobierz Prywatna strefa DNS właściwości strefy w formacie JSON. Należy zauważyć, że to polecenie nie pobiera sieci wirtualnych, do których jest połączona strefa Prywatna strefa DNS lub zestawy rekordów zawarte w strefie. |
> | Działanie | Microsoft. Network/privateDnsZones/zestawy rekordów/odczyt | Pobiera Prywatna strefa DNS zestawy rekordów dla różnych typów |
> | Działanie | Microsoft. Network/privateDnsZones/SOA/Read | Pobierz zestaw rekordów typu "SOA" w ramach strefy Prywatna strefa DNS w formacie JSON. |
> | Działanie | Microsoft. Network/privateDnsZones/SOA/Write | Zaktualizuj zestaw rekordów typu "SOA" w ramach strefy Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/SRV/Delete | Usuń zestaw rekordów o danej nazwie i typie "SRV" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/SRV/Read | Pobierz zestaw rekordów typu "SRV" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/privateDnsZones/SRV/Write | Utwórz lub zaktualizuj zestaw rekordów typu "SRV" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/privateDnsZones/TXT/Delete | Usuń zestaw rekordów o danej nazwie i typie "TXT" ze strefy Prywatna strefa DNS. |
> | Działanie | Microsoft. Network/privateDnsZones/TXT/odczyt | Pobierz zestaw rekordów typu "TXT" w strefie Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Działanie | Microsoft. Network/privateDnsZones/TXT/Write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Działanie | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Delete | Usuń link strefy Prywatna strefa DNS do sieci wirtualnej. |
> | Działanie | Microsoft. Network/privateDnsZones/virtualNetworkLinks/odczyt | Pobierz łącze strefy Prywatna strefa DNS do właściwości sieci wirtualnej w formacie JSON. |
> | Działanie | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write | Utwórz lub zaktualizuj link strefy Prywatna strefa DNS do sieci wirtualnej. |
> | Działanie | Microsoft. Network/privateDnsZones/Write | Utwórz lub zaktualizuj strefę Prywatna strefa DNS w grupie zasobów. Należy pamiętać, że tego polecenia nie można użyć do tworzenia lub aktualizowania linków sieci wirtualnej lub zestawów rekordów w strefie. |
> | Działanie | Microsoft. Network/privateEndpointRedirectMaps/odczyt | Pobiera prywatny punkt końcowy RedirectMap |
> | Działanie | Microsoft. Network/privateEndpointRedirectMaps/Write | Tworzy prywatny punkt końcowy RedirectMap lub aktualizuje istniejący prywatny punkt końcowy RedirectMap |
> | Działanie | Microsoft. Network/privateEndpoints/Delete | Usuwa prywatny zasób punktu końcowego. |
> | Działanie | Microsoft. Network/privateEndpoints/odczyt | Pobiera prywatny zasób punktu końcowego. |
> | Działanie | Microsoft. Network/privateEndpoints/Write | Tworzy nowy prywatny punkt końcowy lub aktualizuje istniejący prywatny punkt końcowy. |
> | Działanie | Microsoft. Network/privateLinkServices/Delete | Usuwa zasób usługi link prywatny. |
> | Działanie | Microsoft. Network/privateLinkServices/privateEndpointConnections/Delete | Usuwa połączenie prywatnego punktu końcowego. |
> | Działanie | Microsoft. Network/privateLinkServices/privateEndpointConnections/odczyt | Pobiera definicję połączenia z prywatnym punktem końcowym. |
> | Działanie | Microsoft. Network/privateLinkServices/privateEndpointConnections/Write | Tworzy nowe połączenie prywatnego punktu końcowego lub aktualizuje istniejące połączenie prywatnego punktu końcowego. |
> | Działanie | Microsoft. Network/privateLinkServices/odczyt | Pobiera zasób usługi linku prywatnego. |
> | Działanie | Microsoft. Network/privateLinkServices/Write | Tworzy nową usługę linku prywatnego lub aktualizuje istniejącą usługę linku prywatnego. |
> | Działanie | Microsoft. Network/adresów publicipaddress/Delete | Usuwa publiczny adres IP. |
> | Działanie | Microsoft. Network/adresów publicipaddress/Join/Action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | Działanie | Microsoft. Network/adresów publicipaddress/odczyt | Pobiera definicję publicznego adresu IP. |
> | Działanie | Microsoft. Network/adresów publicipaddress/Write | Tworzy publiczny adres IP lub aktualizuje istniejący publiczny adres IP.  |
> | Działanie | Microsoft. Network/publicIPPrefixes/Delete | Usuwa prefiks publicznego adresu IP |
> | Działanie | Microsoft. Network/publicIPPrefixes/Join/Action | Sprzęga PublicIPPrefix. Brak alertów. |
> | Działanie | Microsoft. Network/publicIPPrefixes/odczyt | Pobiera definicję prefiksu publicznego adresu IP |
> | Działanie | Microsoft. Network/publicIPPrefixes/Write | Tworzy publiczny prefiks adresu IP lub aktualizuje istniejący prefiks publicznego adresu IP |
> | Działanie | Microsoft. Network/Register/Action | Rejestruje subskrypcję |
> | Działanie | Microsoft. Network/routeFilters/Delete | Usuwa definicję filtru tras |
> | Działanie | Microsoft. Network/routeFilters/Join/Action | Sprzęga filtr tras. Brak alertów. |
> | Działanie | Microsoft. Network/routeFilters/odczyt | Pobiera definicję filtru tras |
> | Działanie | Microsoft. Network/routeFilters/routeFilterRules/Delete | Usuwa definicję reguły filtru trasy |
> | Działanie | Microsoft. Network/routeFilters/routeFilterRules/odczyt | Pobiera definicję reguły filtru trasy |
> | Działanie | Microsoft. Network/routeFilters/routeFilterRules/Write | Tworzy regułę filtru tras lub aktualizuje istniejącą regułę filtru tras |
> | Działanie | Microsoft. Network/routeFilters/Write | Tworzy filtr trasy lub aktualizuje istniejący filtr tras |
> | Działanie | Microsoft. Network/routeTables/Delete | Usuwa definicję tabeli tras |
> | Działanie | Microsoft. Network/routeTables/Join/Action | Sprzęga tabelę tras. Brak alertów. |
> | Działanie | Microsoft. Network/routeTables/odczyt | Pobiera definicję tabeli tras |
> | Działanie | Microsoft. Network/routeTables/Routes/Delete | Usuwa definicję trasy |
> | Działanie | Microsoft. Network/routeTables/Routes/Read | Pobiera definicję trasy |
> | Działanie | Microsoft. Network/routeTables/Routes/write | Tworzy trasę lub aktualizuje istniejącą trasę |
> | Działanie | Microsoft. Network/routeTables/Write | Tworzy tabelę tras lub aktualizuje istniejącą tabelę tras |
> | Działanie | Microsoft. Network/securegateways/Delete | Usuwanie bramy Secure Gateway |
> | Działanie | Microsoft. Network/securegateways/odczyt | Uzyskaj bezpieczną bramę |
> | Działanie | Microsoft. Network/securegateways/Write | Tworzy lub aktualizuje bramę Secure Gateway |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/Delete | Usuwa zasady punktu końcowego usługi |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/Join/Action | Przyłącza zasady punktu końcowego usługi. Brak alertów. |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/joinSubnet/akcja | Przyłączanie podsieci do zasad punktu końcowego usługi. Brak alertów. |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/odczyt | Pobiera opis zasad punktu końcowego usługi |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Delete | Usuwa definicję zasad punktu końcowego usługi |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/odczyt | Pobiera opis definicji zasad punktu końcowego usługi |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Write | Tworzy definicję zasad punktu końcowego usługi lub aktualizuje istniejącą definicję zasad punktu końcowego usługi |
> | Działanie | Microsoft. Network/serviceEndpointPolicies/Write | Tworzy zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi |
> | Działanie | Microsoft. Network/trafficManagerGeographicHierarchies/odczyt | Pobiera Traffic Manager hierarchię geograficzną zawierającą regiony, które mogą być używane z metodą routingu ruchu geograficznego |
> | Działanie | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Delete | Usuwa punkt końcowy platformy Azure z istniejącego profilu Traffic Manager. Traffic Manager przestanie kierować ruch do usuniętego punkt końcowy platformy Azure. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/azureEndpoints/odczyt | Pobiera punkt końcowy platformy Azure, które należy do profilu Traffic Manager, w tym wszystkie właściwości tego punkt końcowy platformy Azure. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Write | Dodaj nowy punkt końcowy platformy Azure w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącej punkt końcowy platformy Azure w tym profilu Traffic Manager. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/Delete | Usuń profil Traffic Manager. Wszystkie ustawienia skojarzone z profilem Traffic Manager zostaną utracone i nie będzie można już używać profilu do kierowania ruchem. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Delete | Usuwa zewnętrzny punkt końcowy z istniejącego profilu Traffic Manager. Traffic Manager zatrzyma kierowanie ruchu do usuniętego zewnętrznego punktu końcowego. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/externalEndpoints/odczyt | Pobiera zewnętrzny punkt końcowy, który należy do profilu Traffic Manager, łącznie ze wszystkimi właściwościami tego zewnętrznego punktu końcowego. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Write | Dodaj nowy zewnętrzny punkt końcowy w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącego zewnętrznego punktu końcowego w tym profilu Traffic Manager. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/map cieplnych/odczyt | Pobiera Traffic Managerową mapę cieplną dla danego profilu Traffic Manager, który zawiera liczby zapytań i dane opóźnienia według lokalizacji i źródłowego adresu IP. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Delete | Usuwa zagnieżdżony punkt końcowy z istniejącego profilu Traffic Manager. Traffic Manager przestanie kierować ruch do usuniętego zagnieżdżonego punktu końcowego. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/odczyt | Pobiera zagnieżdżony punkt końcowy, który należy do profilu Traffic Manager, łącznie ze wszystkimi właściwościami tego zagnieżdżonego punktu końcowego. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Write | Dodaj nowy zagnieżdżony punkt końcowy w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącego zagnieżdżonego punktu końcowego w tym Traffic Manager profilu. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/odczyt | Pobierz konfigurację profilu Traffic Manager. Obejmuje to ustawienia DNS, ustawienia routingu ruchu, ustawienia monitorowania punktu końcowego oraz listę punktów końcowych kierowanych przez ten profil Traffic Manager. |
> | Działanie | Microsoft. Network/trafficManagerProfiles/Write | Utwórz profil Traffic Manager lub zmodyfikuj konfigurację istniejącego profilu Traffic Manager.<br>Obejmuje to Włączanie lub wyłączanie profilu oraz modyfikowanie ustawień DNS, ustawień routingu ruchu lub ustawień monitorowania punktu końcowego.<br>Punkty końcowe kierowane przez profil Traffic Manager można dodawać, usuwać, włączać lub wyłączać. |
> | Działanie | Microsoft. Network/trafficManagerUserMetricsKeys/Delete | Usuwa klucz poziomu subskrypcji używany do zbierania metryk użytkownika w czasie rzeczywistym. |
> | Działanie | Microsoft. Network/trafficManagerUserMetricsKeys/odczyt | Pobiera klucz poziomu subskrypcji używany na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Działanie | Microsoft. Network/trafficManagerUserMetricsKeys/Write | Tworzy nowy klucz poziomu subskrypcji, który będzie używany na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Działanie | Microsoft. Network/Unregister/Action | Wyrejestrowuje subskrypcję |
> | Działanie | Microsoft. Network/virtualHubs/Delete | Usuwa koncentrator wirtualny |
> | Działanie | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/Delete | Usuwa element HubVirtualNetworkConnection |
> | Działanie | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/odczyt | Pobierz HubVirtualNetworkConnection |
> | Działanie | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/Write | Utwórz lub zaktualizuj HubVirtualNetworkConnection |
> | Działanie | Microsoft. Network/virtualHubs/odczyt | Pobierz koncentrator wirtualny |
> | Działanie | Microsoft. Network/virtualHubs/routeTables/Delete | Usuń VirtualHubRouteTableV2 |
> | Działanie | Microsoft. Network/virtualHubs/routeTables/odczyt | Pobierz VirtualHubRouteTableV2 |
> | Działanie | Microsoft. Network/virtualHubs/routeTables/Write | Utwórz lub zaktualizuj VirtualHubRouteTableV2 |
> | Działanie | Microsoft. Network/virtualHubs/Write | Tworzenie lub aktualizowanie koncentratora wirtualnego |
> | Działanie | Microsoft. Network/virtualnetworkgateways/Connections/Read | Pobierz VirtualNetworkGatewayConnection |
> | Działanie | Microsoft. Network/virtualNetworkGateways/Delete | Usuwa element virtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/generatevpnclientpackage/akcja | Generuj pakiet klienta dla virtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/generatevpnprofile/akcja | Generuj pakiet VpnProfile dla VirtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/getadvertisedroutes/akcja | Pobiera trasy anonsowane przez virtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/getbgppeerstatus/akcja | Pobiera stan elementu równorzędnego BGP virtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/getlearnedroutes/akcja | Pobiera virtualnetworkgatewayne trasy |
> | Działanie | Microsoft. Network/virtualnetworkgateways/getvpnclientconnectionhealth/akcja | Pobierz kondycję połączenia klienta sieci VPN dla VirtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/getvpnclientipsecparameters/akcja | Pobierz parametry klienta IPSec dla klienta VirtualNetworkGateway P2S. |
> | Działanie | Microsoft. Network/virtualnetworkgateways/getvpnprofilepackageurl/akcja | Pobiera adres URL wstępnie wygenerowanego pakietu profilu klienta VPN |
> | Działanie | Microsoft. Network/virtualNetworkGateways/odczyt | Pobiera VirtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/Reset/akcja | Resetuje virtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/akcja | Zresetuj klucz współużytkowany klienta dla klienta VirtualNetworkGateway P2S. |
> | Działanie | Microsoft. Network/virtualnetworkgateways/setvpnclientipsecparameters/akcja | Ustaw parametry klienta IPSec dla klienta VirtualNetworkGateway P2S. |
> | Działanie | Microsoft. Network/virtualnetworkgateways/supportedvpndevices/akcja | Wyświetla listę obsługiwanych urządzeń sieci VPN |
> | Działanie | Microsoft. Network/virtualNetworkGateways/Write | Tworzy lub aktualizuje VirtualNetworkGateway |
> | Działanie | Microsoft. Network/virtualNetworks/BastionHosts/akcja | Pobiera odwołania do hostów bastionu w Virtual Network. |
> | Działanie | Microsoft. Network/virtualNetworks/bastionHosts/default/Action | Pobiera odwołania do hostów bastionu w Virtual Network. |
> | Działanie | Microsoft. Network/virtualNetworks/checkIpAddressAvailability/odczyt | Sprawdź, czy adres IP jest dostępny w określonej sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/Delete | Usuwa sieć wirtualną |
> | Działanie | Microsoft. Network/virtualNetworks/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Działanie | Microsoft. Network/virtualNetworks/peer/Action | Tworzy równorzędną sieć wirtualną z inną siecią wirtualną |
> | Działanie | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Delete | Usuwa zasady punktu końcowego usługi kontekstowej |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Read | Pobiera zasady punktu końcowego usługi kontekstowej |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Write | Tworzy kontekstowe zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi kontekstowej |
> | Działanie | Microsoft. Network/virtualNetworks/podsieci/usuwanie | Usuwa podsieć sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/prepareNetworkPolicies/Action | Przygotowuje podsieć przez zastosowanie niezbędnych zasad sieciowych |
> | Działanie | Microsoft. Network/virtualNetworks/podsieci/odczyt | Pobiera definicję podsieci sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/unprepareNetworkPolicies/Action | Cofnięcie przygotowania podsieci przez usunięcie zastosowanych zasad sieciowych |
> | Działanie | Microsoft. Network/virtualNetworks/Subnets/virtualMachines/Read | Pobiera odwołania do wszystkich maszyn wirtualnych w podsieci sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/podsieci/zapis | Tworzy podsieć sieci wirtualnej lub aktualizuje istniejącą podsieć sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/Usages/Read | Uzyskaj użycie adresów IP dla każdej podsieci sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/virtualMachines/odczyt | Pobiera odwołania do wszystkich maszyn wirtualnych w sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Delete | Usuwa komunikację równorzędną sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/odczyt | Pobiera definicję komunikacji równorzędnej sieci wirtualnej |
> | Działanie | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Tworzy komunikację równorzędną sieci wirtualnej lub aktualizuje istniejącą komunikację równorzędną sieci wirtualnej |
> | Działanie | Microsoft. Network/virtualNetworks/Write | Tworzy sieć wirtualną lub aktualizuje istniejącą sieć wirtualną |
> | Działanie | Microsoft. Network/virtualNetworkTaps/Delete | Usuń naciśnięcie Virtual Network |
> | Działanie | Microsoft. Network/virtualNetworkTaps/Join/Action | Umożliwia dołączenie do programu TAP sieci wirtualnej. Brak alertów. |
> | Działanie | Microsoft. Network/virtualNetworkTaps/odczyt | Pobierz Virtual Network naciśnij pozycję |
> | Działanie | Microsoft. Network/virtualNetworkTaps/Write | Utwórz lub zaktualizuj Virtual Network TAP |
> | Działanie | Microsoft. Network/virtualRouters/Delete | Usuwa element VirtualRouter |
> | Działanie | Microsoft. Network/virtualRouters/Join/Action | Sprzęga VirtualRouter. Brak alertów. |
> | Działanie | Microsoft. Network/virtualRouters/odczyt | Pobiera VirtualRouter |
> | Działanie | Microsoft. Network/virtualRouters/virtualRouterPeerings/Delete | Usuwa element VirtualRouterPeering |
> | Działanie | Microsoft. Network/virtualRouters/virtualRouterPeerings/odczyt | Pobiera VirtualRouterPeering |
> | Działanie | Microsoft. Network/virtualRouters/virtualRouterPeerings/Write | Tworzy VirtualRouterPeering lub aktualizuje istniejący VirtualRouterPeering |
> | Działanie | Microsoft. Network/virtualRouters/Write | Tworzy VirtualRouter lub aktualizuje istniejący VirtualRouter |
> | Działanie | Microsoft. Network/virtualWans/Delete | Usuwa wirtualną sieć WAN |
> | Działanie | Microsoft. Network/virtualwans/generateVpnProfile/akcja | Generuj VirtualWanVpnServerConfiguration VpnProfile |
> | Działanie | Microsoft. Network/virtualWans/odczyt | Uzyskaj wirtualną sieć WAN |
> | Działanie | Microsoft. Network/virtualwans/supportedSecurityProviders/odczyt | Pobiera obsługiwanych dostawców zabezpieczeń VirtualWan. |
> | Działanie | Microsoft. Network/virtualWans/virtualHubs/odczyt | Pobiera wszystkie centra wirtualne odwołujące się do wirtualnej sieci WAN. |
> | Działanie | Microsoft. Network/virtualwans/vpnconfiguration/akcja | Pobiera konfigurację sieci VPN |
> | Działanie | Microsoft. Network/virtualwans/vpnServerConfigurations/akcja | Pobierz VirtualWanVpnServerConfigurations |
> | Działanie | Microsoft. Network/virtualWans/vpnSites/odczyt | Pobiera wszystkie lokacje sieci VPN odwołujące się do wirtualnej sieci WAN. |
> | Działanie | Microsoft. Network/virtualWans/Write | Utwórz lub zaktualizuj wirtualną sieć WAN |
> | Działanie | Microsoft. Network/vpnGateways/Delete | Usuwa element bramy vpngateway. |
> | Działanie | Microsoft. Network/vpngateways/listvpnconnectionshealth/akcja | Pobiera kondycję połączenia dla wszystkich lub podzbioru połączeń na bramy vpngateway |
> | Działanie | Microsoft. Network/vpnGateways/odczyt | Pobiera bramy vpngateway. |
> | Działanie | Microsoft. Network/vpngateways/Reset/akcja | Resetuje bramy vpngateway |
> | Działanie | Microsoft. Network/vpnGateways/vpnConnections/Delete | Usuwa element VpnConnection. |
> | Działanie | Microsoft. Network/vpnGateways/vpnConnections/odczyt | Pobiera VpnConnection. |
> | Działanie | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Pobiera połączenie sieci VPN |
> | Działanie | Microsoft. Network/vpnGateways/vpnConnections/Write | VpnConnection. |
> | Działanie | Microsoft. Network/vpnGateways/Write | Bramy vpngateway. |
> | Działanie | Microsoft. Network/vpnServerConfigurations/Delete | Usuń VpnServerConfiguration |
> | Działanie | Microsoft. Network/vpnServerConfigurations/odczyt | Pobierz VpnServerConfiguration |
> | Działanie | Microsoft. Network/vpnServerConfigurations/Write | Utwórz lub zaktualizuj VpnServerConfiguration |
> | Działanie | Microsoft. Network/vpnsites/Delete | Usuwa zasób witryny sieci VPN. |
> | Działanie | Microsoft. Network/vpnsites/odczyt | Pobiera zasób witryny sieci VPN. |
> | Działanie | Microsoft. Network/vpnSites/vpnSiteLinks/odczyt | Pobiera łącze do witryny sieci VPN |
> | Działanie | Microsoft. Network/vpnsites/Write | Tworzy lub aktualizuje zasób witryny sieci VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. NotificationHubs/CheckNamespaceAvailability/Action | Sprawdza, czy dana nazwa zasobu przestrzeni nazw jest dostępna w ramach usługi NotificationHub. |
> | Działanie | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Action | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Działanie | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Działanie | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Działanie | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Działanie | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/regenerateKeys/Action | Reguła autoryzacji przestrzeni nazw Wygeneruj ponownie podstawowy/SecondaryKey, określ klucz, który należy ponownie wygenerować |
> | Działanie | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Działanie | Microsoft. NotificationHubs/Namespaces/CheckNotificationHubAvailability/Action | Sprawdza, czy dana nazwa NotificationHub jest dostępna wewnątrz przestrzeni nazw. |
> | Działanie | Microsoft. NotificationHubs/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Action | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Delete | Usuń reguły autoryzacji centrum powiadomień |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do centrum powiadomień |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Read | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/regenerateKeys/Action | Reguła autoryzacji centrum powiadomień Regenerate Primary/SecondaryKey, określ klucz, który należy ponownie wygenerować |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Write | Utwórz reguły autoryzacji centrum powiadomień i zaktualizuj jego właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/debugSend/Action | Wyślij testowe Powiadomienie wypychane. |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Delete | Usuwanie zasobu centrum powiadomień |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/Action | Pobierz wszystkie poświadczenia PNS centrum powiadomień. Obejmuje to, WNS, usługi MPNS, APNS, GCM i Baidu |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Read | Pobierz listę opisów zasobów centrum powiadomień |
> | Działanie | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Write | Utwórz centrum powiadomień i zaktualizuj jego właściwości. Jego właściwości obejmują głównie poświadczenia PNS. Reguły autoryzacji i czas wygaśnięcia |
> | Działanie | Microsoft. NotificationHubs/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Działanie | Microsoft. NotificationHubs/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Działanie | Microsoft. NotificationHubs/operationResults/odczyt | Zwraca wyniki operacji dla dostawcy Notification Hubs |
> | Działanie | Microsoft. NotificationHubs/Operations/Read | Zwraca listę obsługiwanych operacji dla dostawcy Notification Hubs |
> | Działanie | Microsoft. NotificationHubs/Register/Action | Rejestruje subskrypcję dostawcy zasobów NotificationHubs i umożliwia tworzenie przestrzeni nazw i NotificationHubs |
> | Działanie | Microsoft. NotificationHubs/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy zasobów NotificationHubs i umożliwia tworzenie przestrzeni nazw i NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

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
> | Działanie | Microsoft. OffAzure/HyperVSites/runasaccounts/Read | Pobiera właściwości kont Uruchom jako funkcji Hyper-V |
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
> | Działanie | Microsoft. OffAzure/VMwareSites/Delete | Usuwa witrynę programu VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/healthsummary/Read | Pobiera podsumowanie kondycji zasobu VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/Jobs/odczyt | Pobiera właściwości zadań programu VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/Machines/Read | Pobiera właściwości maszyn VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/Machines/Start/Action | Uruchom maszyny VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/Machines/akcja | Powoduje zatrzymanie maszyn VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/operationsstatus/Read | Pobiera właściwości stanu operacji VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/odczyt | Pobiera właściwości witryny programu VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/Odśwież/akcja | Odświeża obiekty w witrynie programu VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/runasaccounts/Read | Pobiera właściwości kont Uruchom jako VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/użycie/odczyt | Pobiera użycie witryny programu VMware |
> | Działanie | Microsoft. OffAzure/VMwareSites/vCenter/odczyt | Pobiera właściwości programu VMware vCenter |
> | Działanie | Microsoft. OffAzure/VMwareSites/vcenters/Write | Tworzy lub aktualizuje program VMware vCenter |
> | Działanie | Microsoft. OffAzure/VMwareSites/Write | Tworzy lub aktualizuje witrynę programu VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. OperationalInsights/linkTargets/odczyt | Wyświetla listę istniejących kont, które nie są skojarzone z subskrypcją platformy Azure. Aby połączyć tę subskrypcję platformy Azure z obszarem roboczym, użyj identyfikatora klienta zwróconego przez tę operację we właściwości identyfikator klienta operacji Utwórz obszar roboczy. |
> | Działanie | Microsoft. operationalinsights/Operations/Read | Wyświetla wszystkie dostępne operacje interfejsu API REST OperationalInsights. |
> | Działanie | Microsoft. operationalinsights/Register/Action | Rergisters subskrypcję. |
> | Działanie | Microsoft. OperationalInsights/Register/Action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Działanie | Microsoft. operationalinsights/Unregister/Action | Wyrejestrowuje subskrypcję. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/analiza/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Analytics/Query/Schema/Read | Pobierz schemat wyszukiwania w wersji 2. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/interfejs API/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/API/Query/Schema/Read | Pobierz schemat wyszukiwania w wersji 2. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/configurationScopes/Delete | Usuń zakres konfiguracji |
> | Działanie | Microsoft. OperationalInsights/Workspaces/configurationScopes/Read | Pobierz zakres konfiguracji |
> | Działanie | Microsoft. OperationalInsights/Workspaces/configurationScopes/Write | Ustaw zakres konfiguracji |
> | Działanie | Microsoft. OperationalInsights/Workspaces/websources/Delete | Usuń źródła danych w obszarze roboczym. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/DataSources/odczyt | Pobierz źródła danych w obszarze roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/DataSources/Write | Utwórz/zaktualizuj źródła danych w obszarze roboczym. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/usuwanie | Usuwa obszar roboczy. Jeśli obszar roboczy został połączony z istniejącym obszarem roboczym podczas tworzenia, a obszar roboczy, z którym został on połączony, nie został usunięty. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/bramy/usuwanie | Usuwa bramę skonfigurowaną dla obszaru roboczego. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/generateregistrationcertificate/akcja | Generuje certyfikat rejestracji dla obszaru roboczego. Ten certyfikat służy do łączenia programu Microsoft System Center Operations Manager z obszarem roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Disable/Action | Wyłącza pakiet Intelligence Pack dla danego obszaru roboczego. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Enable/Action | Włącza pakiet Intelligence Pack dla danego obszaru roboczego. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Read | Wyświetla listę wszystkich pakietów analizy, które są widoczne dla danego obszaru roboczego, a także listę informacji o tym, czy pakiet jest włączony, czy wyłączony dla tego obszaru roboczego. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/linkedServices/Delete | Usuń połączone usługi w podanym obszarze roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/linkedServices/Read | Pobierz połączone usługi w podanym obszarze roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/linkedServices/Write | Utwórz/zaktualizuj połączone usługi w podanym obszarze roboczym. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/listKeys/akcja | Pobiera klucze list dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/listKeys/Read | Pobiera klucze list dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/managementGroups/Read | Pobiera nazwy i metadane dla System Center Operations Manager grup zarządzania połączonych z tym obszarem roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/metricDefinitions/Read | Pobierz definicje metryki w obszarze roboczym |
> | Działanie | Microsoft. OperationalInsights/Workspaces/notificationSettings/Delete | Usuń ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/notificationSettings/Read | Pobierz ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/notificationSettings/Write | Ustaw ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Działanie | Microsoft. operationalinsights/obszary robocze/operacje/odczyt | Pobiera stan operacji obszaru roboczego OperationalInsights. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/przeczyszczanie/akcja | Usuń określone dane z obszaru roboczego |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountLogon/Read | Odczytaj dane z tabeli AADDomainServicesAccountLogon |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountManagement/Read | Odczytaj dane z tabeli AADDomainServicesAccountManagement |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesDirectoryServiceAccess/Read | Odczytaj dane z tabeli AADDomainServicesDirectoryServiceAccess |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesLogonLogoff/Read | Odczytaj dane z tabeli AADDomainServicesLogonLogoff |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPolicyChange/Read | Odczytaj dane z tabeli AADDomainServicesPolicyChange |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPrivilegeUse/Read | Odczytaj dane z tabeli AADDomainServicesPrivilegeUse |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesSystemSecurity/Read | Odczytaj dane z tabeli AADDomainServicesSystemSecurity |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ADAssessmentRecommendation/Read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupAlerts/Read | Odczytaj dane z tabeli AddonAzureBackupAlerts |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupJobs/Read | Odczytaj dane z tabeli AddonAzureBackupJobs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupPolicy/Read | Odczytaj dane z tabeli AddonAzureBackupPolicy |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupProtectedInstance/Read | Odczytaj dane z tabeli AddonAzureBackupProtectedInstance |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupStorage/Read | Odczytaj dane z tabeli AddonAzureBackupStorage |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ADFActivityRun/Read | Odczytaj dane z tabeli ADFActivityRun |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ADFPipelineRun/Read | Odczytaj dane z tabeli ADFPipelineRun |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ADFTriggerRun/Read | Odczytaj dane z tabeli ADFTriggerRun |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ADReplicationResult/Read | Odczytaj dane z tabeli ADReplicationResult |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ADSecurityAssessmentRecommendation/Read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/alert/odczyt | Odczytaj dane z tabeli alertów |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AlertHistory/Read | Odczytaj dane z tabeli AlertHistory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterEvent/Read | Odczytaj dane z tabeli AmlComputeClusterEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterNodeEvent/Read | Odczytaj dane z tabeli AmlComputeClusterNodeEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeJobEvent/Read | Odczytaj dane z tabeli AmlComputeJobEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ApiManagementGatewayLogs/Read | Odczytaj dane z tabeli ApiManagementGatewayLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppCenterError/Read | Odczytaj dane z tabeli AppCenterError |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ApplicationInsights/Read | Odczytaj dane z tabeli ApplicationInsights |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppPlatformLogsforSpring/Read | Odczytaj dane z tabeli AppPlatformLogsforSpring |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AppServiceEnvironmentPlatformLogs/Read | Odczytaj dane z tabeli AppServiceEnvironmentPlatformLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AuditLogs/Read | Odczytaj dane z tabeli AuditLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AutoscaleEvaluationsLog/Read | Odczytaj dane z tabeli AutoscaleEvaluationsLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AutoscaleScaleActionsLog/Read | Odczytaj dane z tabeli AutoscaleScaleActionsLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AWSCloudTrail/Read | Odczytaj dane z tabeli AWSCloudTrail |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/Azure — odczyt | Odczytywanie danych z tabeli platformy Azure |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AzureAssessmentRecommendation/Read | Odczytaj dane z tabeli AzureAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/AzureMetrics/Read | Odczytaj dane z tabeli AzureMetrics |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterEvent/Read | Odczytaj dane z tabeli BaiClusterEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterNodeEvent/Read | Odczytaj dane z tabeli BaiClusterNodeEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BaiJobEvent/Read | Odczytaj dane z tabeli BaiJobEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BlockchainApplicationLog/Read | Odczytaj dane z tabeli BlockchainApplicationLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BlockchainProxyLog/Read | Odczytaj dane z tabeli BlockchainProxyLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/BoundPort/Read | Odczytaj dane z tabeli BoundPort |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/CommonSecurityLog/Read | Odczytaj dane z tabeli CommonSecurityLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/Computers/Read | Odczytywanie danych z tabeli komputerów |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/Zmianakonfiguracji/Read | Odczytaj dane z tabeli Zmianakonfiguracji |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ConfigurationData/Read | Odczytaj dane z tabeli ConfigurationData |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerImageInventory/Read | Odczytaj dane z tabeli ContainerImageInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerInventory/Read | Odczytaj dane z tabeli ContainerInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerLog/Read | Odczytaj dane z tabeli ContainerLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerNodeInventory/Read | Odczytaj dane z tabeli ContainerNodeInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryLoginEvents/Read | Odczytaj dane z tabeli ContainerRegistryLoginEvents |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryRepositoryEvents/Read | Odczytaj dane z tabeli ContainerRegistryRepositoryEvents |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ContainerServiceLog/Read | Odczytaj dane z tabeli ContainerServiceLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/CoreAzureBackup/Read | Odczytaj dane z tabeli CoreAzureBackup |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksAccounts/Read | Odczytaj dane z tabeli DatabricksAccounts |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksClusters/Read | Odczytaj dane z tabeli DatabricksClusters |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksDBFS/Read | Odczytaj dane z tabeli DatabricksDBFS |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksInstancePools/Read | Odczytaj dane z tabeli DatabricksInstancePools |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksJobs/Read | Odczytaj dane z tabeli DatabricksJobs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksNotebook/Read | Odczytaj dane z tabeli DatabricksNotebook |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSecrets/Read | Odczytaj dane z tabeli DatabricksSecrets |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSQLPermissions/Read | Odczytaj dane z tabeli DatabricksSQLPermissions |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSSH/Read | Odczytaj dane z tabeli DatabricksSSH |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksTables/Read | Odczytaj dane z tabeli DatabricksTables |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DatabricksWorkspace/Read | Odczytaj dane z tabeli DatabricksWorkspace |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceAppCrash/Read | Odczytaj dane z tabeli DeviceAppCrash |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceAppLaunch/Read | Odczytaj dane z tabeli DeviceAppLaunch |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceCalendar/Read | Odczytaj dane z tabeli DeviceCalendar |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceCleanup/Read | Odczytaj dane z tabeli DeviceCleanup |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceConnectSession/Read | Odczytaj dane z tabeli DeviceConnectSession |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceEtw/Read | Odczytaj dane z tabeli DeviceEtw |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceHardwareHealth/Read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/zameldowaniem/Read | Odczytaj dane z tabeli zameldowaniem |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceHeartbeat/Read | Odczytaj dane z tabeli DeviceHeartbeat |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceSkypeHeartbeat/Read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceSkypeSignIn/Read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DeviceSleepState/Read | Odczytaj dane z tabeli DeviceSleepState |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHAppFailure/Read | Odczytaj dane z tabeli DHAppFailure |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHAppReliability/Read | Odczytaj dane z tabeli DHAppReliability |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHDriverReliability/Read | Odczytaj dane z tabeli DHDriverReliability |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHLogonFailures/Read | Odczytaj dane z tabeli DHLogonFailures |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHLogonMetrics/Read | Odczytaj dane z tabeli DHLogonMetrics |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHOSCrashData/Read | Odczytaj dane z tabeli DHOSCrashData |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHOSReliability/Read | Odczytaj dane z tabeli DHOSReliability |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DHWipAppLearning/Read | Odczytaj dane z tabeli DHWipAppLearning |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DnsEvents/Read | Odczytaj dane z tabeli DnsEvents |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/DnsInventory/Read | Odczytaj dane z tabeli DnsInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ETWEvent/Read | Odczytaj dane z tabeli ETWEvent |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytanie/zdarzenie/odczyt | Odczytaj dane z tabeli zdarzeń |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ExchangeAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ExchangeOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/FailedIngestion/Read | Odczytaj dane z tabeli FailedIngestion |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/puls/odczyt | Odczytaj dane z tabeli pulsu |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/HuntingBookmark/Read | Odczytaj dane z tabeli HuntingBookmark |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/IISAssessmentRecommendation/Read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/InboundConnection/Read | Odczytaj dane z tabeli InboundConnection |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/InsightsMetrics/Read | Odczytaj dane z tabeli InsightsMetrics |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/IntuneAuditLogs/Read | Odczytaj dane z tabeli IntuneAuditLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/IntuneOperationalLogs/Read | Odczytaj dane z tabeli IntuneOperationalLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/KubeEvents/Read | Odczytaj dane z tabeli KubeEvents |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/KubeHealth/Read | Odczytaj dane z tabeli KubeHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/KubeNodeInventory/Read | Odczytaj dane z tabeli KubeNodeInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/KubePodInventory/Read | Odczytaj dane z tabeli KubePodInventory |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/KubeServices/Read | Odczytaj dane z tabeli KubeServices |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/LinuxAuditLog/Read | Odczytaj dane z tabeli LinuxAuditLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplication/Read | Odczytaj dane z tabeli MAApplication |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealth/Read | Odczytaj dane z tabeli MAApplicationHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthAlternativeVersions/Read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthIssues/Read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationInstance/Read | Odczytaj dane z tabeli MAApplicationInstance |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationInstanceReadiness/Read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationReadiness/Read | Odczytaj dane z tabeli MAApplicationReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADeploymentPlan/Read | Odczytaj dane z tabeli MADeploymentPlan |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADevice/Read | Odczytaj dane z tabeli MADevice |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNotEnrolled/Read | Odczytaj dane z tabeli MADeviceNotEnrolled |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNRT/Read | Odczytaj dane z tabeli MADeviceNRT |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADevicePnPHealth/Read | Odczytaj dane z tabeli MADevicePnPHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADevicePnPHealthAlternativeVersions/Read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADevicePnPHealthIssues/Read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADeviceReadiness/Read | Odczytaj dane z tabeli MADeviceReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADriverInstanceReadiness/Read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MADriverReadiness/Read | Odczytaj dane z tabeli MADriverReadiness |
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
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeDeploymentStatus/Read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeDeploymentStatusNRT/Read | Odczytaj dane z tabeli MAOfficeDeploymentStatusNRT |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroErrorNRT/Read | Odczytaj dane z tabeli MAOfficeMacroErrorNRT |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroGlobalHealth/Read | Odczytaj dane z tabeli MAOfficeMacroGlobalHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealth/Read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealthIssues/Read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroSummary/Read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeSuite/Read | Odczytaj dane z tabeli MAOfficeSuite |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeSuiteInstance/Read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAProposedPilotDevices/Read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsBuildInfo/Read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsCurrencyAssessment/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsDeploymentStatus/Read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsDeploymentStatusNRT/Read | Odczytaj dane z tabeli MAWindowsDeploymentStatusNRT |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsSysReqInstanceReadiness/Read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/McasShadowItReporting/Read | Odczytaj dane z tabeli McasShadowItReporting |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareShareLog/Read | Odczytaj dane z tabeli MicrosoftDataShareShareLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftHealthcareApisAuditLogs/Read | Odczytaj dane z tabeli MicrosoftHealthcareApisAuditLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftInsightsAzureActivityLog/Read | Odczytaj dane z tabeli MicrosoftInsightsAzureActivityLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebApplicationLog/Read | Odczytaj dane z tabeli MicrosoftWebApplicationLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebFunctionExecutionLogs/Read | Odczytaj dane z tabeli MicrosoftWebFunctionExecutionLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebStdOutStdErrLog/Read | Odczytaj dane z tabeli MicrosoftWebStdOutStdErrLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebW3CLog/Read | Odczytaj dane z tabeli MicrosoftWebW3CLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/NetworkMonitoring/Read | Odczytaj dane z tabeli NetworkMonitoring |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/Office/odczyt | Odczytaj dane z tabeli pakietu Office |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/operacja/odczyt | Odczytaj dane z tabeli operacji |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/OutboundConnection/Read | Odczytaj dane z tabeli OutboundConnection |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/perf/Read | Odczytaj dane z tabeli wydajności |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ProtectionStatus/Read | Odczytaj dane z tabeli ProtectionStatus |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytanie/odczyt | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ReservedAzureCommonFields/Read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ReservedCommonFields/Read | Odczytaj dane z tabeli ReservedCommonFields |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SCCMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SCOMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityAlert/Read | Odczytaj dane z tabeli SecurityAlert |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaseline/Read | Odczytaj dane z tabeli SecurityBaseline |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaselineSummary/Read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityDetection/Read | Odczytaj dane z tabeli SecurityDetection |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityEvent/Read | Odczytaj dane z tabeli SecurityEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityIoTRawEvent/Read | Odczytaj dane z tabeli SecurityIoTRawEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SecurityRecommendation/Read | Odczytaj dane z tabeli SecurityRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricOperationalEvent/Read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricReliableActorEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricReliableServiceEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SfBAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SfBOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SharePointOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SigninLogs/Read | Odczytaj dane z tabeli SigninLogs |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SPAssessmentRecommendation/Read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SQLAssessmentRecommendation/Read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SQLQueryPerformance/Read | Odczytaj dane z tabeli SQLQueryPerformance |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SqlThreatProtectionLoginAudits/Read | Odczytaj dane z tabeli SqlThreatProtectionLoginAudits |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SqlVulnerabilityAssessmentResult/Read | Odczytaj dane z tabeli SqlVulnerabilityAssessmentResult |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SucceededIngestion/Read | Odczytaj dane z tabeli SucceededIngestion |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/dziennik systemowy/odczyt | Odczytaj dane z tabeli dziennika systemowego |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/SysmonEvent/Read | Odczytaj dane z tabeli SysmonEvent |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/tabele. Custom/Read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/ThreatIntelligenceIndicator/Read | Odczytaj dane z tabeli ThreatIntelligenceIndicator |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAApp/Read | Odczytaj dane z tabeli UAApp |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAComputer/Read | Odczytaj dane z tabeli UAComputer |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAComputerRank/Read | Odczytaj dane z tabeli UAComputerRank |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UADriver/Read | Odczytaj dane z tabeli UADriver |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UADriverProblemCodes/Read | Odczytaj dane z tabeli UADriverProblemCodes |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAFeedback/Read | Odczytaj dane z tabeli UAFeedback |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAHardwareSecurity/Read | Odczytaj dane z tabeli UAHardwareSecurity |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAIESiteDiscovery/Read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAOfficeAddIn/Read | Odczytaj dane z tabeli UAOfficeAddIn |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAProposedActionPlan/Read | Odczytaj dane z tabeli UAProposedActionPlan |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UASysReqIssue/Read | Odczytaj dane z tabeli UASysReqIssue |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UAUpgradedComputer/Read | Odczytaj dane z tabeli UAUpgradedComputer |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/Update/Read | Odczytaj dane z tabeli aktualizacji |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UpdateRunProgress/Read | Odczytaj dane z tabeli UpdateRunProgress |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/UpdateSummary/Read | Odczytaj dane z tabeli UpdateSummary |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapytania/użycie/odczyt | Odczytywanie danych z tabeli użycia |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/VMBoundPort/Read | Odczytaj dane z tabeli VMBoundPort |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/VMConnection/Read | Odczytaj dane z tabeli VMConnection |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/W3CIISLog/Read | Odczytaj dane z tabeli W3CIISLog |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WaaSDeploymentStatus/Read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WaaSInsiderStatus/Read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WaaSUpdateStatus/Read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WDAVStatus/Read | Odczytaj dane z tabeli WDAVStatus |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WDAVThreat/Read | Odczytaj dane z tabeli WDAVThreat |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WindowsClientAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WindowsEvent/Read | Odczytaj dane z tabeli WindowsEvent |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WindowsFirewall/Read | Odczytaj dane z tabeli WindowsFirewall |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WindowsServerAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/typowe/Read | Odczytaj dane z tabeli typowe |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WorkloadMonitoringPerf/Read | Odczytaj dane z tabeli WorkloadMonitoringPerf |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WUDOAggregatedStatus/Read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | Działanie | Microsoft. OperationalInsights/Workspaces/Query/WUDOStatus/Read | Odczytaj dane z tabeli WUDOStatus |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/odczyt | Pobiera istniejący obszar roboczy |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/regeneratesharedkey/akcja | Ponownie generuje klucz współużytkowany określonego obszaru roboczego |
> | Działanie | Microsoft. operationalinsights/obszary robocze/reguły/odczyt | Pobierz wszystkie reguły alertów. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/savedSearches/Delete | Usuwa zapisane zapytanie wyszukiwania |
> | Działanie | Microsoft. OperationalInsights/Workspaces/savedSearches/Read | Pobiera zapisane zapytanie wyszukiwania |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/wyniki/odczyt | Pobierz wyniki zapisanych wyszukiwań. Przestarzałe |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Delete | Usuń zaplanowane akcje wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Read | Pobierz zaplanowane akcje wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Write | Utwórz lub zaktualizuj zaplanowane akcje wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Delete | Usuń zaplanowane wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Read | Pobierz zaplanowane wyszukiwania. |
> | Działanie | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Write | Utwórz lub zaktualizuj zaplanowane wyszukiwania. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/savedSearches/Write | Tworzy zapytanie zapisanego wyszukiwania |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/schemat/odczyt | Pobiera schemat wyszukiwania dla obszaru roboczego.  Schemat wyszukiwania obejmuje uwidocznione pola i ich typy. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/wyszukiwanie/akcja | Wykonuje zapytanie wyszukiwania |
> | Działanie | Microsoft. operationalinsights/obszary robocze/wyszukiwanie/odczyt | Pobierz wyniki wyszukiwania. Przestarzałe. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/sharedKeys/akcja | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/sharedKeys/Read | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Delete | Usuwa konfigurację magazynu. Spowoduje to zatrzymanie przez program Microsoft Operational Insights odczytywania danych z konta magazynu. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Read | Pobiera konfigurację magazynu. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Write | Tworzy nową konfigurację magazynu. Te konfiguracje są używane do ściągania danych z lokalizacji na istniejącym koncie magazynu. |
> | Działanie | Microsoft. OperationalInsights/Workspaces/upgradetranslationfailures/Read | Pobierz dziennik błędów tłumaczenia aktualizacji wyszukiwania dla obszaru roboczego |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/użycie/odczyt | Pobiera dane użycia dla obszaru roboczego, w tym ilość danych odczytanych przez obszar roboczy. |
> | Działanie | Microsoft. OperationalInsights/obszary robocze/zapis | Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącego obszaru roboczego. |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. OperationsManagement/managementAssociations/Delete | Usuń istniejące skojarzenie zarządzania |
> | Działanie | Microsoft. OperationsManagement/managementAssociations/odczyt | Pobierz istniejące skojarzenie zarządzania |
> | Działanie | Microsoft. OperationsManagement/managementAssociations/Write | Utwórz nowe skojarzenie zarządzania |
> | Działanie | Microsoft. OperationsManagement/managementConfigurations/Delete | Usuń istniejącą konfigurację zarządzania |
> | Działanie | Microsoft. OperationsManagement/managementConfigurations/odczyt | Pobieranie istniejącej konfiguracji zarządzania |
> | Działanie | Microsoft. OperationsManagement/managementConfigurations/Write | Utwórz nową konfigurację zarządzania |
> | Działanie | Microsoft. OperationsManagement/Register/Action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Działanie | Microsoft. OperationsManagement/Solutions/Delete | Usuń istniejące rozwiązanie OMS |
> | Działanie | Microsoft. OperationsManagement/Solutions/Read | Pobierz wyjście z rozwiązania OMS |
> | Działanie | Microsoft. OperationsManagement/Solutions/Write | Utwórz nowe rozwiązanie OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. PolicyInsights/asyncOperationResults/odczyt | Pobiera wynik operacji asynchronicznej. |
> | Akcja dataaction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Sprawdź stan zgodności danego składnika względem zasad dotyczących danych. |
> | Działanie | Microsoft. PolicyInsights/Operations/Read | Pobiera obsługiwane operacje w przestrzeni nazw Microsoft. PolicyInsights |
> | Akcja dataaction | Microsoft. PolicyInsights/policyEvents/logDataEvents/akcja | Rejestruj zdarzenia zasad składnika zasobów. |
> | Działanie | Microsoft. PolicyInsights/policyEvents/queryResults/akcja | Zapytanie informacji o zdarzeniach zasad. |
> | Działanie | Microsoft. PolicyInsights/policyEvents/queryResults/Read | Zapytanie informacji o zdarzeniach zasad. |
> | Działanie | Microsoft. PolicyInsights/policyStates/queryResults/akcja | Zapytanie o informacje dotyczące stanów zasad. |
> | Działanie | Microsoft. PolicyInsights/policyStates/queryResults/Read | Zapytanie o informacje dotyczące stanów zasad. |
> | Działanie | Microsoft. PolicyInsights/policyStates/podsumowanie/akcja | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych Stanów zasad. |
> | Działanie | Microsoft. PolicyInsights/policyStates/podsumowanie/odczyt | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych Stanów zasad. |
> | Działanie | Microsoft. PolicyInsights/policyStates/triggerEvaluation/akcja | Wyzwala nową ocenę zgodności dla wybranego zakresu. |
> | Działanie | Microsoft. PolicyInsights/policyTrackedResources/queryResults/Read | Zapytanie dotyczące zasobów wymaganych przez zasady DeployIfNotExistsymi. |
> | Działanie | Microsoft. PolicyInsights/Register/Action | Rejestruje dostawcę zasobów usługi Microsoft Policy Insights i włącza na nim akcje. |
> | Działanie | Microsoft. PolicyInsights/korygowania/anulowanie/akcja | Anuluj korygowanie zasad firmy Microsoft w toku. |
> | Działanie | Microsoft. PolicyInsights/korygowania/usuwanie | Usuń korygowanie zasad. |
> | Działanie | Microsoft. PolicyInsights/korygowania/listDeployments/odczyt | Wyświetla listę wdrożeń wymaganych przez korygowanie zasad. |
> | Działanie | Microsoft. PolicyInsights/korygowania/odczyt | Pobierz korygowanie zasad. |
> | Działanie | Microsoft. PolicyInsights/korygowania/zapis | Utwórz lub zaktualizuj korekty zasad firmy Microsoft. |
> | Działanie | Microsoft. PolicyInsights/Unregister/Action | Wyrejestrowuje dostawcę zasobów usługi Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft. Portal

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
> | Działanie | Microsoft. Portal/UserSettings/Usuń | Usuwa Cloud Shell ustawienia użytkownika. |
> | Działanie | Microsoft. Portal/UserSettings/odczyt | Odczytuje ustawienia użytkownika Cloud Shell. |
> | Działanie | Microsoft. Portal/UserSettings/zapis | Utwórz lub zaktualizuj ustawienie użytkownika Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. PowerBIDedicated/pojemność/usuwanie | Usuwa dedykowaną pojemność Power BI. |
> | Działanie | Microsoft. PowerBIDedicated/pojemność/odczyt | Pobiera informacje o określonej Power BI dedykowanej pojemności. |
> | Działanie | Microsoft. PowerBIDedicated/pojemność/wznowienie/akcja | Wznawia pojemność. |
> | Działanie | Microsoft. PowerBIDedicated/pojemność/jednostki SKU/odczyt | Pobierz informacje o dostępnych jednostkach SKU dla pojemności |
> | Działanie | Microsoft. PowerBIDedicated/pojemność/wstrzymywanie/akcja | Wstrzymuje pojemność. |
> | Działanie | Microsoft. PowerBIDedicated/pojemność/zapis | Tworzy lub aktualizuje określony Power BI dedykowanej pojemności. |
> | Działanie | Microsoft. PowerBIDedicated/Locations/checkNameAvailability/Action | Sprawdza, czy dana nazwa dedykowanej pojemności Power BI jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft. PowerBIDedicated/Locations/operationresults/Read | Pobiera informacje o wyniku określonej operacji. |
> | Działanie | Microsoft. PowerBIDedicated/Locations/operationstatuses/Read | Pobiera informacje o stanie określonej operacji. |
> | Działanie | Microsoft. PowerBIDedicated/Operations/Read | Pobiera informacje o operacjach |
> | Działanie | Microsoft. PowerBIDedicated/Register/Action | Rejestruje Power BI dedykowanego dostawcę zasobów. |
> | Działanie | Microsoft. PowerBIDedicated/jednostki SKU/odczyt | Pobiera informacje o jednostkach SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | Działanie | Microsoft. RecoveryServices/Locations/allocateStamp/Action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Działanie | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Działanie | Microsoft. RecoveryServices/Locations/backupProtectedItem/Write | Utwórz chroniony element kopii zapasowej |
> | Działanie | Microsoft. RecoveryServices/Locations/backupProtectedItems/Read | Zwraca listę wszystkich elementów chronionych. |
> | Działanie | Microsoft. RecoveryServices/Locations/backupStatus/Action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Działanie | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Weryfikuj funkcje |
> | Działanie | Microsoft. RecoveryServices/Locations/checkNameAvailability/Action | Sprawdź dostępność nazwy zasobu jest interfejsem API, aby sprawdzić, czy nazwa zasobu jest dostępna |
> | Działanie | Microsoft. RecoveryServices/Locations/operationStatus/Read | Pobiera stan operacji dla danej operacji |
> | Działanie | Microsoft. RecoveryServices/Operations/Read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Działanie | Microsoft. RecoveryServices/Register/Action | Rejestruje subskrypcję dla danego dostawcy zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupconfig/odczyt | Zwraca konfigurację magazynu Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupconfig/zapis | Aktualizuje konfigurację magazynu Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupEngines/odczyt | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/usuwanie | Usuń zamiar ochrony kopii zapasowej |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/odczyt | Pobierz cel ochrony kopii zapasowej |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/zapis | Tworzenie opcji ochrony kopii zapasowej |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/operationResults/odczyt | Zwraca stan operacji |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/operationsStatus/odczyt | Zwraca stan operacji |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectableContainers/odczyt | Pobierz wszystkie kontenery z ochroną |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/usuwanie | Usuwa zarejestrowany kontener |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/zapytania/akcja | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/Items/Read | Pobierz wszystkie elementy w kontenerze |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/operationResults/Read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/operationsStatus/Read | Pobiera stan operacji wykonanej na kontenerze ochrony. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/kopia zapasowa/akcja | Wykonuje kopię zapasową dla chronionego elementu. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Delete | Usuwa chroniony element |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/operationResults/odczyt | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/operationsStatus/odczyt | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Read | Zwraca szczegóły obiektu chronionego elementu |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Zapewnij natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/odczyt | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/przywracanie/akcja | Przywracanie punktów odzyskiwania dla elementów chronionych. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Odwołaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Write | Utwórz chroniony element kopii zapasowej |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/odczyt | Zwraca wszystkie zarejestrowane kontenery |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/zapis | Tworzy zarejestrowany kontener |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupFabrics/refreshContainers/akcja | Odświeża listę kontenerów |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupJobs/anulowanie/akcja | Anuluj zadanie |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupJobs/operationResults/odczyt | Zwraca wynik operacji zadania. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupJobs/operationsStatus/odczyt | Zwraca stan operacji zadania. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupJobs/odczyt | Zwraca wszystkie obiekty zadań |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupJobsExport/akcja | Eksportuj zadania |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupOperationResults/odczyt | Zwraca wynik operacji tworzenia kopii zapasowej dla magazynu Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupOperations/odczyt | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupPolicies/usuwanie | Usuwanie zasad ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupPolicies/operationResults/odczyt | Pobierz wyniki operacji zasad. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupPolicies/operacje/odczyt | Pobierz stan operacji zasad. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupPolicies/odczyt | Zwraca wszystkie zasady ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupPolicies/zapis | Tworzy zasady ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupProtectableItems/odczyt | Zwraca listę wszystkich elementów podlegających ochronie. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupProtectedItems/odczyt | Zwraca listę wszystkich elementów chronionych. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupProtectionContainers/odczyt | Zwraca wszystkie kontenery należące do subskrypcji |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupProtectionIntents/odczyt | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupSecurityPIN/akcja | Zwraca informacje zabezpieczającego numeru PIN dla magazynu Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupstorageconfig/odczyt | Zwraca konfigurację magazynu dla magazynu Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupstorageconfig/zapis | Aktualizuje konfigurację magazynu dla magazynu Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupUsageSummaries/odczyt | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/backupValidateOperation/akcja | Sprawdź poprawność operacji dla chronionego elementu |
> | Działanie | Microsoft. RecoveryServices/magazyny/certyfikaty/zapis | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Działanie | Microsoft. RecoveryServices/magazyny/usuwanie | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft. RecoveryServices/magazyny/extendedInformation/usuwanie | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft. RecoveryServices/magazyny/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft. RecoveryServices/magazyny/extendedInformation/zapis | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft. RecoveryServices/magazyny/monitoringAlerts/odczyt | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/monitoringAlerts/zapis | Rozwiązuje alert. |
> | Działanie | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/odczyt | Pobiera konfigurację powiadomień magazynu usługi Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/zapis | Konfiguruje powiadomienia e-mail dla magazynu usługi Recovery Services. |
> | Działanie | Microsoft. RecoveryServices/magazyny/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft. RecoveryServices/magazyny/registeredIdentities/usuwanie | Operacja Wyrejestruj kontener może służyć do wyrejestrowywania kontenera. |
> | Działanie | Microsoft. RecoveryServices/magazyny/registeredIdentities/operationResults/odczyt | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Działanie | Microsoft. RecoveryServices/magazyny/registeredIdentities/odczyt | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Działanie | Microsoft. RecoveryServices/magazyny/registeredIdentities/zapis | Operacji rejestrowania kontenera usługi można użyć do zarejestrowania kontenera z usługą odzyskiwania. |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationAlertSettings/odczyt | Odczytaj wszystkie ustawienia alertów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationAlertSettings/zapis | Utwórz lub zaktualizuj ustawienia alertów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationEvents/odczyt | Odczytaj wszystkie zdarzenia |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/checkConsistency/akcja | Sprawdza spójność sieci szkieletowej |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/usuwanie | Usuń wszystkie sieci szkieletowe |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/deployProcessServerImage/akcja | Wdróż obraz serwera przetwarzania |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/migratetoaad/akcja | Migrowanie sieci szkieletowej do usługi AAD |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/operationresults/odczyt | Śledź wyniki operacji asynchronicznej w sieciach szkieletowych zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/odczyt | Odczytaj wszystkie sieci szkieletowe |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/reassociateGateway/akcja | Skojarz ponownie bramę |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/usuwanie/akcja | Usuń sieć szkieletową |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/renewcertificate/akcja | Odnów certyfikat sieci szkieletowej |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationLogicalNetworks/odczyt | Odczytaj wszystkie sieci logiczne |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/odczyt | Odczytaj wszystkie sieci |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/replicationNetworkMappings/Delete | Usuń wszystkie mapowania sieci |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Odczytaj wszystkie mapowania sieci |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/replicationNetworkMappings/Write | Utwórz lub zaktualizuj dowolne mapowania sieci |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/Action | Odkryj element objęty ochroną |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/operationresults/Read | Śledź wyniki operacji asynchronicznej w kontenerach ochrony zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/odczyt | Odczytaj wszystkie kontenery ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/usuwanie/akcja | Usuń kontener ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Delete | Usuń wszystkie elementy migracji |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Migruj/akcja | Migrowanie elementu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/odczyt | Odczytaj wszystkie punkty odzyskiwania migracji |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na elementach migracji zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Read | Odczytaj wszystkie elementy migracji |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/akcja | Migracja testowa |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/akcja | Oczyszczanie migracji testów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Write | Utwórz lub zaktualizuj dowolne elementy migracji |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Odczytaj dowolne elementy podlegające ochronie |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/adddisks/Action | Dodawanie dysków |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/akcja | Zastosuj punkt odzyskiwania |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Delete | Usuń wszystkie chronione elementy |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/akcja | Zatwierdzanie trybu failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na elementach chronionych przez zasoby |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/akcja | Planowana praca w trybie failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Odczytaj wszystkie chronione elementy |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/odczyt | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/usuwanie/akcja | Usuń chroniony element |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/akcja | Usuń dyski |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/akcja | Napraw replikację |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Włącz ochronę/akcję | Włącz ponownie ochronę chronionego elementu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/akcja |  |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/akcja | Prześlij opinię |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/odczyt | Odczytaj wszystkie docelowe rozmiary obliczeń |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/akcja | Test pracy w trybie failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/akcja | Czyszczenie testu pracy w trybie failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/akcja | Tryb failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/akcja | Aktualizowanie usługi mobilności |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Write | Utwórz lub zaktualizuj wszystkie chronione elementy |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Delete | Usuń wszystkie mapowania kontenerów ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na mapowaniach kontenerów ochrony zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/usuwanie/akcja | Usuń mapowanie kontenera ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Write | Utwórz lub zaktualizuj wszelkie mapowania kontenerów ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Przełącz kontener ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/zapis | Tworzenie lub aktualizowanie wszelkich kontenerów ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/usuwanie | Usuń wszystkich dostawców Recovery Services |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/operationresults/Read | Śledź wyniki operacji asynchronicznej dla dostawców Recovery Services zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/odczyt | Odczytaj wszystkich dostawców Recovery Services |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Odśwież dostawcę |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/usuwanie/akcja | Usuń dostawcę Recovery Services |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/zapis | Utwórz lub zaktualizuj wszelkich Recovery Services dostawców |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/odczyt | Odczytaj wszystkie klasyfikacje magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Delete | Usuń wszystkie mapowania klasyfikacji magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na mapowaniach klasyfikacji magazynu zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Write | Utwórz lub zaktualizuj dowolne mapowania klasyfikacji magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/usuwanie | Usuń wszystkie vCenter |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/operationresults/Read | Śledź wyniki operacji asynchronicznej na serwerze vCenter zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/odczyt | Odczytaj dowolne vCenter |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/zapis | Utwórz lub zaktualizuj dowolne vCenter |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationFabrics/zapis | Utwórz lub zaktualizuj wszystkie sieci szkieletowe |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationJobs/anulowanie/akcja | Anuluj zadanie |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationJobs/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na zadaniach zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationJobs/odczyt | Odczytaj wszystkie zadania |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationJobs/ponowne uruchomienie/akcja | Uruchom ponownie zadanie |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationJobs/wznowienie/akcja | Wznów zadanie |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationMigrationItems/odczyt | Odczytaj wszystkie elementy migracji |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationNetworkMappings/odczyt | Odczytaj wszystkie mapowania sieci |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationNetworks/odczyt | Odczytaj wszystkie sieci |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationPolicies/usuwanie | Usuń wszystkie zasady |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationPolicies/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na zasadach zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationPolicies/odczyt | Odczytaj wszystkie zasady |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationPolicies/zapis | Tworzenie lub aktualizowanie zasad |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationProtectedItems/odczyt | Odczytaj wszystkie chronione elementy |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationProtectionContainerMappings/odczyt | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationProtectionContainers/odczyt | Odczytaj wszystkie kontenery ochrony |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/usuwanie | Usuń wszystkie plany odzyskiwania |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/failoverCommit/akcja | Plan odzyskiwania zatwierdzania w trybie failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na planach odzyskiwania zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/plannedFailover/akcja | Plan odzyskiwania planowanej pracy w trybie failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/odczyt | Zapoznaj się z planami odzyskiwania |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/ponowne Włączanie/działanie | Ponowne włączanie ochrony planu odzyskiwania |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/testFailover/akcja | Plan odzyskiwania testowego trybu failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/testFailoverCleanup/akcja | Plan odzyskiwania oczyszczania testów w trybie failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/unplannedFailover/akcja | Plan odzyskiwania w trybie failover |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/zapis | Tworzenie lub aktualizowanie planów odzyskiwania |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationRecoveryServicesProviders/odczyt | Odczytaj wszystkich dostawców Recovery Services |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationStorageClassificationMappings/odczyt | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationStorageClassifications/odczyt | Odczytaj wszystkie klasyfikacje magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationSupportedOperatingSystems/odczyt | Odczytaj wszystkie  |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationUsages/odczyt | Odczytaj wszystkie użycia replikacji magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na kondycji replikacji magazynu zasobów |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/odczyt | Odczytaj wszystkie kondycje replikacji magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/odświeżanie/akcja | Odśwież kondycję magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationVaultSettings/odczyt | Odczytaj wszystkie  |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationVaultSettings/zapis | Utwórz lub zaktualizuj dowolne  |
> | Działanie | Microsoft. RecoveryServices/magazyny/replicationvCenters/odczyt | Odczytaj dowolne vCenter |
> | Działanie | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Odczytaj wszystkie użycia magazynu |
> | Działanie | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Działanie | Microsoft. RecoveryServices/magazyny/vaultTokens/odczyt | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Działanie | Microsoft. RecoveryServices/magazyny/zapis | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |

## <a name="microsoftrelay"></a>Microsoft. Relay

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
> | Działanie | Microsoft. Relay/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Działanie | Microsoft. Relay/Namespaces/disasterRecoveryConfigs/reguł autoryzacji/ListKeys/Action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft. Relay/Namespaces/disasterRecoveryConfigs/reguł autoryzacji/Read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/breakPairing/Action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/disasterrecoveryconfigs/checkNameAvailability/Action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/Delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/tryb failover/akcja | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/Read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/Write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Action | Operacja zaktualizowania HybridConnection. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji HybridConnection |
> | Działanie | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do HybridConnection |
> | Działanie | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji HybridConnection |
> | Działanie | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/regeneratekeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Write | Utwórz reguły autoryzacji HybridConnection i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/HybridConnections/Delete | Operacja usuwania zasobu HybridConnection |
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
> | Działanie | Microsoft. Relay/przestrzenie nazw/removeAcsNamepsace/Action | Usuń przestrzeń nazw ACS |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Action | Operacja zaktualizowania WcfRelay. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji WcfRelay |
> | Działanie | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do WcfRelay |
> | Działanie | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji WcfRelay |
> | Działanie | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/regeneratekeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Write | Utwórz reguły autoryzacji WcfRelay i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/Delete | Operacja usuwania zasobu WcfRelay |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/Read | Pobierz listę opisów zasobów WcfRelay |
> | Działanie | Microsoft. Relay/przestrzenie nazw/WcfRelays/Write | Utwórz lub zaktualizuj właściwości WcfRelay. |
> | Działanie | Microsoft. Relay/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Działanie | Microsoft. Relay/Operations/Read | Pobierz operacje |
> | Działanie | Microsoft. Relay/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i umożliwia tworzenie zasobów przekazywania |
> | Działanie | Microsoft. Relay/Unregister/Action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i umożliwia tworzenie zasobów przekazywania |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ResourceHealth/AvailabilityStatuses/Current/Read | Pobiera stan dostępności dla określonego zasobu |
> | Działanie | Microsoft. ResourceHealth/AvailabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Działanie | Microsoft. ResourceHealth/Events/odczyt | Pobierz zdarzenia Service Health dla danej subskrypcji |
> | Działanie | Microsoft. Resourcehealth/healthevent/Action | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft. Resourcehealth/healthevent/aktywowany/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft. Resourcehealth/healthevent/InProgress/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft. Resourcehealth/healthevent/Pending/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft. Resourcehealth/healthevent/rozwiązane/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft. Resourcehealth/healthevent/zaktualizowane/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Działanie | Microsoft. ResourceHealth/impactedResources/odczyt | Pobierz zasoby, których dotyczy problem dla danej subskrypcji |
> | Działanie | Microsoft. ResourceHealth/Metadata/Read | Pobiera metadane |
> | Działanie | Microsoft. ResourceHealth/Operations/Read | Pobierz operacje dostępne dla programu Microsoft ResourceHealthe |
> | Działanie | Microsoft. ResourceHealth/Register/Action | Rejestruje subskrypcję programu Microsoft ResourceHealthe |
> | Działanie | Microsoft. ResourceHealth/Unregister/Action | Wyrejestrowuje subskrypcję programu Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. resources/calculateTemplateHash/Action | Oblicz wartość skrótu podanego szablonu. |
> | Działanie | Microsoft. resources/checkPolicyCompliance/Action | Sprawdź stan zgodności danego zasobu względem zasad zasobów. |
> | Działanie | Microsoft. resources/checkResourceName/Action | Sprawdź nazwę zasobu, aby sprawdzić poprawność. |
> | Działanie | Microsoft. resources/Deployments/Cancel/Action | Anuluje wdrożenie. |
> | Działanie | Microsoft. resources/Deployments/Delete | Usuwa wdrożenie. |
> | Działanie | Microsoft. resources/Deployments/exportTemplate/Action | Eksportuj szablon wdrożenia |
> | Działanie | Microsoft. resources/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Działanie | Microsoft. resources/Deployments/operationstatuses/Read | Pobiera lub wyświetla stan operacji wdrożenia. |
> | Działanie | Microsoft. resources/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | Działanie | Microsoft. resources/Deployments/Validate/Action | Sprawdza poprawność wdrożenia. |
> | Działanie | Microsoft. resources/Deployments/whatIf/Action | Przewiduje zmiany wdrożenia szablonu. |
> | Działanie | Microsoft. resources/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | Działanie | Microsoft. resources/Links/Delete | Usuwa łącze zasobu. |
> | Działanie | Microsoft. resources/Links/Read | Pobiera linki do zasobów lub wyświetla ich listę. |
> | Działanie | Microsoft. resources/Links/Write | Tworzy lub aktualizuje łącze zasobu. |
> | Działanie | Microsoft. resources/Marketplace/Purchase/Action | Zakup zasobu z portalu Marketplace. |
> | Działanie | Microsoft. resources/Providers/Read | Pobierz listę dostawców. |
> | Działanie | Microsoft. resources/Resources/Read | Pobierz listę zasobów na podstawie filtrów. |
> | Działanie | Microsoft. resources/subscriptions/Locations/Read | Pobiera listę obsługiwanych lokalizacji. |
> | Działanie | Microsoft. resources/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | Działanie | Microsoft. resources/subscriptions/Providers/Read | Pobiera dostawców zasobów lub wyświetla ich listę. |
> | Działanie | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Działanie | Microsoft. resources/subscriptions/resourceGroups/Delete | Usuwa grupę zasobów i wszystkie jej zasoby. |
> | Działanie | Microsoft. resources/subscriptions/ResourceGroups/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Działanie | Microsoft. resources/subscriptions/ResourceGroups/Deployments/operationstatuses/Read | Pobiera lub wyświetla stan operacji wdrożenia. |
> | Działanie | Microsoft. resources/subscriptions/ResourceGroups/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | Działanie | Microsoft. resources/subscriptions/ResourceGroups/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | Działanie | Microsoft. resources/subscriptions/resourceGroups/moveResources/Action | Przenosi zasoby z jednej grupy zasobów do innej. |
> | Działanie | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Działanie | Microsoft. resources/subscriptions/ResourceGroups/Resources/Read | Pobiera zasoby dla grupy zasobów. |
> | Działanie | Microsoft. resources/subscriptions/resourceGroups/validateMoveResources/Action | Weryfikowanie przenoszenia zasobów z jednej grupy zasobów do innej. |
> | Działanie | Microsoft. resources/subscriptions/resourceGroups/Write | Tworzy lub aktualizuje grupę zasobów. |
> | Działanie | Microsoft. resources/subscriptions/sources/Read | Pobiera zasoby subskrypcji. |
> | Działanie | Microsoft. resources/subscriptions/tagNames/Delete | Usuwa tag subskrypcji. |
> | Działanie | Microsoft. resources/subscriptions/tagNames/Read | Pobiera lub wyświetla Tagi subskrypcji. |
> | Działanie | Microsoft. resources/subscriptions/tagNames/tagValues/Delete | Usuwa wartość tagu subskrypcji. |
> | Działanie | Microsoft. resources/subscriptions/tagNames/tagValues/Read | Pobiera lub wyświetla wartości tagów subskrypcji. |
> | Działanie | Microsoft. resources/subscriptions/tagNames/tagValues/Write | Dodaje wartość tagu subskrypcji. |
> | Działanie | Microsoft. resources/subscriptions/tagNames/Write | Dodaje tag subskrypcji. |
> | Działanie | Microsoft. resources/Tags/Delete | Usuwa wszystkie znaczniki w zasobie. |
> | Działanie | Microsoft. resources/Tags/Read | Pobiera wszystkie znaczniki w zasobie. |
> | Działanie | Microsoft. resources/Tags/Write | Aktualizuje Tagi w zasobie, zastępując lub scalając istniejące Tagi nowym zestawem tagów lub usuwając istniejące Tagi. |
> | Działanie | Microsoft. resources/dzierżawcy/odczyt | Pobiera listę dzierżawców. |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Scheduler/jobcollections/Delete | Usuwa kolekcję zadań. |
> | Działanie | Microsoft. Scheduler/jobcollections/Disable/akcja | Wyłącza kolekcję zadań. |
> | Działanie | Microsoft. Scheduler/jobcollections/Włącz/akcja | Włącza zbieranie zadań. |
> | Działanie | Microsoft. Scheduler/jobcollections/Jobs/Delete | Usuwa zadanie. |
> | Działanie | Microsoft. Scheduler/jobcollections/Jobs/generateLogicAppDefinition/Action | Generuje definicję aplikacji logiki na podstawie zadania usługi Scheduler. |
> | Działanie | Microsoft. Scheduler/jobcollections/Jobs/jobhistories/Read | Pobiera historię zadania. |
> | Działanie | Microsoft. Scheduler/jobcollections/Jobs/Read | Pobiera zadanie. |
> | Działanie | Microsoft. Scheduler/jobcollections/Jobs/Run/Action | Uruchamia zadanie. |
> | Działanie | Microsoft. Scheduler/jobcollections/Jobs/Write | Tworzy lub aktualizuje zadanie. |
> | Działanie | Microsoft. Scheduler/jobcollections/odczyt | Pobierz kolekcję zadań |
> | Działanie | Microsoft. Scheduler/jobcollections/Write | Tworzy lub aktualizuje kolekcję zadań. |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Search/checkNameAvailability/Action | Sprawdza dostępność nazwy usługi. |
> | Działanie | Microsoft. Search/Operations/Read | Wyświetla wszystkie dostępne operacje dostawcy Microsoft. Search. |
> | Działanie | Microsoft. Search/Register/Action | Rejestruje subskrypcję dostawcy zasobów wyszukiwania i umożliwia tworzenie usług wyszukiwania. |
> | Działanie | Microsoft. Search/searchServices/createQueryKey/Action | Tworzy klucz zapytania. |
> | Działanie | Microsoft. Search/searchServices/Delete | Usuwa usługę wyszukiwania. |
> | Działanie | Microsoft. Search/searchServices/deleteQueryKey/Delete | Usuwa klucz zapytania. |
> | Działanie | Microsoft. Search/searchServices/listAdminKeys/Action | Odczytuje klucze administratora. |
> | Działanie | Microsoft. Search/searchServices/listQueryKeys/Action | Zwraca listę kluczy interfejsu API zapytania dla danej usługi Wyszukiwanie poznawcze platformy Azure. |
> | Działanie | Microsoft. Search/searchServices/listQueryKeys/Read | Zwraca listę kluczy interfejsu API zapytania dla danej usługi Wyszukiwanie poznawcze platformy Azure. |
> | Działanie | Microsoft. Search/searchServices/Read | Odczytuje usługę wyszukiwania. |
> | Działanie | Microsoft. Search/searchServices/regenerateAdminKey/Action | Generuje ponownie klucz administratora. |
> | Działanie | Microsoft. Search/searchServices/Start/akcja | Uruchamia usługę wyszukiwania. |
> | Działanie | Microsoft. Search/searchServices/Stop/Action | Powoduje zatrzymanie usługi wyszukiwania. |
> | Działanie | Microsoft. Search/searchServices/Write | Tworzy lub aktualizuje usługę wyszukiwania. |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Security/adaptiveNetworkHardenings/Wymuszaj/akcja | Wymusza stosowanie zasad ograniczania ruchu przez tworzenie reguł zabezpieczeń zgodnych z podaną grupą zabezpieczeń sieci |
> | Działanie | Microsoft. Security/adaptiveNetworkHardenings/Read | Pobiera adaptacyjne zalecenia dotyczące ograniczania przepustowości sieci chronionej przez platformę Azure |
> | Działanie | Microsoft. Security/advancedThreatProtectionSettings/Read | Pobiera ustawienia zaawansowanej ochrony przed zagrożeniami dla zasobu |
> | Działanie | Microsoft. Security/advancedThreatProtectionSettings/Write | Aktualizuje ustawienia zaawansowanej ochrony przed zagrożeniami dla zasobu |
> | Działanie | Microsoft. Security/Alerts/Read | Pobiera wszystkie dostępne alerty zabezpieczeń |
> | Działanie | Microsoft. Security/applicationWhitelistings/Read | Pobiera aplikację whitelistings |
> | Działanie | Microsoft. Security/applicationWhitelistings/Write | Tworzy nową aplikację listy dozwolonych lub aktualizuje istniejącą |
> | Działanie | Microsoft. Security/assessmentMetadata/Read | Uzyskaj dostępne metadane oceny zabezpieczeń w ramach subskrypcji |
> | Działanie | Microsoft. Security/assessmentMetadata/Write | Tworzenie lub aktualizowanie metadanych oceny zabezpieczeń |
> | Działanie | Microsoft. Security/Assessments/Read | Uzyskaj oceny zabezpieczeń w ramach subskrypcji |
> | Działanie | Microsoft. Security/Assessments/Write | Utwórz lub zaktualizuj oceny zabezpieczeń w ramach subskrypcji |
> | Działanie | Microsoft. Security/complianceResults/Read | Pobiera wyniki zgodności dla zasobu |
> | Działanie | Microsoft. Security/informationProtectionPolicies/Read | Pobiera zasady usługi Information Protection dla zasobu |
> | Działanie | Microsoft. Security/informationProtectionPolicies/Write | Aktualizuje zasady usługi Information Protection dla zasobu |
> | Działanie | Microsoft. Security/Locations/Alerts/Activate/Action | Aktywuj alert zabezpieczeń |
> | Działanie | Microsoft. zabezpieczenia/lokalizacje/alerty/Odrzuć/akcja | Odrzuć alert zabezpieczeń |
> | Działanie | Microsoft. Security/Locations/Alerts/Read | Pobiera wszystkie dostępne alerty zabezpieczeń |
> | Działanie | Microsoft. Security/Locations/jitNetworkAccessPolicies/Delete | Usuwa zasady dostępu just in Time do sieci |
> | Działanie | Microsoft. Security/Locations/jitNetworkAccessPolicies/initiate/Action | Inicjuje żądanie zasady dostępu just in Time do sieci |
> | Działanie | Microsoft. Security/Locations/jitNetworkAccessPolicies/Read | Pobiera zasady dostępu just in Time do sieci |
> | Działanie | Microsoft. Security/Locations/jitNetworkAccessPolicies/Write | Tworzy nowe zasady dostępu just in Time do sieci lub aktualizuje istniejące |
> | Działanie | Microsoft. Security/Locations/Read | Pobiera lokalizację danych zabezpieczeń |
> | Działanie | Microsoft. Security/Locations/Tasks/Activate/Action | Aktywuj zalecenie dotyczące zabezpieczeń |
> | Działanie | Microsoft. zabezpieczenia/lokalizacje/zadania/Odrzuć/akcja | Odrzuć zalecenie dotyczące zabezpieczeń |
> | Działanie | Microsoft. Security/Locations/Tasks/Read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Działanie | Microsoft. zabezpieczenia/lokalizacje/zadania/Rozwiązywanie/akcja | Rozwiązywanie zalecenia dotyczącego zabezpieczeń |
> | Działanie | Microsoft. Security/Locations/Tasks/Start/Action | Rozpocznij zalecenie dotyczące zabezpieczeń |
> | Działanie | Microsoft. Security/Policies/Read | Pobiera zasady zabezpieczeń |
> | Działanie | Microsoft. Security/Policies/Write | Aktualizuje zasady zabezpieczeń |
> | Działanie | Microsoft. Security/cenniki/usuwanie | Usuwa ustawienia cen dla zakresu |
> | Działanie | Microsoft. zabezpieczenia/cenniki/odczyt | Pobiera ustawienia cen dla zakresu |
> | Działanie | Microsoft. zabezpieczenia/cenniki/zapis | Aktualizuje ustawienia cen dla zakresu |
> | Działanie | Microsoft. Security/Register/Action | Rejestruje subskrypcję dla Azure Security Center |
> | Działanie | Microsoft. Security/securityContacts/Delete | Usuwa kontakt zabezpieczeń |
> | Działanie | Microsoft. Security/securityContacts/Read | Pobiera kontakt zabezpieczeń |
> | Działanie | Microsoft. Security/securityContacts/Write | Aktualizuje kontakt zabezpieczeń |
> | Działanie | Microsoft. Security/securitySolutions/Delete | Usuwa rozwiązanie zabezpieczeń |
> | Działanie | Microsoft. Security/securitySolutions/Read | Pobiera rozwiązania zabezpieczeń |
> | Działanie | Microsoft. Security/securitySolutions/Write | Tworzy nowe rozwiązanie zabezpieczeń lub aktualizuje istniejące |
> | Działanie | Microsoft. Security/securitySolutionsReferenceData/Read | Pobiera dane referencyjne rozwiązań zabezpieczeń |
> | Działanie | Microsoft. Security/securityStatuses/Read | Pobiera stan kondycji zabezpieczeń dla zasobów platformy Azure |
> | Działanie | Microsoft. Security/securityStatusesSummaries/Read | Pobiera podsumowania stanu zabezpieczeń dla zakresu |
> | Działanie | Microsoft. Security/Settings/Read | Pobiera ustawienia dla zakresu |
> | Działanie | Microsoft. Security/Settings/Write | Aktualizuje ustawienia zakresu |
> | Działanie | Microsoft. Security/Tasks/Read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Działanie | Microsoft. Security/Unregister/Action | Wyrejestrowuje subskrypcję z Azure Security Center |
> | Działanie | Microsoft. Security/webApplicationFirewalls/Delete | Usuwa zaporę aplikacji sieci Web |
> | Działanie | Microsoft. Security/webApplicationFirewalls/Read | Pobiera zapory aplikacji sieci Web |
> | Działanie | Microsoft. Security/webApplicationFirewalls/Write | Tworzy nową zaporę aplikacji sieci Web lub aktualizuje istniejącą |
> | Działanie | Microsoft. Security/workspaceSettings/Connect/Action | Zmień ustawienia ponownego połączenia ustawień obszaru roboczego |
> | Działanie | Microsoft. Security/workspaceSettings/Delete | Usuwa ustawienia obszaru roboczego |
> | Działanie | Microsoft. Security/workspaceSettings/Read | Pobiera ustawienia obszaru roboczego |
> | Działanie | Microsoft. Security/workspaceSettings/Write | Aktualizuje ustawienia obszaru roboczego |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. SecurityGraph/diagnosticsettings/Delete | Usuwanie ustawień diagnostycznych |
> | Działanie | Microsoft. SecurityGraph/diagnosticsettings/odczyt | Odczytywanie ustawień diagnostycznych |
> | Działanie | Microsoft. SecurityGraph/diagnosticsettings/Write | Zapisywanie ustawień diagnostycznych |
> | Działanie | Microsoft. SecurityGraph/diagnosticsettingscategories/odczyt | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. ServiceBus/checkNameAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft. ServiceBus/checkNamespaceAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Działanie | Microsoft. ServiceBus/Locations/deleteVirtualNetworkOrSubnets/Action | Usuwa reguły sieci wirtualnej w dostawcy zasobów ServiceBus dla określonej sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/reguł autoryzacji/ListKeys/Action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/reguł autoryzacji/Read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Działanie | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/breakPairing/Action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/disasterrecoveryconfigs/checkNameAvailability/Action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Działanie | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/Delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/tryb failover/akcja | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/Read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/Write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/eventGridFilters/Delete | Usuwa filtr Event Grid skojarzony z przestrzenią nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/eventGridFilters/Read | Pobiera Filtr Event Grid skojarzony z przestrzenią nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/eventGridFilters/Write | Tworzy lub aktualizuje filtr Event Grid skojarzony z przestrzenią nazw. |
> | Działanie | Microsoft. ServiceBus/Namespaces/eventhubs/Read | Pobierz listę opisów zasobów EventHub |
> | Działanie | Microsoft. ServiceBus/Namespaces/ipFilterRules/Delete | Usuń zasób filtru IP |
> | Działanie | Microsoft. ServiceBus/Namespaces/ipFilterRules/Read | Pobierz zasób filtru IP |
> | Działanie | Microsoft. ServiceBus/Namespaces/ipFilterRules/Write | Utwórz zasób filtru IP |
> | Akcja dataaction | Microsoft. ServiceBus/przestrzenie nazw/komunikaty/odbieranie/akcja | Odbieranie komunikatów |
> | Akcja dataaction | Microsoft. ServiceBus/przestrzenie nazw/komunikaty/wysyłanie/akcja | Wysyłanie komunikatów |
> | Działanie | Microsoft. ServiceBus/Namespaces/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. ServiceBus/Namespaces/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/migracja/akcja | Operacja migracji przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Delete | Usuwa konfigurację migracji. |
> | Działanie | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Read | Pobiera konfigurację migracji, która wskazuje stan migracji i oczekujących operacji replikacji |
> | Działanie | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Przywróć/akcja | Przywraca migrację przestrzeni nazw Standard do warstwy Premium |
> | Działanie | Microsoft. ServiceBus/Namespaces/migrationConfigurations/upgrade/Action | Przypisuje serwer DNS skojarzony z przestrzenią nazw w warstwie Premium, która kończy migrację i kończy synchronizowanie zasobów ze standardowego do Premium przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Write | Tworzy lub aktualizuje konfigurację migracji. Spowoduje to rozpoczęcie synchronizowania zasobów ze standardowego do przestrzeni nazw Premium |
> | Działanie | Microsoft. ServiceBus/Namespaces/networkruleset/Delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/Namespaces/networkruleset/Read | Pobiera zasób NetworkRuleSet |
> | Działanie | Microsoft. ServiceBus/Namespaces/networkruleset/Write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/Namespaces/networkrulesets/Delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/Namespaces/networkrulesets/Read | Pobiera zasób NetworkRuleSet |
> | Działanie | Microsoft. ServiceBus/Namespaces/networkrulesets/Write | Utwórz zasób reguły sieci wirtualnej |
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
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/kolejki/usuwanie | Operacja usuwania zasobu kolejki |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/kolejki/odczyt | Pobierz listę opisów zasobów kolejki |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/kolejki/zapis | Utwórz lub zaktualizuj właściwości kolejki. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Działanie | Microsoft. ServiceBus/Namespaces/removeAcsNamepsace/Action | Usuń przestrzeń nazw ACS |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/akcja | Operacja zaktualizowania tematu. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/usuwanie | Operacja usuwania tematu reguły autoryzacji |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/ListKeys/akcja | Pobierz parametry połączenia z tematem |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/odczyt |  Pobierz listę reguł autoryzacji tematów |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/regenerateKeys/akcja | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/zapis | Tworzenie reguł autoryzacji tematów i aktualizowanie ich właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/Usuwanie | Operacja usuwania zasobu tematu |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/odczyt | Pobierz listę opisów zasobów tematu |
> | Działanie | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/usuwanie | Operacja usuwania zasobu TopicSubscription |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/subskrypcje/odczyt | Pobierz listę opisów zasobów TopicSubscription |
> | Działanie | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/usuwanie | Operacja usuwania zasobu reguły |
> | Działanie | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/odczyt | Pobierz listę opisów zasobów reguł |
> | Działanie | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/zapis | Utwórz lub zaktualizuj właściwości reguły. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/subskrypcje/zapis | Utwórz lub zaktualizuj właściwości TopicSubscription. |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/tematy/zapis | Utwórz lub zaktualizuj właściwości tematu. |
> | Działanie | Microsoft. ServiceBus/Namespaces/virtualNetworkRules/Delete | Usuń zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/Namespaces/virtualNetworkRules/Read | Pobiera zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/Namespaces/virtualNetworkRules/Write | Utwórz zasób reguły sieci wirtualnej |
> | Działanie | Microsoft. ServiceBus/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Działanie | Microsoft. ServiceBus/Operations/Read | Pobierz operacje |
> | Działanie | Microsoft. ServiceBus/Register/Action | Rejestruje subskrypcję dostawcy zasobów ServiceBus i umożliwia tworzenie zasobów ServiceBus |
> | Działanie | Microsoft. ServiceBus/SKU/odczyt | Pobierz listę opisów zasobów jednostki SKU |
> | Działanie | Microsoft. ServiceBus/SKU/regiony/odczyt | Pobierz listę opisów zasobów SkuRegions |
> | Działanie | Microsoft. ServiceBus/Unregister/Action | Rejestruje subskrypcję dostawcy zasobów ServiceBus i umożliwia tworzenie zasobów ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft. servicefabric

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. servicefabric/klastry/aplikacje/usuwanie | Usuń dowolną aplikację |
> | Działanie | Microsoft. servicefabric/klastry/aplikacje/odczyt | Odczytaj dowolną aplikację |
> | Działanie | Microsoft. servicefabric/klastry/aplikacje/usługi/usuwanie | Usuwanie dowolnej usługi |
> | Działanie | Microsoft. servicefabric/klastry/aplikacje/usługi/partycje/odczyt | Odczytaj dowolną partycję |
> | Działanie | Microsoft. servicefabric/klastry/aplikacje/usługi/partycje/repliki/odczyt | Odczytaj dowolną replikę |
> | Działanie | Microsoft. servicefabric/klastry/aplikacje/usługi/odczyt | Odczytaj dowolną usługę |
> | Działanie | Microsoft. servicefabric/klastry/aplikacje/usługi/Stany/odczyt | Odczytaj wszystkie stany usług |
> | Działanie | Microsoft. servicefabric/klastry/aplikacje/usługi/zapis | Utwórz lub zaktualizuj dowolną usługę |
> | Działanie | Microsoft. servicefabric/klastry/aplikacje/zapis | Utwórz lub zaktualizuj dowolną aplikację |
> | Działanie | Microsoft. servicefabric/klastry/applicationTypes/Delete | Usuń dowolny typ aplikacji |
> | Działanie | Microsoft. servicefabric/klastry/applicationTypes/Read | Odczytaj dowolny typ aplikacji |
> | Działanie | Microsoft. servicefabric/klastry/applicationTypes/wersje/Usuń | Usuń wszystkie wersje typu aplikacji |
> | Działanie | Microsoft. servicefabric/klastry/applicationTypes/wersje/odczyt | Odczytaj dowolną wersję typu aplikacji |
> | Działanie | Microsoft. servicefabric/klastry/applicationTypes/wersje/zapis | Utwórz lub zaktualizuj dowolną wersję typu aplikacji |
> | Działanie | Microsoft. servicefabric/klastry/applicationTypes/Write | Utwórz lub zaktualizuj dowolny typ aplikacji |
> | Działanie | Microsoft. servicefabric/klastry/usuwanie | Usuń dowolny klaster |
> | Działanie | Microsoft. servicefabric/klastry/węzły/odczyt | Odczytaj dowolny węzeł |
> | Działanie | Microsoft. servicefabric/klastry/odczyt | Odczytaj dowolny klaster |
> | Działanie | Microsoft. servicefabric/klastry/Stany/odczyt | Odczytaj wszystkie Stany klastra |
> | Działanie | Microsoft. servicefabric/klastry/zapis | Utwórz lub zaktualizuj dowolny klaster |
> | Działanie | Microsoft. servicefabric/Locations/clusterVersions/Read | Odczytaj dowolną wersję klastra |
> | Działanie | Microsoft. servicefabric/Locations/Environments/clusterVersions/Read | Odczytaj dowolną wersję klastra dla określonego środowiska |
> | Działanie | Microsoft. servicefabric/Locations/operationresults/Read | Odczytaj wszystkie wyniki operacji |
> | Działanie | Microsoft. servicefabric/Locations/Operations/Read | Odczytaj wszystkie operacje według lokalizacji |
> | Działanie | Microsoft. servicefabric/Operations/Read | Odczytaj wszystkie dostępne operacje |
> | Działanie | Microsoft. servicefabric/Register/Action | Zarejestruj dowolną akcję |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. SignalRService/Locations/checknameavailability/Action | Sprawdza, czy nazwa jest dostępna do użycia z nową usługą sygnalizującego |
> | Działanie | Microsoft. SignalRService/Locations/operationresults/sygnalizujący/odczytany | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Działanie | Microsoft. SignalRService/Locations/operationStatuses/operationId/Read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Działanie | Microsoft. SignalRService/lokalizacje/użycia/odczyt | Pobierz użycie przydziału dla usługi Azure Signal Service |
> | Działanie | Microsoft. SignalRService/operationresults/odczyt | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Działanie | Microsoft. SignalRService/Operations/Read | Wyświetl listę operacji usługi Azure Signal Service. |
> | Działanie | Microsoft. SignalRService/operationstatus/odczyt | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Działanie | Microsoft. SignalRService/Register/Action | Rejestruje dostawcę zasobów "Microsoft. SignalRService" z subskrypcją |
> | Działanie | Microsoft. SignalRService/sygnalizujący/Usuń | Usuwanie całej usługi sygnalizującej |
> | Działanie | Microsoft. SignalRService/Signaler/eventGridFilters/Delete | Usuń filtr usługi Event Grid ze sygnalizującego. |
> | Działanie | Microsoft. SignalRService/Signaler/eventGridFilters/odczyt | Pobierz właściwości określonego filtru usługi Event Grid lub Wyświetl listę wszystkich filtrów usługi Event Grid dla określonego sygnalizującego. |
> | Działanie | Microsoft. SignalRService/Signaler/eventGridFilters/Write | Utwórz lub zaktualizuj filtr usługi Event Grid dla sygnalizującego o określonych parametrach. |
> | Działanie | Microsoft. SignalRService/Signaler/ListKeys/Action | Wyświetlanie wartości kluczy dostępu sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Działanie | Microsoft. SignalRService/sygnalizujący/odczytający | Wyświetlanie ustawień i konfiguracji sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Działanie | Microsoft. SignalRService/Signaler/regeneratekey/Action | Zmiana wartości kluczy dostępu sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Działanie | Microsoft. SignalRService/sygnalizujący/Uruchom ponownie/akcję | Aby ponownie uruchomić usługę sygnałów platformy Azure w portalu zarządzania lub za pomocą interfejsu API. Zostanie określony przestój. |
> | Działanie | Microsoft. SignalRService/sygnalizujący/Write | Modyfikowanie ustawień i konfiguracji sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Działanie | Microsoft. SignalRService/Unregister/Action | Wyrejestrowuje dostawcę zasobów "Microsoft. SignalRService" z subskrypcją |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Solutions/applicationDefinitions/applicationArtifacts/odczyt | Wyświetla listę artefaktów aplikacji definicji aplikacji. |
> | Działanie | Microsoft. Solutions/applicationDefinitions/Delete | Usuwa definicję aplikacji. |
> | Działanie | Microsoft. Solutions/applicationDefinitions/Read | Pobiera listę definicji aplikacji. |
> | Działanie | Microsoft. Solutions/applicationDefinitions/Write | Dodaj lub zmodyfikuj definicję aplikacji. |
> | Działanie | Microsoft. Solutions/Applications/applicationArtifacts/Read | Wyświetla listę artefaktów aplikacji. |
> | Działanie | Microsoft. Solutions/Applications/Delete | Usuwa aplikację. |
> | Działanie | Microsoft. Solutions/Applications/Read | Pobiera listę aplikacji. |
> | Działanie | Microsoft. Solutions/Applications/refreshPermissions/Action | Odświeża uprawnienia aplikacji. |
> | Działanie | Microsoft. Solutions/Applications/updateAccess/Action | Aktualizuje dostęp do aplikacji. |
> | Działanie | Microsoft. Solutions/Applications/Write | Tworzy aplikację. |
> | Działanie | Microsoft. Solutions/jitRequests/Delete | Usuń JitRequest |
> | Działanie | Microsoft. Solutions/jitRequests/Read | Pobiera listę JitRequests |
> | Działanie | Microsoft. Solutions/jitRequests/Write | Tworzy element JitRequest |
> | Działanie | Microsoft. Solutions/Locations/operationStatuses/Read | Odczytuje stan operacji dla zasobu. |
> | Działanie | Microsoft. Solutions/Register/Action | Zarejestruj się w rozwiązaniach. |
> | Działanie | Microsoft. Solutions/Unregister/Action | Wyrejestrowuje z rozwiązań. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. SQL/checkNameAvailability/akcja | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Działanie | Microsoft. SQL/instancePools/Delete | Usuwa pulę wystąpień |
> | Działanie | Microsoft. SQL/instancePools/odczyt | Pobiera pulę wystąpień |
> | Działanie | Microsoft. SQL/instancePools/Usages/Read | Pobiera informacje o użyciu puli wystąpień |
> | Działanie | Microsoft. SQL/instancePools/Write | Tworzy lub aktualizuje pulę wystąpień |
> | Działanie | Microsoft. SQL/Locations/auditingSettingsAzureAsyncOperation/Read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Działanie | Microsoft. SQL/Locations/auditingSettingsOperationResults/Read | Pobierz wynik operacji ustawiania zasad inspekcji obiektu BLOB serwera |
> | Działanie | Microsoft. SQL/lokalizacje/możliwości/odczyt | Pobiera możliwości dla tej subskrypcji w danej lokalizacji |
> | Działanie | Microsoft. SQL/Locations/databaseAzureAsyncOperation/Read | Pobiera stan operacji bazy danych. |
> | Działanie | Microsoft. SQL/Locations/databaseOperationResults/Read | Pobiera stan operacji bazy danych. |
> | Działanie | Microsoft. SQL/Locations/deletedServerAsyncOperation/Read | Pobiera operacje w toku na usuniętym serwerze |
> | Działanie | Microsoft. SQL/Locations/deletedServerOperationResults/Read | Pobiera operacje w toku na usuniętym serwerze |
> | Działanie | Microsoft. SQL/Locations/deletedServers/Read | Zwróć listę usuniętych serwerów lub pobiera właściwości dla określonego usuniętego serwera. |
> | Działanie | Microsoft. SQL/Locations/deletedServers/Recover/Action | Odzyskiwanie usuniętego serwera |
> | Działanie | Microsoft. SQL/Locations/elasticPoolAzureAsyncOperation/Read | Pobiera asynchroniczną operację platformy Azure dla operacji asynchronicznej puli elastycznej |
> | Działanie | Microsoft. SQL/Locations/elasticPoolOperationResults/Read | Pobiera wynik operacji puli elastycznej. |
> | Działanie | Microsoft. SQL/Locations/encryptionProtectorAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania danych transparentu |
> | Działanie | Microsoft. SQL/Locations/encryptionProtectorOperationResults/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania danych transparentu |
> | Działanie | Microsoft. SQL/Locations/extendedAuditingSettingsAzureAsyncOperation/Read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Działanie | Microsoft. SQL/Locations/extendedAuditingSettingsOperationResults/Read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Działanie | Microsoft. SQL/Locations/firewallRulesAzureAsyncOperation/Read | Pobiera stan operacji reguły zapory. |
> | Działanie | Microsoft. SQL/Locations/firewallRulesOperationResults/Read | Pobiera stan operacji reguły zapory. |
> | Działanie | Microsoft. SQL/Locations/instanceFailoverGroups/Delete | Usuwa istniejącą grupę trybu failover wystąpienia. |
> | Działanie | Microsoft. SQL/Locations/instanceFailoverGroups/tryb failover/akcja | Wykonuje planowaną pracę w trybie failover w istniejącej grupie trybu failover wystąpienia. |
> | Działanie | Microsoft. SQL/Locations/instanceFailoverGroups/forceFailoverAllowDataLoss/Action | Wykonuje wymuszone przejście w tryb failover w istniejącej grupie trybu failover wystąpienia. |
> | Działanie | Microsoft. SQL/Locations/instanceFailoverGroups/Read | Zwraca listę grup trybu failover wystąpienia lub pobiera właściwości dla określonego wystąpienia grupy trybu failover. |
> | Działanie | Microsoft. SQL/Locations/instanceFailoverGroups/Write | Tworzy grupę trybu failover wystąpienia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej grupy trybu failover. |
> | Działanie | Microsoft. SQL/Locations/instancePoolAzureAsyncOperation/Read | Pobiera stan operacji puli wystąpień |
> | Działanie | Microsoft. SQL/Locations/instancePoolOperationResults/Read | Pobiera wynik operacji puli wystąpień |
> | Działanie | Microsoft. SQL/Locations/interfaceEndpointProfileAzureAsyncOperation/Read | Zwraca szczegółowe informacje o określonym interfejsie operacji asynchronicznej platformy Azure |
> | Działanie | Microsoft. SQL/Locations/interfaceEndpointProfileOperationResults/Read | Zwraca szczegóły podanej operacji profilu punktu końcowego interfejsu |
> | Działanie | Microsoft. SQL/Locations/jobAgentAzureAsyncOperation/Read | Pobiera stan operacji agenta zadań. |
> | Działanie | Microsoft. SQL/Locations/jobAgentOperationResults/Read | Pobiera wynik operacji agenta zadań. |
> | Działanie | Microsoft. SQL/Locations/longTermRetentionBackups/Read | Wyświetla długoterminowe kopie zapasowe przechowywania dla każdej bazy danych na każdym serwerze w lokalizacji |
> | Działanie | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read | Wyświetla kopie zapasowe przechowywania długoterminowego dla każdej bazy danych na serwerze |
> | Działanie | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete | Usuwa długoterminową kopię zapasową przechowywania |
> | Działanie | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read | Wyświetla kopie zapasowe przechowywania długoterminowego dla bazy danych |
> | Działanie | Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/Action | Kończy operację przywracania zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania wystąpienia zarządzanego szyfrowania danych |
> | Działanie | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorOperationResults/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania wystąpienia zarządzanego szyfrowania danych |
> | Działanie | Microsoft. SQL/Locations/managedInstanceKeyAzureAsyncOperation/Read | Pobiera operacje w toku na przezroczystych kluczach wystąpienia zarządzanego szyfrowania danych |
> | Działanie | Microsoft. SQL/Locations/managedInstanceKeyOperationResults/Read | Pobiera operacje w toku na przezroczystych kluczach wystąpienia zarządzanego szyfrowania danych |
> | Działanie | Microsoft. SQL/Locations/managedTransparentDataEncryptionAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/Locations/managedTransparentDataEncryptionOperationResults/Read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/Locations/privateEndpointConnectionOperationResults/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/lokalizacje/odczyt | Pobiera dostępne lokalizacje dla danej subskrypcji |
> | Działanie | Microsoft. SQL/Locations/serverKeyAzureAsyncOperation/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft. SQL/Locations/serverKeyOperationResults/Read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Działanie | Microsoft. SQL/Locations/syncAgentOperationResults/Read | Pobierz wynik operacji zasobu agenta synchronizacji |
> | Działanie | Microsoft. SQL/Locations/syncDatabaseIds/Read | Pobieranie identyfikatorów bazy danych synchronizacji dla określonego regionu i subskrypcji |
> | Działanie | Microsoft. SQL/Locations/syncGroupOperationResults/Read | Pobierz wynik operacji zasobu grupy synchronizacji |
> | Działanie | Microsoft. SQL/Locations/syncMemberOperationResults/Read | Pobierz wynik operacji synchronizacji zasobu elementu członkowskiego |
> | Działanie | Microsoft. SQL/lokalizacje/użycia/odczyt | Pobiera kolekcję metryk użycia dla tej subskrypcji w lokalizacji |
> | Działanie | Microsoft. SQL/Locations/virtualNetworkRulesAzureAsyncOperation/Read | Zwraca szczegóły określonych reguł sieci wirtualnej Azure asynchronicznej operacji  |
> | Działanie | Microsoft. SQL/Locations/virtualNetworkRulesOperationResults/Read | Zwraca szczegóły określonej operacji dotyczącej reguł sieci wirtualnej  |
> | Działanie | Microsoft. SQL/managedInstances/Administratorzy/usuwanie | Usuwa istniejącego administratora wystąpienia zarządzanego. |
> | Działanie | Microsoft. SQL/managedInstances/Administratorzy/odczyt | Pobiera listę administratorów wystąpienia zarządzanego. |
> | Działanie | Microsoft. SQL/managedInstances/Administratorzy/zapis | Tworzy lub aktualizuje administratora wystąpienia zarządzanego o określonych parametrach. |
> | Działanie | Microsoft. SQL/managedInstances/Databases/backupShortTermRetentionPolicies/Read | Pobiera krótkoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/backupShortTermRetentionPolicies/Write | Aktualizuje krótkoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/kolumny/odczyt | Zwracanie listy kolumn dla zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/Write | Etykiety czułości aktualizacji partii |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/usuwanie | Usuwa istniejącą zarządzaną bazę danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla baz danych wystąpienia zarządzanego |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/Odczyt | Pobiera istniejącą zarządzaną bazę danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/Write | Etykiety czułości zalecanych aktualizacji wsadowych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/restoreDetails/Read | Zwraca szczegóły przywracania zarządzanej bazy danych, gdy przywracanie jest w toku. |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/odczyt | Pobierz schemat zarządzanej bazy danych. |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/odczyt | Pobieranie kolumny zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/usuwanie | Usuń etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Disable/Action | Wyłącz zalecenia dotyczące czułości w danej kolumnie |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Włącz/akcja | Włącz zalecenia dotyczące czułości w danej kolumnie |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/odczyt | Pobierz etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/zapis | Utwórz lub zaktualizuj etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/odczyt | Pobieranie tabeli zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/securityAlertPolicies/Read | Pobieranie listy zasad wykrywania zagrożeń zarządzanych baz danych skonfigurowanych dla danego serwera |
> | Działanie | Microsoft. SQL/managedInstances/Databases/securityAlertPolicies/Write | Zmień zasady wykrywania zagrożeń bazy danych dla danej zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/securityEvents/Read | Pobiera zdarzenia zabezpieczeń zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/Read | Pobierz szczegóły Transparent Data Encryption bazy danych w danej zarządzanej bazie danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/Write | Zmień Transparent Data Encryption bazy danych dla danej zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Delete | Usuń ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Read | Pobieranie zasad oceny luk w zabezpieczeniach na givendatabase |
> | Działanie | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Rules/baselines/Delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Rules/baselines/Read | Pobierz linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Rules/baselines/Write | Zmień linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/skany/eksport/akcja | Przekonwertuj istniejący wynik skanowania na format możliwy do odczytania przez człowieka. Jeśli już nie istnieje, nic się nie dzieje |
> | Działanie | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/skany/initiateScan/akcja | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Działanie | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/skany/odczyt | Zwróć listę rekordów skanowania oceny luk w zabezpieczeniach bazy danych lub Pobierz rekord skanowania pod kątem określonego identyfikatora skanowania. |
> | Działanie | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/Write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/bazy danych/zapis | Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych. |
> | Działanie | Microsoft. SQL/managedInstances/Delete | Usuwa istniejące wystąpienie zarządzane. |
> | Działanie | Microsoft. SQL/managedInstances/encryptionProtector/odczyt | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/akcja | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft. SQL/managedInstances/encryptionProtector/Write | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft. SQL/managedInstances/klucze/usuwanie | Usuwa istniejący klucz wystąpienia zarządzanego Azure SQL. |
> | Działanie | Microsoft. SQL/managedInstances/klucze/odczyt | Zwróć listę kluczy wystąpienia zarządzanego lub pobiera właściwości dla określonego klucza wystąpienia zarządzanego. |
> | Działanie | Microsoft. SQL/managedInstances/klucze/zapis | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza wystąpienia zarządzanego. |
> | Działanie | Microsoft. SQL/managedInstances/metricDefinitions/odczyt | Pobierz definicje metryk wystąpienia zarządzanego |
> | Działanie | Microsoft. SQL/managedInstances/Metrics/Read | Pobierz metryki wystąpienia zarządzanego |
> | Działanie | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla wystąpień zarządzanych |
> | Działanie | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla wystąpień zarządzanych |
> | Działanie | Microsoft. SQL/managedInstances/odczyt | Zwróć listę wystąpień zarządzanych lub pobiera właściwości dla określonego wystąpienia zarządzanego. |
> | Działanie | Microsoft. SQL/managedInstances/recoverableDatabases/odczyt | Zwraca listę możliwych do odzyskania zarządzanych baz danych |
> | Działanie | Microsoft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/Read | Pobiera krótkoterminowe zasady przechowywania dla porzuconej zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/Write | Aktualizuje krótkoterminowe zasady przechowywania dla porzuconej zarządzanej bazy danych |
> | Działanie | Microsoft. SQL/managedInstances/restorableDroppedDatabases/odczyt | Zwraca listę dostępnych usuniętych zarządzanych baz danych. |
> | Działanie | Microsoft. SQL/managedInstances/securityAlertPolicies/odczyt | Pobieranie listy zasad wykrywania zagrożeń serwera zarządzanego skonfigurowanych dla danego serwera |
> | Działanie | Microsoft. SQL/managedInstances/securityAlertPolicies/Write | Zmień zasady wykrywania zagrożeń serwera zarządzanego dla danego serwera zarządzanego |
> | Działanie | Microsoft. SQL/managedInstances/tdeCertificates/akcja | Utwórz/zaktualizuj certyfikat TDE |
> | Działanie | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Delete | Usuń ocenę luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Działanie | Microsoft. SQL/managedInstances/vulnerabilityAssessments/odczyt | Pobieranie zasad oceny luk w zabezpieczeniach w danym wystąpieniu zarządzanym |
> | Działanie | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Write | Zmiana oceny luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Działanie | Microsoft. SQL/managedInstances/Write | Tworzy wystąpienie zarządzane o określonych parametrach lub aktualizuje właściwości lub Tagi dla określonego wystąpienia zarządzanego. |
> | Działanie | Microsoft. SQL/Operations/Read | Pobiera dostępne operacje REST |
> | Działanie | Microsoft. SQL/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/Register/akcja | Rejestruje subskrypcję dostawcy zasobów programu Microsoft SQL Database i umożliwia tworzenie baz danych Microsoft SQL. |
> | Działanie | Microsoft. SQL/serwery/Administratorzy/usuwanie | Usuwa określony obiekt administratora Azure Active Directory |
> | Działanie | Microsoft. SQL/serwery/Administratorzy/odczyt | Pobiera określony obiekt administratora Azure Active Directory |
> | Działanie | Microsoft. SQL/serwery/Administratorzy/zapis | Dodaje lub aktualizuje określony obiekt administratora Azure Active Directory |
> | Działanie | Microsoft. SQL/serwery/doradcy/odczyt | Zwraca listę doradców dostępnych dla serwera |
> | Działanie | Microsoft. SQL/serwery/doradcy/recommendedActions/odczyt | Zwraca listę zalecanych akcji określonego klasyfikatora dla serwera |
> | Działanie | Microsoft. SQL/serwery/doradcy/recommendedActions/zapis | Zastosuj zalecaną akcję na serwerze |
> | Działanie | Microsoft. SQL/serwery/doradcy/zapis | Aktualizuje stan autouruchomienia klasyfikatora na poziomie serwera. |
> | Działanie | Microsoft. SQL/serwery/auditingPolicies/odczyt | Pobierz szczegóły domyślnych zasad inspekcji tabeli serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft. SQL/serwery/auditingPolicies/Write | Zmień domyślną inspekcję tabeli serwera dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/auditingSettings/operationResults/odczyt | Pobierz wynik operacji ustawiania zasad inspekcji obiektu BLOB serwera |
> | Działanie | Microsoft. SQL/serwery/auditingSettings/odczyt | Pobierz szczegóły zasad inspekcji obiektów BLOB serwera skonfigurowanych na danym serwerze |
> | Działanie | Microsoft. SQL/serwery/auditingSettings/Write | Zmień inspekcję obiektu BLOB serwera dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/automaticTuning/odczyt | Zwraca ustawienia automatycznego dostrajania dla serwera. |
> | Działanie | Microsoft. SQL/serwery/automaticTuning/Write | Aktualizuje ustawienia dostrajania automatycznego dla serwera i zwraca zaktualizowane ustawienia |
> | Działanie | Microsoft. SQL/serwery/backupLongTermRetentionVaults/Delete | Usuwa istniejący magazyn archiwum kopii zapasowych. |
> | Działanie | Microsoft. SQL/serwery/backupLongTermRetentionVaults/odczyt | Ta operacja służy do pobierania magazynu długoterminowego przechowywania kopii zapasowych. Zwraca informacje o magazynie zarejestrowanym na tym serwerze |
> | Działanie | Microsoft. SQL/serwery/backupLongTermRetentionVaults/Write | Ta operacja służy do rejestrowania magazynu długoterminowego przechowywania kopii zapasowej na serwerze programu. |
> | Działanie | Microsoft. SQL/serwery/communicationLinks/Delete | Usuwa istniejące łącze komunikacji z serwerem. |
> | Działanie | Microsoft. SQL/serwery/communicationLinks/odczyt | Zwróć listę linków komunikacyjnych określonego serwera. |
> | Działanie | Microsoft. SQL/serwery/communicationLinks/Write | Utwórz lub zaktualizuj łącze do komunikacji z serwerem. |
> | Działanie | Microsoft. SQL/serwery/connectionPolicies/odczyt | Zwróć listę zasad połączenia serwera określonego serwera. |
> | Działanie | Microsoft. SQL/serwery/connectionPolicies/Write | Utwórz lub zaktualizuj zasady połączenia z serwerem. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/doradcy/odczyt | Zwraca listę doradców dostępnych dla bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/doradcy/recommendedActions/odczyt | Zwraca listę zalecanych akcji określonego klasyfikatora dla bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/doradcy/recommendedActions/Write | Zastosuj zalecaną akcję w bazie danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/doradcy/zapis | Aktualizowanie stanu autouzupełniania usługi Advisor na poziomie bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/auditingPolicies/odczyt | Pobierz szczegóły zasad inspekcji tabeli skonfigurowanych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/auditingPolicies/Write | Zmień zasady inspekcji tabeli dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/auditingSettings/odczyt | Pobierz szczegóły zasad inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/auditingSettings/Write | Zmień zasady inspekcji obiektów BLOB dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/auditRecords/odczyt | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/automaticTuning/odczyt | Zwraca ustawienia automatycznego dostrajania dla bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/automaticTuning/Write | Aktualizuje ustawienia dostrajania automatycznego dla bazy danych i zwraca zaktualizowane ustawienia |
> | Działanie | Microsoft. SQL/serwery/bazy danych/azureAsyncOperation/odczyt | Pobiera stan operacji bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/backupLongTermRetentionPolicies/odczyt | Zwróć listę zasad archiwizacji kopii zapasowych określonej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/backupLongTermRetentionPolicies/Write | Utwórz lub zaktualizuj Zasady archiwizacji kopii zapasowej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/backupShortTermRetentionPolicies/odczyt | Pobiera krótkoterminowe zasady przechowywania dla bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/backupShortTermRetentionPolicies/Write | Aktualizuje krótkoterminowe zasady przechowywania dla bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/kolumny/odczyt | Zwracanie listy kolumn dla bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/connectionPolicies/odczyt | Pobierz szczegóły zasad połączenia skonfigurowanych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/connectionPolicies/Write | Zmień zasady połączenia dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/currentSensitivityLabels/odczyt | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/currentSensitivityLabels/Write | Etykiety czułości aktualizacji partii |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/odczyt | Zwróć listę zasad maskowania danych bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/reguły/usuwanie | Usuń regułę zasad maskowania danych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/reguły/odczyt | Pobierz szczegóły reguły zasad maskowania danych skonfigurowanej w danej bazie danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/reguły/zapis | Zmień regułę zasad maskowania danych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/Write | Zmień zasady maskowania danych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dataWarehouseQueries/dataWarehouseQuerySteps/odczyt | Zwraca informacje o kroku zapytania rozproszonego dotyczące zapytania hurtowni danych dla wybranego identyfikatora kroku |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dataWarehouseQueries/odczyt | Zwraca informacje o zapytaniu dystrybucji magazynu danych dla wybranego identyfikatora zapytania |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dataWarehouseUserActivities/odczyt | Pobiera działania użytkownika wystąpienia SQL Data Warehouse, w tym uruchomione i wstrzymane zapytania |
> | Działanie | Microsoft. SQL/serwery/bazy danych/usuwanie | Usuwa istniejącą bazę danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/eksport/akcja | Eksportuj Azure SQL Database |
> | Działanie | Microsoft. SQL/serwery/bazy danych/extendedAuditingSettings/odczyt | Pobierz szczegóły zasad rozszerzonej inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/extendedAuditingSettings/Write | Zmiana zasad rozszerzonej inspekcji obiektów BLOB dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/rozszerzenia/odczyt | Pobiera kolekcję rozszerzeń dla bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/rozszerzenia/zapis | Zmień rozszerzenie dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/tryb failover/akcja | Klient przełączenia w tryb failover bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/geoBackupPolicies/odczyt | Pobierz zasady geograficznej kopii zapasowej dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/geoBackupPolicies/Write | Tworzenie lub aktualizowanie zasad tworzenia kopii zapasowej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/importExportOperationResults/odczyt | Pobiera operacje importu/eksportu w toku |
> | Działanie | Microsoft. SQL/serwery/bazy danych/maintenanceWindowOptions/odczyt | Pobiera listę dostępnych okien obsługi dla wybranej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/maintenanceWindows/odczyt | Pobiera ustawienia okien obsługi dla wybranej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/maintenanceWindows/Write | Ustawia ustawienia okien obsługi dla wybranej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/metricDefinitions/odczyt | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/metryki/odczyt | Metryki zwracane dla baz danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/Przenieś/akcja | Zmień nazwę istniejącej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/operationResults/odczyt | Pobiera stan operacji bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/operacje/Anuluj/akcja | Anuluje Azure SQL Database oczekującej operacji asynchronicznej, która nie została jeszcze zakończona. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/operacje/odczyt | Zwróć listę operacji wykonanych na bazie danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/wstrzymywanie/akcja | Wstrzymywanie bazy danych usługi Azure SQL DataWarehouse |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla baz danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/queryStore/queryTexts/odczyt | Zwraca kolekcję tekstów zapytania, które odpowiadają określonym parametrom. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/queryStore/odczyt | Zwraca bieżące wartości ustawień magazynu zapytań dla bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/queryStore/Write | Aktualizuje ustawienie magazynu zapytań dla bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/Odczyt | Zwróć listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/recommendedSensitivityLabels/odczyt | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/recommendedSensitivityLabels/Write | Etykiety czułości zalecanych aktualizacji wsadowych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/replicationLinks/Usuń | Zakończenie wymuszonej relacji replikacji z potencjalną utratą danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/replicationLinks/tryb failover/akcja | Przełączenie w tryb failover po zsynchronizowaniu wszystkich zmian z poziomu podstawowego, co sprawia, że ta baza danych jest podzielona na relationship\u0027s replikacji i udostępnianie zdalnego elementu głównego do pomocniczego |
> | Działanie | Microsoft. SQL/serwery/bazy danych/replicationLinks/forceFailoverAllowDataLoss/akcja | Natychmiastowe przejście w tryb failover z potencjalną utratą danych, dzięki czemu ta baza danych jest relationship\u0027s replikacją podstawową i udostępniająca ją jako pomocniczy. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/replicationLinks/odczyt | Zwróć listę linków replikacji lub pobiera właściwości dla określonych linków replikacji. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/replicationLinks/Odłącz/akcja | Wymuszone zakończenie relacji replikacji lub po zsynchronizowaniu z partnerem |
> | Działanie | Microsoft. SQL/serwery/bazy danych/replicationLinks/updateReplicationMode/akcja | Aktualizowanie trybu replikacji dla łącza do trybu synchronicznego lub asynchronicznego |
> | Działanie | Microsoft. SQL/serwery/bazy danych/restorePoints/akcja | Tworzy nowy punkt przywracania |
> | Działanie | Microsoft. SQL/serwery/bazy danych/restorePoints/Usuń | Usuwa punkt przywracania bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/restorePoints/odczyt | Zwraca punkty przywracania bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/wznowienie/akcja | Wznów bazę danych usługi Azure SQL DataWarehouse |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/odczyt | Pobierz schemat bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/odczyt | Pobierz kolumnę bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Delete | Usuń etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Disable/Action | Wyłącz zalecenia dotyczące czułości w danej kolumnie |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Włącz/akcja | Włącz zalecenia dotyczące czułości w danej kolumnie |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/odczyt | Pobierz etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Write | Utwórz lub zaktualizuj etykietę czułości danej kolumny |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/odczyt | Pobierz tabelę bazy danych. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/recommendedIndexes/odczyt | Pobierz listę zaleceń dotyczących indeksów w bazie danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/schematy/tabele/recommendedIndexes/Write | Zastosuj zalecenie dotyczące indeksu |
> | Działanie | Microsoft. SQL/serwery/bazy danych/securityAlertPolicies/odczyt | Pobieranie listy zasad wykrywania zagrożeń bazy danych skonfigurowanych dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/bazy danych/securityAlertPolicies/Write | Zmień zasady wykrywania zagrożeń bazy danych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/securityMetrics/odczyt | Pobiera kolekcję metryk zabezpieczeń bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/sensitivityLabels/odczyt | Wyświetl etykiety czułości danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/serviceTierAdvisors/odczyt | Zwróć sugestię dotyczącą skalowania bazy danych w górę lub w dół na podstawie statystyk wykonywania zapytań w celu zwiększenia wydajności lub obniżenia kosztów |
> | Działanie | Microsoft. SQL/serwery/bazy danych/jednostki SKU/odczyt | Pobiera kolekcję jednostek SKU dostępnych dla bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/cancelSync/akcja | Anuluj synchronizację grupy synchronizacji |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/Usuń | Usuwa istniejącą grupę synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/hubSchemas/odczyt | Zwróć listę schematów bazy danych Centrum synchronizacji |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/Logs/odczyt | Zwróć listę dzienników grup synchronizacji |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/odczyt | Zwróć listę grup synchronizacji lub pobiera właściwości dla określonej grupy synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/refreshHubSchema/akcja | Odśwież schemat bazy danych Centrum synchronizacji |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/refreshHubSchemaOperationResults/odczyt | Pobierz wynik operacji odświeżania schematu narzędzia Sync Hub |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/Delete | Usuwa istniejący element członkowski synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/odczyt | Zwróć listę elementów członkowskich synchronizacji lub pobiera właściwości dla określonego elementu członkowskiego synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/refreshSchema/akcja | Odśwież schemat elementu członkowskiego synchronizacji |
> | Działanie | Microsoft. SQL/Servers/Databases/syncGroups/syncMembers/refreshSchemaOperationResults/Read | Pobierz wynik operacji odświeżania schematu elementu członkowskiego synchronizacji |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/schematy/odczyt | Zwróć listę schematów bazy danych elementu członkowskiego synchronizacji |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/syncMembers/zapis | Tworzy element członkowski synchronizacji z określonymi parametrami lub aktualizuje właściwości określonego elementu członkowskiego synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/triggerSync/akcja | Wyzwalanie synchronizacji grupy synchronizacji |
> | Działanie | Microsoft. SQL/serwery/bazy danych/syncGroups/Write | Tworzy grupę synchronizacji z określonymi parametrami lub aktualizuje właściwości określonej grupy synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/topQueries/queryText/akcja | Zwraca tekst Transact-SQL dla wybranego identyfikatora zapytania |
> | Działanie | Microsoft. SQL/serwery/bazy danych/topQueries/odczyt | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Działanie | Microsoft. SQL/serwery/bazy danych/topQueries/Statystyka/odczyt | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Działanie | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/operationResults/odczyt | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/odczyt | Pobierz stan i szczegóły funkcji zabezpieczenia szyfrowania danych przezroczystych dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/Write | Zmień stan przezroczystego szyfrowania danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/upgradeDataWarehouse/akcja | Uaktualnianie bazy danych usługi Azure SQL DataWarehouse |
> | Działanie | Microsoft. SQL/serwery/bazy danych/użycia/odczyt | Pobiera informacje o użyciach Azure SQL Database |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/Usuń | Usuń ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/odczyt | Pobieranie zasad oceny luk w zabezpieczeniach na givendatabase |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/reguły/linie bazowe/usuwanie | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/reguły/linie bazowe/odczyt | Pobierz linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/reguły/linie bazowe/zapis | Zmień linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/skanowania/eksport/akcja | Przekonwertuj istniejący wynik skanowania na format możliwy do odczytania przez człowieka. Jeśli już nie istnieje, nic się nie dzieje |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/skanowania/initiateScan/akcja | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/skany/odczyt | Zwróć listę rekordów skanowania oceny luk w zabezpieczeniach bazy danych lub Pobierz rekord skanowania pod kątem określonego identyfikatora skanowania. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/Write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentScans/akcja | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentScans/operationResults/odczyt | Pobieranie wyniku operacji skanowania oceny luk w zabezpieczeniach bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentSettings/odczyt | Pobierz szczegóły oceny luk w zabezpieczeniach skonfigurowanej w danej bazie danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentSettings/Write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Działanie | Microsoft. SQL/serwery/bazy danych/zapis | Tworzy bazę danych z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej bazy danych. |
> | Działanie | Microsoft. SQL/serwery/usuwanie | Usuwa istniejący serwer. |
> | Działanie | Microsoft. SQL/serwery/disasterRecoveryConfiguration/Delete | Usuwa istniejące konfiguracje odzyskiwania po awarii dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/disasterRecoveryConfiguration/tryb failover/akcja | Przełączenie w tryb failover DisasterRecoveryConfiguration |
> | Działanie | Microsoft. SQL/serwery/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/akcja | Wymuś tryb failover DisasterRecoveryConfiguration |
> | Działanie | Microsoft. SQL/serwery/disasterRecoveryConfiguration/odczyt | Pobiera kolekcję konfiguracji odzyskiwania po awarii, która obejmuje ten serwer |
> | Działanie | Microsoft. SQL/serwery/disasterRecoveryConfiguration/Write | Zmień konfigurację odzyskiwania po awarii serwera |
> | Działanie | Microsoft. SQL/serwery/elasticPoolEstimates/odczyt | Zwraca listę oszacowań elastycznej puli już utworzonych dla tego serwera |
> | Działanie | Microsoft. SQL/serwery/elasticPoolEstimates/Write | Tworzy nowe szacowanie puli elastycznej dla listy dostarczonych baz danych |
> | Działanie | Microsoft. SQL/serwery/elasticPools/doradcy/odczyt | Zwraca listę doradców dostępnych dla puli elastycznej |
> | Działanie | Microsoft. SQL/Servers/elasticPools/Advisors/recommendedActions/Read | Zwraca listę zalecanych akcji określonego klasyfikatora dla puli elastycznej |
> | Działanie | Microsoft. SQL/Servers/elasticPools/Advisors/recommendedActions/Write | Zastosuj zalecaną akcję w puli elastycznej |
> | Działanie | Microsoft. SQL/serwery/elasticPools/doradcy/zapis | Aktualizacja stanu autouruchomienia klasyfikatora na poziomie puli elastycznej. |
> | Działanie | Microsoft. SQL/serwery/elasticPools/bazy danych/Odczyt | Pobiera listę baz danych dla puli elastycznej |
> | Działanie | Microsoft. SQL/serwery/elasticPools/Delete | Usuń istniejącą pulę elastyczną |
> | Działanie | Microsoft. SQL/serwery/elasticPools/elasticPoolActivity/odczyt | Pobieranie działań i szczegółów w danej elastycznej puli baz danych |
> | Działanie | Microsoft. SQL/serwery/elasticPools/elasticPoolDatabaseActivity/odczyt | Pobierz działania i szczegóły dotyczące danej bazy danych będącej częścią elastycznej puli baz danych |
> | Działanie | Microsoft. SQL/serwery/elasticPools/tryb failover/akcja | Klient zainicjował pracę awaryjną puli elastycznej. |
> | Działanie | Microsoft. SQL/serwery/elasticPools/metricDefinitions/odczyt | Zwracaj typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Działanie | Microsoft. SQL/Server/elasticPools/Metrics/Read | Metryki zwracane dla elastycznych pul baz danych |
> | Działanie | Microsoft. SQL/Servers/elasticPools/Operations/Cancel/Action | Anuluje oczekującą operację asynchroniczną w puli elastycznej SQL platformy Azure, która nie została jeszcze zakończona. |
> | Działanie | Microsoft. SQL/serwery/elasticPools/Operations/Read | Zwróć listę operacji wykonywanych w puli elastycznej |
> | Działanie | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Działanie | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Działanie | Microsoft. SQL/serwery/elasticPools/odczyt | Pobierz szczegóły puli elastycznej na danym serwerze |
> | Działanie | Microsoft. SQL/Server/elasticPools/SKU/odczyt | Pobiera kolekcję jednostek SKU dostępnych dla puli elastycznej |
> | Działanie | Microsoft. SQL/serwery/elasticPools/Write | Utwórz nowe lub zmień właściwości istniejącej puli elastycznej |
> | Działanie | Microsoft. SQL/serwery/encryptionProtector/odczyt | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft. SQL/serwery/encryptionProtector/ponownie Zweryfikuj/akcja | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft. SQL/serwery/encryptionProtector/Write | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Działanie | Microsoft. SQL/serwery/extendedAuditingSettings/odczyt | Pobierz szczegóły zasad inspekcji rozszerzonego serwera obiektów BLOB skonfigurowanych na danym serwerze |
> | Działanie | Microsoft. SQL/serwery/extendedAuditingSettings/Write | Zmień rozszerzoną inspekcję obiektów BLOB serwera dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/failoverGroups/Delete | Usuwa istniejącą grupę trybu failover. |
> | Działanie | Microsoft. SQL/serwery/failoverGroups/tryb failover/akcja | Wykonuje planowaną pracę w trybie failover w istniejącej grupie trybu failover. |
> | Działanie | Microsoft. SQL/serwery/failoverGroups/forceFailoverAllowDataLoss/akcja | Wykonuje wymuszone przejście w tryb failover w istniejącej grupie trybu failover. |
> | Działanie | Microsoft. SQL/serwery/failoverGroups/odczyt | Zwraca listę grup trybu failover lub pobiera właściwości dla określonej grupy trybu failover. |
> | Działanie | Microsoft. SQL/serwery/failoverGroups/Write | Tworzy grupę trybu failover z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej grupy trybu failover. |
> | Działanie | Microsoft. SQL/serwery/firewallRules/Delete | Usuwa istniejącą regułę zapory serwera. |
> | Działanie | Microsoft. SQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory serwera lub pobiera właściwości dla określonej reguły zapory serwera. |
> | Działanie | Microsoft. SQL/serwery/firewallRules/Write | Tworzy regułę zapory serwera z określonymi parametrami, aktualizuje właściwości określonej reguły lub zastępuje wszystkie istniejące reguły przy użyciu nowych reguł zapory serwera. |
> | Działanie | Microsoft. SQL/serwery/import/akcja | Utwórz nową bazę danych na serwerze i Wdróż schemat oraz dane z pakietu DacPac |
> | Działanie | Microsoft. SQL/serwery/importExportOperationResults/odczyt | Pobiera operacje importu/eksportu w toku |
> | Działanie | Microsoft. SQL/serwery/interfaceEndpointProfiles/Delete | Usuwa określony profil punktu końcowego interfejsu |
> | Działanie | Microsoft. SQL/serwery/interfaceEndpointProfiles/odczyt | Zwraca właściwości dla określonego profilu punktu końcowego interfejsu |
> | Działanie | Microsoft. SQL/serwery/interfaceEndpointProfiles/Write | Tworzy profil punktu końcowego interfejsu z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego punktu końcowego interfejsu |
> | Działanie | Microsoft. SQL/serwery/jobAgents/Delete | Usuwa agenta zadań usługi Azure SQL DB |
> | Działanie | Microsoft. SQL/serwery/jobAgents/odczyt | Pobiera agenta zadań usługi Azure SQL DB |
> | Działanie | Microsoft. SQL/serwery/jobAgents/Write | Tworzy lub aktualizuje agenta zadań usługi Azure SQL DB |
> | Działanie | Microsoft. SQL/serwery/klucze/usuwanie | Usuwa istniejący klucz serwera. |
> | Działanie | Microsoft. SQL/serwery/klucze/odczyt | Zwróć listę kluczy serwera lub pobiera właściwości dla określonego klucza serwera. |
> | Działanie | Microsoft. SQL/serwery/klucze/zapis | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza serwera. |
> | Działanie | Microsoft. SQL/serwery/operationResults/odczyt | Pobiera operacje serwera w toku |
> | Działanie | Microsoft. SQL/serwery/privateEndpointConnectionProxies/Delete | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/serwery/privateEndpointConnectionProxies/odczyt | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. SQL/serwery/privateEndpointConnectionProxies/Validate/akcja | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Działanie | Microsoft. SQL/serwery/privateEndpointConnectionProxies/Write | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. SQL/serwery/privateEndpointConnections/Delete | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/serwery/privateEndpointConnections/odczyt | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Działanie | Microsoft. SQL/serwery/privateEndpointConnections/Write | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/serwery/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. SQL/serwery/privateLinkResources/odczyt | Pobierz zasoby linku prywatnego dla odpowiedniego programu SQL Server |
> | Działanie | Microsoft. SQL/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla serwerów |
> | Działanie | Microsoft. SQL/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Działanie | Microsoft. SQL/serwery/recommendedElasticPools/bazy danych/Odczyt | Pobierz metryki dla zalecanych elastycznych pul baz danych dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/recommendedElasticPools/odczyt | Pobieranie zalecenia dla elastycznych pul baz danych w celu ograniczenia kosztów lub zwiększenia wydajności w oparciu o historyczne wykorzystanie zasobów |
> | Działanie | Microsoft. SQL/serwery/recoverableDatabases/odczyt | Ta operacja służy do odzyskiwania po awarii usługi Live Database w celu przywrócenia bazy danych do ostatniego znanego dobrego punktu kopii zapasowej. Zwraca informacje o ostatniej dobrej kopii zapasowej, ale doesn\u0027t w rzeczywistości przywraca bazę danych. |
> | Działanie | Microsoft. SQL/serwery/replicationLinks/odczyt | Zwróć listę linków replikacji lub pobiera właściwości dla określonych linków replikacji. |
> | Działanie | Microsoft. SQL/serwery/restorableDroppedDatabases/odczyt | Pobierz listę baz danych, które zostały porzucone na danym serwerze, które są nadal w ramach zasad przechowywania. |
> | Działanie | Microsoft. SQL/serwery/securityAlertPolicies/operationResults/odczyt | Pobierz wyniki operacji zapisu zasad wykrywania zagrożeń serwera |
> | Działanie | Microsoft. SQL/serwery/securityAlertPolicies/odczyt | Pobieranie listy zasad wykrywania zagrożeń serwera skonfigurowanych dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/securityAlertPolicies/Write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/servicedążyć/odczyt | Pobierz listę celów poziomu usług (nazywanych również warstwami wydajności) dostępnych na danym serwerze |
> | Działanie | Microsoft. SQL/serwery/syncAgents/Delete | Usuwa istniejącego agenta synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/syncAgents/do generowania kluczy/akcja | Generuj klucz rejestracji agenta synchronizacji |
> | Działanie | Microsoft. SQL/serwery/syncAgents/linkedDatabases/odczyt | Zwróć listę połączonych baz danych agenta synchronizacji |
> | Działanie | Microsoft. SQL/serwery/syncAgents/odczyt | Zwróć listę agentów synchronizacji lub pobiera właściwości dla określonego agenta synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/syncAgents/Write | Tworzy agenta synchronizacji z określonymi parametrami lub aktualizuje właściwości określonego agenta synchronizacji. |
> | Działanie | Microsoft. SQL/serwery/tdeCertificates/akcja | Utwórz/zaktualizuj certyfikat TDE |
> | Działanie | Microsoft. SQL/serwery/użycia/odczyt | Limit przydziału jednostek DTU serwera i bieżące użycie jednostek DTU przez wszystkie bazy danych na serwerze |
> | Działanie | Microsoft. SQL/serwery/virtualNetworkRules/Delete | Usuwa istniejącą regułę Virtual Network |
> | Działanie | Microsoft. SQL/serwery/virtualNetworkRules/odczyt | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft. SQL/serwery/virtualNetworkRules/Write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Działanie | Microsoft. SQL/serwery/vulnerabilityAssessments/Delete | Usuń ocenę luk w zabezpieczeniach dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/vulnerabilityAssessments/odczyt | Pobierz zasady oceny luk w zabezpieczeniach na danym serwerze |
> | Działanie | Microsoft. SQL/serwery/vulnerabilityAssessments/Write | Zmiana oceny luk w zabezpieczeniach dla danego serwera |
> | Działanie | Microsoft. SQL/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |
> | Działanie | Microsoft. SQL/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy zasobów SQL Database firmy Microsoft i umożliwia tworzenie baz danych Microsoft SQL. |
> | Działanie | Microsoft. SQL/virtualClusters/Delete | Usuwa istniejący klaster wirtualny. |
> | Działanie | Microsoft. SQL/virtualClusters/odczyt | Zwróć listę klastrów wirtualnych lub pobiera właściwości dla określonego klastra wirtualnego. |
> | Działanie | Microsoft. SQL/virtualClusters/Write | Aktualizuje Tagi klastra wirtualnego. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Storage/checknameavailability/Read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft. Storage/Locations/checknameavailability/Read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft. Storage/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadamia Microsoft. Storage, że sieć wirtualna lub podsieć jest usuwana |
> | Działanie | Microsoft. Storage/lokalizacje/użycia/odczyt | Zwraca limit i bieżącą liczbę użycia dla zasobów w określonej subskrypcji |
> | Działanie | Microsoft. Storage/Operations/Read | Sonduje stan operacji asynchronicznej. |
> | Działanie | Microsoft. Storage/Register/Action | Rejestruje subskrypcję dostawcy zasobów magazynu i umożliwia tworzenie kont magazynu. |
> | Działanie | Microsoft. Storage/SKU/odczyt | Wyświetla listę jednostek SKU obsługiwanych przez firmę Microsoft. Storage. |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Dodawanie/działanie | Zwraca wynik dodania zawartości obiektu BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie | Zwraca wynik usunięcia obiektu BLOB |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/deleteAutomaticSnapshot/akcja | Zwraca wynik usunięcia automatycznej migawki |
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
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/setLegalHold/akcja | Ustaw blokadę prawną kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik poprawki kontenera obiektów BLOB |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik umieszczenia kontenera obiektów BLOB. |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla usługi BLOB Service |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/odczyt | Zwraca statystykę lub właściwości usługi BLOB Service |
> | Działanie | Microsoft. Storage/storageAccounts/blobServices/Write | Zwraca wynik właściwości Put usługi BLOB Service |
> | Działanie | Microsoft. Storage/storageAccounts/Delete | Usuwa istniejące konto magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/encryptionScopes/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Działanie | Microsoft. Storage/storageAccounts/tryb failover/akcja | Klient może kontrolować pracę w trybie failover w przypadku problemów z dostępnością |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/Files/actassuperuser/Action | Uzyskaj uprawnienia administratora pliku |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/usuwanie | Zwraca wynik usunięcia pliku/folderu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/Files/modifypermissions/Action | Zwraca wynik modyfikacji uprawnienia do pliku/folderu. |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/odczyt | Zwraca plik/folder lub listę plików/folderów |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/zapis | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/odczyt | Pobierz właściwości usługi plików |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/udziały/usuwanie |  |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/udziały/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/udziały/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/udziały/zapis |  |
> | Działanie | Microsoft. Storage/storageAccounts/fileServices/Write |  |
> | Działanie | Microsoft. Storage/storageAccounts/listAccountSas/akcja | Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/ListKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/listServiceSas/akcja | Zwraca token sygnatury dostępu współdzielonego usługi dla określonego konta magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/managementPolicies/Delete | Usuwanie zasad zarządzania kontami magazynu |
> | Działanie | Microsoft. Storage/storageAccounts/managementPolicies/odczyt | Pobierz Zasady konta zarządzania magazynem |
> | Działanie | Microsoft. Storage/storageAccounts/managementPolicies/Write | Umieść zasady zarządzania kontami magazynu |
> | Działanie | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Delete |  |
> | Działanie | Microsoft. Storage/storageAccounts/objectReplicationPolicies/odczyt |  |
> | Działanie | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Działanie | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Delete | Usuwanie prywatnych serwerów proxy połączeń punktu końcowego |
> | Działanie | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/odczyt | Pobierz serwer proxy połączenia prywatnego punktu końcowego |
> | Działanie | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Write | Umieść proxy połączeń prywatnych punktów końcowych |
> | Działanie | Microsoft. Storage/storageAccounts/privateEndpointConnections/Delete | Usuń połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. Storage/storageAccounts/privateEndpointConnections/odczyt | Pobierz połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. Storage/storageAccounts/privateEndpointConnections/Write | Umieść połączenie prywatnego punktu końcowego |
> | Działanie | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/akcja | Zatwierdzanie połączeń prywatnych punktów końcowych |
> | Działanie | Microsoft. Storage/storageAccounts/privateLinkResources/odczyt | Pobierz StorageAccount groupids |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/Queues/Delete | Zwraca wynik usunięcia kolejki. |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Add/Action | Zwraca wynik dodania komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Delete | Zwraca wynik usunięcia komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Process/Action | Zwraca wynik przetwarzania komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Read | Zwraca komunikat |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Write | Zwraca wynik zapisania komunikatu |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/Queues/Read | Zwraca kolejkę lub listę kolejek. |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/Queues/Write | Zwraca wynik zapisania kolejki |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/odczyt | Pobierz usługa kolejki właściwości |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/odczyt | Zwraca właściwości usługi kolejki lub dane statystyczne. |
> | Działanie | Microsoft. Storage/storageAccounts/queueServices/Write | Zwraca wynik ustawienia właściwości usługi kolejki |
> | Działanie | Microsoft. Storage/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/regeneratekey/akcja | Generuje ponownie klucze dostępu dla określonego konta magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/restoreBlobRanges/akcja | Przywróć zakresy obiektów BLOB do stanu określonego czasu |
> | Działanie | Microsoft. Storage/storageAccounts/revokeUserDelegationKeys/akcja | Odwołuje wszystkie klucze delegowania użytkowników dla określonego konta magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/Services/diagnosticSettings/Write | Utwórz/zaktualizuj ustawienia diagnostyczne konta magazynu. |
> | Działanie | Microsoft. Storage/storageAccounts/tableServices/odczyt | Pobierz Table service właściwości |
> | Działanie | Microsoft. Storage/storageAccounts/Write | Tworzy konto magazynu z określonymi parametrami lub aktualizuje właściwości lub Tagi albo dodaje niestandardową domenę dla określonego konta magazynu. |
> | Działanie | Microsoft. Storage/Usages/Read | Zwraca limit i bieżącą liczbę użycia dla zasobów w określonej subskrypcji |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. storagesync/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa usługi synchronizacji magazynu jest prawidłowa i czy nie jest używana. |
> | Działanie | Microsoft. storagesync/lokalizacje/przepływy pracy/operacje/odczyt | Pobiera stan operacji asynchronicznej |
> | Działanie | Microsoft. storagesync/Operations/Read | Pobiera listę obsługiwanych operacji |
> | Działanie | Microsoft. storagesync/Register/Action | Rejestruje subskrypcję dostawcy synchronizacji magazynu |
> | Działanie | Microsoft. storagesync/storageSyncServices/Delete | Usuń wszystkie usługi synchronizacji magazynu |
> | Działanie | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla usług synchronizacji magazynu |
> | Działanie | Microsoft. storagesync/storageSyncServices/odczyt | Odczytaj wszystkie usługi synchronizacji magazynu |
> | Działanie | Microsoft. storagesync/storageSyncServices/registeredServers/Delete | Usuń wszystkie zarejestrowane serwery |
> | Działanie | Microsoft. storagesync/storageSyncServices/registeredServers/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla zarejestrowanego serwera |
> | Działanie | Microsoft. storagesync/storageSyncServices/registeredServers/Read | Odczytaj wszystkie zarejestrowane serwery |
> | Działanie | Microsoft. storagesync/storageSyncServices/registeredServers/Write | Utwórz lub zaktualizuj dowolny zarejestrowany serwer |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/Delete | Usuń wszystkie punkty końcowe chmury |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/Read | Pobiera stan asynchronicznej operacji tworzenia kopii zapasowej/przywracania |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/Action | Wywołaj tę akcję po utworzeniu kopii zapasowej |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/PostRestore/Action | Wywołaj tę akcję po przywróceniu |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/Action | Wywołaj tę akcję przed wykonaniem kopii zapasowej |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/PreRestore/Action | Wywołaj tę akcję przed przywróceniem |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/Read | Odczytaj wszystkie punkty końcowe w chmurze |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/Action | Przywróć puls |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/Write | Utwórz lub zaktualizuj wszystkie punkty końcowe w chmurze |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/Delete | Usuń wszystkie grupy synchronizacji |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla grup synchronizacji |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/Read | Odczytaj wszystkie grupy synchronizacji |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Delete | Usuń wszystkie punkty końcowe serwera |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla punktów końcowych serwera |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Read | Odczytaj wszystkie punkty końcowe serwera |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/Action | Wywołaj tę akcję, aby odwołać pliki do serwera |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Write | Utwórz lub zaktualizuj wszystkie punkty końcowe serwera |
> | Działanie | Microsoft. storagesync/storageSyncServices/syncGroups/Write | Utwórz lub zaktualizuj wszystkie grupy synchronizacji |
> | Działanie | Microsoft. storagesync/storageSyncServices/Workflows/operationresults/Read | Pobiera stan operacji asynchronicznej |
> | Działanie | Microsoft. storagesync/storageSyncServices/przepływy pracy/operacje odczytu | Pobiera stan operacji asynchronicznej |
> | Działanie | Microsoft. storagesync/storageSyncServices/przepływy pracy/odczyt | Odczytaj przepływy pracy |
> | Działanie | Microsoft. storagesync/storageSyncServices/Write | Utwórz lub zaktualizuj wszystkie usługi synchronizacji magazynu |
> | Działanie | Microsoft. storagesync/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy synchronizacji magazynu |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. StorSimple/menedżerowie/accessControlRecords/Usuń | Usuwa rekordy Access Control |
> | Działanie | Microsoft. StorSimple/menedżerowie/accessControlRecords/operationResults/Read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/kierownicy/accessControlRecords/odczyt | Wyświetla lub pobiera Access Control rekordy |
> | Działanie | Microsoft. StorSimple/kierownicy/accessControlRecords/Write | Tworzenie lub aktualizowanie rekordów Access Control |
> | Działanie | Microsoft. StorSimple/menedżerowie/alerty/odczyt | Wyświetla lub pobiera alerty |
> | Działanie | Microsoft. StorSimple/menedżerowie/kopie zapasowe/odczyt | Wyświetla lub Pobiera zestaw kopii zapasowych |
> | Działanie | Microsoft. StorSimple/menedżerowie/bandwidthSettings/Usuń | Usuwa istniejące ustawienia przepustowości (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/menedżerowie/bandwidthSettings/operationResults/Read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/kierownicy/bandwidthSettings/odczyt | Wyświetl listę ustawień przepustowości (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/kierownicy/bandwidthSettings/Write | Tworzy nowe lub aktualizuje ustawienia przepustowości (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/menedżerowie/certyfikaty/zapis | Utwórz lub zaktualizuj certyfikaty |
> | Działanie | Microsoft. StorSimple/menedżerowie/certyfikaty/zapis | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Działanie | Microsoft. StorSimple/kierownicy/clearAlerts/akcja | Wyczyść wszystkie alerty skojarzone z menedżerem urządzeń. |
> | Działanie | Microsoft. StorSimple/kierownicy/cloudApplianceConfigurations/odczyt | Wyświetl listę obsługiwanych konfiguracji urządzenia w chmurze |
> | Działanie | Microsoft. StorSimple/kierownicy/configureDevice/akcja | Konfiguruje urządzenie |
> | Działanie | Microsoft. StorSimple/menedżerowie/usuwanie | Usuwa menedżerów urządzeń |
> | Działanie | Microsoft. StorSimple/menedżerowie/usuwanie | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/alertSettings/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/alertSettings/odczyt | Wyświetla lub pobiera ustawienia alertu |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/alertSettings/zapis | Utwórz lub zaktualizuj ustawienia alertu |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/authorizeForServiceEncryptionKeyRollover/akcja | Autoryzuj do przerzucania kluczy szyfrowania usługi |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/kopia zapasowa/akcja | Wykonaj ręczną kopię zapasową, aby utworzyć kopię zapasową wszystkich woluminów chronionych przez zasady. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/Usuń | Usuwa istniejące zasady tworzenia kopii zapasowych (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/odczyt | Utwórz listę zasad tworzenia kopii zapasowych (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/harmonogramy/usuwanie | Usuwa istniejące harmonogramy |
> | Działanie | Microsoft. StorSimple/kierownicy/Devices/backupPolicies/Schedules/operationResults/Read | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/harmonogramy/odczyt | Wyświetlanie listy harmonogramów |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/harmonogramy/zapis | Tworzy harmonogramy nowych lub aktualizacji |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/backupPolicies/zapis | Tworzy nowe lub aktualizuje zasady tworzenia kopii zapasowych (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/usuwanie | Usuwa zestaw kopii zapasowych |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/elementy/klon/akcja | Klonowanie udziału lub woluminu przy użyciu elementu kopii zapasowej. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/elementy/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/odczyt | Wyświetla lub Pobiera zestaw kopii zapasowych |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/przywracanie/akcja | Przywróć wszystkie woluminy z zestawu kopii zapasowych. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupScheduleGroups/Usuń | Usuwa grupy harmonogramów kopii zapasowych |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupScheduleGroups/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/backupScheduleGroups/odczyt | Wyświetla lub pobiera grupy harmonogramów kopii zapasowych |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/backupScheduleGroups/zapis | Tworzenie lub aktualizowanie grup harmonogramów kopii zapasowych |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/chapSettings/Usuń | Usuwa ustawienia protokołu CHAP |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/chapSettings/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/chapSettings/odczyt | Wyświetla lub pobiera ustawienia protokołu CHAP |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/chapSettings/zapis | Utwórz lub zaktualizuj ustawienia protokołu CHAP |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/dezaktywowanie/akcja | Dezaktywuje urządzenie. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/usuwanie | Usuwa urządzenia |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/dyski/odczyt | Wyświetla lub pobiera dyski |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/pobieranie/akcja | Pobierz aktualizacje dla urządzenia. |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/tryb failover/akcja | Przełączenie urządzenia w tryb failover. |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/tryb failover/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/failoverTargets/odczyt | Wyświetla lub Pobiera elementy docelowe trybu failover urządzeń |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/kopia zapasowa/akcja | Utwórz kopię zapasową serwera plików. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/Usuń | Usuwa serwery plików |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/fileservers/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/odczyt | Wyświetla lub pobiera serwery plików |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/usuwanie | Usuwa udziały |
> | Działanie | Microsoft. StorSimple/kierownicy/Devices/fileservers/udziały/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/zapis | Utwórz lub zaktualizuj udziały |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/fileservers/zapis | Utwórz lub zaktualizuj serwery plików |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/hardwareComponentGroups/changeControllerPowerState/akcja | Zmień stan elektrowni sprzętowych grup składników |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/hardwareComponentGroups/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/hardwareComponentGroups/odczyt | Wyświetl listę grup składników sprzętowych |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/instalacja/akcja | Zainstaluj aktualizacje na urządzeniu. |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/installUpdates/akcja | Instaluje aktualizacje na urządzeniach (tylko seria 8000). |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/kopia zapasowa/akcja | Utwórz kopię zapasową serwera iSCSI. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/Usuń | Usuwa serwery iSCSI |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/dyski/usuwanie | Usuwa dyski |
> | Działanie | Microsoft. StorSimple/kierownicy/Devices/iscsiservers/disks/Metrics/Read | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft. StorSimple/kierownicy/Devices/iscsiservers/disks/metricsDefinitions/Read | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft. StorSimple/kierownicy/Devices/iscsiservers/disks/operationResults/Read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/iscsiservers/dyski/odczyt | Wyświetla lub pobiera dyski |
> | Działanie | Microsoft. StorSimple/kierownicy/Devices/iscsiservers/disks/Write | Utwórz lub zaktualizuj dyski |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/iscsiservers/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/odczyt | Wyświetla lub pobiera serwery iSCSI |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/iscsiservers/zapis | Utwórz lub zaktualizuj serwery iSCSI |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/zadania/Anuluj/akcja | Anuluj uruchomione zadanie |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/zadania/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/zadania/odczyt | Wyświetla lub pobiera zadania |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/listFailoverSets/akcja | Wyświetl listę zestawów trybu failover dla istniejącego urządzenia (tylko seria 8000). |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/listFailoverTargets/akcja | Utwórz listę elementów docelowych trybu failover dla urządzeń (tylko seria 8000). |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/confirmMigration/akcja | Potwierdza pomyślną migrację i jej zatwierdzenie. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/confirmMigrationStatus/odczyt | Wyświetlenie listy Potwierdź stan migracji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/fetchConfirmMigrationStatus/akcja | Pobierz potwierdzenie stanu migracji. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/fetchMigrationEstimate/akcja | Pobierz stan zadania szacowania migracji. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/fetchMigrationStatus/akcja | Pobierz stan migracji. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/import/akcja | Importuj konfiguracje źródeł do migracji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/migrationEstimate/odczyt | Wystaw oszacowanie migracji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/migrationStatus/odczyt | Wyświetl stan migracji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/startMigration/akcja | Rozpocznij migrację przy użyciu konfiguracji źródła |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/startMigrationEstimate/akcja | Rozpocznij zadanie, aby oszacować czas trwania procesu migracji. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/networkSettings/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/networkSettings/odczyt | Wyświetla lub pobiera ustawienia sieci |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/networkSettings/zapis | Tworzy nowe lub aktualizuje ustawienia sieci |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/publicEncryptionKey/akcja | Wyświetl publiczny klucz szyfrowania Menedżera urządzeń |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/publishSupportPackage/akcja | Opublikuj pakiet pomocy technicznej dla istniejącego urządzenia. Pakiet pomocy technicznej StorSimple to łatwy w użyciu mechanizm, który gromadzi wszystkie odpowiednie dzienniki, aby ułatwić pomoc techniczna firmy Microsoft Rozwiązywanie problemów z urządzeniami StorSimple. |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/odczyt | Wyświetla lub pobiera urządzenia |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/scanForUpdates/akcja | Skanuj w poszukiwaniu aktualizacji na urządzeniu. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/odczyt | Wyświetl listę ustawień zabezpieczeń |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/syncRemoteManagementCertificate/akcja | Zsynchronizuj certyfikat zarządzania zdalnego dla urządzenia. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/aktualizacja/akcja | Zaktualizuj ustawienia zabezpieczeń. |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/securitySettings/zapis | Tworzy nowe lub aktualizuje ustawienia zabezpieczeń |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/sendTestAlertEmail/akcja | Wyślij wiadomość e-mail z alertem testowym do skonfigurowanych adresatów wiadomości e-mail. |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/timeSettings/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/timeSettings/odczyt | Wyświetla lub pobiera ustawienia czasu |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/timeSettings/zapis | Tworzy nowe lub zaktualizowane ustawienia czasu |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/aktualizacje/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/updateSummary/odczyt | Wyświetla lub pobiera podsumowanie aktualizacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/Usuń | Usuwa istniejące kontenery woluminów (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/volumeContainers/metryki/odczyt | Wyświetl metryki |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/metricsDefinitions/odczyt | Wyświetl listę definicji metryk |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/odczyt | Utwórz listę kontenerów woluminów (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/usuwanie | Usuwa istniejące woluminy |
> | Działanie | Microsoft. StorSimple/kierownicy/Devices/volumeContainers/Volumes/Metrics/Read | Wyświetl metryki |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/metricsDefinitions/odczyt | Wyświetl listę definicji metryk |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/operationResults/odczyt | Wyświetl listę wyników operacji |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/odczyt | Wyświetl listę woluminów |
> | Działanie | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/zapis | Tworzy nowe lub aktualizuje woluminy |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/volumeContainers/zapis | Tworzy nowe lub zaktualizowane kontenery woluminów (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/woluminy/odczyt | Wyświetl listę woluminów |
> | Działanie | Microsoft. StorSimple/kierownicy/urządzenia/zapis | Utwórz lub zaktualizuj urządzenia |
> | Działanie | Microsoft. StorSimple/kierownicy/encryptionSettings/odczyt | Wyświetla lub pobiera ustawienia szyfrowania |
> | Działanie | Microsoft. StorSimple/menedżerowie/extendedInformation/Usuń | Usuwa informacje o rozszerzonym magazynie |
> | Działanie | Microsoft. StorSimple/menedżerowie/extendedInformation/Usuń | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft. StorSimple/kierownicy/extendedInformation/odczyt | Wyświetla lub pobiera rozszerzone informacje o magazynie |
> | Działanie | Microsoft. StorSimple/kierownicy/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft. StorSimple/kierownicy/extendedInformation/Write | Utwórz lub zaktualizuj informacje o rozszerzonym magazynie |
> | Działanie | Microsoft. StorSimple/kierownicy/extendedInformation/Write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Działanie | Microsoft. StorSimple/menedżerowie/funkcje/odczyt | Wyświetl listę funkcji |
> | Działanie | Microsoft. StorSimple/kierownicy/fileservers/odczyt | Wyświetla lub pobiera serwery plików |
> | Działanie | Microsoft. StorSimple/kierownicy/getEncryptionKey/akcja | Pobierz klucz szyfrowania dla Menedżera urządzeń. |
> | Działanie | Microsoft. StorSimple/kierownicy/iscsiservers/odczyt | Wyświetla lub pobiera serwery iSCSI |
> | Działanie | Microsoft. StorSimple/menedżerowie/zadania/odczyt | Wyświetla lub pobiera zadania |
> | Działanie | Microsoft. StorSimple/kierownicy/listActivationKey/akcja | Pobiera klucz aktywacji Menedżer urządzeń StorSimple. |
> | Działanie | Microsoft. StorSimple/kierownicy/listPublicEncryptionKey/akcja | Wyświetl listę publicznych kluczy szyfrowania StorSimple Menedżer urządzeń. |
> | Działanie | Microsoft. StorSimple/menedżerowie/metryki/odczyt | Wyświetla lub pobiera metryki |
> | Działanie | Microsoft. StorSimple/kierownicy/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Działanie | Microsoft. StorSimple/kierownicy/migrateClassicToResourceManager/akcja | Migrowanie klasyczne do Menedżer zasobów menedżerów |
> | Działanie | Microsoft. StorSimple/kierownicy/migrationSourceConfigurations/odczyt | Wyświetl listę konfiguracji źródła migracji (tylko seria 8000) |
> | Działanie | Microsoft. StorSimple/kierownicy/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/kierownicy/provisionCloudAppliance/akcja | Utwórz nowe urządzenie w chmurze. |
> | Działanie | Microsoft. StorSimple/menedżerowie/odczyt | Wyświetla lub pobiera menedżerów urządzeń |
> | Działanie | Microsoft. StorSimple/menedżerowie/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft. StorSimple/kierownicy/regenerateActivationKey/akcja | Wygeneruj ponownie klucz aktywacji dla istniejących Menedżer urządzeń StorSimple. |
> | Działanie | Microsoft. StorSimple/menedżerowie/storageAccountCredentials/Usuń | Usuwa poświadczenia konta magazynu |
> | Działanie | Microsoft. StorSimple/menedżerowie/storageAccountCredentials/operationResults/Read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/kierownicy/storageAccountCredentials/odczyt | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Działanie | Microsoft. StorSimple/kierownicy/storageAccountCredentials/Write | Utwórz lub zaktualizuj poświadczenia konta magazynu |
> | Działanie | Microsoft. StorSimple/menedżerowie/storageDomains/Usuń | Usuwa domeny magazynu |
> | Działanie | Microsoft. StorSimple/menedżerowie/storageDomains/operationResults/Read | Wyświetla lub pobiera wyniki operacji |
> | Działanie | Microsoft. StorSimple/kierownicy/storageDomains/odczyt | Wyświetla lub pobiera domeny magazynu |
> | Działanie | Microsoft. StorSimple/kierownicy/storageDomains/Write | Utwórz lub zaktualizuj domeny magazynu |
> | Działanie | Microsoft. StorSimple/menedżerowie/zapis | Utwórz lub zaktualizuj menedżerów urządzeń |
> | Działanie | Microsoft. StorSimple/menedżerowie/zapis | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |
> | Działanie | Microsoft. StorSimple/Operations/Read | Wyświetla lub pobiera operacje |
> | Działanie | Microsoft. StorSimple/Register/Action | Zarejestruj dostawcę Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. StreamAnalytics/lokalizacje/przydziały/odczyt | Odczytaj Stream Analytics przydziału subskrypcji |
> | Działanie | Microsoft. StreamAnalytics/Operations/Read | Odczyt Stream Analytics operacji |
> | Działanie | Microsoft. StreamAnalytics/Register/Action | Zarejestruj subskrypcję za pomocą dostawcy zasobów Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Delete | Usuwanie zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Functions/Delete | Usuwanie funkcji zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Functions/operationresults/Read | Wyniki operacji odczytu dla funkcji zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Functions/Read | Odczytaj Stream Analytics funkcji zadania |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Functions/RetrieveDefaultDefinition/Action | Pobieranie domyślnej definicji funkcji zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Functions/test/Action | Funkcja zadania Stream Analytics testowego |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Functions/Write | Zapisz funkcję zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Input/Delete | Usuń dane wejściowe zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Inputs/operationresults/Read | Wyniki operacji odczytu dla danych wejściowych zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Inputs/Read | Odczytaj Stream Analytics dane wejściowe zadania |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Input/Sample/Action | Przykładowe dane wejściowe zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Inputs/test/Action | Dane wejściowe zadania Stream Analytics testu |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Inputs/Write | Zapisz dane wejściowe zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/metricdefinitions/Read | Odczytaj definicje metryk |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/operationresults/Read | Odczytaj wyniki operacji dla zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Output/Delete | Usuń dane wyjściowe zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Output/operationresults/Read | Wyniki operacji odczytu dla danych wyjściowych zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Output/odczyt | Odczytaj Stream Analytics dane wyjściowe zadania |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/wyjście/test/akcja | Testowanie danych wyjściowych zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Output/Write | Zapisz dane wyjściowe zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne. |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/diagnosticSettings/Write | Zapisz ustawienie diagnostyczne. |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla streamingjobs |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla streamingjobs |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/odczyt | Odczytaj Stream Analytics zadania |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Start/akcja | Uruchom zadanie Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Stop/akcja | Zatrzymaj zadanie Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Transformations/Delete | Usuń transformację zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Transformations/Read | Odczytaj Stream Analytics przekształcenie zadania |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Transformations/Write | Zapisz transformację zadania Stream Analytics |
> | Działanie | Microsoft. StreamAnalytics/streamingjobs/Write | Stream Analytics zadania zapisu |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Subscription/Anuluj/akcja | Anuluje subskrypcję |
> | Działanie | Microsoft. subskrypcja/subskrypcja/akcja | Tworzenie subskrypcji platformy Azure |
> | Działanie | Microsoft. Subscription/rejestr/akcja | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. Subscription |
> | Działanie | Microsoft. Subscription/zmiana nazwy/akcji | Zmienia nazwę subskrypcji |
> | Działanie | Microsoft. Subscription/SubscriptionDefinitions/Read | Pobierz definicję subskrypcji platformy Azure w grupie zarządzania. |
> | Działanie | Microsoft. Subscription/SubscriptionDefinitions/Write | Tworzenie definicji subskrypcji platformy Azure |

## <a name="microsoftsupport"></a>Microsoft. Support

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft.Support/register/action | Przeprowadza rejestrację u dostawcy zasobów pomocy technicznej |
> | Działanie | Microsoft.Support/supportTickets/read | Pobiera szczegóły biletu pomocy technicznej (w tym stan, ważność, szczegóły dotyczące kontaktu i komunikacji) lub pobiera listę biletów pomocy technicznej dla różnych subskrypcji. |
> | Działanie | Microsoft.Support/supportTickets/write | Tworzy lub aktualizuje bilet pomocy technicznej. Można utworzyć bilet pomocy technicznej dotyczący problemów technicznych, rozliczeń, limitów przydziału lub problemów związanych z zarządzaniem subskrypcją. Można także zaktualizować ważność, szczegóły dotyczące kontaktu i komunikacji dla istniejących biletów pomocy technicznej. |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Delete | Usuwa zasady dostępu. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Read | Pobierz właściwości zasad dostępu. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Write | Tworzy nowe zasady dostępu dla środowiska lub aktualizuje istniejące zasady dostępu. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/Delete | Usuwa środowisko. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/eventsources/Delete | Usuwa Źródło zdarzenia. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/eventsources/Read | Pobierz właściwości źródła zdarzeń. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/eventsources/Write | Tworzy nowe źródło zdarzenia dla środowiska lub aktualizuje istniejące źródło zdarzeń. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/Read | Pobierz właściwości środowiska. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/referencedatasets/Delete | Usuwa zestaw danych referencyjnych. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/referencedatasets/Read | Pobierz właściwości zestawu danych referencyjnych. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/referencedatasets/Write | Tworzy nowy zestaw danych referencyjnych dla środowiska lub aktualizuje istniejący zestaw danych referencyjnych. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/status/Read | Pobierz stan środowiska, stan skojarzonych operacji, takich jak ruch przychodzący. |
> | Działanie | Microsoft. TimeSeriesInsights/Environments/Write | Tworzy nowe środowisko lub aktualizuje istniejące środowisko. |
> | Działanie | Microsoft. TimeSeriesInsights/Register/Action | Rejestruje subskrypcję dostawcy zasobów Time Series Insights i umożliwia tworzenie środowisk Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. VisualStudio/Account/Delete | Usuń konto |
> | Działanie | Microsoft. VisualStudio/Account/Extension/Read | Odczytaj konto/rozszerzenie |
> | Działanie | Microsoft. VisualStudio/Account/Project/Read | Odczytaj konto/projekt |
> | Działanie | Microsoft. VisualStudio/Account/Project/Write | Ustaw konto/projekt |
> | Działanie | Microsoft. VisualStudio/Account/Read | Odczytaj konto |
> | Działanie | Microsoft. VisualStudio/Account/Write | Ustaw konto |
> | Działanie | Microsoft. VisualStudio/rozszerzenie/usuwanie | Usuń rozszerzenie |
> | Działanie | Microsoft. VisualStudio/rozszerzenie/odczyt | Odczyt rozszerzenia |
> | Działanie | Microsoft. VisualStudio/rozszerzenie/zapis | Ustaw rozszerzenie |
> | Działanie | Microsoft. VisualStudio/Project/Delete | Usuń projekt |
> | Działanie | Microsoft. VisualStudio/Project/Read | Odczytaj projekt |
> | Działanie | Microsoft. VisualStudio/Project/Write | Ustaw projekt |
> | Działanie | Microsoft. VisualStudio/Register/Action | Zarejestruj subskrypcję platformy Azure przy użyciu dostawcy Microsoft. VisualStudio |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Web/apimanagementaccounts/apiacls/odczyt | Pobierz Apiacls kont usługi API Management. |
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
> | Działanie | Microsoft. Web/apimanagementaccounts/connectionacls/odczyt | Pobierz Connectionacls kont usługi API Management. |
> | Działanie | Microsoft. Web/availablestacks/odczyt | Pobierz dostępne stosy. |
> | Działanie | Microsoft. Web/certyfikaty/usuwanie | Usuń istniejący certyfikat. |
> | Działanie | Microsoft. Web/Certificates/Read | Pobierz listę certyfikatów. |
> | Działanie | Microsoft. Web/Certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejący. |
> | Działanie | Microsoft. Web/checknameavailability/odczyt | Sprawdź, czy nazwa zasobu jest dostępna. |
> | Działanie | Microsoft. Web/classicmobileservices/odczyt | Pobierz klasyczne Mobile Services. |
> | Działanie | Microsoft. Web/connectionGateways/Delete | Usuwa bramę połączenia. |
> | Działanie | Microsoft. Web/connectionGateways/Join/Action | Sprzęga bramę połączenia. |
> | Działanie | Microsoft. Web/connectionGateways/ListStatus/akcja | Wyświetla stan bramy połączenia. |
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
> | Działanie | Microsoft. Web/customApis/extractApiDefinitionFromWsdl/akcja | Wyodrębnia definicję interfejsu API z WSDL. |
> | Działanie | Microsoft. Web/customApis/Join/Action | Sprzęga niestandardowy interfejs API. |
> | Działanie | Microsoft. Web/customApis/listWsdlInterfaces/akcja | Wyświetla listę interfejsów WSDL dla niestandardowego interfejsu API. |
> | Działanie | Microsoft. Web/customApis/Przenieś/akcja | Przenosi niestandardowy interfejs API. |
> | Działanie | Microsoft. Web/customApis/odczyt | Pobierz listę niestandardowych interfejsów API. |
> | Działanie | Microsoft. Web/customApis/Write | Tworzy lub aktualizuje niestandardowy interfejs API. |
> | Działanie | Microsoft. Web/deletedSites/odczyt | Pobierz właściwości usuniętej aplikacji sieci Web |
> | Działanie | Microsoft. Web/deploymentlocations/odczyt | Pobierz lokalizacje wdrożenia. |
> | Działanie | Microsoft. Web/georegions/Read | Pobierz listę regionów geograficznych. |
> | Działanie | Microsoft. Web/hostingenvironments/pojemności/odczyt | Uzyskaj możliwości środowiska hostingu. |
> | Działanie | Microsoft. Web/hostingEnvironments/Delete | Usuwanie App Service Environment |
> | Działanie | Microsoft. Web/hostingenvironments/detektory/odczyt | Pobierz detektory środowisk hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/Diagnostyka/odczyt | Uzyskaj diagnostykę środowisk hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/inboundnetworkdependenciesendpoints/odczyt | Pobierz punkty końcowe sieci wszystkich zależności przychodzących. |
> | Działanie | Microsoft. Web/hostingEnvironments/Join/Action | Sprzęga App Service Environment |
> | Działanie | Microsoft. Web/hostingenvironments/metricdefinitions/odczyt | Pobierz definicje metryk środowisk hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/multirolepools/metricdefinitions/odczyt | Pobierz definicje metryk pul wielu ról środowiska hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/multirolepools/Metrics/Read | Uzyskaj metryki pul wielu ról. |
> | Działanie | Microsoft. Web/hostingEnvironments/multiRolePools/odczyt | Pobierz właściwości puli frontonu w App Service Environment |
> | Działanie | Microsoft. Web/hostingenvironments/multirolepools/SKU/odczyt | Uzyskaj środowiska hostingu pule wieloról jednostek SKU. |
> | Działanie | Microsoft. Web/hostingenvironments/multirolepools/Usages/Read | Uzyskaj środowiska hostingu użycia pul wielorolach. |
> | Działanie | Microsoft. Web/hostingEnvironments/multiRolePools/zapis | Utwórz nową pulę frontonu w App Service Environment lub zaktualizuj istniejącą |
> | Działanie | Microsoft. Web/hostingenvironments/Operations/Read | Pobierz operacje środowiska hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/outboundnetworkdependenciesendpoints/odczyt | Pobierz punkty końcowe sieci wszystkich zależności wychodzących. |
> | Działanie | Microsoft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/akcja | Zatwierdzanie połączeń prywatnych punktów końcowych |
> | Działanie | Microsoft. Web/hostingEnvironments/odczyt | Pobierz właściwości App Service Environment |
> | Działanie | Microsoft. Web/hostingEnvironments/ponowny rozruch/akcja | Uruchom ponownie wszystkie maszyny w App Service Environment |
> | Działanie | Microsoft. Web/hostingenvironments/Resume/akcja | Wznów środowiska hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/dopuszczalna/odczyt | Pobierz środowiska hostingu App Service plany. |
> | Działanie | Microsoft. Web/hostingenvironments/sites/odczyt | Pobierz środowiska hostingu Web Apps. |
> | Działanie | Microsoft. Web/hostingenvironments/Suspend/akcja | Wstrzymywanie środowisk hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/Usages/Read | Uzyskaj użycie środowisk hostingu. |
> | Działanie | Microsoft. Web/hostingenvironments/workerpools/metricdefinitions/odczyt | Pobierz środowiska hostingu Workerpools definicje metryki. |
> | Działanie | Microsoft. Web/hostingenvironments/workerpools/Metrics/Read | Uzyskaj środowiska hostingu Workerpools metryki. |
> | Działanie | Microsoft. Web/hostingEnvironments/workerPools/odczyt | Pobierz właściwości puli procesów roboczych w App Service Environment |
> | Działanie | Microsoft. Web/hostingenvironments/workerpools/SKU/odczyt | Uzyskaj środowiska hostingu Workerpools jednostki SKU. |
> | Działanie | Microsoft. Web/hostingenvironments/workerpools/Usages/Read | Uzyskaj środowiska hostingu Workerpools użycia. |
> | Działanie | Microsoft. Web/hostingEnvironments/workerPools/zapis | Utwórz nową pulę procesów roboczych w App Service Environment lub zaktualizuj istniejącą |
> | Działanie | Microsoft. Web/hostingEnvironments/Write | Utwórz nowy App Service Environment lub zaktualizuj istniejący |
> | Działanie | Microsoft. Web/ishostingenvironmentnameavailable/odczyt | Pobierz, jeśli nazwa środowiska hostingu jest dostępna. |
> | Działanie | Microsoft. Web/ishostnameavailable/odczyt | Sprawdź, czy nazwa hosta jest dostępna. |
> | Działanie | Microsoft. Web/isusernameavailable/odczyt | Sprawdź, czy nazwa użytkownika jest dostępna. |
> | Działanie | Microsoft. Web/listSitesAssignedToHostName/odczyt | Pobieranie nazw lokacji przypisanych do nazwy hosta. |
> | Działanie | Microsoft. Web/Locations/apioperations/Read | Pobieranie lokalizacji operacji interfejsu API. |
> | Działanie | Microsoft. Web/Locations/connectiongatewayinstallations/Read | Pobierz lokalizacje instalacji bramy połączeń. |
> | Działanie | Microsoft. Web/Locations/deleteVirtualNetworkOrSubnets/Action | Powiadomienie o usunięciu sieci wirtualnej lub podsieci dla lokalizacji. |
> | Działanie | Microsoft. Web/Locations/extractapidefinitionfromwsdl/Action | Wyodrębnij definicję interfejsu API z WSDL dla lokalizacji. |
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
> | Działanie | Microsoft. Web/dopuszczalna/firstpartyapps/Settings/Delete | Usuń ustawienia App Service plany aplikacji pierwszej strony. |
> | Działanie | Microsoft. Web/dopuszczalna/firstpartyapps/Settings/Read | Pobierz App Service plany. |
> | Działanie | Microsoft. Web/dopuszczalna/firstpartyapps/Settings/Write | Aktualizacja App Service planuje ustawienia aplikacji innych firm. |
> | Działanie | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/Relay/usuwanie | Usuwanie App Service planów przestrzenie nazw połączeń hybrydowych przekaźników. |
> | Działanie | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/Relay/odczyt | Pobierz App Service plany przestrzeni nazw połączenia hybrydowego. |
> | Działanie | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/przekaźniki/witryny/odczyt | Pobierz App Service plany przestrzeń nazw połączenia hybrydowego przekazuje Web Apps. |
> | Działanie | Microsoft. Web/dopuszczalna/hybridconnectionplanlimits/odczyt | Pobierz limity planu połączeń hybrydowych planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/hybridconnectionrelays/odczyt | Pobierz App Service plany połączeń hybrydowych. |
> | Działanie | Microsoft. Web/dopuszczalna/metricdefinitions/odczyt | Pobierz definicje metryk planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/Metrics/Read | Pobierz metryki planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/operationresults/odczyt | Pobierz wyniki operacji planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/odczyt | Pobierz właściwości planu App Service |
> | Działanie | Microsoft. Web/dopuszczalna/restartSites/akcja | Uruchom ponownie wszystkie Web Apps w planie App Service |
> | Działanie | Microsoft. Web/dopuszczalna/sites/odczyt | Pobierz Web Apps planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/SKU/odczyt | Pobierz jednostki SKU planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/Usages/Read | Pobierz użycia planów App Service. |
> | Działanie | Microsoft. Web/dopuszczalna/virtualnetworkconnections/Gateways/Write | App Service plany Virtual Network połączeń z bramą. |
> | Działanie | Microsoft. Web/dopuszczalna/virtualnetworkconnections/odczyt | Pobierz App Service plany Virtual Network połączeń. |
> | Działanie | Microsoft. Web/dopuszczalna/virtualnetworkconnections/Routes/Delete | Usuń App Service plany Virtual Network połączeń. |
> | Działanie | Microsoft. Web/dopuszczalna/virtualnetworkconnections/Routes/Read | Pobierz App Service plany Virtual Network połączeń. |
> | Działanie | Microsoft. Web/dopuszczalna/virtualnetworkconnections/Routes/write | Aktualizowanie App Service planów Virtual Network połączeń. |
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
> | Działanie | Microsoft. Web/Sites/Diagnostyka/failedrequestsperuri/odczyt | Pobierz Nieudane żądania diagnostyki Web Apps na identyfikator URI. |
> | Działanie | Microsoft. Web/Sites/Diagnostyka/frebanalysis/odczyt | Web Apps Pobierz analizę FREB diagnostyki. |
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
> | Działanie | Microsoft. Web/Sites/rozszerzenia/usuwanie | Usuń rozszerzenia Web Apps lokacji. |
> | Działanie | Microsoft. Web/Sites/rozszerzenia/odczyt | Pobierz rozszerzenia witryny Web Apps. |
> | Działanie | Microsoft. Web/Sites/rozszerzenia/zapis | Aktualizowanie rozszerzeń lokacji Web Apps. |
> | Działanie | Microsoft. Web/Sites/Functions/Action | Web Apps funkcji. |
> | Działanie | Microsoft. Web/Sites/Functions/Delete | Usuń funkcje Web Apps. |
> | Działanie | Microsoft. Web/Sites/funkcje/klucze/usuwanie | Usuń klucze funkcji. |
> | Działanie | Microsoft. Web/Sites/Functions/Keys/Write | Zaktualizuj klucze funkcji. |
> | Działanie | Microsoft. Web/Sites/Functions/ListKeys/Action | Wyświetl klucze funkcji. |
> | Działanie | Microsoft. Web/Sites/Functions/listsecrets/Action | Utwórz listę wpisów tajnych funkcji. |
> | Działanie | Microsoft. Web/Sites/Functions/MasterKey/Read | Pobierz funkcje Web Apps MasterKey. |
> | Działanie | Microsoft. Web/Sites/Functions/Properties/Read | Odczytywanie właściwości Web Apps funkcji. |
> | Działanie | Microsoft. Web/Sites/Functions/Properties/Write | Aktualizowanie właściwości funkcji Web Apps. |
> | Działanie | Microsoft. Web/Sites/Functions/Read | Pobierz funkcje Web Apps. |
> | Działanie | Microsoft. Web/Sites/Functions/token/Read | Pobierz token funkcji Web Apps. |
> | Działanie | Microsoft. Web/Sites/Functions/Write | Aktualizowanie funkcji Web Apps. |
> | Działanie | Microsoft. Web/Sites/Host/functionkeys/Delete | Usuń klucze funkcji hosta funkcji. |
> | Działanie | Microsoft. Web/Sites/Host/functionkeys/Write | Aktualizowanie kluczy funkcji hosta funkcji. |
> | Działanie | Microsoft. Web/Sites/Host/ListKeys/akcja | Lista kluczy hosta funkcji. |
> | Działanie | Microsoft. Web/Sites/Host/listsyncstatus/akcja | Lista Stanów wyzwalaczy funkcji synchronizacji. |
> | Działanie | Microsoft. Web/Sites/Host/właściwości/odczyt | Odczytywanie właściwości hosta funkcji Web Apps Functions. |
> | Działanie | Microsoft. Web/Sites/Host/synchronizacja/akcja | Wyzwalacze funkcji synchronizacji. |
> | Działanie | Microsoft. Web/Sites/Host/systemkeys/Delete | Usuwanie funkcji klucze systemowe hosta. |
> | Działanie | Microsoft. Web/Sites/Host/systemkeys/Write | Aktualizowanie kluczy systemu hosta funkcji. |
> | Działanie | Microsoft. Web/Sites/hostnamebindings/Delete | Usuń powiązania Web Apps nazwy hosta. |
> | Działanie | Microsoft. Web/Sites/hostnamebindings/odczyt | Pobierz powiązania Web Apps nazwy hosta. |
> | Działanie | Microsoft. Web/Sites/hostnamebindings/Write | Aktualizowanie powiązań nazwy hosta Web Apps. |
> | Działanie | Microsoft. Web/Sites/hostruntime/Functions/Keys/odczyt | Pobierz Web Apps klucze funkcji Hostruntime. |
> | Działanie | Microsoft. Web/Sites/hostruntime/Host/_master/Read | Pobierz klucz główny aplikacja funkcji dla operacji administracyjnych |
> | Działanie | Microsoft. Web/Sites/hostruntime/Host/akcja | Wykonywanie akcji środowiska uruchomieniowego aplikacja funkcji, takich jak wyzwalacze synchronizacji, Dodawanie funkcji, wywoływanie funkcji, funkcje Delete itd. |
> | Działanie | Microsoft. Web/Sites/hostruntime/Host/odczyt | Pobierz Web Apps hosta Hostruntime. |
> | Działanie | Microsoft. Web/Sites/hybridconnection/Delete | Usuń połączenie hybrydowe Web Apps. |
> | Działanie | Microsoft. Web/Sites/hybridconnection/odczyt | Pobierz Web Apps połączenie hybrydowe. |
> | Działanie | Microsoft. Web/Sites/hybridconnection/Write | Aktualizacja Web Apps połączenie hybrydowe. |
> | Działanie | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/usuwanie | Usuń Web Apps przekaźników przestrzeni nazw połączenia hybrydowego. |
> | Działanie | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/ListKeys/Action | Klucze List Web Apps przekaźników przestrzeni nazw połączeń hybrydowych. |
> | Działanie | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/odczyt | Uzyskaj Web Apps przekaźników przestrzeni nazw połączenia hybrydowego. |
> | Działanie | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/Write | Aktualizacja Web Apps przekaźników przestrzeni nazw połączenia hybrydowego. |
> | Działanie | Microsoft. Web/Sites/hybridconnectionrelays/odczyt | Pobierz Web Apps przekaźników połączeń hybrydowych. |
> | Działanie | Microsoft. Web/Sites/Instances/Deployments/Delete | Usuń wdrożenia wystąpień Web Apps. |
> | Działanie | Microsoft. Web/Sites/Instances/Deployments/Read | Pobierz wdrożenia wystąpień Web Apps. |
> | Działanie | Microsoft. Web/Sites/Instances/Extensions/log/Read | Pobierz dziennik rozszerzeń wystąpień Web Apps. |
> | Działanie | Microsoft. Web/Sites/Instances/Extensions/processs/Read | Pobierz procesy rozszerzenia Web Apps Instances. |
> | Działanie | Microsoft. Web/Sites/Instances/Extensions/Read | Pobierz rozszerzenia wystąpień Web Apps. |
> | Działanie | Microsoft. Web/Sites/Instances/processs/Delete | Usuwanie procesów wystąpień Web Apps. |
> | Działanie | Microsoft. Web/Sites/Instances/processs/modules/Read | Pobierz wystąpienia Web Apps procesy przetwarzają moduły. |
> | Działanie | Microsoft. Web/Sites/Instances/processs/Read | Pobierz procesy Web Apps wystąpień. |
> | Działanie | Microsoft. Web/Sites/Instances/processs/Threads/Read | Pobierz wystąpienia Web Apps przetwarza wątki. |
> | Działanie | Microsoft. Web/Sites/Instances/Read | Pobierz wystąpienia Web Apps. |
> | Działanie | Microsoft. Web/Sites/listsyncfunctiontriggerstatus/akcja | Wyświetl stan wyzwalacza funkcji synchronizacji. |
> | Działanie | Microsoft. Web/Sites/metricdefinitions/odczyt | Pobierz definicje metryk Web Apps. |
> | Działanie | Microsoft. Web/Sites/Metrics/odczyt | Pobierz metryki Web Apps. |
> | Działanie | Microsoft. Web/Sites/metricsdefinitions/odczyt | Pobierz definicje metryk Web Apps. |
> | Działanie | Microsoft. Web/Sites/migratemysql/akcja | Migrowanie Web Apps MySql. |
> | Działanie | Microsoft. Web/Sites/migratemysql/odczyt | Pobierz Web Apps przeprowadzić migrację bazy danych MySql. |
> | Działanie | Microsoft. Web/Sites/networktrace/akcja | Web Apps śledzenia sieci. |
> | Działanie | Microsoft. Web/Sites/networktraces/operationresults/odczyt | Pobierz wyniki operacji śledzenia sieci Web Apps. |
> | Działanie | Microsoft. Web/Sites/NoweHasło/akcja | NoweHasło Web Apps. |
> | Działanie | Microsoft. Web/Sites/operationresults/odczyt | Pobierz wyniki operacji Web Apps. |
> | Działanie | Microsoft. Web/Sites/Operations/Read | Pobierz Web Apps operacji. |
> | Działanie | Microsoft. Web/Sites/perfcounters/odczyt | Pobierz Web Apps liczniki wydajności. |
> | Działanie | Microsoft. Web/Sites/premieraddons/Delete | Usuń Web Apps Dodatki Premier. |
> | Działanie | Microsoft. Web/Sites/premieraddons/odczyt | Uzyskaj Web Apps Dodatki Premier. |
> | Działanie | Microsoft. Web/Sites/premieraddons/Write | Aktualizacja dodatków do Web Apps Premier. |
> | Działanie | Microsoft. Web/Sites/privateaccess/odczyt | Uzyskaj dane dotyczące włączania dostępu do lokacji prywatnej i autoryzowanych sieci wirtualnych, które mogą uzyskać dostęp do witryny. |
> | Działanie | Microsoft. Web/Sites/PrivateEndpointConnectionsApproval/akcja | Zatwierdzanie połączeń prywatnych punktów końcowych |
> | Działanie | Microsoft. Web/Sites/processs/modules/Read | Pobierz moduły Web Apps procesów. |
> | Działanie | Microsoft. Web/Sites/processs/Read | Pobierz Web Apps procesy. |
> | Działanie | Microsoft. Web/Sites/processs/Threads/Read | Pobierz wątki procesów Web Apps. |
> | Działanie | Microsoft. Web/Sites/publiccertificates/Delete | Usuń Web Apps certyfikaty publiczne. |
> | Działanie | Microsoft. Web/Sites/publiccertificates/odczyt | Pobierz Web Apps certyfikaty publiczne. |
> | Działanie | Microsoft. Web/Sites/publiccertificates/Write | Aktualizowanie Web Apps certyfikatów publicznych. |
> | Działanie | Microsoft. Web/Sites/Publikuj/akcja | Publikowanie aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/publishxml/akcja | Pobieranie pliku XML profilu publikowania dla aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/publishxml/odczyt | Pobierz Web Apps publikowania XML. |
> | Działanie | Microsoft. Web/Sites/odczyt | Pobieranie właściwości aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/recommendationhistory/odczyt | Pobierz historię rekomendacji Web Apps. |
> | Działanie | Microsoft. Web/witryny/zalecenia/Wyłącz/akcję | Wyłącz zalecenia dotyczące Web Apps. |
> | Działanie | Microsoft. Web/Sites/zalecenia/odczyt | Pobierz listę zaleceń dotyczących aplikacji sieci Web. |
> | Działanie | Microsoft. Web/Sites/odzyskiwanie/akcja | Odzyskaj Web Apps. |
> | Działanie | Microsoft. Web/Sites/resetSlotConfig/akcja | Zresetuj konfigurację aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/resourcehealthmetadata/odczyt | Pobierz metadane Resource Health Web Apps. |
> | Działanie | Microsoft. Web/Sites/restart/akcja | Ponowne uruchamianie aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Restore/Read | Pobierz Web Apps przywracanie. |
> | Działanie | Microsoft. Web/Sites/Restore/Write | Przywracanie Web Apps. |
> | Działanie | Microsoft. Web/Sites/restorefrombackupblob/akcja | Przywróć aplikację internetową z obiektu BLOB kopii zapasowej. |
> | Działanie | Microsoft. Web/Sites/restorefromdeletedapp/akcja | Przywracanie Web Apps z usuniętej aplikacji. |
> | Działanie | Microsoft. Web/Sites/restoresnapshot/akcja | Przywracanie migawek Web Apps. |
> | Działanie | Microsoft. Web/Sites/siteextensions/Delete | Usuń rozszerzenia Web Apps lokacji. |
> | Działanie | Microsoft. Web/Sites/siteextensions/odczyt | Pobierz rozszerzenia witryny Web Apps. |
> | Działanie | Microsoft. Web/Sites/siteextensions/Write | Aktualizowanie rozszerzeń lokacji Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/analyzecustomhostname/Read | Uzyskaj Web Apps gniazda analizują niestandardową nazwę hosta. |
> | Działanie | Microsoft. Web/Sites/szczeliny/applySlotConfig/akcja | Zastosuj konfigurację miejsca aplikacji sieci Web z gniazda docelowego do bieżącego gniazda. |
> | Działanie | Microsoft. Web/Sites/gniazda/kopia zapasowa/akcja | Utwórz nową kopię zapasową miejsca aplikacji sieci Web. |
> | Działanie | Microsoft. Web/Sites/gniazda/kopia zapasowa/odczyt | Pobierz kopie zapasowe Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/gniazda/kopia zapasowa/zapis | Aktualizacja kopii zapasowej Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/gniazda/kopie zapasowe/akcja | Odkryj kopie zapasowe Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/gniazda/kopie zapasowe/usuwanie | Usuń kopie zapasowe miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/szczeliny/kopie zapasowe/lista/akcja | Utwórz listę kopii zapasowych miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/gniazda/kopie zapasowe/odczyt | Pobierz właściwości kopii zapasowej miejsc aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/gniazda/kopie zapasowe/przywracanie/akcja | Przywracanie kopii zapasowych miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/szczeliny/konfiguracja/usuwanie | Usuń konfigurację gniazd Web Apps. |
> | Działanie | Microsoft. Web/Sites/szczeliny/konfiguracja/lista/akcja | Wyświetlanie ustawień poufnych informacji o zabezpieczeniach miejsca aplikacji sieci Web, takich jak poświadczenia publikowania, ustawienia aplikacji i parametry połączenia |
> | Działanie | Microsoft. Web/Sites/szczeliny/konfiguracja/odczyt | Pobierz ustawienia konfiguracji miejsca aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Slots/config/Write | Zaktualizuj ustawienia konfiguracji miejsca aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/szczeliny/containerlogs/akcja | Pobierz spakowane dzienniki kontenerów dla miejsca aplikacji sieci Web. |
> | Działanie | Microsoft. Web/Sites/szczeliny/containerlogs/pobieranie/akcja | Pobierz dzienniki kontenerów Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/Slots/continuouswebjobs/Delete | Usuń Web Apps miejsca ciągłe zadania w sieci Web. |
> | Działanie | Microsoft. Web/Sites/Slots/continuouswebjobs/Read | Uzyskaj miejsca na Web Apps ciągłe zadania w sieci Web. |
> | Działanie | Microsoft. Web/Sites/szczeliny/continuouswebjobs/Start/akcja | Rozpocznij Web Apps miejsca na ciągłe zadania w sieci Web. |
> | Działanie | Microsoft. Web/Sites/szczeliny/continuouswebjobs/akcja | Zatrzymaj Web Apps miejsca na ciągłe zadania w sieci Web. |
> | Działanie | Microsoft. Web/Sites/Slots/Delete | Usuń istniejące miejsce aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Slots/Deployments/Delete | Usuń wdrożenia Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Deployments/log/Read | Pobierz dziennik wdrożeń Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Deployments/Read | Pobierz wdrożenia z Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Deployments/Write | Aktualizowanie wdrożeń na Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/gniazda/detektory/odczyt | Pobierz Web Apps wykrywaczy gniazd. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/analiza/wykonywanie/akcja | Uruchom analizę diagnostyki gniazd Web Apps. |
> | Działanie | Microsoft. Web/Sites/szczeliny/Diagnostyka/analizy/odczyt | Pobierz analizę diagnostyki miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/aspnetcore/Read | Pobierz diagnostykę Web Appsych miejsc dla aplikacji ASP.NET Core. |
> | Działanie | Microsoft. Web/Sites/szczeliny/Diagnostyka/autozabliźnione/odczyt | Pobierz Web Apps autozabliźnione diagnostyki miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/Deployment/Read | Pobierz wdrożenie diagnostyki miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/Deployments/Read | Pobierz Web Apps wdrożenia diagnostyczne. |
> | Działanie | Microsoft. Web/Sites/szczeliny/Diagnostyka/detektory/wykonaj/akcję | Uruchom detektor diagnostyki Web Appsych gniazd. |
> | Działanie | Microsoft. Web/Sites/szczeliny/Diagnostyka/detektory/odczyt | Pobierz detektor diagnostyki Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/frebanalysis/Read | Pobierz Web Apps FREB analiza diagnostyki gniazd. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/loganalyzer/Read | Pobierz Analizator dziennika diagnostyki gniazd Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/Read | Pobierz diagnostykę Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/runtimeavailability/Read | Pobierz dostępność w czasie wykonywania diagnostyki Web Apps gniazd. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/servicehealth/Read | Pobierz Service Health Web Apps diagnostyki miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/sitecpuanalysis/Read | Pobierz przeanalizowanie procesora CPU lokacji diagnostyki Web Apps gniazd. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/sitecrashes/Read | Pobierz awarie lokacji diagnostyki Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/sitelatency/Read | Pobierz opóźnienie witryny diagnostyki Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/sitememoryanalysis/Read | Pobierz analizę pamięci lokacji diagnostyki Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/siterestartsettingupdate/Read | Pobierz aktualizację ustawień ponownego uruchamiania lokacji diagnostyki Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/siterestartuserinitiated/Read | Pobierz lokację diagnostyki Web Apps miejsc ponownie zainicjowane przez użytkownika. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/siteswap/Read | Pobierz zamianę lokacji diagnostyki Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/ThreadCount/Read | Pobierz liczbę wątków diagnostyki Web Apps gniazd. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/workeravailability/Read | Pobierz Web Apps diagnostyki Workeravailability miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Diagnostics/workerprocessrecycle/Read | Pobierz procedurę odzyskiwania procesu roboczego diagnostyki gniazd Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/domainownershipidentifiers/Read | Pobierz identyfikatory własności domeny miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/Functions/listsecrets/Action | Wyświetl klucze tajne Web Apps funkcji miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Functions/Read | Pobierz funkcje Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/hostnamebindings/Delete | Usuń powiązania nazwy hosta Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/hostnamebindings/Read | Pobierz powiązania nazwy hosta Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/hostnamebindings/Write | Aktualizowanie powiązań nazwy hosta Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/hybridconnection/Delete | Usuń połączenie hybrydowe miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/hybridconnection/Read | Pobierz połączenie hybrydowe miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/hybridconnection/Write | Aktualizowanie połączenia hybrydowego dla gniazd Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/hybridconnectionnamespaces/Relay/Delete | Usuń Web Apps gniazda przestrzenie nazw połączenia hybrydowego. |
> | Działanie | Microsoft. Web/Sites/Slots/hybridconnectionnamespaces/Relay/Write | Aktualizacja funkcji przekazywania przestrzeni nazw połączeń hybrydowych na Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/Slots/hybridconnectionrelays/Read | Pobierz Web Apps gniazda połączeń hybrydowych. |
> | Działanie | Microsoft. Web/Sites/Slots/Instances/Deployments/Read | Pobierz wystąpienia Web Apps wystąpień. |
> | Działanie | Microsoft. Web/Sites/Slots/Instances/processs/Delete | Usuwanie procesów wystąpień Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/Instances/processs/Read | Pobierz procesy Web Apps wystąpieniach gniazd. |
> | Działanie | Microsoft. Web/Sites/Slots/Instances/Read | Pobierz wystąpienia Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/metricdefinitions/Read | Pobierz definicje metryk Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/Metrics/Read | Pobierz metryki Web Apps gniazd. |
> | Działanie | Microsoft. Web/Sites/Slots/migratemysql/Read | Pobierz Web Apps gniazda migracji bazy danych MySql. |
> | Działanie | Microsoft. Web/Sites/szczeliny/networktrace/akcja | Gniazda Web Apps śledzenia sieci. |
> | Działanie | Microsoft. Web/Sites/Slots/networktraces/operationresults/Read | Pobierz wyniki operacji śledzenia sieci Web Apps gniazd. |
> | Działanie | Microsoft. Web/Sites/szczeliny/NoweHasło/akcja | NoweHasło Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/Slots/operationresults/Read | Pobierz wyniki operacji dla miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/Operations/Read | Pobierz operacje na Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/Slots/perfcounters/Read | Pobierz liczniki wydajności Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/phplogging/Read | Pobierz Web Apps Phplogging miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/premieraddons/Delete | Usuń dodatki Web Apps miejsc premier. |
> | Działanie | Microsoft. Web/Sites/Slots/premieraddons/Read | Uzyskaj Web Apps miejsca na dodatki Premier. |
> | Działanie | Microsoft. Web/Sites/Slots/premieraddons/Write | Aktualizowanie dodatków do Web Apps miejsc premier. |
> | Działanie | Microsoft. Web/Sites/Slots/processs/Read | Pobierz procesy Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/Slots/publiccertificates/Delete | Usuwanie Web Apps miejscowych certyfikatów publicznych. |
> | Działanie | Microsoft. Web/Sites/Slots/publiccertificates/Read | Pobierz Web Apps gniazda certyfikatów publicznych. |
> | Działanie | Microsoft. Web/Sites/Slots/publiccertificates/Write | Utwórz lub zaktualizuj certyfikaty publiczne na Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/szczeliny/publikowanie/akcja | Publikowanie miejsca aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/szczeliny/publishxml/akcja | Pobierz plik XML profilu publikowania dla miejsca aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/gniazda/odczyt | Pobierz właściwości miejsca wdrożenia aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/gniazda/odzyskiwanie/akcja | Odzyskaj Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/szczeliny/resetSlotConfig/akcja | Resetuj konfigurację miejsca aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Slots/resourcehealthmetadata/Read | Pobierz Web Apps miejsca Resource Health metadanych. |
> | Działanie | Microsoft. Web/Sites/szczeliny/ponowne uruchomienie/akcja | Uruchom ponownie miejsce aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Slots/Restore/Read | Pobierz Web Apps miejsc przywracania. |
> | Działanie | Microsoft. Web/Sites/Slots/Restore/Write | Przywracanie Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/szczeliny/restorefrombackupblob/akcja | Przywróć miejsce Web Apps z obiektu BLOB kopii zapasowej. |
> | Działanie | Microsoft. Web/Sites/szczeliny/restorefromdeletedapp/akcja | Przywróć miejsca aplikacji sieci Web z usuniętej aplikacji. |
> | Działanie | Microsoft. Web/Sites/szczeliny/restoresnapshot/akcja | Przywracanie migawek miejsc Web Apps. |
> | Działanie | Microsoft. Web/Sites/Slots/siteextensions/Delete | Usuwanie rozszerzeń lokacji Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/siteextensions/Read | Pobierz rozszerzenia witryny Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/siteextensions/Write | Aktualizowanie rozszerzeń lokacji Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/szczeliny/slotsdiffs/akcja | Poznaj różnice między konfiguracją aplikacji sieci Web i miejsc |
> | Działanie | Microsoft. Web/Sites/szczeliny/slotsswap/akcja | Wymiana miejsc wdrożenia aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Slots/snapshots/Read | Pobierz migawki Web Apps miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Slots/sourcecontrols/Read | Pobierz ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Slots/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/szczeliny/uruchomienie/akcja | Uruchom miejsce aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Slots/akcja | Zatrzymaj miejsce aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/szczeliny/synchronizacja/akcja | Zsynchronizuj Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/Slots/triggeredwebjobs/Delete | Usuwanie Web Appsych gniazd wyzwalanych przez Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites/Slots/triggeredwebjobs/Read | Pobierz Web Apps gniazda wyzwolone Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites/Slots/triggeredwebjobs/Run/Action | Uruchom uruchomione Web Apps gniazda wyzwalane przez Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites/Slots/Usages/Read | Uzyskaj Web Apps użycia miejsc. |
> | Działanie | Microsoft. Web/Sites/Slots/virtualnetworkconnections/Delete | Usuwanie Web Apps miejsc Virtual Network połączeń. |
> | Działanie | Microsoft. Web/Sites/szczeliny/virtualnetworkconnections/Gateways/Write | Aktualizowanie Web Apps miejsc Virtual Network połączeń bram. |
> | Działanie | Microsoft. Web/Sites/Slots/virtualnetworkconnections/Read | Pobierz Web Apps gniazda Virtual Network połączeń. |
> | Działanie | Microsoft. Web/Sites/Slots/virtualnetworkconnections/Write | Aktualizacja Virtual Network połączeń Web Apps. |
> | Działanie | Microsoft. Web/Sites/szczeliny/Zadania WebJob/odczyt | Pobierz Zadania WebJob na Web Apps gniazda. |
> | Działanie | Microsoft. Web/Sites/szczeliny/zapis | Utwórz nowe miejsce aplikacji sieci Web lub zaktualizuj istniejące |
> | Działanie | Microsoft. Web/Sites/slotsdiffs/akcja | Poznaj różnice między konfiguracją aplikacji sieci Web i miejsc |
> | Działanie | Microsoft. Web/Sites/slotsswap/akcja | Wymiana miejsc wdrożenia aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/snapshots/Read | Pobierz migawki Web Apps. |
> | Działanie | Microsoft. Web/Sites/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/sourcecontrols/odczyt | Pobierz ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/Start/akcja | Uruchamianie aplikacji sieci Web |
> | Działanie | Microsoft. Web/Sites/zatrzymywanie/akcja | Zatrzymaj aplikację internetową |
> | Działanie | Microsoft. Web/Sites/synchronizacja/akcja | Web Apps synchronizacji. |
> | Działanie | Microsoft. Web/Sites/syncfunctiontriggers/akcja | Wyzwalacze funkcji synchronizacji. |
> | Działanie | Microsoft. Web/Sites/triggeredwebjobs/Delete | Usuń Web Apps wyzwolone Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites/triggeredwebjobs/historia/odczyt | Pobierz Web Apps wyzwoloną historię zadań WebJob. |
> | Działanie | Microsoft. Web/Sites/triggeredwebjobs/odczyt | Pobierz Web Apps wyzwolone Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites/triggeredwebjobs/Run/Action | Uruchom Web Apps wyzwolone Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites//użycia/odczyt | Pobierz Web Apps użycia. |
> | Działanie | Microsoft. Web/Sites/virtualnetworkconnections/Delete | Usuń połączenia Virtual Network Web Apps. |
> | Działanie | Microsoft. Web/Sites/virtualnetworkconnections/Gateways/Read | Pobierz Web Apps bram Virtual Network połączeń. |
> | Działanie | Microsoft. Web/Sites/virtualnetworkconnections/Gateways/Write | Aktualizowanie bram Web Apps Virtual Network połączeń. |
> | Działanie | Microsoft. Web/Sites/virtualnetworkconnections/odczyt | Uzyskaj Web Apps połączenia Virtual Network. |
> | Działanie | Microsoft. Web/Sites/virtualnetworkconnections/Write | Aktualizowanie Web Apps Virtual Network połączeń. |
> | Działanie | Microsoft. Web/Sites/WebJobs/odczyt | Pobierz Web Apps Zadania WebJob. |
> | Działanie | Microsoft. Web/Sites/Write | Utwórz nową aplikację sieci Web lub zaktualizuj istniejącą |
> | Działanie | Microsoft. Web/SKU/odczyt | Pobierz jednostki SKU. |
> | Działanie | Microsoft. Web/sourcecontrols/odczyt | Pobierz kontrolę źródła. |
> | Działanie | Microsoft. Web/sourcecontrols/Write | Aktualizowanie kontroli źródła. |
> | Działanie | Microsoft. Web/Unregister/akcja | Wyrejestruj dostawcę zasobów Microsoft. Web dla subskrypcji. |
> | Działanie | Microsoft. Web/Validate/akcja | Legalizacj. |
> | Działanie | Microsoft. Web/verifyhostingenvironmentvnet/akcja | Sprawdź sieć wirtualną środowiska hostingu. |

## <a name="microsoftworkloadmonitor"></a>Microsoft. Monitor obciążenia został

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Działanie | Microsoft. Monitor obciążenia został/Components/Read | Pobiera składniki dla zasobu |
> | Działanie | Microsoft. Monitor obciążenia został/componentsSummary/odczyt | Pobiera podsumowanie składników |
> | Działanie | Microsoft. Monitor obciążenia został/monitorInstances/odczyt | Pobiera wystąpienia monitorów dla zasobu |
> | Działanie | Microsoft. Monitor obciążenia został/monitorInstancesSummary/odczyt | Pobiera podsumowanie wystąpień monitora |
> | Działanie | Microsoft. Monitor obciążenia został/Monitors/Read | Pobiera monitory dla zasobu |
> | Działanie | Microsoft. Monitor obciążenia został/Monitors/Write | Konfigurowanie monitora dla zasobu |
> | Działanie | Microsoft. Monitor obciążenia został/notificationSettings/odczyt | Pobiera ustawienia powiadomień dla zasobu |
> | Działanie | Microsoft. Monitor obciążenia został/notificationSettings/Write | Konfigurowanie ustawień powiadomień dla zasobu |
> | Działanie | Microsoft. Monitor obciążenia został/Operations/Read | Pobiera obsługiwane operacje |

## <a name="next-steps"></a>Następne kroki

- [Dopasuj dostawcę zasobów do usługi](../azure-resource-manager/azure-services-resource-providers.md)
- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
