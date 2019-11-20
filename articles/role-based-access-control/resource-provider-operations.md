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
ms.openlocfilehash: c413c03c000ef9ff1ebf742359551567d488584b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185732"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operacje dostawcy zasobów Azure Resource Manager

W tym artykule wymieniono operacje dostępne dla każdego dostawcy zasobów Azure Resource Manager. Te operacje mogą być używane w [rolach niestandardowych](custom-roles.md) w celu zapewnienia szczegółowej [kontroli dostępu opartej na rolach (RBAC)](overview.md) na potrzeby zasobów na platformie Azure. Ciągi operacji mają następujący format: `{Company}.{ProviderName}/{resourceType}/{action}`. Aby uzyskać listę sposobu mapowania przestrzeni nazw dostawcy zasobów na usługi platformy Azure, zobacz [dopasowanie dostawcy zasobów do usługi](../azure-resource-manager/azure-services-resource-providers.md).

Operacje dostawcy zasobów są zawsze rozwijane. Aby uzyskać najnowsze operacje, Użyj listy operacji [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) lub [AZ Provider](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. AAD/domainServices/Delete | Usuwanie usługi domeny |
> | Akcja | Microsoft. AAD/domainServices/oucontainer/usuwanie | Usuń kontener jednostki organizacyjnej |
> | Akcja | Microsoft. AAD/domainServices/oucontainer/odczyt | Odczytaj kontenery jednostek organizacyjnych |
> | Akcja | Microsoft. AAD/domainServices/oucontainer/zapis | Zapisz kontener jednostki organizacyjnej |
> | Akcja | Microsoft.AAD/domainServices/read | Odczytaj usługi domenowe |
> | Akcja | Microsoft.AAD/domainServices/replicaSets/delete | Usuń lokację klastra |
> | Akcja | Microsoft.AAD/domainServices/replicaSets/read | Odczytaj lokację klastra |
> | Akcja | Microsoft. AAD/domainServices/replicaSets/zapis | Zapisz witrynę klastra |
> | Akcja | Microsoft. AAD/domainServices/Write | Zapisz usługę domenową |
> | Akcja | Microsoft.AAD/locations/operationresults/read |  |
> | Akcja | Microsoft. AAD/Operations/Read |  |
> | Akcja | Microsoft. AAD/rejestrowanie/akcja | Zarejestruj usługę domeny |
> | Akcja | Microsoft. AAD/Wyrejestruj/akcja | Wyrejestruj usługę domenową |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. aadiam/diagnosticsettings/Delete | Usuwanie ustawień diagnostycznych |
> | Akcja | Microsoft. aadiam/diagnosticsettings/odczyt | Odczytywanie ustawień diagnostycznych |
> | Akcja | Microsoft. aadiam/diagnosticsettings/Write | Zapisywanie ustawień diagnostycznych |
> | Akcja | Microsoft. aadiam/diagnosticsettingscategories/odczyt | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Dodatki/operacje/odczyt | Pobiera obsługiwane operacje RP. |
> | Akcja | Microsoft. Dodatki/rejestrowanie/akcja | Zarejestruj określoną subskrypcję w programie Microsoft. Dodatki |
> | Akcja | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Wyświetla bieżące informacje o planach pomocy technicznej dla określonej subskrypcji. |
> | Akcja | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Usuwa określony plan obsługi kanonicznej |
> | Akcja | Microsoft.Addons/supportProviders/supportPlanTypes/read | Pobierz określony stan kanonicznego planu pomocy technicznej. |
> | Akcja | Microsoft.Addons/supportProviders/supportPlanTypes/write | Dodaje określony typ planu obsługi kanonicznej. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

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

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Advisor/configurations/read | Pobierz konfiguracje |
> | Akcja | Microsoft.Advisor/configurations/write | Tworzy/aktualizuje konfigurację |
> | Akcja | Microsoft. Advisor/generateRecommendations/akcja | Generuje zalecenia |
> | Akcja | Microsoft.Advisor/generateRecommendations/read | Pobiera stan zaleceń generowania |
> | Akcja | Microsoft.Advisor/metadata/read | Pobierz metadane |
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

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AlertsManagement/actionRules/delete | Usuń regułę akcji w danej subskrypcji. |
> | Akcja | Microsoft.AlertsManagement/actionRules/read | Pobierz wszystkie reguły akcji dla filtrów wejściowych. |
> | Akcja | Microsoft. AlertsManagement/actionRules/Write | Utwórz lub zaktualizuj regułę akcji w danej subskrypcji |
> | Akcja | Microsoft. AlertsManagement/Alerts/changestate/Action | Zmień stan alertu. |
> | Akcja | Microsoft.AlertsManagement/alerts/diagnostics/read | Pobierz całą diagnostykę dla alertu |
> | Akcja | Microsoft.AlertsManagement/alerts/history/read | Pobierz historię alertu |
> | Akcja | Microsoft.AlertsManagement/alerts/read | Pobierz wszystkie alerty dla filtrów wejściowych. |
> | Akcja | Microsoft.AlertsManagement/alertsList/read | Pobierz wszystkie alerty dla filtrów wejściowych między subskrypcjami |
> | Akcja | Microsoft. AlertsManagement/alertsMetaData/odczyt | Pobierz metadane alertów dla parametru wejściowego. |
> | Akcja | Microsoft. AlertsManagement/alertsSummary/odczyt | Pobierz podsumowanie alertów |
> | Akcja | Microsoft. AlertsManagement/alertsSummaryList/odczyt | Pobierz podsumowanie alertów w ramach subskrypcji |
> | Akcja | Microsoft.AlertsManagement/Operations/read | Odczytuje podane operacje |
> | Akcja | Microsoft. AlertsManagement/Register/Action | Rejestruje subskrypcję zarządzania alertami firmy Microsoft |
> | Akcja | Microsoft. AlertsManagement/smartDetectorAlertRules/Delete | Usuń regułę alertu inteligentnego wykrywania w danej subskrypcji |
> | Akcja | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Pobieranie wszystkich reguł alertów dotyczących detektora inteligentnego dla filtrów wejściowych |
> | Akcja | Microsoft. AlertsManagement/smartDetectorAlertRules/Write | Utwórz lub zaktualizuj regułę alertu inteligentnego wykrywania w danej subskrypcji |
> | Akcja | Microsoft.AlertsManagement/smartGroups/changestate/action | Zmiana stanu grupy inteligentnej |
> | Akcja | Microsoft.AlertsManagement/smartGroups/history/read | Pobierz historię grupy inteligentnej |
> | Akcja | Microsoft.AlertsManagement/smartGroups/read | Pobierz wszystkie grupy inteligentne dla filtrów wejściowych |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. AnalysisServices/Locations/checkNameAvailability/Action | Sprawdza, czy dana nazwa Analysis Server jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft.AnalysisServices/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | Akcja | Microsoft.AnalysisServices/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | Akcja | Microsoft. AnalysisServices/Operations/Read | Pobiera informacje o operacjach |
> | Akcja | Microsoft. AnalysisServices/Register/Action | Rejestruje dostawcę zasobów Analysis Services. |
> | Akcja | Microsoft. AnalysisServices/serwery/usuwanie | Usuwa Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Wyświetlenie listy stan bramy skojarzonej z serwerem. |
> | Akcja | Microsoft. AnalysisServices/serwery/odczyt | Pobiera informacje o określonym Analysis Server. |
> | Akcja | Microsoft. AnalysisServices/serwery/wznowienie/akcja | Wznawia Analysis Server. |
> | Akcja | Microsoft. AnalysisServices/serwery/jednostki SKU/odczyt | Pobierz informacje o dostępnych jednostkach SKU dla serwera |
> | Akcja | Microsoft.AnalysisServices/servers/suspend/action | Wstrzymuje Analysis Server. |
> | Akcja | Microsoft. AnalysisServices/serwery/zapis | Tworzy lub aktualizuje określony Analysis Server. |
> | Akcja | Microsoft. AnalysisServices/jednostki SKU/odczyt | Pobiera informacje o jednostkach SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ApiManagement/checkNameAvailability/read | Sprawdza, czy podana nazwa usługi jest dostępna |
> | Akcja | Microsoft.ApiManagement/operations/read | Odczytuj wszystkie operacje interfejsu API dostępne dla zasobu Microsoft. ApiManagement |
> | Akcja | Microsoft.ApiManagement/register/action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. ApiManagement |
> | Akcja | Microsoft.ApiManagement/reports/read | Uzyskuj raporty zagregowane według okresów, regionu geograficznego, deweloperów, produktów, interfejsów API, operacji, subskrypcji i byRequest. |
> | Akcja | Microsoft.ApiManagement/service/apis/delete | Usuwa określony interfejs API wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/delete | Usuwa określoną diagnostykę z interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/read | Wyświetla listę wszystkich diagnostyki interfejsu API. lub pobiera szczegóły diagnostyki dla interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/Diagnostyka/zapis | Tworzy nową diagnostykę dla interfejsu API lub aktualizuje istniejący. lub aktualizuje szczegóły diagnostyki dla interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Usuwa określony komentarz z problemu. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/attachments/read | Wyświetla listę wszystkich załączników dla problemu skojarzonego z określonym interfejsem API. lub pobiera szczegóły załącznika problemu dla interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/attachments/write | Tworzy nowy załącznik dla problemu w interfejsie API lub aktualizuje istniejący. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/Komentarze/usuwanie | Usuwa określony komentarz z problemu. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/Komentarze/odczyt | Wyświetla listę wszystkich komentarzy dotyczących problemu skojarzonego z określonym interfejsem API. lub pobiera szczegóły komentarza dotyczącego problemu dla interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/API/problemy/Komentarze/zapis | Tworzy nowy komentarz dotyczący problemu w interfejsie API lub aktualizuje istniejący. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/delete | Usuwa określony problem z interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/read | Wyświetla listę wszystkich problemów skojarzonych z określonym interfejsem API. lub pobiera szczegóły problemu dotyczącego interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/write | Tworzy nowy problem dla interfejsu API lub aktualizuje istniejący. lub aktualizuje istniejący problem dla interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/delete | Usuwa określoną operację w interfejsie API. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policies/delete | Usuwa konfigurację zasad w operacji interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policies/read | Pobierz listę konfiguracji zasad na poziomie operacji interfejsu API. lub Pobierz konfigurację zasad na poziomie operacji interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policies/write | Tworzy lub aktualizuje konfigurację zasad dla poziomu operacji interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policy/delete | Usuń konfigurację zasad na poziomie operacji |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policy/read | Pobierz konfigurację zasad na poziomie operacji |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policy/write | Utwórz konfigurację zasad na poziomie operacji |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/read | Wyświetla kolekcję operacji dla określonego interfejsu API. lub pobiera szczegóły operacji interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/tags/delete | Odłącz tag od operacji. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/tags/read | Wyświetla listę wszystkich tagów skojarzonych z operacją. lub Pobierz tag skojarzony z operacją. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/tags/write | Przypisz tag do operacji. |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/write | Tworzy nową operację w interfejsie API lub aktualizuje istniejącą. lub aktualizuje szczegóły operacji w interfejsie API określonym przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/operationsByTags/read | Wyświetla kolekcję operacji skojarzonych ze znacznikami. |
> | Akcja | Microsoft.ApiManagement/service/apis/policies/delete | Usuwa konfigurację zasad w interfejsie API. |
> | Akcja | Microsoft.ApiManagement/service/apis/policies/read | Pobierz konfigurację zasad na poziomie interfejsu API. lub Pobierz konfigurację zasad na poziomie interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/policies/write | Tworzy lub aktualizuje konfigurację zasad dla interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/policy/delete | Usuń konfigurację zasad na poziomie interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/policy/read | Pobierz konfigurację zasad na poziomie interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/policy/write | Utwórz konfigurację zasad na poziomie interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/products/read | Wyświetla listę wszystkich produktów, które są częścią interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/read | Wyświetla wszystkie interfejsy API wystąpienia usługi API Management. lub pobiera szczegóły interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/releases/delete | Usuwa wszystkie wersje interfejsu API lub usuwa określoną wersję w interfejsie API. |
> | Akcja | Microsoft.ApiManagement/service/apis/releases/read | Wyświetla wszystkie wersje interfejsu API.<br>Wersja interfejsu API jest tworzona podczas aktualnej wersji interfejsu API.<br>Wersje są również używane do wycofywania do poprzednich wersji.<br>Wyniki będą stronicowane i mogą być ograniczone przez $top i $skip parametrów.<br>lub zwraca szczegółowe informacje o wersji interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/releases/write | Tworzy nową wersję interfejsu API. lub aktualizuje szczegóły wydania interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apis/revisions/delete | Usuwa wszystkie poprawki interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/revisions/read | Wyświetla wszystkie wersje interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/delete | Usuwa konfigurację schematu w interfejsie API. |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/read | Pobierz konfigurację schematu na poziomie interfejsu API. lub zapoznaj się z konfiguracją schematu na poziomie interfejsu API. |
> | Akcja | Microsoft. ApiManagement/Service/API/schematy/zapis | Tworzy lub aktualizuje konfigurację schematu dla interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Usuń opis tagu dla interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Wyświetla listę wszystkich opisów tagów w zakresie interfejsu API. Model podobny do struktury Swagger-tagDescription jest zdefiniowany na poziomie interfejsu API, ale tag może być przypisany do operacji lub uzyskać opis tagu w zakresie interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Utwórz/zaktualizuj opis tagu w zakresie interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/tags/delete | Odłącz tag od interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/tags/read | Wyświetla listę wszystkich tagów skojarzonych z interfejsem API. lub uzyskaj tag skojarzony z interfejsem API. |
> | Akcja | Microsoft.ApiManagement/service/apis/tags/write | Przypisz tag do interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apis/write | Tworzy nowy lub aktualizuje istniejący interfejs API wystąpienia usługi API Management. lub aktualizuje określony interfejs API wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/apisByTags/read | Wyświetla kolekcję interfejsów API skojarzonych ze znacznikami. |
> | Akcja | Microsoft.ApiManagement/service/apiVersionSets/delete | Usuwa określony zestaw wersji interfejsu API. |
> | Akcja | Microsoft.ApiManagement/service/apiVersionSets/read | Wyświetla kolekcję zestawów wersji interfejsu API w określonym wystąpieniu usługi. lub pobiera szczegóły zestawu wersji interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Pobierz listę jednostek wersji |
> | Akcja | Microsoft.ApiManagement/service/apiVersionSets/write | Tworzy lub aktualizuje zestaw wersji interfejsu API. lub aktualizuje szczegóły VersionSet interfejsu API określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Aktualizuje zasoby Microsoft. ApiManagement uruchomione w Virtual Network w celu pobrania zaktualizowanych ustawień sieci. |
> | Akcja | Microsoft.ApiManagement/service/authorizationServers/delete | Usuwa określone wystąpienie serwera autoryzacji. |
> | Akcja | Microsoft.ApiManagement/service/authorizationServers/read | Wyświetla kolekcję serwerów autoryzacji zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły serwera autoryzacji określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/authorizationServers/write | Tworzy nowy serwer autoryzacji lub aktualizuje istniejący serwer autoryzacji. lub aktualizuje szczegóły serwera autoryzacji określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/backends/delete | Usuwa określone zaplecze. |
> | Akcja | Microsoft.ApiManagement/service/backends/read | Wyświetla kolekcję punktów końcowych w określonym wystąpieniu usługi. lub pobiera szczegóły zaplecza określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/backends/reconnect/action | Powiadamia serwer proxy APIM o utworzeniu nowego połączenia z zapleczem po upływie określonego limitu czasu. Jeśli nie określono limitu czasu, zostanie użyty limit czasu wynoszący 2 minuty. |
> | Akcja | Microsoft.ApiManagement/service/backends/write | Tworzy lub aktualizuje zaplecze. lub aktualizuje istniejące zaplecze. |
> | Akcja | Microsoft.ApiManagement/service/backup/action | Usługa tworzenia kopii zapasowych API Management do określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Akcja | Microsoft.ApiManagement/service/caches/delete | Usuwa określoną pamięć podręczną. |
> | Akcja | Microsoft.ApiManagement/service/caches/read | Wyświetla kolekcję wszystkich zewnętrznych pamięci podręcznych w określonym wystąpieniu usługi. lub pobiera szczegóły pamięci podręcznej określonej przez jej identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/caches/write | Tworzy lub aktualizuje zewnętrzną pamięć podręczną, która ma być używana w wystąpieniu usługi API Management. lub aktualizuje szczegóły pamięci podręcznej określonej przez jej identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/certificates/delete | Usuwa określony certyfikat. |
> | Akcja | Microsoft.ApiManagement/service/certificates/read | Wyświetla kolekcję wszystkich certyfikatów w określonym wystąpieniu usługi. lub pobiera szczegóły certyfikatu określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/certificates/write | Tworzy lub aktualizuje certyfikat używany do uwierzytelniania w zapleczu. |
> | Akcja | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Usuwa określony element zawartości. |
> | Akcja | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Zwraca listę elementów zawartości lub zwraca szczegóły elementu zawartości |
> | Akcja | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Tworzy nowy element zawartości lub aktualizuje określony element zawartości |
> | Akcja | Microsoft.ApiManagement/service/contentTypes/read | Zwraca listę typów zawartości |
> | Akcja | Microsoft.ApiManagement/service/delete | Usuń wystąpienie usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/delete | Usuwa określoną diagnostykę. |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/read | Wyświetla listę wszystkich diagnostyki wystąpienia usługi API Management. lub pobiera szczegóły diagnostyki określone przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Diagnostics/Write | Tworzy nową diagnostykę lub aktualizuje istniejącą. lub aktualizuje szczegóły diagnostyki określonej przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/Action | Pobiera konfigurację bramy. lub aktualizuje puls bramy. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/Delete | Usuwa określoną bramę. |
> | Akcja | Microsoft. ApiManagement/Service/Gateway//klucze/akcja | Pobiera klucze bramy. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/Read | Wyświetla kolekcję bram zarejestrowanych w wystąpieniu usługi. lub pobiera szczegóły bramy określone przez jego identyfikator. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/regeneratePrimaryKey/Action | Ponownie generuje podstawowy klucz bramy invalidationg wszystkie utworzone przez niego tokeny. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/regenerateSecondaryKey/Action | Ponownie generuje pomocniczy klucz bramy invalidationg wszystkie utworzone przez niego tokeny. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/token/Action | Pobiera token autoryzacji dostępu współdzielonego dla bramy. |
> | Akcja | Microsoft. ApiManagement/Service/Gateways/Write | Tworzy lub aktualizuje bramę do użycia w wystąpieniu usługi API Management. lub aktualizuje szczegóły bramy określone przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/getssotoken/action | Pobiera token rejestracji jednokrotnej, którego można użyć do zalogowania się do portalu usługi API Management starszej wersji jako administrator |
> | Akcja | Microsoft.ApiManagement/service/groups/delete | Usuwa konkretną grupę wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/groups/read | Wyświetla kolekcję grup zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły grupy określonej przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/groups/users/delete | Usuń istniejącego użytkownika z istniejącej grupy. |
> | Akcja | Microsoft.ApiManagement/service/groups/users/read | Wyświetla kolekcję jednostek użytkownika skojarzonych z grupą. |
> | Akcja | Microsoft.ApiManagement/service/groups/users/write | Dodaj istniejącego użytkownika do istniejącej grupy |
> | Akcja | Microsoft. ApiManagement/Service/Groups/Write | Tworzy lub aktualizuje grupę. lub aktualizuje Szczegóły grupy określonej przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/identityProviders/delete | Usuwa określoną konfigurację dostawcy tożsamości. |
> | Akcja | Microsoft.ApiManagement/service/identityProviders/read | Wyświetla kolekcję dostawców tożsamości skonfigurowanych w określonym wystąpieniu usługi. lub pobiera szczegóły konfiguracji dostawcy tożsamości skonfigurowanego w określonym wystąpieniu usługi. |
> | Akcja | Microsoft.ApiManagement/service/identityProviders/write | Tworzy lub aktualizuje konfigurację IdentityProvider. lub aktualizuje istniejącą konfigurację IdentityProvider. |
> | Akcja | Microsoft.ApiManagement/service/issues/read | Wyświetla kolekcję problemów w określonym wystąpieniu usługi. lub pobiera API Management Szczegóły problemu |
> | Akcja | Microsoft.ApiManagement/service/locations/networkstatus/read | Pobiera stan dostępu do sieci dla zasobów, od których zależy usługa w danej lokalizacji. |
> | Akcja | Microsoft.ApiManagement/service/loggers/delete | Usuwa określony rejestrator. |
> | Akcja | Microsoft.ApiManagement/service/loggers/read | Wyświetla kolekcję rejestratorów w określonym wystąpieniu usługi. lub pobiera szczegóły rejestratora określony przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/loggers/write | Tworzy lub aktualizuje rejestrator. lub aktualizuje istniejący rejestrator. |
> | Akcja | Microsoft.ApiManagement/service/managedeployments/action | Zmień jednostkę SKU/jednostki, Dodaj/Usuń wdrożenia regionalne usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/networkstatus/read | Pobiera stan dostępu do sieci dla zasobów, od których zależy usługa. |
> | Akcja | Microsoft.ApiManagement/service/notifications/action | Wysyła powiadomienie do określonego użytkownika |
> | Akcja | Microsoft.ApiManagement/service/notifications/read | Wyświetla kolekcję właściwości zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły powiadomienia określone przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Usuwa wiadomość e-mail z listy powiadomień. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Pobiera listę wiadomości E-mail dotyczących adresatów powiadomień subskrybowanych w ramach powiadomienia. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Dodaje adres E-mail do listy adresatów powiadomienia. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Usuwa użytkownika API Management z listy powiadomień. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Pobiera listę użytkowników adresatów powiadomień, którzy zasubskrybują powiadomienie. |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Dodaje API Management użytkownika do listy adresatów powiadomienia. |
> | Akcja | Microsoft.ApiManagement/service/notifications/write | Utwórz lub zaktualizuj powiadomienia wydawcy API Management. |
> | Akcja | Microsoft.ApiManagement/service/openidConnectProviders/delete | Usuwa określonego dostawcę połączenia OpenID Connect dla wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/openidConnectProviders/read | Listy wszystkich dostawców połączeń OpenID Connect. lub pobiera określonego dostawcę połączenia OpenID Connect. |
> | Akcja | Microsoft.ApiManagement/service/openidConnectProviders/write | Tworzy lub aktualizuje dostawcę połączenia OpenID Connect. lub aktualizuje określonego dostawcę połączenia OpenID Connect. |
> | Akcja | Microsoft.ApiManagement/service/operationresults/read | Pobiera bieżący stan długotrwałej operacji |
> | Akcja | Microsoft.ApiManagement/service/policies/delete | Usuwa globalną konfigurację zasad usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/policies/read | Wyświetla wszystkie globalne definicje zasad usługi API Management. lub uzyskać definicję zasad globalnych usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/policies/write | Tworzy lub aktualizuje globalną konfigurację zasad usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/policy/delete | Usuń konfigurację zasad na poziomie dzierżawy |
> | Akcja | Microsoft.ApiManagement/service/policy/read | Pobierz konfigurację zasad na poziomie dzierżawy |
> | Akcja | Microsoft.ApiManagement/service/policy/write | Utwórz konfigurację zasad na poziomie dzierżawy |
> | Akcja | Microsoft. ApiManagement/Service/policyDescriptions/Read | Wyświetla listę wszystkich opisów zasad. |
> | Akcja | Microsoft.ApiManagement/service/policySnippets/read | Wyświetla wszystkie fragmenty kodu zasad. |
> | Akcja | Microsoft.ApiManagement/service/portalsettings/read | Uzyskaj ustawienia logowania dla portalu lub uzyskaj ustawienia rejestracji w portalu lub uzyskaj ustawienia delegowania dla portalu. |
> | Akcja | Microsoft.ApiManagement/service/portalsettings/write | Zaktualizuj ustawienia logowania. lub Utwórz lub zaktualizuj ustawienia logowania. lub zaktualizuj ustawienia rejestracji lub zaktualizuj ustawienia rejestracji lub zaktualizuj ustawienia delegowania. lub Utwórz lub zaktualizuj ustawienia delegowania. |
> | Akcja | Microsoft.ApiManagement/service/products/apis/delete | Usuwa określony interfejs API z określonego produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/apis/read | Wyświetla kolekcję interfejsów API skojarzonych z produktem. |
> | Akcja | Microsoft.ApiManagement/service/products/apis/write | Dodaje interfejs API do określonego produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/delete | Usuń produkt. |
> | Akcja | Microsoft.ApiManagement/service/products/groups/delete | Usuwa skojarzenie między określoną grupą i produktem. |
> | Akcja | Microsoft.ApiManagement/service/products/groups/read | Wyświetla listę grup deweloperów skojarzonych z określonym produktem. |
> | Akcja | Microsoft.ApiManagement/service/products/groups/write | Dodaje skojarzenie między określoną grupą deweloperów i określonym produktem. |
> | Akcja | Microsoft.ApiManagement/service/products/policies/delete | Usuwa konfigurację zasad w produkcie. |
> | Akcja | Microsoft.ApiManagement/service/products/policies/read | Pobierz konfigurację zasad na poziomie produktu. lub Pobierz konfigurację zasad na poziomie produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/policies/write | Tworzy lub aktualizuje konfigurację zasad dla produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/policy/delete | Usuń konfigurację zasad na poziomie produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policy/read | Pobierz konfigurację zasad na poziomie produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policy/write | Utwórz konfigurację zasad na poziomie produktu |
> | Akcja | Microsoft.ApiManagement/service/products/read | Wyświetla kolekcję produktów w określonym wystąpieniu usługi. lub pobiera szczegóły produktu określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/products/subscriptions/read | Zawiera listę kolekcji subskrypcji dla określonego produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/tags/delete | Odłącz tag od produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/tags/read | Wyświetla listę wszystkich tagów skojarzonych z produktem. lub Pobierz tag skojarzony z produktem. |
> | Akcja | Microsoft.ApiManagement/service/products/tags/write | Przypisz tag do produktu. |
> | Akcja | Microsoft.ApiManagement/service/products/write | Tworzy lub aktualizuje produkt. lub zaktualizuj istniejące informacje o produkcie. |
> | Akcja | Microsoft.ApiManagement/service/productsByTags/read | Wyświetla kolekcję produktów skojarzonych ze znacznikami. |
> | Akcja | Microsoft.ApiManagement/service/properties/delete | Usuwa określoną właściwość z wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/properties/read | Wyświetla kolekcję właściwości zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły właściwości określonej przez jej identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/properties/write | Tworzy lub aktualizuje właściwość. lub aktualizuje konkretną właściwość. |
> | Akcja | Microsoft.ApiManagement/service/quotas/periods/read | Pobierz wartość licznika przydziału dla okresu |
> | Akcja | Microsoft. ApiManagement/Service/limit przydziału/okresy/zapis | Ustaw bieżącą wartość licznika przydziału |
> | Akcja | Microsoft.ApiManagement/service/quotas/read | Pobierz wartości przydziału |
> | Akcja | Microsoft.ApiManagement/service/quotas/write | Ustaw bieżącą wartość licznika przydziału |
> | Akcja | Microsoft.ApiManagement/service/read | Odczytaj metadane dla wystąpienia usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/regions/read | Wyświetla listę wszystkich regionów świadczenia usługi Azure, w których istnieje usługa. |
> | Akcja | Microsoft.ApiManagement/service/reports/read | Pobierz raport zagregowany według okresów lub Pobierz raport zagregowany według regionu geograficznego lub Pobierz raport zagregowany przez deweloperów.<br>lub uzyskać raport zagregowany według produktów.<br>lub uzyskać raport zagregowany przez interfejsy API lub uzyskać raport zagregowany według operacji lub uzyskać raport zagregowany według subskrypcji.<br>lub Pobierz żądania danych raportowania |
> | Akcja | Microsoft.ApiManagement/service/restore/action | Przywracanie usługi API Management z określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/delete | Usuwa określoną subskrypcję. |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/read | Wyświetla wszystkie subskrypcje wystąpienia usługi API Management. lub pobiera określoną jednostkę subskrypcji. |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Ponownie generuje klucz podstawowy istniejącej subskrypcji wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Generuje ponownie klucz pomocniczy istniejącej subskrypcji wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/write | Tworzy lub aktualizuje subskrypcję określonego użytkownika dla określonego produktu. lub aktualizuje szczegóły subskrypcji określonej przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/tagResources/read | Wyświetla kolekcję zasobów skojarzonych ze znacznikami. |
> | Akcja | Microsoft.ApiManagement/service/tags/delete | Usuwa konkretny tag wystąpienia usługi API Management. |
> | Akcja | Microsoft.ApiManagement/service/tags/read | Wyświetla kolekcję tagów zdefiniowanych w ramach wystąpienia usługi. lub pobiera szczegóły tagu określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/tags/write | Tworzy tag. lub aktualizuje szczegóły tagu określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/templates/delete | Resetuj szablon wiadomości e-mail domyślnego API Management |
> | Akcja | Microsoft.ApiManagement/service/templates/read | Pobiera wszystkie szablony wiadomości e-mail lub pobiera API Management szczegóły szablonu wiadomości e-mail |
> | Akcja | Microsoft.ApiManagement/service/templates/write | Utwórz lub zaktualizuj szablon wiadomości e-mail API Management lub API Management szablon wiadomości e-mail |
> | Akcja | Microsoft.ApiManagement/service/tenant/delete | Usuń konfigurację zasad dla dzierżawy |
> | Akcja | Microsoft.ApiManagement/service/tenant/deploy/action | Uruchamia zadanie wdrażania, aby zastosować zmiany z określonej gałęzi git do konfiguracji w bazie danych. |
> | Akcja | Microsoft.ApiManagement/service/tenant/operationResults/read | Pobierz listę wyników operacji lub Pobierz wynik określonej operacji |
> | Akcja | Microsoft.ApiManagement/service/tenant/read | Pobierz definicję zasad globalnych usługi API Management. lub Uzyskaj szczegółowe informacje o dostępie dzierżawy |
> | Akcja | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Wygeneruj ponownie podstawowy klucz dostępu |
> | Akcja | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Ponowne generowanie pomocniczego klucza dostępu |
> | Akcja | Microsoft.ApiManagement/service/tenant/save/action | Tworzy potwierdzeń z migawką konfiguracji do określonej gałęzi w repozytorium |
> | Akcja | Microsoft.ApiManagement/service/tenant/syncState/read | Pobierz stan ostatniej synchronizacji git |
> | Akcja | Microsoft.ApiManagement/service/tenant/validate/action | Sprawdza poprawność zmian z określonej gałęzi git |
> | Akcja | Microsoft. ApiManagement/Service/dzierżawca/zapis | Ustaw konfigurację zasad dla dzierżawy lub Zaktualizuj szczegóły informacji o dostępie dzierżawy |
> | Akcja | Microsoft.ApiManagement/service/updatecertificate/action | Przekaż certyfikat SSL dla usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/updatehostname/action | Instalowanie, aktualizowanie lub usuwanie niestandardowych nazw domen dla usługi API Management |
> | Akcja | Microsoft.ApiManagement/service/users/action | Rejestrowanie nowego użytkownika |
> | Akcja | Microsoft.ApiManagement/service/users/confirmations/send/action | Wysyła potwierdzenie |
> | Akcja | Microsoft.ApiManagement/service/users/delete | Usuwa określonego użytkownika. |
> | Akcja | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Pobiera adres URL przekierowania zawierający token uwierzytelniania do podpisywania danego użytkownika w portalu dla deweloperów. |
> | Akcja | Microsoft.ApiManagement/service/users/groups/read | Wyświetla listę wszystkich grup użytkowników. |
> | Akcja | Microsoft.ApiManagement/service/users/identities/read | Lista wszystkich tożsamości użytkowników. |
> | Akcja | Microsoft.ApiManagement/service/users/keys/read | Pobierz klucze skojarzone z użytkownikiem |
> | Akcja | Microsoft.ApiManagement/service/users/read | Wyświetla kolekcję zarejestrowanych użytkowników w określonym wystąpieniu usługi. lub pobiera szczegóły użytkownika określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/users/subscriptions/read | Zawiera listę kolekcji subskrypcji określonego użytkownika. |
> | Akcja | Microsoft.ApiManagement/service/users/token/action | Pobiera token autoryzacji dostępu współdzielonego dla użytkownika. |
> | Akcja | Microsoft.ApiManagement/service/users/write | Tworzy lub aktualizuje użytkownika. lub aktualizuje szczegóły użytkownika określonego przez jego identyfikator. |
> | Akcja | Microsoft.ApiManagement/service/write | Utwórz nowe wystąpienie usługi API Management Service |
> | Akcja | Microsoft.ApiManagement/unregister/action | Wyrejestrowywanie subskrypcji dla dostawcy zasobów Microsoft. ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

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
> | Akcja | Microsoft.Authorization/roleDefinitions/read | Pobierz informacje o definicji roli. |
> | Akcja | Microsoft. Authorization/roleDefinitions/Write | Utwórz lub zaktualizuj niestandardową definicję roli z określonymi uprawnieniami i możliwymi do przypisania zakresami. |

## <a name="microsoftautomation"></a>Microsoft.Automation

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
> | Akcja | Microsoft.Automation/automationAccounts/configurations/getCount/action | Odczytuje liczbę zawartości Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/konfiguracje/odczyt | Pobiera zawartość Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/konfiguracje/zapis | Zapisuje zawartość Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/Connections/Delete | Usuwa zasób połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Connections/akcja | Odczytuje liczbę połączeń |
> | Akcja | Microsoft. Automation/automationAccounts/Connections/Read | Pobiera zasób połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Connections/Write | Tworzy lub aktualizuje zasób połączenia Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/connectionTypes/delete | Usuwa zasób typu połączenia Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/connectionTypes/read | Pobiera zasób typu połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/connectionTypes/Write | Tworzy zasób typu połączenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/poświadczenia/usuwanie | Usuwa zasób poświadczenia Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/getCount/action | Odczytuje liczbę poświadczeń |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/read | Pobiera zasób poświadczenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/poświadczenia/zapis | Tworzy lub aktualizuje zasób poświadczenia Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Delete | Usuwa konto Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Usuwa zasoby hybrydowego procesu roboczego elementu Runbook |
> | Akcja | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Akcja | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Pobiera zawartość elementu Runbook Azure Automation w czasie wykonywania zadania |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/stop/action | Kończy zadanie Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadania Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadania Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/jobSchedules/Delete | Usuwa harmonogram zadań Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobSchedules/read | Pobiera harmonogram zadań Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/jobSchedules/write | Tworzy harmonogram zadań Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem usługi Automation |
> | Akcja | Microsoft. Automation/automationAccounts/listKeys/akcja | Odczytuje klucze dla konta usługi Automation |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/działania/odczyt | Pobiera działania Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/usuwanie | Usuwa moduł Azure Automation PowerShell |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/GetCount/Action | Pobiera liczbę modułów programu PowerShell w ramach konta usługi Automation |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/odczyt | Pobiera moduł Azure Automation PowerShell |
> | Akcja | Microsoft. Automation/automationAccounts/moduły/zapis | Tworzy lub aktualizuje moduł Azure Automation PowerShell |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Usuwa konfigurację węzła Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodeConfigurations/rawContent/Action | Odczytuje zawartość konfiguracji węzła Azure Automation DSC |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Odczytuje konfigurację węzła Azure Automation DSC |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Zapisuje konfigurację węzła Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodecounts/odczyt | Odczytuje podsumowanie liczby węzłów dla określonego typu |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Delete | Usuwa Azure Automation węzły DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Read | Odczytuje Azure Automation węzłów DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Reports/Content/Read | Odczytuje zawartość raportu Azure Automation DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Reports/Read | Odczytuje Azure Automation raporty DSC |
> | Akcja | Microsoft. Automation/automationAccounts/nodes/Write | Tworzy lub aktualizuje Azure Automation węzłów DSC |
> | Akcja | Microsoft. Automation/automationAccounts/objectDataTypes/Fields/Read | Pobiera Azure Automation TypeFields |
> | Akcja | Microsoft. Automation/automationAccounts/python2Packages/Delete | Usuwa pakiet Azure Automation Python 2 |
> | Akcja | Microsoft.Automation/automationAccounts/python2Packages/read | Pobiera pakiet Azure Automation Python 2 |
> | Akcja | Microsoft.Automation/automationAccounts/python2Packages/write | Tworzy lub aktualizuje pakiet Azure Automation Python 2 |
> | Akcja | Microsoft. Automation/automationAccounts/python3Packages/Delete | Usuwa pakiet Azure Automation Python 3 |
> | Akcja | Microsoft.Automation/automationAccounts/python3Packages/read | Pobiera pakiet Azure Automation Python 3 |
> | Akcja | Microsoft. Automation/automationAccounts/python3Packages/Write | Tworzy lub aktualizuje pakiet Azure Automation Python 3 |
> | Akcja | Microsoft.Automation/automationAccounts/read | Pobiera konto Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Content/Read | Pobiera zawartość elementu Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Delete | Usuwa element Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/Draft/Content/Write | Tworzy zawartość wersji roboczej elementu Runbook Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Pobiera wyniki operacji wersji roboczej elementu Runbook Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/read | Pobiera wersję roboczą elementu Runbook Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Pobiera zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Wznawia zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Kończy zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Wstrzymuje zadanie testowe wersji roboczej elementu Runbook programu Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Tworzy zadanie testowe wersji roboczej elementu Runbook Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Cofnij edycję wersji roboczej elementu Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Runbooks/GetCount/Action | Pobiera liczbę elementów Runbook Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publikuje wersję roboczą elementu Runbook Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element Runbook Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/write | Tworzy lub aktualizuje element Runbook Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/harmonogramy/usuwanie | Usuwa zasób harmonogramu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/Schedules/GetCount/Action | Pobiera liczbę harmonogramów Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/read | Pobiera zasób harmonogramu Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/write | Tworzy lub aktualizuje zasób harmonogramu Azure Automation |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/odczyt | Pobiera Azure Automation uruchomienie komputera z konfiguracją aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/odczyt | Pobiera Azure Automation uruchomienie konfiguracji aktualizacji oprogramowania |
> | Akcja | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Usuwa Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Usuwa Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Pobiera Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Pobiera Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Tworzy lub aktualizuje Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Tworzy lub aktualizuje Azure Automation konfigurację aktualizacji oprogramowania |
> | Akcja | Microsoft. Automation/automationAccounts/Statystyka/odczyt | Pobiera statystyki Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Pobierz maszynę wdrożenia aktualizacji Azure Automation |
> | Akcja | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Pobiera zadanie aktualizacji Azure Automation Update Management |
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
> | Akcja | Microsoft.Automation/automationAccounts/write | Tworzy lub aktualizuje konto Azure Automation |
> | Akcja | Microsoft. Automation/Operations/Read | Pobiera dostępne operacje dla Azure Automation zasobów |
> | Akcja | Microsoft. Automation/rejestrowanie/akcja | Rejestruje subskrypcję w usłudze Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Usuń zasób katalogu B2C |
> | Akcja | Microsoft.AzureActiveDirectory/b2cDirectories/read | Wyświetl zasób katalogu B2C |
> | Akcja | Microsoft.AzureActiveDirectory/b2cDirectories/write | Utwórz lub zaktualizuj zasób katalogu B2C |
> | Akcja | Microsoft.AzureActiveDirectory/b2ctenants/read | Wyświetla wszystkie dzierżawy B2C, w których użytkownik jest członkiem |
> | Akcja | Microsoft.AzureActiveDirectory/operations/read | Przeczytaj wszystkie operacje interfejsu API dostępne dla dostawcy zasobów Microsoft. usługi azureactivedirectory |
> | Akcja | Microsoft.AzureActiveDirectory/register/action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. usługi azureactivedirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. AzureStack/Operations/Read | Pobiera właściwości operacji dostawcy zasobów |
> | Akcja | Microsoft. AzureStack/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. AzureStack |
> | Akcja | Microsoft. AzureStack/registrations/customerSubscriptions/Delete | Usuwa subskrypcję klienta Azure Stack |
> | Akcja | Microsoft.AzureStack/registrations/customerSubscriptions/read | Pobiera właściwości subskrypcji klienta Azure Stack |
> | Akcja | Microsoft.AzureStack/registrations/customerSubscriptions/write | Tworzy lub aktualizuje subskrypcję klienta Azure Stack |
> | Akcja | Microsoft. AzureStack/rejestracje/usuwanie | Usuwa rejestrację Azure Stack |
> | Akcja | Microsoft.AzureStack/registrations/getActivationKey/action | Pobiera najnowszy klucz aktywacji Azure Stack |
> | Akcja | Microsoft.AzureStack/registrations/products/listDetails/action | Pobiera rozszerzone szczegóły dotyczące produktu Azure Stack Marketplace |
> | Akcja | Microsoft.AzureStack/registrations/products/read | Pobiera właściwości produktu Azure Stack Marketplace |
> | Akcja | Microsoft. AzureStack/rejestracje/produkty/uploadProductLog/akcja | Rejestrowanie stanu i sygnatury czasowej operacji produktu Azure Stack Marketplace |
> | Akcja | Microsoft.AzureStack/registrations/read | Pobiera właściwości rejestracji Azure Stack |
> | Akcja | Microsoft. AzureStack/rejestracje/zapis | Tworzy lub aktualizuje rejestrację Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Batch/batchAccounts/applications/delete | Usuwa aplikację |
> | Akcja | Microsoft.Batch/batchAccounts/applications/read | Wyświetla listę aplikacji lub pobiera właściwości aplikacji |
> | Akcja | Microsoft. Batch/batchAccounts/Applications/Versions/Activate/Action | Aktywuje pakiet aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/delete | Usuwa pakiet aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/read | Pobiera właściwości pakietu aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/write | Tworzy nowy pakiet aplikacji lub aktualizuje istniejący pakiet aplikacji |
> | Akcja | Microsoft. Batch/batchAccounts/Applications/Write | Tworzy nową aplikację lub aktualizuje istniejącą aplikację |
> | Akcja | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Pobiera wyniki długotrwałej operacji certyfikatu na koncie wsadowym |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Anuluje nieudane usunięcie certyfikatu na koncie wsadowym |
> | Akcja | Microsoft. Batch/batchAccounts/Certificates/Delete | Usuwa certyfikat z konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/read | Wyświetla listę certyfikatów na koncie wsadowym lub pobiera właściwości certyfikatu |
> | Akcja | Microsoft. Batch/batchAccounts/Certificates/Write | Tworzy nowy certyfikat na koncie wsadowym lub aktualizuje istniejący certyfikat |
> | Akcja | Microsoft.Batch/batchAccounts/delete | Usuwa konto wsadowe |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Delete | Usuwa zadanie z konta usługi Batch |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Read | Wyświetla listę zadań na koncie wsadowym lub pobiera właściwości zadania |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/Jobs/Write | Tworzy nowe zadanie na koncie wsadowym lub aktualizuje istniejące zadanie |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Delete | Usuwa harmonogram zadań z konta usługi Batch |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/odczyt | Wyświetla listę harmonogramów zadań na koncie wsadowym lub pobiera właściwości harmonogramu zadań |
> | Akcja dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Write | Tworzy nowy harmonogram zadań na koncie wsadowym lub aktualizuje istniejący harmonogram zadań |
> | Akcja | Microsoft.Batch/batchAccounts/listkeys/action | Wyświetla klucze dostępu dla konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/operationResults/read | Pobiera wyniki długotrwałej operacji konta partii |
> | Akcja | Microsoft.Batch/batchAccounts/poolOperationResults/read | Pobiera wyniki długotrwałej operacji puli na koncie wsadowym |
> | Akcja | Microsoft.Batch/batchAccounts/pools/delete | Usuwa pulę z konta usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Wyłącza automatyczne skalowanie puli kont usługi Batch |
> | Akcja | Microsoft.Batch/batchAccounts/pools/read | Wyświetla listę pul na koncie wsadowym lub pobiera właściwości puli |
> | Akcja | Microsoft.Batch/batchAccounts/pools/stopResize/action | Kończy trwającą operację zmiany rozmiaru w puli kont wsadowych |
> | Akcja | Microsoft.Batch/batchAccounts/pools/write | Tworzy nową pulę na koncie wsadowym lub aktualizuje istniejącą pulę |
> | Akcja | Microsoft.Batch/batchAccounts/read | Wyświetla listę kont wsadowych lub pobiera właściwości konta w usłudze Batch |
> | Akcja | Microsoft.Batch/batchAccounts/regeneratekeys/action | Generuje ponownie klucze dostępu dla konta usługi Batch |
> | Akcja | Microsoft. Batch/batchAccounts/syncAutoStorageKeys/Action | Synchronizuje klucze dostępu dla konta usługi autostorage skonfigurowanego dla konta usługi Batch |
> | Akcja | Microsoft. Batch/batchAccounts/Write | Tworzy nowe konto wsadowe lub aktualizuje istniejące konto w usłudze Batch |
> | Akcja | Microsoft.Batch/locations/accountOperationResults/read | Pobiera wyniki długotrwałej operacji konta partii |
> | Akcja | Microsoft.Batch/locations/checkNameAvailability/action | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft.Batch/locations/quotas/read | Pobiera przydziały usługi Batch dla określonej subskrypcji w określonym regionie platformy Azure |
> | Akcja | Microsoft.Batch/operations/read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. Batch |
> | Akcja | Microsoft.Batch/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Batch i umożliwia tworzenie kont usługi Batch |
> | Akcja | Microsoft.Batch/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów usługi Batch uniemożliwiającą tworzenie kont usługi Batch |

## <a name="microsoftbilling"></a>Microsoft.Billing

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

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.BingMaps/mapApis/Delete | Operacja usuwania |
> | Akcja | Microsoft.BingMaps/mapApis/listSecrets/action | Wystaw wpisy tajne |
> | Akcja | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Odczytaj Token autoryzacji logowania jednokrotnego dla zasobu |
> | Akcja | Microsoft.BingMaps/mapApis/Read | Operacja odczytu |
> | Akcja | Microsoft.BingMaps/mapApis/regenerateKey/action | Generuje ponownie klucz |
> | Akcja | Microsoft.BingMaps/mapApis/Write | Operacja zapisu |
> | Akcja | Microsoft.BingMaps/Operations/read | Opis operacji. |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/Delete | Usuwa istniejącego członka łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/listApiKeys/akcja | Pobiera lub Wyświetla istniejące klucze interfejsu API elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/odczyt | Pobiera lub Wyświetla istniejące elementy członkowskie łańcucha bloków. |
> | Akcja dataaction | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Connect/Action | Nawiązuje połączenie z węzłem transakcji elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Usuwa istniejący węzeł transakcji elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Pobiera lub Wyświetla istniejące klucze interfejsu API węzła transakcji elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Pobiera lub Wyświetla istniejące węzły transakcji składowych łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Write | Tworzy lub aktualizuje węzeł transakcji elementu członkowskiego łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/blockchainMembers/Write | Tworzy lub aktualizuje element członkowski łańcucha bloków. |
> | Akcja | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Pobiera wyniki operacji elementów członkowskich łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/Locations/checkNameAvailability/Action | Sprawdza, czy nazwa zasobu jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft. łańcucha bloków/Operations/Read | Wyświetl listę wszystkich operacji w dostawcy zasobów programu Microsoft łańcucha bloków. |
> | Akcja | Microsoft. łańcucha bloków/Register/Action | Rejestruje subskrypcję dostawcy zasobów łańcucha bloków. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

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

## <a name="microsoftbotservice"></a>Microsoft.BotService

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
> | Akcja | Microsoft.BotService/locations/operationresults/read | Odczytywanie stanu operacji asynchronicznej |
> | Akcja | Microsoft. BotService/Operations/Read | Odczytaj operacje dla wszystkich typów zasobów |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. cache/checknameavailability/akcja | Sprawdza, czy nazwa jest dostępna do użycia z nowym Redis Cache |
> | Akcja | Microsoft.Cache/locations/operationresults/read | Pobiera wynik długotrwałej operacji, dla której nagłówek "Location" został wcześniej zwrócony do klienta |
> | Akcja | Microsoft.Cache/operations/read | Wyświetla listę operacji obsługiwanych przez dostawcę "Microsoft. cache". |
> | Akcja | Microsoft. cache/Redis/Delete | Usuń cały Redis Cache |
> | Akcja | Microsoft. cache/Redis/eksport/akcja | Eksportuj dane Redis do przedprawionych obiektów blob magazynu w określonym formacie |
> | Akcja | Microsoft. cache/Redis/firewallRules/Delete | Usuwanie reguł zapory adresów IP Redis Cache |
> | Akcja | Microsoft. cache/Redis/firewallRules/odczyt | Pobieranie reguł zapory adresów IP Redis Cache |
> | Akcja | Microsoft. cache/Redis/firewallRules/zapis | Edytowanie reguł zapory adresów IP Redis Cache |
> | Akcja | Microsoft. cache/Redis/forceReboot/akcja | Wymuś ponowne uruchomienie wystąpienia pamięci podręcznej, potencjalnie z utratą danych. |
> | Akcja | Microsoft. cache/Redis/import/akcja | Importuj dane określonego formatu z wielu obiektów BLOB do Redis |
> | Akcja | Microsoft.Cache/redis/linkedservers/delete | Usuwanie połączonego serwera z Redis Cache |
> | Akcja | Microsoft.Cache/redis/linkedservers/read | Pobierz połączone serwery skojarzone z pamięcią podręczną Redis. |
> | Akcja | Microsoft.Cache/redis/linkedservers/write | Dodawanie połączonego serwera do Redis Cache |
> | Akcja | Microsoft. cache/Redis/listKeys/akcja | Wyświetlanie wartości Redis Cache kluczy dostępu w portalu zarządzania |
> | Akcja | Microsoft.Cache/redis/listUpgradeNotifications/read | Wyświetl listę najnowszych powiadomień dotyczących uaktualnienia dla dzierżawy pamięci podręcznej. |
> | Akcja | Microsoft. cache/Redis/metricDefinitions/odczyt | Pobiera dostępne metryki dla Redis Cache |
> | Akcja | Microsoft. cache/Redis/patchSchedules/Delete | Usuwanie harmonogramu poprawek Redis Cache |
> | Akcja | Microsoft.Cache/redis/patchSchedules/read | Pobiera harmonogram stosowania poprawek Redis Cache |
> | Akcja | Microsoft. cache/Redis/patchSchedules/zapis | Modyfikowanie harmonogramu poprawek Redis Cache |
> | Akcja | Microsoft.Cache/redis/read | Wyświetlanie ustawień i konfiguracji Redis Cache w portalu zarządzania |
> | Akcja | Microsoft. cache/Redis/regenerateKey/akcja | Zmiana wartości Redis Cache kluczy dostępu w portalu zarządzania |
> | Akcja | Microsoft. cache/Redis/Start/akcja | Uruchom wystąpienie pamięci podręcznej. |
> | Akcja | Microsoft. cache/Redis/Stop/akcja | Zatrzymaj wystąpienie pamięci podręcznej. |
> | Akcja | Microsoft. cache/Redis/Write | Modyfikowanie ustawień i konfiguracji Redis Cache w portalu zarządzania |
> | Akcja | Microsoft. cache/Register/Action | Rejestruje dostawcę zasobów "Microsoft. cache" z subskrypcją |
> | Akcja | Microsoft. cache/Unregister/akcja | Wyrejestrowuje dostawcę zasobów "Microsoft. cache" z subskrypcją |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Capacity/appliedreservations/read | Odczytuj wszystkie rezerwacje |
> | Akcja | Microsoft. Pojemność/calculateexchange/akcja | Oblicza kwotę i cenę wymiany nowych zakupów i zwraca błędy zasad. |
> | Akcja | Microsoft. Pojemność/calculateprice/akcja | Oblicz dowolną cenę rezerwacji |
> | Akcja | Microsoft. Pojemność/wykazy/odczyt | Odczytaj wykaz rezerwacji |
> | Akcja | Microsoft. Pojemność/checkoffers/akcja | Sprawdź wszystkie oferty subskrypcji |
> | Akcja | Microsoft. Pojemność/checkscopes/akcja | Sprawdź dowolną subskrypcję |
> | Akcja | Microsoft. Pojemność/commercialreservationorders/odczyt | Pobieranie zamówień rezerwacji utworzonych w dowolnej dzierżawie |
> | Akcja | Microsoft. Pojemność/wymiana/akcja | Wymiana dowolnej rezerwacji |
> | Akcja | Microsoft.Capacity/operations/read | Odczytaj dowolną operację |
> | Akcja | Microsoft. Pojemność/rejestr/akcja | Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie zasobów pojemności. |
> | Akcja | Microsoft. Pojemność/reservationorders/akcja | Aktualizowanie dowolnej rezerwacji |
> | Akcja | Microsoft. Pojemność/reservationorders/availablescopes/akcja | Znajdowanie dowolnego dostępnego zakresu |
> | Akcja | Microsoft. Pojemność/reservationorders/calculaterefund/akcja | Oblicza kwotę zwrotu i cenę nowego zakupu oraz zwraca błędy zasad. |
> | Akcja | Microsoft. Pojemność/reservationorders/usuwanie | Usuń dowolne zastrzeżenie |
> | Akcja | Microsoft. Pojemność/reservationorders/scalanie/akcja | Scalanie wszelkich rezerwacji |
> | Akcja | Microsoft.Capacity/reservationorders/read | Odczytuj wszystkie rezerwacje |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/akcja | Aktualizowanie dowolnej rezerwacji |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/availablescopes/akcja | Znajdowanie dowolnego dostępnego zakresu |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/usuwanie | Usuń dowolne zastrzeżenie |
> | Akcja | Microsoft. Pojemność/reservationorders/rezerwacje/odczyt | Odczytuj wszystkie rezerwacje |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/revisions/read | Odczytuj wszystkie rezerwacje |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/write | Utwórz dowolną rezerwację |
> | Akcja | Microsoft. Pojemność/reservationorders/powrót/akcja | Zwróć wszelkie rezerwacje |
> | Akcja | Microsoft. Pojemność/reservationorders/Split/akcja | Podziel wszystkie rezerwacje |
> | Akcja | Microsoft. Pojemność/reservationorders/swap/akcja | Zamień wszystkie rezerwacje |
> | Akcja | Microsoft.Capacity/reservationorders/write | Utwórz dowolną rezerwację |
> | Akcja | Microsoft. Pojemność/dzierżawcy/rejestr/akcja | Rejestracja dowolnej dzierżawy |
> | Akcja | Microsoft. Pojemność/Wyrejestrowanie/akcja | Wyrejestrowywanie dzierżawy |
> | Akcja | Microsoft.Capacity/validatereservationorder/action | Weryfikowanie wszelkich rezerwacji |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. CDN/CheckNameAvailability/akcja |  |
> | Akcja | Microsoft. CDN/CheckResourceUsage/akcja |  |
> | Akcja | Microsoft.Cdn/edgenodes/delete |  |
> | Akcja | Microsoft.Cdn/edgenodes/read |  |
> | Akcja | Microsoft.Cdn/edgenodes/write |  |
> | Akcja | Microsoft. CDN/operationresults/Delete |  |
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
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/przeczyszczanie/akcja |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Akcja | Microsoft. CDN/operationresults/profileresults/endpointresults/Start/akcja |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Akcja | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Akcja | Microsoft.Cdn/operationresults/read |  |
> | Akcja | Microsoft. CDN/operationresults/Write |  |
> | Akcja | Microsoft.Cdn/operations/read |  |
> | Akcja | Microsoft. CDN/profile/CheckResourceUsage/akcja |  |
> | Akcja | Microsoft. CDN/profile/usuwanie |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/customdomains/EnableCustomHttps/Action |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/customdomains/Write |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/obciążenie/akcja |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/źródła/zapis |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/Przeczyść/akcja |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/read |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/uruchomienie/akcja |  |
> | Akcja | Microsoft. CDN/profile/Endpoints/akcja |  |
> | Akcja | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Akcja | Microsoft. CDN/profile/punkty końcowe/zapis |  |
> | Akcja | Microsoft. CDN/profile/GenerateSsoUri/akcja |  |
> | Akcja | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Akcja | Microsoft.Cdn/profiles/read |  |
> | Akcja | Microsoft. CDN/profile/zapis |  |
> | Akcja | Microsoft. CDN/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi CDN i umożliwia tworzenie profilów sieci CDN. |
> | Akcja | Microsoft. CDN/ValidateProbe/akcja |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Usuń istniejący certyfikat |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Pobieranie listy certyfikatów |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejący |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/Delete | Usuń istniejący AppServiceCertificate |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/Read | Pobierz listę CertificateOrders |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Wydaj ponownie istniejące zamówienia certyfikatu |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/Renew/akcja | Odnów istniejący zamówienia certyfikatu |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Wyślij ponownie wiadomość e-mail z certyfikatem |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Wyślij ponownie wiadomości e-mail z żądaniem na inny adres e-mail |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Pobierz pieczęć witryny dla wystawionego Certyfikat usługi App Service |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/retrieveCertificateActions/akcja | Pobieranie listy akcji certyfikatów |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/retrieveEmailHistory/akcja | Pobierz historię poczty e-mail certyfikatu |
> | Akcja | Microsoft. CertificateRegistration/certificateOrders/verifyDomainOwnership/akcja | Weryfikuj własność domeny |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/Write | Dodaj nowy zamówienia certyfikatu lub zaktualizuj istniejący |
> | Akcja | Microsoft.CertificateRegistration/operations/Read | Wyświetl listę wszystkich operacji z rejestracji certyfikatu usługi App Service |
> | Akcja | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Zainicjuj obsługę główną usługi dla jednostki aplikacji usługi |
> | Akcja | Microsoft. CertificateRegistration/Register/Action | Zarejestruj dostawcę zasobów Microsoft Certificates dla subskrypcji |
> | Akcja | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Weryfikowanie obiektu zakupu certyfikatu bez jego przesyłania |

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
> | Akcja | Microsoft.ClassicCompute/domainNames/delete | Usuń nazwy domen dla zasobów. |
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
> | Akcja | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Odczytuje stan operacji dla wewnętrznych modułów równoważenia obciążenia nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Read | Pobiera wewnętrzne moduły równoważenia obciążenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Write | Tworzy nowe wewnętrzne równoważenie obciążenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Odczytuje stan operacji dla zestawów punktów końcowych z równoważeniem obciążenia nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Read | Pobierz zestawy punktów końcowych ze zrównoważonym obciążeniem. |
> | Akcja | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Write | Dodaj zestaw punktów końcowych ze zrównoważonym obciążeniem. |
> | Akcja | Microsoft. ClassicCompute/domainNames/operationstatuses/Read | Pobierz stan operacji dla nazwy domeny. |
> | Akcja | Microsoft. ClassicCompute/domainNames/operationStatuses/Read | Odczytuje stan operacji dla rozszerzeń nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/read | Zwróć nazwy domen dla zasobów. |
> | Akcja | Microsoft. ClassicCompute/domainNames/servicecertificates/Delete | Usuń używane certyfikaty usługi. |
> | Akcja | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Odczytuje stan operacji dla certyfikatów usługi nazw domen. |
> | Akcja | Microsoft. ClassicCompute/domainNames/servicecertificates/Read | Zwraca używane certyfikaty usługi. |
> | Akcja | Microsoft. ClassicCompute/domainNames/servicecertificates/Write | Dodaj lub zmodyfikuj używane certyfikaty usługi. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/abortMigration/Action | Przerywa migrację miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/commitMigration/Action | Zatwierdza migrację miejsca wdrożenia. |
> | Akcja | Microsoft. ClassicCompute/domainNames/Slots/Delete | Usuwa określone miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Odczytuje stan operacji dla gniazd nazw domen. |
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
> | Akcja | Microsoft.ClassicCompute/domainNames/write | Dodaj lub zmodyfikuj nazwy domen dla zasobów. |
> | Akcja | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Przenieś wszystkie zasoby klasyczne do innej subskrypcji. |
> | Akcja | Microsoft.ClassicCompute/operatingSystemFamilies/read | Wyświetla listę rodzin systemów operacyjnych gościa dostępnych w Microsoft Azure, a także listę wersji systemu operacyjnego dostępnych dla każdej rodziny. |
> | Akcja | Microsoft.ClassicCompute/operatingSystems/read | Wyświetla listę wersji systemu operacyjnego gościa, które są obecnie dostępne w Microsoft Azure. |
> | Akcja | Microsoft. ClassicCompute/Operations/Read | Pobiera listę operacji. |
> | Akcja | Microsoft.ClassicCompute/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.ClassicCompute/quotas/read | Pobieranie przydziału dla subskrypcji. |
> | Akcja | Microsoft. ClassicCompute/Register/Action | Zarejestruj się w ramach obliczeń klasycznych |
> | Akcja | Microsoft.ClassicCompute/resourceTypes/skus/read | Pobiera listę jednostek SKU dla obsługiwanych typów zasobów. |
> | Akcja | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Sprawdź dostępność subskrypcji dla klasycznej operacji przenoszenia. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszynami wirtualnymi. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Pobiera możliwe operacje asynchroniczne |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/attachDisk/akcja | Dołącza dysk danych do maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/przechwytywanie/akcja | Przechwyć maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/delete | Usuwa maszyny wirtualne. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/detachDisk/akcja | Odłącza dysk danych z maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/diagnosticsettings/Read | Pobierz ustawienia diagnostyki maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/disks/read | Pobiera listę dysków z danymi |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Pobiera plik RDP dla maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Odczytuje stan operacji dla rozszerzeń maszyn wirtualnych. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/extensions/read | Pobiera rozszerzenie maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Extensions/Write | Umieszcza rozszerzenie maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Pobierz definicję metryki maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/metrics/read | Pobiera metryki. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszynami wirtualnymi. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Odczytuje stan operacji dla maszyn wirtualnych. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/performMaintenance/akcja | Wykonuje konserwację na maszynie wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/read | Pobiera listę maszyn wirtualnych. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Ponownie wdraża maszynę wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/restart/akcja | Uruchamia ponownie maszyny wirtualne. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Shutdown/akcja | Zamknij maszynę wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Start/akcja | Uruchom maszynę wirtualną. |
> | Akcja | Microsoft. ClassicCompute/virtualMachines/Stop/akcja | Wyłącza maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/write | Dodaj lub zmodyfikuj maszyny wirtualne. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Pobierz stan operacji połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/Komunikacja równorzędna/usuwanie | Usuń komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Pobierz stan operacji komunikacji równorzędnej połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Pobierz komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/Peers/Write | Dodaj komunikację równorzędną połączenia krzyżowego usługi Express Route. |
> | Akcja | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Pobierz połączenia krzyżowe w usłudze Express Route. |
> | Akcja | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Dodaj połączenia krzyżowe dla trasy Express Route. |
> | Akcja | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Pobiera listę obsługiwanych urządzeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla sieciowej grupy zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne sieciowych grup zabezpieczeń |
> | Akcja | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne grup zabezpieczeń sieci. Ta operacja jest uzupełniana przez dostawcę zasobów usługi Insights. |
> | Akcja | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Pobiera zdarzenia dla sieciowej grupy zabezpieczeń |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Usuwa regułę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Odczytuje stan operacji dla reguł zabezpieczeń sieciowych grup zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Pobiera regułę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Dodaje lub aktualizuje regułę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/write | Dodaje nową sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft. ClassicNetwork/Operations/Read | Pobierz operacje dotyczące sieci klasycznej. |
> | Akcja | Microsoft.ClassicNetwork/quotas/read | Pobieranie przydziału dla subskrypcji. |
> | Akcja | Microsoft. ClassicNetwork/Register/Action | Zarejestruj w sieci klasycznej |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/delete | Usuń zastrzeżony adres IP. |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/join/action | Dołącz zastrzeżony adres IP |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/link/action | Połącz zastrzeżony adres IP |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Odczytuje stan operacji dla zarezerwowanych adresów IP. |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/read | Pobiera zastrzeżone adresy IP |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/write | Dodaj nowy zastrzeżony adres IP |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/akcja | Przerywa migrację Virtual Network |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/możliwości/odczyt | Pokazuje możliwości |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/akcja | Sprawdza dostępność danego adresu IP w sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/akcja | Zatwierdza migrację Virtual Network |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Delete | Usuwa sieć wirtualną. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Cofa odwołanie certyfikatu klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Odczytaj odwołane certyfikaty klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Odwołuje certyfikat klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Usuwa certyfikat klienta bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Pobiera certyfikat według odcisku palca. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Wyświetla pakiet certyfikatu bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Znajdź certyfikaty główne klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Przekazuje nowy certyfikat główny klienta. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Connect/Action | Nawiązuje połączenie z lokacją usługi Site Gateway. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Disconnect/Action | Rozłącza połączenie między lokacją a bramą lokacji. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/Read | Pobiera listę połączeń. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Connections/test/akcja | Testuje połączenie bramy lokacja-lokacja. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Delete | Usuwa bramę sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Pobiera skrypt konfiguracji urządzenia. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/downloadDiagnostics/Action | Pobiera diagnostykę bramy. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Pobiera klucz usługi obwodu. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/listPackage/Action | Wyświetla pakiet bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Odczytuje stan operacji dla bram sieci wirtualnych. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Packages/Read | Pobiera pakiet bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/Read | Pobiera bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/startDiagnostics/Action | Uruchamia diagnostykę dla bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Gateways/stopDiagnostics/Action | Wyłącza diagnostykę bramy sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Brama/zapis | Dodaje bramę sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/join/action | Przyłącza do sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Odczytuje stan operacji dla sieci wirtualnych. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/peer/Action | Tworzy równorzędną sieć wirtualną z inną siecią wirtualną. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/akcja | Przygotowuje migrację Virtual Network |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/read | Pobierz sieć wirtualną. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Usuwa zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Pobiera zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Dodaje lub aktualizuje zdalny serwer proxy komunikacji równorzędnej sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grupy zabezpieczeń sieci skojarzonej z podsiecią sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/akcja | Sprawdza poprawność migracji Virtual Network |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Pobiera komunikację równorzędną sieci wirtualnej. |
> | Akcja | Microsoft. ClassicNetwork/virtualNetworks/Write | Dodaj nową sieć wirtualną. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ClassicStorage/możliwości/odczyt | Pokazuje możliwości |
> | Akcja | Microsoft. ClassicStorage/checkStorageAccountAvailability/Action | Sprawdza dostępność konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/checkStorageAccountAvailability/odczyt | Pobierz dostępność konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/disks/read | Zwraca dysk konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/images/operationstatuses/Read | Pobiera stan operacji obrazu. |
> | Akcja | Microsoft.ClassicStorage/images/read | Zwraca obraz. |
> | Akcja | Microsoft. ClassicStorage/Operations/Read | Pobiera operacje magazynu klasycznego |
> | Akcja | Microsoft.ClassicStorage/osImages/read | Zwraca obraz systemu operacyjnego. |
> | Akcja | Microsoft.ClassicStorage/osPlatformImages/read | Pobiera obraz platformy systemu operacyjnego. |
> | Akcja | Microsoft.ClassicStorage/publicImages/read | Pobiera publiczny obraz maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicStorage/quotas/read | Pobieranie przydziału dla subskrypcji. |
> | Akcja | Microsoft. ClassicStorage/Register/Action | Zarejestruj do magazynu klasycznego |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/abortMigration/akcja | Przerywa migrację konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/commitMigration/akcja | Zatwierdza migrację konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Delete | Usuń konto magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/disks/Delete | Usuwa dany dysk konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/read | Zwraca dysk konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/disks/Write | Dodaje dysk konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/images/Delete | Usuwa dany obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/images/operationstatuses/Read | Zwraca stan operacji obrazu konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/images/read | Zwraca obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/operationStatuses/Read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Usuwa dany obraz systemu operacyjnego konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/osImages/read | Zwraca obraz systemu operacyjnego konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/osImages/write | Dodaje dany obraz systemu operacyjnego konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/prepareMigration/akcja | Przygotowuje migrację konta magazynu. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobierz ustawienia diagnostyki. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera definicje metryk. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/read | Zwróć konto magazynu z danym kontem. |
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
> | Akcja | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Usuwa dany obraz maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Pobiera stan operacji obrazu maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Zwraca obraz maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Dodaje dany obraz maszyny wirtualnej. |
> | Akcja | Microsoft. ClassicStorage/storageAccounts/Write | Dodaje nowe konto magazynu. |
> | Akcja | Microsoft.ClassicStorage/vmImages/read | Wyświetla listę obrazów maszyn wirtualnych. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/automatyczne sugerowanie/wyszukiwanie/akcja | Ta operacja zawiera sugestie dotyczące danego zapytania lub zapytania częściowego. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ComputerVision/analiza/akcja | Ta operacja wyodrębnia bogaty zestaw funkcji wizualnych na podstawie zawartości obrazu.  |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Ta operacja zwraca pole ograniczenia wokół najważniejszego obszaru obrazu. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Ta operacja generuje opis obrazu w języku czytelnym dla ludzi z pełnymi zdaniami.<br> Opis jest oparty na kolekcji tagów zawartości, które są również zwracane przez operację.<br>Dla każdego obrazu można wygenerować więcej niż jeden opis.<br> Opisy są uporządkowane według ich wyniku ufności.<br>Wszystkie opisy są w języku angielskim. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Ta operacja służy do wykrywania obiektów na określonym obrazie.  |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Ta operacja generuje obraz miniatury z szerokością i wysokością określoną przez użytkownika.<br> Domyślnie usługa analizuje obraz, identyfikuje region zainteresowania (zwrotny) i generuje inteligentne współrzędne przycinania na podstawie zwrotu z inwestycji.<br> Inteligentne przycinanie pomaga w przypadku określenia współczynnika proporcji, który różni się od obrazu wejściowego |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Ta operacja rozpoznaje zawartość w obrazie, stosując model specyficzny dla domeny.<br> Listę modeli specyficznych dla domeny, które są obsługiwane przez interfejs API przetwarzania obrazów, można pobrać przy użyciu żądania GET/models.<br> Obecnie interfejs API udostępnia następujące modele specyficzne dla domeny: osobistości, dzielnice. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Ta operacja zwraca listę modeli specyficznych dla domeny, które są obsługiwane przez interfejs API przetwarzania obrazów.  Obecnie interfejs API obsługuje następujące modele specyficzne dla domeny: aparat rozpoznawania osobistości, punkt rozpoznawania punktu orientacyjnego. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | Optyczne rozpoznawanie znaków (OCR) wykrywa tekst w obrazie i wyodrębnia rozpoznane znaki do strumienia znaków do użycia maszynowego.    |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Użyj tego interfejsu, aby uzyskać wynik operacji Rozpoznawanie tekstu. Gdy używasz interfejsu Rozpoznawanie tekstu, odpowiedź zawiera pole o nazwie "Operation-Location". Pole "operacja — lokalizacja" zawiera adres URL, którego należy użyć dla operacji pobierania Rozpoznawanie tekstu. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Ta operacja generuje listę słów lub tagów, które są istotne dla zawartości dostarczonego obrazu.<br>Interfejs API przetwarzania obrazów może zwracać Tagi na podstawie obiektów, żywych, scenerii lub akcji znalezionych w obrazach.<br>W przeciwieństwie do kategorii, Tagi nie są zorganizowane zgodnie z hierarchicznym systemem klasyfikacji, ale odpowiadają zawartości obrazu.<br>Tagi mogą zawierać wskazówki, aby uniknąć niejednoznaczności lub zapewnienia kontekstu, na przykład tag "Cello" może towarzyszyć wskazówki "instrument muzyczny".<br>Wszystkie Tagi są w języku angielskim. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Ten interfejs jest używany do pobierania wyniku operacji tekstu. Adres URL tego interfejsu powinien zostać pobrany z pola <b>"Lokalizacja operacji"</b> zwróconego z interfejsu rozpoznawanie tekstu. |
> | Akcja | Microsoft.CognitiveServices/accounts/delete | Usuwa konta interfejsu API |
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
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImageSearch/szczegóły/akcja | Zwraca szczegółowe informacje o obrazie, takie jak strony sieci Web, które zawierają obraz. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImageSearch/Search/Action | Pobierz odpowiednie obrazy dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImageSearch/trendy/akcja | Pobierz obecnie popularne obrazy. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/Action | Tworzy sesję czytnika immersyjny |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/InkRecognizer/Rozpoznaj/akcja | Dane zestawu pociągnięć analizują zawartość i generują listę rozpoznanych jednostek, w tym rozpoznany tekst. |
> | Akcja | Microsoft.CognitiveServices/accounts/listKeys/action | Klucze list |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Pobiera opublikowaną prognozę punktów końcowych dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/categorysearch/Action | Zwraca wiadomości dla podanej kategorii. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/Search/Action | Uzyskaj artykuły z wiadomościami istotnymi dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/NewsSearch/Trendingtopics/Action | Poznaj tematy trendów identyfikowane przez usługę Bing. Są to te same tematy widoczne na transparencie u dołu strony głównej usługi Bing. |
> | Akcja | Microsoft.CognitiveServices/accounts/read | Odczytuje konta interfejsu API. |
> | Akcja | Microsoft.CognitiveServices/accounts/regenerateKey/action | Wygeneruj ponownie klucz |
> | Akcja | Microsoft.CognitiveServices/accounts/skus/read | Odczytuje dostępne jednostki SKU dla istniejącego zasobu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/sprawdzanie pisowni/sprawdzanie pisowni/akcja | Pobierz wynik zapytania sprawdzania pisowni za pomocą GET lub POST. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | Interfejs API zwraca listę znanych jednostek i ogólnych nazwanych jednostek (\"osoby\", \"lokalizację\", \"organizacji\" itp.) w danym dokumencie. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | Interfejs API zwraca listę ciągów wskazujących najważniejsze punkty w tekście wejściowym. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | Interfejs API zwraca wykryty język i wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 oznaczają 100% pewności, że określony język jest poprawny. Obsługiwanych jest 120 języków. |
> | Akcja dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | Interfejs API zwraca wynik liczbowy z zakresu od 0 do 1.<br>Wyniki zbliżone do 1 oznaczają pozytywne tonacji, podczas gdy wyniki zbliżone do 0 oznaczają ujemną tonacji.<br>Wynik 0,5 wskazuje brak tonacji (np.<br>Instrukcja factoid). |
> | Akcja | Microsoft.CognitiveServices/accounts/usages/read | Pobierz użycie przydziału dla istniejącego zasobu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/szczegóły/akcja | Uzyskaj wgląd w dane wideo, takie jak powiązane wideo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/Search/Action | Pobierz wideo odpowiednie dla danego zapytania. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VideoSearch/trendy/akcja | Pobierz obecnie trendy wideo. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/VisualSearch/Search/Action | Zwraca listę tagów odnoszących się do podanego obrazu. |
> | Akcja dataaction | Microsoft. CognitiveServices/accounts/WebSearch/Search/Action | Pobierz wyniki dla danego zapytania z Internetu, obrazu, wiadomości & wideo. |
> | Akcja | Microsoft.CognitiveServices/accounts/write | Zapisuje konta interfejsu API. |
> | Akcja | Microsoft.CognitiveServices/checkDomainAvailability/action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Akcja | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Akcja | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Odczytuje dostępne jednostki SKU dla subskrypcji. |
> | Akcja | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Powiadomienie z firmy Microsoft. Network usuwającej VirtualNetworks lub podsieci. |
> | Akcja | Microsoft.CognitiveServices/Operations/read | Wyświetl wszystkie dostępne operacje |
> | Akcja | Microsoft.CognitiveServices/register/action | Rejestruje subskrypcję dla Cognitive Services |
> | Akcja | Microsoft.CognitiveServices/register/action | Rejestruje subskrypcję dla Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Commerce/RateCard/read | Zwraca dane oferty, metadane zasobów/licznika i stawki dla danej subskrypcji. |
> | Akcja | Microsoft.Commerce/UsageAggregates/read | Pobiera użycie Microsoft Azure przez subskrypcję. Wynik zawiera zagregowane dane użycia, informacje dotyczące subskrypcji i zasobów, w określonym przedziale czasu. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. COMPUTE/availabilitySets/Delete | Usuwa zestaw dostępności |
> | Akcja | Microsoft.Compute/availabilitySets/read | Pobierz właściwości zestawu dostępności |
> | Akcja | Microsoft.Compute/availabilitySets/vmSizes/read | Wyświetl dostępne rozmiary dla tworzenia lub aktualizowania maszyny wirtualnej w zestawie dostępności |
> | Akcja | Microsoft. COMPUTE/availabilitySets/Write | Tworzy nowy zestaw dostępności lub aktualizuje istniejący |
> | Akcja | Microsoft. COMPUTE/diskEncryptionSets/Delete | Usuń zestaw szyfrowania dysków |
> | Akcja | Microsoft. COMPUTE/diskEncryptionSets/Read | Pobierz właściwości zestawu szyfrowania dysków |
> | Akcja | Microsoft. COMPUTE/diskEncryptionSets/Write | Utwórz nowy zestaw szyfrowania dysków lub zaktualizuj istniejący |
> | Akcja | Microsoft. COMPUTE/disks/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dysku na potrzeby usługi dostęp do obiektów BLOB |
> | Akcja | Microsoft. COMPUTE/disks/Delete | Usuwa dysk |
> | Akcja | Microsoft.Compute/disks/endGetAccess/action | Odwołaj identyfikator URI sygnatury dostępu współdzielonego dysku |
> | Akcja | Microsoft.Compute/disks/read | Pobieranie właściwości dysku |
> | Akcja | Microsoft.Compute/disks/write | Tworzy nowy dysk lub aktualizuje istniejący |
> | Akcja | Microsoft.Compute/galleries/delete | Usuwa galerię |
> | Akcja | Microsoft.Compute/galleries/images/delete | Usuwa obraz galerii |
> | Akcja | Microsoft.Compute/galleries/images/read | Pobiera właściwości obrazu galerii |
> | Akcja | Microsoft. COMPUTE/Galerie/obrazy/wersje/usuwanie | Usuwa wersję obrazu galerii |
> | Akcja | Microsoft.Compute/galleries/images/versions/read | Pobiera właściwości wersji obrazu galerii |
> | Akcja | Microsoft.Compute/galleries/images/versions/write | Tworzy nową wersję obrazu galerii lub aktualizuje istniejącą |
> | Akcja | Microsoft.Compute/galleries/images/write | Tworzy nowy obraz galerii lub aktualizuje istniejący |
> | Akcja | Microsoft.Compute/galleries/read | Pobiera właściwości galerii |
> | Akcja | Microsoft.Compute/galleries/write | Tworzy nową galerię lub aktualizuje istniejącą. |
> | Akcja | Microsoft. COMPUTE/hostGroups/Delete | Usuwa grupę hostów |
> | Akcja | Microsoft. COMPUTE/hostGroups/hosts/Delete | Usuwa hosta |
> | Akcja | Microsoft. COMPUTE/hostGroups/hosty/odczyt | Pobierz właściwości hosta |
> | Akcja | Microsoft. COMPUTE/hostGroups/hosts/Write | Tworzy nowy host lub aktualizuje istniejącego hosta |
> | Akcja | Microsoft.Compute/hostGroups/read | Pobierz właściwości grupy hostów |
> | Akcja | Microsoft.Compute/hostGroups/write | Tworzy nową grupę hostów lub aktualizuje istniejącą grupę hostów |
> | Akcja | Microsoft.Compute/images/delete | Usuwa obraz |
> | Akcja | Microsoft.Compute/images/read | Pobierz właściwości obrazu |
> | Akcja | Microsoft.Compute/images/write | Tworzy nowy obraz lub aktualizuje istniejący |
> | Akcja | Microsoft.Compute/locations/capsOperations/read | Pobiera stan operacji asynchronicznej Cap |
> | Akcja | Microsoft.Compute/locations/diskOperations/read | Pobiera stan operacji na dysku asynchronicznym |
> | Akcja | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Utwórz dzienniki, aby wyświetlić łączną liczbę żądań według przedziałów czasu, aby ułatwić diagnostykę. |
> | Akcja | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Utwórz dzienniki, aby wyświetlić zagregowane żądania ograniczane pogrupowane według resourceName, OperationName lub stosowanych zasad ograniczania. |
> | Akcja | Microsoft.Compute/locations/operations/read | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Pobierz właściwości oferty obrazu platformy |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Pobierz właściwości jednostki SKU obrazu platformy |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Pobierz właściwości wersji obrazu platformy |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Pobierz właściwości typu VMExtension |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Pobierz właściwości wersji VMExtension |
> | Akcja | Microsoft.Compute/locations/publishers/read | Pobierz właściwości wydawcy |
> | Akcja | Microsoft.Compute/locations/runCommands/read | Wyświetla dostępne polecenia uruchamiania w lokalizacji |
> | Akcja | Microsoft.Compute/locations/usages/read | Pobiera limity usługi i bieżące ilości użycia dla zasobów obliczeniowych subskrypcji w lokalizacji |
> | Akcja | Microsoft.Compute/locations/vmSizes/read | Wyświetla dostępne rozmiary maszyn wirtualnych w lokalizacji |
> | Akcja | Microsoft. COMPUTE/Operations/Read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. COMPUTE |
> | Akcja | Microsoft. COMPUTE/proximityPlacementGroups/Delete | Usuwa grupę umieszczania zbliżeniowego |
> | Akcja | Microsoft. COMPUTE/proximityPlacementGroups/Read | Pobierz właściwości grupy położenia zbliżeniowe |
> | Akcja | Microsoft. COMPUTE/proximityPlacementGroups/Write | Tworzy nową grupę umieszczania w sąsiedztwie lub aktualizuje istniejącą |
> | Akcja | Microsoft. COMPUTE/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. COMPUTE |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/Delete | Usuwa kolekcję punktów przywracania i zawiera punkty przywracania |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/Read | Pobierz właściwości kolekcji punktów przywracania |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/restorePoints/Delete | Usuwa punkt przywracania |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/restorePoints/odczyt | Pobierz właściwości punktu przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Pobierz właściwości punktu przywracania wraz z identyfikatorami URI SAS obiektów BLOB |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/restorePoints/Write | Tworzy nowy punkt przywracania |
> | Akcja | Microsoft. COMPUTE/restorePointCollections/Write | Tworzy nową kolekcję punktów przywracania lub aktualizuje istniejącą. |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/Delete | Usuwa element sharedvmimage |
> | Akcja | Microsoft.Compute/sharedVMImages/read | Pobierz właściwości elementu element sharedvmimage |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/wersje/usuwanie | Usuń element sharedvmimageversion |
> | Akcja | Microsoft.Compute/sharedVMImages/versions/read | Pobierz właściwości elementu element sharedvmimageversion |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/Versions/Replikacja/Akcja | Replikowanie element sharedvmimageversion do regionów docelowych |
> | Akcja | Microsoft. COMPUTE/sharedVMImages/wersje/zapis | Utwórz nowy element sharedvmimageversion lub zaktualizuj istniejący |
> | Akcja | Microsoft.Compute/sharedVMImages/write | Tworzy nowy element sharedvmimage lub aktualizuje istniejący |
> | Akcja | Microsoft.Compute/skus/read | Pobiera listę jednostek SKU Microsoft. COMPUTE dostępnych dla Twojej subskrypcji |
> | Akcja | Microsoft. COMPUTE/snapshots/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dla migawki do obiektu BLOB |
> | Akcja | Microsoft. COMPUTE/snapshots/Delete | Usuń migawkę |
> | Akcja | Microsoft.Compute/snapshots/endGetAccess/action | Odwołaj identyfikator URI sygnatury dostępu współdzielonego dla migawki |
> | Akcja | Microsoft.Compute/snapshots/read | Pobierz właściwości migawki |
> | Akcja | Microsoft.Compute/snapshots/write | Utwórz nową migawkę lub zaktualizuj istniejącą |
> | Akcja | Microsoft. COMPUTE/Unregister/Action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. COMPUTE |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Capture/Action | Przechwytuje maszynę wirtualną przez kopiowanie wirtualnych dysków twardych i generuje szablon, którego można użyć do tworzenia podobnych maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachines/convertToManagedDisks/akcja | Konwertuje dyski oparte na obiektach Blob maszyny wirtualnej na dyski zarządzane |
> | Akcja | Microsoft.Compute/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Akcja | Microsoft.Compute/virtualMachines/delete | Usuwa maszynę wirtualną |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Extensions/Delete | Usuwa rozszerzenie maszyny wirtualnej |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Extensions/Read | Pobierz właściwości rozszerzenia maszyny wirtualnej |
> | Akcja | Microsoft. COMPUTE/virtualMachines/Extensions/Write | Tworzy nowe rozszerzenie maszyny wirtualnej lub aktualizuje istniejące |
> | Akcja | Microsoft. COMPUTE/virtualMachines/generalize/akcja | Ustawia stan maszyny wirtualnej na uogólnione i przygotowuje maszynę wirtualną do przechwycenia |
> | Akcja | Microsoft.Compute/virtualMachines/instanceView/read | Pobiera szczegółowy stan środowiska uruchomieniowego maszyny wirtualnej i jej zasobów |
> | Akcja dataaction | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | Akcja dataaction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logowanie się do maszyny wirtualnej przy użyciu uprawnień administratora systemu Windows lub użytkownika root w systemie Linux |
> | Akcja | Microsoft. COMPUTE/virtualMachines/performMaintenance/akcja | Wykonuje operację konserwacji na maszynie wirtualnej. |
> | Akcja | Microsoft. COMPUTE/virtualMachines/wyłączenie/akcja | Wyłącza maszynę wirtualną. Pamiętaj, że opłaty za maszynę wirtualną będą kontynuowane. |
> | Akcja | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | Akcja | Microsoft. COMPUTE/virtualMachines/redeploy/Action | Ponownie wdraża maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/reimage/action | Odtwarza z obrazu maszynę wirtualną, która korzysta z dysku różnicowego. |
> | Akcja | Microsoft.Compute/virtualMachines/restart/action | Uruchamia ponownie maszynę wirtualną |
> | Akcja | Microsoft. COMPUTE/virtualMachines/runCommand/akcja | Wykonuje wstępnie zdefiniowany skrypt na maszynie wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/start/action | Uruchamia maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/vmSizes/read | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/write | Tworzy nową maszynę wirtualną lub aktualizuje istniejącą maszynę wirtualną |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/deallocate/Action | Wyłącza i zwalnia zasoby obliczeniowe dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Delete | Usuwa zestaw skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Delete/Action | Usuwa wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Delete | Usuwa rozszerzenie zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Read | Pobiera właściwości rozszerzenia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write | Tworzy nowe rozszerzenie zestawu skalowania maszyn wirtualnych lub aktualizuje istniejące |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/akcja | Ręcznie przechodzenie do domeny aktualizacji platformy zestawu skalowania maszyn wirtualnych usługi Service Fabric w celu zakończenia oczekującej aktualizacji, która jest zablokowana |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Pobiera widok wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/manualUpgrade/akcja | Ręcznie aktualizuje wystąpienia do najnowszego modelu zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Pobierz właściwości wszystkich interfejsów sieciowych zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/osRollingUpgrade/akcja | Uruchamia uaktualnienie stopniowe, aby przenieść wszystkie wystąpienia zestawu skalowania maszyn wirtualnych do najnowszej dostępnej wersji systemu operacyjnego obrazu platformy. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Pobiera historię uaktualnień systemu operacyjnego dla zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/performMaintenance/akcja | Przeprowadza planowaną konserwację na wystąpieniach zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Wyłącza wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Pobierz właściwości wszystkich publicznych adresów IP zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/read | Pobierz właściwości zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Wdróż ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Odtwarza z obrazu wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/restart/akcja | Uruchamia ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/rollingUpgrades/Cancel/Action | Anuluje stopniowe uaktualnianie zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Pobierz najnowszy stan stopniowego uaktualniania dla zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Skala/akcja | Sprawdź, czy istniejący zestaw skalowania maszyn wirtualnych może skalować w poziomie/skalować do określonej liczby wystąpień |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/skus/read | Wyświetla prawidłowe jednostki SKU dla istniejącego zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Start/akcja | Uruchamia wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/unallocate/Action | Wyłącza i zwalnia zasoby obliczeniowe dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Delete | Usuń konkretną maszynę wirtualną w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Pobiera widok wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Pobierz właściwości publicznego adresu IP utworzonego przy użyciu zestawu skalowania maszyn wirtualnych. Zestaw skalowania maszyn wirtualnych może utworzyć maksymalnie jeden publiczny adres IP na element ipconfiguration (prywatny adres IP) |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Pobierz właściwości jednej lub wszystkich konfiguracji protokołu IP interfejsu sieciowego utworzonego przy użyciu zestawu skalowania maszyn wirtualnych. Konfiguracje adresów IP reprezentują prywatne adresy IP |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Pobierz właściwości jednego lub wszystkich interfejsów sieciowych maszyny wirtualnej utworzonej przy użyciu zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/performMaintenance/Action | Przeprowadza planowaną konserwację na wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/wyłączenie/Action | Wyłącza wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Pobiera właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Ponownie wdraża wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Odtwarza z obrazu wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Odtwarza z obrazu wszystkie dyski (dysk systemu operacyjnego i dyski z danymi) dla wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/restart/akcja | Uruchamia ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/UruchomPolecenie/Action | Wykonuje wstępnie zdefiniowany skrypt w wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Start/akcja | Uruchamia wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Write | Aktualizuje właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Wyświetl dostępne rozmiary dla tworzenia lub aktualizowania maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft. COMPUTE/virtualMachineScaleSets/Write | Tworzy nowy zestaw skalowania maszyn wirtualnych lub aktualizuje istniejący |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Consumption/balances/read | Wyświetl podsumowanie użycia dla okresu rozliczeniowego dla grupy zarządzania. |
> | Akcja | Microsoft. zużycie/budżety/usuwanie | Usuń budżety przez subskrypcję lub grupę zarządzania. |
> | Akcja | Microsoft.Consumption/budgets/read | Wystaw budżety według subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft. zużycie/budżety/zapis | Tworzy i aktualizuje budżety za pomocą subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft. zużycie/opłaty/odczyt | Wystaw opłaty |
> | Akcja | Microsoft.Consumption/credits/read | Wystaw kredyty |
> | Akcja | Microsoft. użycie/zdarzenia/odczyt | Wyświetl listę zdarzeń |
> | Akcja | Microsoft.Consumption/forecasts/read | Prognozowanie list |
> | Akcja | Microsoft.Consumption/lots/read | Lista partii |
> | Akcja | Microsoft.Consumption/marketplaces/read | Wyświetl listę szczegółów użycia zasobów portalu Marketplace dla zakresu subskrypcji EA i WebDirect. |
> | Akcja | Microsoft.Consumption/operationresults/read | Operationresults listy |
> | Akcja | Microsoft.Consumption/operations/read | Wyświetl listę wszystkich obsługiwanych operacji przez dostawcę zasobów Microsoft. zużycie. |
> | Akcja | Microsoft.Consumption/operationstatus/read | Operationstatus listy |
> | Akcja | Microsoft.Consumption/pricesheets/read | Wyświetl listę danych Pricesheets dla subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft. użycie/rejestrowanie/akcja | Zarejestruj się w celu użycia RP |
> | Akcja | Microsoft.Consumption/reservationDetails/read | Wyświetl listę szczegółów użycia dla wystąpień zarezerwowanych według kolejności rezerwacji lub grup zarządzania. Dane szczegółowe są na poziomie każdego wystąpienia dziennie. |
> | Akcja | Microsoft.Consumption/reservationRecommendations/read | Wyświetl listę pojedynczych lub udostępnionych zaleceń dla wystąpień zarezerwowanych dla subskrypcji. |
> | Akcja | Microsoft.Consumption/reservationSummaries/read | Wyświetl listę podsumowań użycia dla wystąpień zarezerwowanych według kolejności rezerwacji lub grup zarządzania. Dane podsumowujące są na poziomie co miesiąc lub codziennie. |
> | Akcja | Microsoft.Consumption/reservationTransactions/read | Wyświetl listę historii transakcji dla wystąpień zarezerwowanych według grup zarządzania. |
> | Akcja | Microsoft.Consumption/tags/read | Lista tagów dla umów EA i Subscriptions. |
> | Akcja | Microsoft. użycie/dzierżawy/odczyt | Wyświetlanie listy dzierżawców |
> | Akcja | Microsoft. użycie/dzierżawcy/rejestr/akcja | Zarejestruj akcję dla zakresu Microsoft. zużycie przez dzierżawcę. |
> | Akcja | Microsoft.Consumption/terms/read | Wyświetl listę warunków subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/usageDetails/read | Wyświetl listę szczegółów użycia zakresu dla subskrypcji EA i WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec do określonego kontenera. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Pobierz dzienniki dla określonego kontenera. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/delete | Usuń określoną grupę kontenerów. |
> | Akcja | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Pobierz wynik operacji asynchronicznej |
> | Akcja | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/read | Pobierz wszystkie grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/restart/action | Uruchamia ponownie określoną grupę kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/start/action | Uruchamia określoną grupę kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/stop/action | Kończy określoną grupę kontenerów. Zasoby obliczeniowe zostaną cofnięte i rozliczenia zostaną zatrzymane. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/write | Utwórz lub zaktualizuj określoną grupę kontenerów. |
> | Akcja | Microsoft.ContainerInstance/locations/cachedImages/read | Pobiera buforowane obrazy dla subskrypcji w regionie. |
> | Akcja | Microsoft.ContainerInstance/locations/capabilities/read | Uzyskaj możliwości dla regionu. |
> | Akcja | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft. ContainerInstance, że sieć wirtualna lub podsieć jest usuwana. |
> | Akcja | Microsoft. ContainerInstance/Locations/Operations/Read | Wyświetl listę operacji usługi wystąpienia kontenera platformy Azure. |
> | Akcja | Microsoft.ContainerInstance/locations/usages/read | Pobierz użycie dla określonego regionu. |
> | Akcja | Microsoft. ContainerInstance/Operations/Read | Wyświetl listę operacji usługi wystąpienia kontenera platformy Azure. |
> | Akcja | Microsoft.ContainerInstance/register/action | Rejestruje subskrypcję dostawcy zasobów wystąpienia kontenera i umożliwia tworzenie grup kontenerów. |
> | Akcja | Microsoft. ContainerInstance/serviceassociationlinks/Delete | Usuń link skojarzenia usługi utworzony przez dostawcę zasobów wystąpienia kontenera platformy Azure w podsieci. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContainerRegistry/checkNameAvailability/read | Sprawdza, czy nazwa rejestru kontenerów jest dostępna do użycia. |
> | Akcja | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft. ContainerRegistry, że sieć wirtualna lub podsieć jest usuwana |
> | Akcja | Microsoft.ContainerRegistry/locations/operationResults/read | Pobiera wynik operacji asynchronicznej |
> | Akcja | Microsoft.ContainerRegistry/operations/read | Wyświetla wszystkie dostępne Azure Container Registry operacje interfejsu API REST |
> | Akcja | Microsoft.ContainerRegistry/register/action | Rejestruje subskrypcję dostawcy zasobów rejestru kontenerów i umożliwia tworzenie rejestrów kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/artefakty/usuwanie | Usuń artefakt w rejestrze kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/kompilacje/akcja | Anuluje istniejącą kompilację. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/kompilacje/getLogLink/akcja | Pobiera link umożliwiający pobranie dzienników kompilacji. |
> | Akcja | Microsoft.ContainerRegistry/registries/builds/read | Pobiera właściwości określonej kompilacji lub wyświetla wszystkie kompilacje dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/kompilacje/zapisywanie | Aktualizuje kompilację dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/delete | Usuwa zadanie kompilacji z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Wyświetla listę właściwości kontroli źródła dla zadania kompilacji. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/read | Pobiera właściwości określonego zadania kompilacji lub wyświetla wszystkie zadania kompilacji dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Usuwa krok kompilacji z zadania kompilacji. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/buildTasks/etapy/listBuildArguments/akcja | Wyświetla listę argumentów kompilacji dla kroku kompilacji, w tym argumentów tajnych. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Pobiera właściwości określonego kroku kompilacji lub wyświetla listę wszystkich kroków kompilacji dla określonego zadania kompilacji. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Tworzy lub aktualizuje krok kompilacji dla zadania kompilacji o określonych parametrach. |
> | Akcja | Microsoft.ContainerRegistry/registries/buildTasks/write | Tworzy lub aktualizuje zadanie kompilacji dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/usuwanie | Usuwa rejestr kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Usuwa filtr usługi Event Grid z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Pobiera właściwości określonego filtru usługi Event Grid lub wyświetla listę wszystkich filtrów usługi Event Grid dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Tworzy lub aktualizuje filtr usługi Event Grid dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Pobiera lokalizację przekazywania dla użytkownika, aby mógł przekazać źródło. |
> | Akcja | Microsoft.ContainerRegistry/registries/importImage/action | Zaimportuj obraz do rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Pobierz lokalizację adresu URL przekazywania źródła dla rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/listCredentials/action | Wyświetla listę poświadczeń logowania dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/listPolicies/read | Wyświetla listę zasad dla określonego rejestru kontenerów |
> | Akcja | Microsoft.ContainerRegistry/registries/listUsages/read | Wyświetla listę użycia przydziału dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/metadata/read | Pobiera metadane określonego repozytorium dla rejestru kontenerów |
> | Akcja | Microsoft. ContainerRegistry/kancelarie/metadane/zapis | Aktualizuje metadane repozytorium dla rejestru kontenerów |
> | Akcja | Microsoft.ContainerRegistry/registries/operationStatuses/read | Pobiera stan operacji asynchronicznej rejestru |
> | Akcja | Microsoft.ContainerRegistry/registries/pull/read | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/push/write | Wypchnij lub Zapisz obrazy do rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/quarantineRead/read | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
> | Akcja | Microsoft. ContainerRegistry/rejestry/quarantineWrite/zapis | Zapisywanie/modyfikowanie stanu kwarantanny obrazów z kwarantanny |
> | Akcja | Microsoft.ContainerRegistry/registries/queueBuild/action | Tworzy nową kompilację opartą na parametrach żądania i Dodaj ją do kolejki kompilacji. |
> | Akcja | Microsoft.ContainerRegistry/registries/read | Pobiera właściwości określonego rejestru kontenerów lub wyświetla listę wszystkich rejestrów kontenerów w ramach określonej grupy zasobów lub subskrypcji. |
> | Akcja | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Generuje ponownie jedno z poświadczeń logowania dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/replikacje/usuwanie | Usuwa replikację z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Pobiera stan operacji asynchronicznej replikacji |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/read | Pobiera właściwości określonej replikacji lub wyświetla wszystkie replikacje dla określonego rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/write | Tworzy lub aktualizuje replikację dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft.ContainerRegistry/registries/runs/cancel/action | Anuluj istniejące uruchomienie. |
> | Akcja | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Pobiera adres URL sygnatury dostępu współdzielonego dziennika dla uruchomienia. |
> | Akcja | Microsoft.ContainerRegistry/registries/runs/read | Pobiera właściwości przebiegu dotyczącego przebiegu rejestru lub listy kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/runs/write | Aktualizuje przebieg. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/scheduleRun/akcja | Zaplanuj przebieg dla rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/rejestrowanie/zapisywanie | Wypychanie/Ściąganie metadanych zaufania zawartości dla rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/zadania/usuwanie | Usuwa zadanie dla rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Wyświetl listę wszystkich szczegółów zadania dla rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/tasks/read | Pobiera zadanie dla rejestru kontenerów lub wyświetla listę wszystkich zadań. |
> | Akcja | Microsoft.ContainerRegistry/registries/tasks/write | Tworzy lub aktualizuje zadanie dla rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/updatePolicies/write | Aktualizuje zasady dla określonego rejestru kontenerów |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/usuwanie | Usuwa element webhook z rejestru kontenerów. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Pobiera konfigurację identyfikatora URI usługi i nagłówków niestandardowych dla elementu webhook. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/listEvents/akcja | Wyświetla listę ostatnich zdarzeń dla określonego elementu webhook. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Pobiera stan operacji asynchronicznej elementu webhook |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/polecenie ping/akcja | Wyzwala zdarzenie ping do wysłania do elementu webhook. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/read | Pobiera właściwości określonego elementu webhook lub wyświetla listę wszystkich elementów webhook dla określonego rejestru kontenerów. |
> | Akcja | Microsoft. ContainerRegistry/rejestry/elementy webhook/zapis | Tworzy lub aktualizuje element webhook dla rejestru kontenerów o określonych parametrach. |
> | Akcja | Microsoft.ContainerRegistry/registries/write | Tworzy lub aktualizuje rejestr kontenerów o określonych parametrach. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContainerService/containerServices/delete | Usuwa usługę kontenera |
> | Akcja | Microsoft.ContainerService/containerServices/read | Pobierz usługę kontenera |
> | Akcja | Microsoft.ContainerService/containerServices/write | Tworzy nową usługę kontenera lub aktualizuje istniejącą |
> | Akcja | Microsoft.ContainerService/locations/operationresults/read | Pobiera stan wyniku operacji asynchronicznej |
> | Akcja | Microsoft.ContainerService/locations/operations/read | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft.ContainerService/locations/orchestrators/read | Wyświetla listę obsługiwanych programów Orchestrator |
> | Akcja | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Uzyskiwanie zarządzanego profilu dostępu do klastra według nazwy roli przy użyciu poświadczeń listy |
> | Akcja | Microsoft.ContainerService/managedClusters/accessProfiles/read | Uzyskaj zarządzany Profil dostępu do klastra według nazwy roli |
> | Akcja | Microsoft.ContainerService/managedClusters/agentPools/delete | Usuwa pulę agentów |
> | Akcja | Microsoft.ContainerService/managedClusters/agentPools/read | Pobiera pulę agentów |
> | Akcja | Microsoft. ContainerService/managedClusters/agentPools/upgradeProfiles/Read | Pobiera profil uaktualnienia puli agentów |
> | Akcja | Microsoft.ContainerService/managedClusters/agentPools/write | Tworzy nową pulę agentów lub aktualizuje istniejącą. |
> | Akcja | Microsoft.ContainerService/managedClusters/delete | Usuwa zarządzany klaster |
> | Akcja | Microsoft. ContainerService/managedClusters/detektory/odczyt | Pobierz detektor zarządzanego klastra |
> | Akcja | Microsoft. ContainerService/managedClusters/diagnosticsState/Read | Pobiera stan diagnostyki klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Wyświetlanie clusterAdmin poświadczenia zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Wyświetlanie clusterUser poświadczenia zarządzanego klastra |
> | Akcja | Microsoft. ContainerService/managedClusters/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft.ContainerService/managedClusters/read | Pobierz zarządzany klaster |
> | Akcja | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Resetowanie profilu usługi AAD w zarządzanym klastrze |
> | Akcja | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Resetowanie profilu jednostki usługi zarządzanego klastra |
> | Akcja | Microsoft. ContainerService/managedClusters/upgradeProfiles/Read | Pobiera profil uaktualnienia klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/write | Tworzy nowy zarządzany klaster lub aktualizuje istniejący. |
> | Akcja | Microsoft.ContainerService/openShiftClusters/delete | Usuń otwarty klaster przesunięcia |
> | Akcja | Microsoft.ContainerService/openShiftClusters/read | Pobierz otwarty klaster przesunięcia |
> | Akcja | Microsoft.ContainerService/openShiftClusters/write | Tworzy nowy otwarty klaster przesunięcia lub aktualizuje istniejący |
> | Akcja | Microsoft.ContainerService/openShiftManagedClusters/delete | Usuń otwarty klaster z zarządzaną przesunięciami |
> | Akcja | Microsoft.ContainerService/openShiftManagedClusters/read | Pobierz klaster zarządzany za otwartym przesunięciami |
> | Akcja | Microsoft.ContainerService/openShiftManagedClusters/write | Tworzy nowy klaster zarządzany przez przesunięcia lub aktualizuje istniejący |
> | Akcja | Microsoft.ContainerService/operations/read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft. ContainerService |
> | Akcja | Microsoft.ContainerService/register/action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. ContainerService |
> | Akcja | Microsoft.ContainerService/unregister/action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContentModerator/applications/delete | Operacja usuwania |
> | Akcja | Microsoft.ContentModerator/applications/listSecrets/action | Wyświetl wpisy tajne |
> | Akcja | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Odczytaj tokeny logowania jednokrotnego |
> | Akcja | Microsoft.ContentModerator/applications/read | Operacja odczytu |
> | Akcja | Microsoft.ContentModerator/applications/write | Operacja zapisu |
> | Akcja | Microsoft.ContentModerator/applications/write | Operacja zapisu |
> | Akcja | Microsoft.ContentModerator/listCommunicationPreference/action | Wyświetl preferencję komunikacji |
> | Akcja | Microsoft.ContentModerator/operations/read | operacje odczytu |
> | Akcja | Microsoft.ContentModerator/updateCommunicationPreference/action | Aktualizuj preferencję komunikacji |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.CostManagement/cloudConnectors/delete | Usuń określony cloudConnector. |
> | Akcja | Microsoft.CostManagement/cloudConnectors/read | Wyświetl listę cloudConnectors dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.CostManagement/cloudConnectors/write | Utwórz lub zaktualizuj określony cloudConnector. |
> | Akcja | Microsoft.CostManagement/dimensions/read | Wyświetl listę wszystkich obsługiwanych wymiarów według zakresu. |
> | Akcja | Microsoft. CostManagement/eksporty/akcja | Uruchom określony eksport. |
> | Akcja | Microsoft. CostManagement/exports/Delete | Usuń określony eksport. |
> | Akcja | Microsoft.CostManagement/exports/read | Wyświetl listę eksportów według zakresu. |
> | Akcja | Microsoft. CostManagement/exports/Run/Action | Uruchom eksporty. |
> | Akcja | Microsoft. CostManagement/exports/Write | Utwórz lub zaktualizuj określony eksport. |
> | Akcja | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Wyświetl listę externalSubscriptions w externalBillingAccount dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft. CostManagement/externalBillingAccounts/odczyt | Wyświetl listę externalBillingAccounts dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.CostManagement/externalSubscriptions/read | Wyświetl listę externalSubscriptions dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.CostManagement/externalSubscriptions/write | Aktualizuj skojarzoną grupę zarządzania externalSubscription |
> | Akcja | Microsoft. CostManagement/kwerenda/akcja | Wykonywanie zapytań o dane użycia według zakresu. |
> | Akcja | Microsoft.CostManagement/query/read | Wykonywanie zapytań o dane użycia według zakresu. |
> | Akcja | Microsoft. CostManagement/Register/Action | Zarejestruj akcję dla zakresu Microsoft. CostManagement przez subskrypcję. |
> | Akcja | Microsoft. CostManagement/raporty/akcja | Zaplanuj raporty dotyczące danych użycia według zakresu. |
> | Akcja | Microsoft.CostManagement/reports/read | Zaplanuj raporty dotyczące danych użycia według zakresu. |
> | Akcja | Microsoft. CostManagement/dzierżawcy/rejestr/akcja | Zarejestruj akcję dla zakresu Microsoft. CostManagement przez dzierżawcę. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DataBox/Jobs/bookShipmentPickUp/akcja | Umożliwia zaksięgowanie pobrania dla wysyłek zwrotnych. |
> | Akcja | Microsoft. DataBox/Jobs/Cancel/Action | Anuluje zamówienie w toku. |
> | Akcja | Microsoft. DataBox/Jobs/Delete | Usuwanie zamówień |
> | Akcja | Microsoft.DataBox/jobs/listCredentials/action | Wyświetla listę nieszyfrowanych poświadczeń związanych z kolejnością. |
> | Akcja | Microsoft.DataBox/jobs/read | Wyświetl lub Pobierz zamówienia |
> | Akcja | Microsoft. DataBox/Jobs/Write | Utwórz lub zaktualizuj zamówienia |
> | Akcja | Microsoft. DataBox/Locations/availableSkus/Action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | Akcja | Microsoft. DataBox/Locations/availableSkus/Read | Wyświetl lub uzyskaj dostępne jednostki SKU |
> | Akcja | Microsoft.DataBox/locations/operationResults/read | Wyświetl lub Pobierz wyniki operacji |
> | Akcja | Microsoft. DataBox/Locations/regionConfiguration/Action | Ta metoda zwraca konfiguracje dla regionu. |
> | Akcja | Microsoft.DataBox/locations/validateAddress/action | Sprawdza poprawność adresu wysyłkowego i udostępnia alternatywne adresy, jeśli istnieją. |
> | Akcja | Microsoft. DataBox/Locations/validateInputs/Action | Ta metoda wykonuje wszystkie typy walidacji. |
> | Akcja | Microsoft. DataBox/Operations/Read | Wyświetl lub Pobierz operacje |
> | Akcja | Microsoft. DataBox/Register/Action | Zarejestruj dostawcę Microsoft. DATAbox |
> | Akcja | Microsoft. DataBox/subscriptions/resourceGroups/moveResources/Action | Ta metoda służy do przenoszenia zasobu. |
> | Akcja | Microsoft. DataBox/subscriptions/resourceGroups/validateMoveResources/Action | Ta metoda sprawdza, czy przenoszenie zasobów jest dozwolone. |
> | Akcja | Microsoft. DataBox/Unregister/Action | Wyrejestruj dostawcę Microsoft. DATAbox |

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
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Usuwa urządzenia Data Box Edge |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Pobierz aktualizacje w urządzeniu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Pobiera rozszerzone informacje o zasobach |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Zainstaluj aktualizacje na urządzeniu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Wyświetla lub pobiera zadania |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Wyświetla lub pobiera ustawienia sieci urządzenia |
> | Akcja | Microsoft. DataBoxEdge/dataBoxEdgeDevices/nodes/Read | Wyświetla lub pobiera węzły |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Wyświetla lub Pobiera stan operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Usuwa zamówienia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Wyświetla lub pobiera zamówienia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Wyświetla lub pobiera zamówienia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Tworzy lub aktualizuje zamówienia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Wyświetla lub Pobiera Data Box Edge urządzeń |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Usuwa role |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Wyświetla lub pobiera role |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Wyświetla lub pobiera role |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Tworzy lub aktualizuje role |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Skanuj w poszukiwaniu aktualizacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Aktualizowanie ustawień zabezpieczeń |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Usuwa udziały |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Wyświetla lub pobiera udziały |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Wyświetla lub pobiera udziały |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Odświeżanie metadanych udziału przy użyciu danych z chmury |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Tworzy lub aktualizuje udziały |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Usuwa poświadczenia konta magazynu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Tworzy lub aktualizuje poświadczenia konta magazynu |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Usuwa wyzwalacze |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Wyświetla lub pobiera wyzwalacze |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Wyświetla lub pobiera wyzwalacze |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Tworzy lub aktualizuje wyzwalacze |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Wyświetla lub pobiera podsumowanie aktualizacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Przekaż certyfikat do rejestracji urządzenia |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Usuwa użytkowników udziału |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Wyświetla lub pobiera wynik operacji |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Wyświetla lub pobiera użytkowników udziału |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Wyświetla lub pobiera użytkowników udziału |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Tworzy lub aktualizuje udział użytkowników |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Tworzy lub aktualizuje urządzenia Data Box Edge |
> | Akcja | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Tworzy lub aktualizuje urządzenia Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Databricks/locations/getNetworkPolicies/action | Pobierz zasady dotyczące zastosowania sieci dla podsieci na podstawie lokalizacji używanej przez dostawcy NRP |
> | Akcja | Microsoft. datacegły/rejestr/akcja | Zarejestruj się w kostkach. |
> | Akcja | Microsoft.Databricks/workspaces/delete | Usuwa obszar roboczy datakosteks. |
> | Akcja | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Ustawia dostępne ustawienia diagnostyczne dla obszaru roboczego datakosteks |
> | Akcja | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Dodawanie lub modyfikowanie ustawień diagnostycznych. |
> | Akcja | Microsoft. datacegły/obszary robocze/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne definicje dzienników dla obszaru roboczego datakosteks |
> | Akcja | Microsoft.Databricks/workspaces/read | Pobiera listę obszarów roboczych elementów datakosteks. |
> | Akcja | Microsoft.Databricks/workspaces/write | Tworzy obszar roboczy datakosteks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataCatalog/catalogs/delete | Usuń zasób wykazów dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft.DataCatalog/catalogs/read | Zasób wykazów odczytu dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft.DataCatalog/catalogs/write | Zasób katalogu zapisu dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft.DataCatalog/checkNameAvailability/read | Sprawdź dostępność nazwy wykazu dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft.DataCatalog/datacatalogs/delete | Usuń zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft.DataCatalog/datacatalogs/read | Odczytaj zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft.DataCatalog/datacatalogs/write | Zapisz zasób usługi datacatalog dla dostawcy zasobów Data Catalog. |
> | Akcja | Microsoft. datacatalog/Operations/Read | Odczytuje wszystkie dostępne operacje w Data Catalog dostawcy zasobów. |
> | Akcja | Microsoft. datacatalog/Register/Action | Zarejestruj subskrypcję dla dostawcy zasobów Data Catalog |
> | Akcja | Microsoft. datacatalog/Unregister/Action | Wyrejestrowywanie subskrypcji dla dostawcy zasobów Data Catalog |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Sprawdza, czy nazwa Data Factory jest dostępna do użycia. |
> | Akcja | Microsoft.DataFactory/datafactories/activitywindows/read | Odczytuje okna działania w Data Factory z określonymi parametrami. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Activities/activitywindows/Read | Odczytuje okna aktywności dla działania potoku z określonymi parametrami. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/activitywindows/Read | Odczytuje okna działań dla potoku z określonymi parametrami. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Delete | Usuwa każdy potok. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Wstrzymuje wszystkie potoki. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/read | Odczytuje wszystkie potoki. |
> | Akcja | Microsoft. DataFactory/datafactors///Resume/akcja | Wznawia wszystkie potoki. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Update/Action | Aktualizuje każdy potok. |
> | Akcja | Microsoft. DataFactory/datafactors/datapipelines/Write | Tworzy lub aktualizuje każdy potok. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Odczytuje okna działań dla zestawu danych z określonymi parametrami. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/delete | Usuwa dowolny zestaw danych. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/read | Odczytuje dowolny zestaw danych. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Odczytuje wycinek danych uruchomienia dla danego zestawu danych z danym czasem rozpoczęcia. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/slices/read | Pobiera wycinki danych w danym okresie. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/slices/write | Zaktualizuj stan wycinka danych. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/datafactors/Delete | Usuwa Data Factory. |
> | Akcja | Microsoft. DataFactory/datafactors/Gateways/ConnectionInfo/Action | Odczytuje informacje o połączeniu dla każdej bramy. |
> | Akcja | Microsoft. DataFactory/datafactors/Gateways/Delete | Usuwa wszystkie bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Wyświetla listę kluczy uwierzytelniania dla każdej bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/read | Odczytuje każdą bramę. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Generuje ponownie klucze uwierzytelniania dla każdej bramy. |
> | Akcja | Microsoft. DataFactory/datafabrykas/Gateways/Write | Tworzy lub aktualizuje bramę. |
> | Akcja | Microsoft.DataFactory/datafactories/linkedServices/delete | Usuwa wszystkie połączone usługi. |
> | Akcja | Microsoft.DataFactory/datafactories/linkedServices/read | Odczytuje wszystkie połączone usługi. |
> | Akcja | Microsoft.DataFactory/datafactories/linkedServices/write | Tworzy lub aktualizuje każdą połączoną usługę. |
> | Akcja | Microsoft.DataFactory/datafactories/read | Odczytuje Data Factory. |
> | Akcja | Microsoft.DataFactory/datafactories/runs/loginfo/read | Odczytuje identyfikator URI sygnatury dostępu współdzielonego do kontenera obiektów BLOB zawierającego dzienniki. |
> | Akcja | Microsoft. DataFactory/datafactors/Tables/Delete | Usuwa dowolny zestaw danych. |
> | Akcja | Microsoft.DataFactory/datafactories/tables/read | Odczytuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/datafactors/Tables/Write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/datafactors/Write | Tworzy lub aktualizuje Data Factory. |
> | Akcja | Microsoft. DataFactory/Factors/cancelpipelinerun/Action | Anuluje uruchomienie potoku określone przez identyfikator uruchomienia. |
> | Akcja | Microsoft. DataFactory/Factors/cancelSandboxPipelineRun/Action | Anuluje uruchomienie debugowania dla potoku. |
> | Akcja | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Tworzy sesję debugowania przepływu danych. |
> | Akcja | Microsoft.DataFactory/factories/dataflows/delete | Usuwa przepływ danych. |
> | Akcja | Microsoft.DataFactory/factories/dataflows/read | Odczytuje przepływ danych. |
> | Akcja | Microsoft.DataFactory/factories/dataflows/write | Utwórz lub zaktualizuj przepływ danych |
> | Akcja | Microsoft.DataFactory/factories/datasets/delete | Usuwa dowolny zestaw danych. |
> | Akcja | Microsoft.DataFactory/factories/datasets/read | Odczytuje dowolny zestaw danych. |
> | Akcja | Microsoft.DataFactory/factories/datasets/write | Tworzy lub aktualizuje dowolny zestaw danych. |
> | Akcja | Microsoft. DataFactory/Factors/debugpipelineruns/Cancel/Action | Anuluje uruchomienie debugowania dla potoku. |
> | Akcja | Microsoft. DataFactory/Factors/Delete | Usuwa Data Factory. |
> | Akcja | Microsoft. DataFactory/Factors/deletedataflowdebugsession/Action | Usuwa sesję debugowania przepływu danych. |
> | Akcja | Microsoft. DataFactory/Factors/getDataPlaneAccess/Action | Uzyskuje dostęp do usługi dataplaning ADF. |
> | Akcja | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Odczytuje dostęp do usługi dataplaning ADF. |
> | Akcja | Microsoft. DataFactory/Factors/getFeatureValue/Action | Wartość funkcji kontroli ekspozycji dla określonej lokalizacji. |
> | Akcja | Microsoft. DataFactory/Factors/getFeatureValue/Read | Odczytuje wartość funkcji kontroli zagrożeń dla określonej lokalizacji. |
> | Akcja | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Pobiera token dostępu usługi GitHub. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Delete | Usuwa wszystkie Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/GetConnectionInfo/Read | Odczytuje Integration Runtime informacji o połączeniu. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/getObjectMetadata/Action | Pobierz metadane usług SSIS Integration Runtime dla określonego Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/GetStatus/Read | Odczytuje Integration Runtime stanu. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Utwórz połączone odwołanie Integration Runtime w określonym Integration Runtime udostępnionym. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Wyświetla listę kluczy uwierzytelniania dla każdej Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/monitoringdata/Read | Pobiera dane monitorowania dla dowolnych Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Delete | Usuwa węzeł dla podanej Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Zwraca adres IP dla określonego węzła Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Read | Odczytuje węzeł dla określonego Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/nodes/Write | Aktualizuje węzeł Integration Runtime samoobsługowego. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Read | Odczytuje wszystkie Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/refreshObjectMetadata/Action | Odśwież Integration Runtime metadanych dla określonego Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Generuje ponownie klucze uwierzytelniania dla podanej Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Usuwa połączone Integration Runtime odwołania z określonego Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Start/Action | Uruchamia dowolne Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Stop/Action | Powoduje zatrzymanie wszelkich Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synchronizuje poświadczenia dla określonego Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/upgrade/Action | Uaktualnia określony Integration Runtime. |
> | Akcja | Microsoft. DataFactory/Factors/integrationruntimes/Write | Tworzy lub aktualizuje wszystkie Integration Runtime. |
> | Akcja | Microsoft.DataFactory/factories/linkedServices/delete | Usuwa połączoną usługę. |
> | Akcja | Microsoft.DataFactory/factories/linkedServices/read | Odczytuje połączoną usługę. |
> | Akcja | Microsoft.DataFactory/factories/linkedServices/write | Utwórz lub zaktualizuj połączoną usługę |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/activityruns/Read | Odczytuje uruchomienia działania dla określonego identyfikatora uruchomienia potoku. |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/Cancel/Action | Anuluje uruchomienie potoku określone przez identyfikator uruchomienia. |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/queryactivityruns/Action | Wysyła zapytanie do przebiegów działania dla określonego identyfikatora uruchomienia potoku. |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/queryactivityruns/Read | Odczytuje wynik przebiegu działania zapytania dla określonego identyfikatora uruchomienia potoku. |
> | Akcja | Microsoft. DataFactory/Factors/pipelineruns/Read | Odczytuje uruchomienia potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/createrun/action | Tworzy przebieg dla potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/usuwanie | Usuwa potok. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Pobiera postęp uruchomienia działania. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/pipelineruns/odczyt | Odczytuje uruchomienie potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/odczyt | Odczytuje potok. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/piaskownica/akcja | Tworzy środowisko uruchamiania debugowania dla potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/piaskownica/Utwórz/akcję | Tworzy środowisko uruchamiania debugowania dla potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/piaskownica/Uruchom/akcja | Tworzy przebieg debugowania dla potoku. |
> | Akcja | Microsoft. DataFactory/fabryki/potoki/zapis | Utwórz lub zaktualizuj potok |
> | Akcja | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Wysyła zapytanie do przebiegów potoku debugowania. |
> | Akcja | Microsoft. DataFactory/Factors/querypipelineruns/Action | Wykonuje zapytanie o uruchomienia potoku. |
> | Akcja | Microsoft.DataFactory/factories/querypipelineruns/read | Odczytuje wynik uruchomienia potoku zapytania. |
> | Akcja | Microsoft. DataFactory/Factors/querytriggerruns/Action | Wysyła zapytanie do uruchomienia wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/querytriggerruns/read | Odczytuje wynik uruchomienia wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/read | Odczytuje Data Factory. |
> | Akcja | Microsoft. DataFactory/Factors/sandboxpipelineruns/sandboxActivityRuns/Read | Pobiera informacje o uruchamianiu debugowania dla działania. |
> | Akcja | Microsoft. DataFactory/Factors/startdataflowdebugsession/Action | Uruchamia sesję debugowania przepływu danych. |
> | Akcja | Microsoft. DataFactory/Factors/submitDataFlowForPreview/Action | Prześlij przepływ danych, aby uzyskać podgląd danych przy użyciu sesji debugowania. |
> | Akcja | Microsoft.DataFactory/factories/triggerruns/read | Odczytuje uruchomienia wyzwalacza. |
> | Akcja | Microsoft. DataFactory/fabryki/wyzwalacze/usuwanie | Usuwa każdy wyzwalacz. |
> | Akcja | Microsoft. DataFactory/fabryki/wyzwalacze/odczyt | Odczytuje dowolny wyzwalacz. |
> | Akcja | Microsoft. DataFactory/Factors/Triggers/Start/Action | Uruchamia dowolny wyzwalacz. |
> | Akcja | Microsoft. DataFactory/Factors/Triggers/akcja | Powoduje zatrzymanie dowolnego wyzwalacza. |
> | Akcja | Microsoft. DataFactory/Factors/Triggers/triggerruns/Read | Odczytuje uruchomienia wyzwalacza. |
> | Akcja | Microsoft. DataFactory/fabryki/wyzwalacze/zapis | Tworzy lub aktualizuje dowolny wyzwalacz. |
> | Akcja | Microsoft. DataFactory/Factors/Write | Utwórz lub zaktualizuj Data Factory |
> | Akcja | Microsoft.DataFactory/locations/configureFactoryRepo/action | Konfiguruje repozytorium dla fabryki. |
> | Akcja | Microsoft. DataFactory/Locations/getFeatureValue/Action | Wartość funkcji kontroli ekspozycji dla określonej lokalizacji. |
> | Akcja | Microsoft.DataFactory/locations/getFeatureValue/read | Odczytuje wartość funkcji kontroli zagrożeń dla określonej lokalizacji. |
> | Akcja | Microsoft.DataFactory/operations/read | Odczytuje wszystkie operacje w programie Microsoft Data Factory Provider. |
> | Akcja | Microsoft. DataFactory/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów Data Factory. |
> | Akcja | Microsoft. DataFactory/Unregister/Action | Wyrejestrowuje subskrypcję dla dostawcy zasobów Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Usuń zasady obliczeń. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Uzyskaj informacje na temat zasad obliczeniowych. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Utwórz lub zaktualizuj zasady obliczeniowe. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Odłączanie konta kontach datalakestore z konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Uzyskaj informacje o połączonym koncie kontach datalakestore konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Utwórz lub zaktualizuj połączone konto kontach datalakestore konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/delete | Usuń konto DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Usuń regułę zapory. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Uzyskaj informacje na temat reguły zapory. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Utwórz lub zaktualizuj regułę zapory. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Pobierz wynik operacji konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/read | Pobierz informacje o istniejącym koncie DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Wyświetl listę tokenów sygnatury dostępu współdzielonego dla kontenerów magazynu połączonego konta magazynu konta usługi DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Pobierz kontenery połączonego konta magazynu dla konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Odłączanie konta magazynu od konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Uzyskaj informacje o połączonym koncie magazynu konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Utwórz lub zaktualizuj połączone konto magazynu dla konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Udziel uprawnień do anulowania zadań przesłanych przez innych użytkowników. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Przenoszenie SystemMaxAnalyticsUnits między kontami DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Usuń regułę sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Uzyskaj informacje na temat reguły sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/capability/read | Uzyskaj informacje o możliwościach subskrypcji dotyczące korzystania z DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/operationResults/read | Pobierz wynik operacji konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/usages/read | Uzyskaj informacje dotyczące użycia przydziału dla subskrypcji dotyczącej korzystania z DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/operations/read | Pobierz dostępne operacje DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/register/action | Zarejestruj subskrypcję usługi DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. kontach datalakestore/accounts/Delete | Usuń konto kontach datalakestore. |
> | Akcja | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Włącz Magazyn kluczy dla konta kontach datalakestore. |
> | Akcja | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Usuń filtr EventGrid. |
> | Akcja | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Pobierz filtr EventGrid. |
> | Akcja | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Utwórz lub zaktualizuj filtr EventGrid. |
> | Akcja | Microsoft. kontach datalakestore/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | Akcja | Microsoft. kontach datalakestore/accounts/firewallRules/Read | Uzyskaj informacje na temat reguły zapory. |
> | Akcja | Microsoft. kontach datalakestore/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | Akcja | Microsoft.DataLakeStore/accounts/operationResults/read | Pobierz wynik operacji konta kontach datalakestore. |
> | Akcja | Microsoft.DataLakeStore/accounts/read | Pobierz informacje o istniejącym koncie kontach datalakestore. |
> | Akcja | Microsoft. kontach datalakestore/accounts/shares/Delete | Usuń udział. |
> | Akcja | Microsoft. kontach datalakestore/accounts/shares/Read | Pobierz informacje o udziale. |
> | Akcja | Microsoft. kontach datalakestore/accounts/shares/Write | Utwórz lub zaktualizuj udział. |
> | Akcja | Microsoft.DataLakeStore/accounts/Superuser/action | Przyznaj administratora na Data Lake Store po udzieleniu z firmą Microsoft. Authorization/roleAssignments/Write. |
> | Akcja | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Usuń zaufanego dostawcę tożsamości. |
> | Akcja | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Uzyskaj informacje na temat zaufanego dostawcy tożsamości. |
> | Akcja | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Utwórz lub zaktualizuj zaufanego dostawcę tożsamości. |
> | Akcja | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Usuń regułę sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Uzyskaj informacje na temat reguły sieci wirtualnej. |
> | Akcja | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Utwórz lub zaktualizuj regułę sieci wirtualnej. |
> | Akcja | Microsoft. kontach datalakestore/accounts/Write | Utwórz lub zaktualizuj konto kontach datalakestore. |
> | Akcja | Microsoft.DataLakeStore/locations/capability/read | Uzyskaj informacje o możliwościach subskrypcji dotyczące korzystania z kontach datalakestore. |
> | Akcja | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta kontach datalakestore. |
> | Akcja | Microsoft.DataLakeStore/locations/operationResults/read | Pobierz wynik operacji konta kontach datalakestore. |
> | Akcja | Microsoft.DataLakeStore/locations/usages/read | Uzyskaj informacje dotyczące użycia przydziału dla subskrypcji dotyczącej korzystania z kontach datalakestore. |
> | Akcja | Microsoft.DataLakeStore/operations/read | Pobierz dostępne operacje kontach datalakestore. |
> | Akcja | Microsoft.DataLakeStore/register/action | Zarejestruj subskrypcję usługi kontach datalakestore. |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataMigration/locations/operationResults/read | Pobieranie stanu długotrwałej operacji związanej z zaakceptowaną odpowiedzią 202 |
> | Akcja | Microsoft.DataMigration/locations/operationStatuses/read | Pobieranie stanu długotrwałej operacji związanej z zaakceptowaną odpowiedzią 202 |
> | Akcja | Microsoft. datamigration/Register/Action | Rejestruje subskrypcję za pomocą dostawcy Azure Database Migration Service |
> | Akcja | Microsoft. datamigration/Services/addworker/Action | Dodaje proces roboczy DMS do dostępny procesów roboczych usługi |
> | Akcja | Microsoft. datamigration/Services/checkStatus/Action | Sprawdź, czy usługa jest wdrożona i uruchomiona |
> | Akcja | Microsoft. datamigration/Services/configureWorker/Action | Konfiguruje proces roboczy DMS dla procesów roboczych dostępny usługi |
> | Akcja | Microsoft. datamigration/Services/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. datamigration/Services/projects/accessArtifacts/Action | Generowanie adresu URL, którego można użyć do pobierania lub UMIESZCZAnia artefaktów projektu |
> | Akcja | Microsoft. datamigration/Services/projects/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. datamigration/Services/projects/Files/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. datamigration/Services/projects/Files/Read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft.DataMigration/services/projects/files/read/action | Uzyskaj adres URL, za pomocą którego można odczytać zawartość pliku |
> | Akcja | Microsoft. datamigration/Services/projects/Files/readWrite/Action | Uzyskaj adres URL, za pomocą którego można odczytywać lub zapisywać zawartość pliku |
> | Akcja | Microsoft. datamigration/Services/projects/Files/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Akcja | Microsoft. datamigration/Services/projects/Read | Przeczytaj informacje o zasobach |
> | Akcja | Microsoft. datamigration/Services/projects/Tasks/Cancel/Action | Anuluj zadanie, jeśli jest aktualnie uruchomione |
> | Akcja | Microsoft. datamigration/Services/projects/Tasks/Delete | Usuwa zasób i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft.DataMigration/services/projects/tasks/read | Przeczytaj informacje o zasobach |
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
> | Akcja | Microsoft.DataMigration/services/updateAgentConfig/action | Aktualizuje konfigurację agenta DMS przy użyciu podanych wartości. |
> | Akcja | Microsoft. datamigration/Services/Write | Tworzenie lub aktualizowanie zasobów i ich właściwości |
> | Akcja | Microsoft. datamigration/SKU/Read | Pobierz listę jednostek SKU obsługiwanych przez zasoby DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DBforMariaDB/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Akcja | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Zwróć wyniki operacji serwera MariaDB |
> | Akcja | Microsoft.DBforMariaDB/locations/operationResults/read | Zwróć wyniki operacji serwera MariaDB opartego na źródle zasobów |
> | Akcja | Microsoft.DBforMariaDB/locations/operationResults/read | Zwróć wyniki operacji serwera MariaDB |
> | Akcja | Microsoft.DBforMariaDB/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforMariaDB/operations/read | Zwróć listę operacji MariaDB. |
> | Akcja | Microsoft.DBforMariaDB/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft.DBforMariaDB/register/action | Zarejestruj dostawcę zasobów MariaDB |
> | Akcja | Microsoft.DBforMariaDB/servers/administrators/delete | Usuwa istniejącego administratora serwera MariaDB. |
> | Akcja | Microsoft.DBforMariaDB/servers/administrators/read | Pobiera listę administratorów serwera MariaDB. |
> | Akcja | Microsoft.DBforMariaDB/servers/administrators/write | Tworzy lub aktualizuje administratora serwera MariaDB o określonych parametrach. |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Utwórz nową sesję akcji rekomendacji |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/read | Zwróć listę doradców |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/read | Zwracanie klasyfikatora |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Zwróć zalecaną akcję |
> | Akcja | Microsoft.DBforMariaDB/servers/configurations/read | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft.DBforMariaDB/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft.DBforMariaDB/servers/databases/delete | Usuwa istniejącą bazę danych MariaDB. |
> | Akcja | Microsoft.DBforMariaDB/servers/databases/read | Zwróć listę baz danych MariaDB lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.DBforMariaDB/servers/databases/write | Tworzy bazę danych MariaDB z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.DBforMariaDB/servers/delete | Usuwa istniejący serwer. |
> | Akcja | Microsoft.DBforMariaDB/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft.DBforMariaDB/servers/firewallRules/read | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Akcja | Microsoft.DBforMariaDB/servers/firewallRules/write | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforMariaDB/servers/logFiles/read | Zwróć listę plików dziennika MariaDB. |
> | Akcja | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie disagnostic dla zasobu |
> | Akcja | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów MariaDB |
> | Akcja | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.DBforMariaDB/servers/queryTexts/action | Zwróć teksty dla listy zapytań |
> | Akcja | Microsoft.DBforMariaDB/servers/queryTexts/action | Zwróć tekst zapytania |
> | Akcja | Microsoft.DBforMariaDB/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforMariaDB/servers/recoverableServers/read | Zwróć informacje o odzyskiwanym serwerze MariaDB |
> | Akcja | Microsoft.DBforMariaDB/servers/replicas/read | Pobierz repliki serwera MariaDB |
> | Akcja | Microsoft. DBforMariaDB/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Akcja | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Akcja | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Zwróć statystykę zapytania |
> | Akcja | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Zaktualizuj konfiguracje dla określonego serwera |
> | Akcja | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę Virtual Network |
> | Akcja | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft.DBforMariaDB/servers/waitStatistics/read | Zwróć statystykę oczekiwania dla wystąpienia |
> | Akcja | Microsoft.DBforMariaDB/servers/waitStatistics/read | Zwróć statystykę oczekiwania |
> | Akcja | Microsoft.DBforMariaDB/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DBforMySQL/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Akcja | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Zwróć wyniki operacji serwera MySQL |
> | Akcja | Microsoft.DBforMySQL/locations/operationResults/read | Zwróć wyniki operacji serwera MySQL na podstawie zasobów |
> | Akcja | Microsoft.DBforMySQL/locations/operationResults/read | Zwróć wyniki operacji serwera MySQL |
> | Akcja | Microsoft.DBforMySQL/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforMySQL/operations/read | Zwróć listę operacji programu MySQL. |
> | Akcja | Microsoft.DBforMySQL/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft. DBforMySQL/Register/Action | Zarejestruj dostawcę zasobów MySQL |
> | Akcja | Microsoft.DBforMySQL/servers/administrators/delete | Usuwa istniejącego administratora serwera MySQL. |
> | Akcja | Microsoft.DBforMySQL/servers/administrators/read | Pobiera listę administratorów serwera MySQL. |
> | Akcja | Microsoft.DBforMySQL/servers/administrators/write | Tworzy lub aktualizuje administratora serwera MySQL o określonych parametrach. |
> | Akcja | Microsoft. DBforMySQL/serwery/doradcy/createRecommendedActionSession/akcja | Utwórz nową sesję akcji rekomendacji |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/read | Zwróć listę doradców |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/read | Zwracanie klasyfikatora |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Zwróć zalecaną akcję |
> | Akcja | Microsoft.DBforMySQL/servers/configurations/read | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft.DBforMySQL/servers/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft.DBforMySQL/servers/databases/delete | Usuwa istniejącą bazę danych MySQL. |
> | Akcja | Microsoft.DBforMySQL/servers/databases/read | Zwróć listę baz danych MySQL lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.DBforMySQL/servers/databases/write | Tworzy bazę danych MySQL z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.DBforMySQL/servers/delete | Usuwa istniejący serwer. |
> | Akcja | Microsoft. DBforMySQL/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft. DBforMySQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Akcja | Microsoft. DBforMySQL/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforMySQL/servers/logFiles/read | Zwróć listę plików dziennika PostgreSQL. |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie disagnostic dla zasobu |
> | Akcja | Microsoft. DBforMySQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów MySQL |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft. DBforMySQL/serwery/queryTexts/akcja | Zwróć teksty dla listy zapytań |
> | Akcja | Microsoft. DBforMySQL/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Akcja | Microsoft.DBforMySQL/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforMySQL/servers/recoverableServers/read | Zwróć informacje o odzyskiwanym serwerze MySQL |
> | Akcja | Microsoft.DBforMySQL/servers/replicas/read | Uzyskaj odczyt replik serwera MySQL |
> | Akcja | Microsoft. DBforMySQL/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Akcja | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Akcja | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Zwróć statystykę zapytania |
> | Akcja | Microsoft.DBforMySQL/servers/updateConfigurations/action | Zaktualizuj konfiguracje dla określonego serwera |
> | Akcja | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę Virtual Network |
> | Akcja | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft.DBforMySQL/servers/waitStatistics/read | Zwróć statystykę oczekiwania dla wystąpienia |
> | Akcja | Microsoft.DBforMySQL/servers/waitStatistics/read | Zwróć statystykę oczekiwania |
> | Akcja | Microsoft.DBforMySQL/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DBforPostgreSQL/checkNameAvailability/Action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Akcja | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Zwróć wyniki operacji serwera PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/locations/operationResults/read | Zwróć wyniki operacji serwera PostgreSQL opartego na źródle zasobów |
> | Akcja | Microsoft.DBforPostgreSQL/locations/operationResults/read | Zwróć wyniki operacji serwera PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Zwróć listę wyników operacji wykrywania zagrożeń serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/operations/read | Zwróć listę operacji PostgreSQL. |
> | Akcja | Microsoft.DBforPostgreSQL/performanceTiers/read | Zwraca listę dostępnych warstw wydajności. |
> | Akcja | Microsoft. DBforPostgreSQL/Register/Action | Zarejestruj dostawcę zasobów PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/servers/administrators/delete | Usuwa istniejącego administratora serwera PostgreSQL. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/administrators/read | Pobiera listę administratorów serwera PostgreSQL. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/administrators/write | Tworzy lub aktualizuje administratora serwera PostgreSQL o określonych parametrach. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/advisors/read | Zwróć listę doradców |
> | Akcja | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Zwróć listę zalecanych akcji |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/doradcy/recommendedActionSessions/akcja | Wykonaj zalecenia |
> | Akcja | Microsoft.DBforPostgreSQL/servers/configurations/read | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/konfiguracje/zapis | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft.DBforPostgreSQL/servers/databases/delete | Usuwa istniejącą bazę danych PostgreSQL. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/databases/read | Zwróć listę baz danych PostgreSQL lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/databases/write | Tworzy bazę danych PostgreSQL z określonymi parametrami lub aktualizuje właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/delete | Usuwa istniejący serwer. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/firewallRules/usuwanie | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/firewallRules/zapis | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/logFiles/read | Zwróć listę plików dziennika PostgreSQL. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/usuwanie | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/odczyt | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Validate/Action | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnectionProxies/zapis | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/usuwanie | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/odczyt | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateEndpointConnections/zapis | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/privateLinkResources/odczyt | Pobierz zasoby linku prywatnego dla odpowiedniego serwera PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie disagnostic dla zasobu |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/queryTexts/akcja | Zwróć tekst zapytania |
> | Akcja | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Zwróć teksty dla listy zapytań |
> | Akcja | Microsoft.DBforPostgreSQL/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Zwróć informacje o odzyskiwanym serwerze PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/servers/replicas/read | Pobierz repliki serwera PostgreSQL |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/ponowne uruchamianie/akcja | Uruchamia ponownie określony serwer. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Pobierz szczegóły zasad wykrywania zagrożeń serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Zwróć listę statystyk zapytania dla najważniejszych zapytań. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Zaktualizuj konfiguracje dla określonego serwera |
> | Akcja | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę Virtual Network |
> | Akcja | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Zwróć statystykę oczekiwania dla wystąpienia |
> | Akcja | Microsoft. DBforPostgreSQL/serwery/zapis | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Zwróć listę konfiguracji dla serwera lub pobiera właściwości dla określonej konfiguracji. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Zaktualizuj wartość dla określonej konfiguracji |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/delete | Usuwa istniejący serwer. |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Delete | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Zwróć listę reguł zapory dla serwera lub pobiera właściwości dla określonej reguły zapory. |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Write | Tworzy regułę zapory z określonymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie disagnostic dla zasobu |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Zaktualizuj konfiguracje dla określonego serwera |
> | Akcja | Microsoft. DBforPostgreSQL/serversv2/Write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Devices/Account/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/Account/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. Devices/Account/logDefinitions/Read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Akcja | Microsoft. Devices/Account/metricDefinitions/Read | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft.Devices/checkNameAvailability/Action | Sprawdź, czy nazwa IotHub jest dostępna |
> | Akcja | Microsoft.Devices/checkNameAvailability/Action | Sprawdź, czy nazwa IotHub jest dostępna |
> | Akcja | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Sprawdź, czy nazwa usługi aprowizacji jest dostępna |
> | Akcja | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Sprawdź, czy nazwa usługi aprowizacji jest dostępna |
> | Akcja | Microsoft. Devices/digitalTwins/Delete | Usuń istniejące konto bliźniaczych reprezentacji Digital i wszystkie jego elementy podrzędne |
> | Akcja | Microsoft. Devices/digitalTwins/operationresults/odczyt | Pobieranie stanu operacji przy użyciu konta bliźniaczych reprezentacji Digital |
> | Akcja | Microsoft. Devices/digitalTwins/odczyt | Pobiera listę kont bliźniaczych reprezentacji cyfrowych skojarzonych z subskrypcją |
> | Akcja | Microsoft. Devices/digitalTwins/SKU/odczyt | Pobierz listę prawidłowych jednostek SKU dla kont Digital bliźniaczych reprezentacji |
> | Akcja | Microsoft. Devices/digitalTwins/Write | Utwórz nowe konto bliźniaczych reprezentacji z cyframi |
> | Akcja | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Usuwanie zasobu dzierżawy IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Usuń grupę odbiorców EventHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Utwórz grupę odbiorców EventHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eksportuj urządzenia |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Pobiera zasób statystyk dzierżawy IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importuj urządzenia |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Pobiera klucz dzierżawy IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Pobierz szczegóły zadań przesłane w danym IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Pobiera klucze dzierżawy IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Pobierz metryki przydziału |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/Read | Pobiera zasób dzierżawy IotHub |
> | Akcja | Microsoft. Devices/elasticPools/iotHubTenants/Routing/trasy/$testall/Action | Przetestuj komunikat dla wszystkich istniejących tras |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Przetestuj komunikat w odniesieniu do podanej trasy testowej |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/Write | Utwórz lub zaktualizuj zasób dzierżawy IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft.Devices/iotHubs/certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft.Devices/iotHubs/certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft. Devices/iotHubs/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Akcja | Microsoft.Devices/iotHubs/certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Akcja | Microsoft.Devices/iotHubs/Delete | Usuń zasób IotHub |
> | Akcja | Microsoft.Devices/IotHubs/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/IotHubs/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Usuwa filtr Event Grid |
> | Akcja | Microsoft.Devices/iotHubs/eventGridFilters/Read | Pobiera Filtr Event Grid |
> | Akcja | Microsoft.Devices/iotHubs/eventGridFilters/Write | Utwórz nowy lub zaktualizuj istniejący filtr Event Grid |
> | Akcja | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Usuń grupę odbiorców EventHub |
> | Akcja | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Akcja | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Utwórz grupę odbiorców EventHub |
> | Akcja | Microsoft.Devices/iotHubs/exportDevices/Action | Eksportuj urządzenia |
> | Akcja | Microsoft.Devices/iotHubs/importDevices/Action | Importuj urządzenia |
> | Akcja | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Pobierz klucz IotHub dla podaną nazwę |
> | Akcja | Microsoft.Devices/iotHubs/iotHubStats/Read | Pobierz statystyki IotHub |
> | Akcja | Microsoft.Devices/iotHubs/jobs/Read | Pobierz szczegóły zadań przesłane w danym IotHub |
> | Akcja | Microsoft.Devices/iotHubs/listkeys/Action | Pobierz wszystkie klucze IotHub |
> | Akcja | Microsoft.Devices/IotHubs/logDefinitions/read | Pobiera dostępne definicje dzienników dla usługi IotHub |
> | Akcja | Microsoft.Devices/IotHubs/metricDefinitions/read | Pobiera dostępne metryki dla usługi IotHub |
> | Akcja | Microsoft.Devices/iotHubs/operationresults/Read | Pobierz wynik operacji (przestarzały interfejs API) |
> | Akcja | Microsoft.Devices/iotHubs/quotaMetrics/Read | Pobierz metryki przydziału |
> | Akcja | Microsoft.Devices/iotHubs/Read | Pobiera zasoby IotHub |
> | Akcja | Microsoft.Devices/iotHubs/routing/$testall/Action | Przetestuj komunikat dla wszystkich istniejących tras |
> | Akcja | Microsoft.Devices/iotHubs/routing/$testnew/Action | Przetestuj komunikat w odniesieniu do podanej trasy testowej |
> | Akcja | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Pobiera kondycję wszystkich punktów końcowych routingu dla IotHub |
> | Akcja | Microsoft.Devices/iotHubs/skus/Read | Pobieranie prawidłowych jednostek SKU IotHub |
> | Akcja | Microsoft.Devices/iotHubs/Write | Utwórz lub zaktualizuj zasób IotHub |
> | Akcja | Microsoft.Devices/locations/operationresults/Read | Pobierz wynik operacji na podstawie lokalizacji |
> | Akcja | Microsoft. Devices/operationresults/odczyt | Pobierz wynik operacji |
> | Akcja | Microsoft. Devices/Operations/Read | Pobierz wszystkie operacje ResourceProvider |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft. Devices/provisioningServices/Certificates/verify/Action | Weryfikuj zasób certyfikatu |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/Write | Utwórz lub zaktualizuj certyfikat |
> | Akcja | Microsoft.Devices/provisioningServices/Delete | Usuń zasób IotDps |
> | Akcja | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Pobierz klucze IotDps dla nazwy klucza |
> | Akcja | Microsoft.Devices/provisioningServices/listkeys/Action | Pobierz wszystkie klucze IotDps |
> | Akcja | Microsoft.Devices/provisioningServices/logDefinitions/read | Pobiera dostępne definicje dzienników dla usługi aprowizacji |
> | Akcja | Microsoft.Devices/provisioningServices/metricDefinitions/read | Pobiera dostępne metryki dla usługi aprowizacji |
> | Akcja | Microsoft.Devices/provisioningServices/operationresults/Read | Pobierz wynik operacji DPS |
> | Akcja | Microsoft.Devices/provisioningServices/Read | Pobierz zasób IotDps |
> | Akcja | Microsoft.Devices/provisioningServices/skus/Read | Pobieranie prawidłowych jednostek SKU IotDps |
> | Akcja | Microsoft.Devices/provisioningServices/Write | Utwórz zasób IotDps |
> | Akcja | Microsoft. Devices/Register/Action | Zarejestruj subskrypcję dostawcy zasobów IotHub i Włącz tworzenie zasobów IotHub |
> | Akcja | Microsoft.Devices/usages/Read | Pobierz szczegóły użycia subskrypcji dla tego dostawcy. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. DevSpaces/controllers/Delete | Usuwanie kontrolera Azure Dev Spaces i usług dataplaning |
> | Akcja | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Wyświetl szczegóły połączenia dla infrastruktury kontrolera Azure Dev Spaces |
> | Akcja | Microsoft.DevSpaces/controllers/read | Odczytaj właściwości kontrolera Azure Dev Spaces |
> | Akcja | Microsoft. DevSpaces/controllers/Write | Utwórz lub zaktualizuj właściwości kontrolera Azure Dev Spaces |
> | Akcja | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Sprawdź istniejące mapowanie kontrolera dla hosta kontenera |
> | Akcja | Microsoft.DevSpaces/locations/operationresults/read | Stan odczytu operacji asynchronicznej |
> | Akcja | Microsoft. DevSpaces/Register/Action | Rejestrowanie dostawcy zasobów Microsoft dev Spaces z subskrypcją |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DevTestLab/labCenters/delete | Usuń centra laboratoryjne. |
> | Akcja | Microsoft.DevTestLab/labCenters/read | Odczytywanie centrów laboratoryjnych. |
> | Akcja | Microsoft.DevTestLab/labCenters/write | Dodawanie lub modyfikowanie centrów laboratoryjnych. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Odczytywanie szablonów usługi Azure Resource Manager. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generuje szablon ARM dla danego artefaktu, przekazuje wymagane pliki na konto magazynu i sprawdza poprawność wygenerowanego artefaktu. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Odczytaj artefakty. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/delete | Usuń źródła artefaktów. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/read | Odczytaj źródła artefaktów. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/write | Dodaj lub zmodyfikuj źródła artefaktów. |
> | Akcja | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Zarezerwuj losową maszynę wirtualną z dodaną odpowiedzialnością w laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/costs/read | Zapoznaj się z kosztami. |
> | Akcja | Microsoft.DevTestLab/labs/costs/write | Dodawanie lub modyfikowanie kosztów. |
> | Akcja | Microsoft.DevTestLab/labs/CreateEnvironment/action | Twórz maszyny wirtualne w laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/customImages/delete | Usuń obrazy niestandardowe. |
> | Akcja | Microsoft.DevTestLab/labs/customImages/read | Odczytaj obrazy niestandardowe. |
> | Akcja | Microsoft.DevTestLab/labs/customImages/write | Dodawanie lub modyfikowanie obrazów niestandardowych. |
> | Akcja | Microsoft.DevTestLab/labs/delete | Usuwanie laboratoriów. |
> | Akcja | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Upewnij się, że bieżący użytkownik ma prawidłowy profil w laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Eksportuje użycie zasobów laboratorium do konta magazynu |
> | Akcja | Microsoft.DevTestLab/labs/formulas/delete | Usuń formuły. |
> | Akcja | Microsoft.DevTestLab/labs/formulas/read | Odczytaj formuły. |
> | Akcja | Microsoft.DevTestLab/labs/formulas/write | Dodaj lub zmodyfikuj formuły. |
> | Akcja | Microsoft.DevTestLab/labs/galleryImages/read | Odczytuj obrazy galerii. |
> | Akcja | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generowanie identyfikatora URI na potrzeby przekazywania niestandardowych obrazów dysków do laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Zaimportuj maszynę wirtualną do innego laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/ListVhds/action | Wyświetlanie listy obrazów dysków dostępnych do tworzenia obrazów niestandardowych. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/delete | Usuwanie kanałów powiadomień. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Wyślij powiadomienie do podanego kanału. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/read | Odczytaj kanały powiadomień. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/write | Dodawanie lub modyfikowanie kanałów powiadomień. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Szacuje zasady laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/policies/delete | Usuń zasady. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/policies/read | Odczytaj zasady. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/policies/write | Dodawanie lub modyfikowanie zasad. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/read | Odczytaj zestawy zasad. |
> | Akcja | Microsoft.DevTestLab/labs/read | Odczytywanie laboratoriów. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft. wspólny/Labs/harmonogramy/wykonywanie/akcja | Wykonaj harmonogram. |
> | Akcja | Microsoft. wspólny/Labs/Schedules/ListApplicable/Action | Wyświetla wszystkie odpowiednie harmonogramy |
> | Akcja | Microsoft.DevTestLab/labs/schedules/read | Odczytaj harmonogramy. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/serviceRunners/delete | Usuwanie modułu uruchamiającego usługi. |
> | Akcja | Microsoft.DevTestLab/labs/serviceRunners/read | Odczytywanie modułu uruchamiającego usługi. |
> | Akcja | Microsoft.DevTestLab/labs/serviceRunners/write | Dodawanie lub modyfikowanie modułu uruchamiającego usługi. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/delete | Usuń udostępnione galerie. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/read | Odczytuj udostępnione galerie. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Usuń obrazy udostępnione. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Odczytuj obrazy udostępnione. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Dodaj lub Modyfikuj obrazy udostępnione. |
> | Akcja | Microsoft.DevTestLab/labs/sharedGalleries/write | Dodaj lub zmodyfikuj udostępnione galerie. |
> | Akcja | Microsoft.DevTestLab/labs/users/delete | Usuń profile użytkowników. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/Attach/action | Dołącz i Utwórz dzierżawę dysku dla maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/delete | Usuń dyski. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/Detach/action | Odłącz i Przerwij dzierżawę dysku dołączonego do maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/read | Odczytywanie dysków. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/write | Dodawanie lub modyfikowanie dysków. |
> | Akcja | Microsoft.DevTestLab/labs/users/environments/delete | Usuń środowiska. |
> | Akcja | Microsoft.DevTestLab/labs/users/environments/read | Odczytuj środowiska. |
> | Akcja | Microsoft.DevTestLab/labs/users/environments/write | Dodaj lub zmodyfikuj środowiska. |
> | Akcja | Microsoft.DevTestLab/labs/users/read | Odczytaj profile użytkowników. |
> | Akcja | Microsoft.DevTestLab/labs/users/secrets/delete | Usuń wpisy tajne. |
> | Akcja | Microsoft.DevTestLab/labs/users/secrets/read | Odczytaj wpisy tajne. |
> | Akcja | Microsoft.DevTestLab/labs/users/secrets/write | Dodaj lub zmodyfikuj wpisy tajne. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Usuwanie sieci szkieletowej usług. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Odczytaj sieci szkieletowe usług. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft. wspólny/Labs/users/servicefabrics/Schedules/Execute/Action | Wykonaj harmonogram. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Odczytaj harmonogramy. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Uruchom sieć szkieletową usług. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zatrzymaj sieć szkieletową usług |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Dodawanie lub modyfikowanie sieci szkieletowej usług. |
> | Akcja | Microsoft.DevTestLab/labs/users/write | Dodaj lub zmodyfikuj profile użytkowników. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Dołącz nowy lub istniejący dysk danych do maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Zastosuj artefakty do maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Przejmowanie na własność istniejącej maszyny wirtualnej |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/delete | Usuwanie maszyn wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Odłącz określony dysk od maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Pobiera ciąg, który reprezentuje zawartość pliku RDP dla maszyny wirtualnej |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/read | Odczytaj maszyny wirtualne. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Ponowne wdrażanie maszyny wirtualnej |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/Zmień rozmiar/akcję | Zmień rozmiar maszyny wirtualnej. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/restart/akcja | Uruchom ponownie maszynę wirtualną. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft. wspólny/Labs/virtualMachines/Schedules/Execute/Action | Wykonaj harmonogram. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Odczytaj harmonogramy. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Uruchom maszynę wirtualną. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Zatrzymaj maszynę wirtualną |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Przenosi wszystkie dyski z danymi dołączone do maszyny wirtualnej, które mają należeć do bieżącego użytkownika. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Zwolnij własność istniejącej maszyny wirtualnej |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/write | Dodaj lub zmodyfikuj maszyny wirtualne. |
> | Akcja | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Delete | Usuń bastionhosts. |
> | Akcja | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Read | Odczytaj bastionhosts. |
> | Akcja | Microsoft. wspólny/Labs/virtualNetworks/bastionHosts/Write | Dodaj lub zmodyfikuj bastionhosts. |
> | Akcja | Microsoft.DevTestLab/labs/virtualNetworks/delete | Usuń sieci wirtualne. |
> | Akcja | Microsoft.DevTestLab/labs/virtualNetworks/read | Odczytaj sieci wirtualne. |
> | Akcja | Microsoft.DevTestLab/labs/virtualNetworks/write | Dodaj lub zmodyfikuj sieci wirtualne. |
> | Akcja | Microsoft.DevTestLab/labs/vmPools/delete | Usuń pule maszyn wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/vmPools/read | Odczytaj pule maszyn wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/vmPools/write | Dodaj lub zmodyfikuj pule maszyn wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/write | Dodawanie lub modyfikowanie laboratoriów. |
> | Akcja | Microsoft.DevTestLab/locations/operations/read | Operacje odczytu. |
> | Akcja | Microsoft.DevTestLab/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.DevTestLab/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft. wspólny/harmonogramy/wykonywanie/akcja | Wykonaj harmonogram. |
> | Akcja | Microsoft.DevTestLab/schedules/read | Odczytaj harmonogramy. |
> | Akcja | Microsoft.DevTestLab/schedules/Retarget/action | Aktualizuje identyfikator zasobu docelowego harmonogramu. |
> | Akcja | Microsoft.DevTestLab/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DocumentDB/databaseAccountNames/read | Sprawdza dostępność nazw. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Delete | Usuwanie kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Read | Odczytaj kolekcję lub Wyświetl listę wszystkich kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Odczytaj przepływność kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Aktualizowanie przepływności kolekcji. Dotyczy tylko typów interfejsu API: "MongoDB". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Collections/Write | Utwórz lub zaktualizuj kolekcję. Dotyczy tylko typów interfejsu API: "MongoDB". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Delete | Usuń kontener. Dotyczy tylko typów interfejsu API: "SQL". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Read | Odczytaj kontener lub Wyświetl listę wszystkich kontenerów. Dotyczy tylko typów interfejsu API: "SQL". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Odczytaj przepływność kontenera. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Zaktualizuj przepływność kontenera. Dotyczy tylko typów interfejsu API: "SQL". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Containers/Write | Utwórz lub zaktualizuj kontener. Dotyczy tylko typów interfejsu API: "SQL". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Delete | Usuń bazę danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Usuwanie grafu. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Odczytaj wykres lub Wyświetl wszystkie wykresy. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Odczytaj przepływność grafu. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Aktualizowanie przepływności grafu. Dotyczy tylko typów interfejsu API: "gremlin'. Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/bazy danych/wykresy/zapis | Utwórz lub zaktualizuj Graf. Dotyczy tylko typów interfejsu API: "gremlin'. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Odczytaj bazę danych lub listę wszystkich baz danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Odczytaj przepływność bazy danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Aktualizowanie przepływności bazy danych. Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/API/Databases/Write | Tworzenie bazy danych Dotyczy tylko typów interfejsu API: "SQL", "MongoDB", "gremlin'". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Usuń przestrzeń kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Odczytaj miejsce na dysku lub Wyświetl wszystkie spacje. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Odczytaj przepustowość przestrzeni kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Aktualizowanie przepływności przestrzeni kluczy. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Usuń tabelę. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Odczytaj tabelę lub listę wszystkich tabel. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Odczytaj tabelę przepływność. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Aktualizowanie przepływności tabeli. Dotyczy tylko typów interfejsu API: "Cassandra". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Utwórz lub zaktualizuj tabelę. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Utwórz miejsce na dysku. Dotyczy tylko typów interfejsu API: "Cassandra". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Usuń tabelę. Dotyczy tylko typów interfejsu API: "Table". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Table". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Odczytaj tabelę lub listę wszystkich tabel. Dotyczy tylko typów interfejsu API: "Table". |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Stan odczytu operacji asynchronicznej. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Odczytaj tabelę przepływność. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Aktualizowanie przepływności tabeli. Dotyczy tylko typów interfejsu API: "Table". Dotyczy tylko typów ustawień: przepływność. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/interfejsy API/tabele/zapis | Utwórz lub zaktualizuj tabelę. Dotyczy tylko typów interfejsu API: "Table". |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/kopia zapasowa/akcja | Prześlij żądanie skonfigurowania kopii zapasowej |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Zmień grupę zasobów konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Odczytuje definicje metryk kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Odczytuje metryki kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Odczytaj metryki na poziomie klucza partycji konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Odczytaj metryki na poziomie partycji konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Odczytywanie partycji konta bazy danych w kolekcji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Odczytaj użycie poziomów partycji konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Odczytuje użycie kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Odczytuje definicje metryk bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Odczytuje metryki bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Odczytuje użycie bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/delete | Usuwa konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Zmień priorytety trybu failover regionów konta bazy danych. Służy do wykonywania ręcznej operacji przełączania do trybu failover |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/akcja | Pobieranie zasad tworzenia kopii zapasowej konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Pobieranie parametrów połączenia dla konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Lista kluczy konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Odczytuje definicje metryk konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/metrics/read | Odczytuje metryki konta bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/offlineRegion/akcja | Offline w regionie konta bazy danych.  |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online region konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Stan odczytu operacji asynchronicznej |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Metryki opóźnienia odczytu |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/read | Odczytaj percentyle opóźnień replikacji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Odczytaj metryki opóźnienia dla określonego źródła i regionu docelowego |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Odczytaj metryki opóźnienia dla określonego regionu docelowego |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Usuń serwer proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Odczytaj prywatny serwer proxy połączenia dla konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Weryfikowanie serwera proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Utwórz lub zaktualizuj serwer proxy połączenia prywatnego punktu końcowego dla konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/read | Odczytuje konto bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Obróć klucze konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Odczytuje metryki kolekcji regionalnej. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Odczytaj metryki na poziomie klucza partycji konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Odczytaj metryki na poziomie partycji konta bazy danych |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/region/bazy danych/kolekcje/partycje/odczyt | Odczytywanie partycji konta bazy danych regionalnych w kolekcji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Odczytuje metryki konta regionu i bazy danych. |
> | Akcja | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Prześlij żądanie przywrócenia |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/usages/read | Odczytuje użycie konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/write | Zaktualizuj konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft. DocumentDB, że VirtualNetwork lub podsieć jest usuwana |
> | Akcja | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Odczytaj stan operacji asynchronicznej deleteVirtualNetworkOrSubnets |
> | Akcja | Microsoft.DocumentDB/locations/operationsStatus/read | Odczytuje stan operacji asynchronicznych |
> | Akcja | Microsoft.DocumentDB/operationResults/read | Stan odczytu operacji asynchronicznej |
> | Akcja | Microsoft.DocumentDB/operations/read | Operacje odczytu dostępne dla programu Microsoft DocumentDBe  |
> | Akcja | Microsoft. DocumentDB/Register/Action |  Zarejestruj dostawcę zasobów Microsoft DocumentDB dla subskrypcji |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

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
> | Akcja | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Lista akcja umowy |
> | Akcja | Microsoft.DomainRegistration/topLevelDomains/Read | Pobierz domeny Toplevel |
> | Akcja | Microsoft.DomainRegistration/topLevelDomains/Read | Pobierz domenę Toplevel |
> | Akcja | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Sprawdzanie poprawności obiektu zakupu domeny bez jego przesyłania |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. EventGrid/domeny/usuwanie | Usuwanie domeny |
> | Akcja | Microsoft. EventGrid/domeny/listKeys/akcja | Wyświetl klucze dla domeny |
> | Akcja | Microsoft. EventGrid/domeny/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla domen |
> | Akcja | Microsoft. EventGrid/domeny/odczyt | Odczytaj domenę |
> | Akcja | Microsoft. EventGrid/domeny/regenerateKey/akcja | Wygeneruj ponownie klucz dla domeny |
> | Akcja | Microsoft. EventGrid/domen/tematów/tematy/Usuń | Usuwanie tematu domeny |
> | Akcja | Microsoft.EventGrid/domains/topics/read | Odczytaj temat domeny |
> | Akcja | Microsoft. EventGrid/domen/tematy/Zapisz | Utwórz lub zaktualizuj temat domeny |
> | Akcja | Microsoft. EventGrid/domeny/zapis | Utwórz lub zaktualizuj domenę |
> | Akcja | Microsoft. EventGrid/eventSubscriptions/Delete | Usuń eventSubscription |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Pobierz pełny adres URL dla subskrypcji zdarzeń |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Akcja | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla eventSubscriptions |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/read | Odczytaj eventSubscription |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/write | Utwórz lub zaktualizuj eventSubscription |
> | Akcja | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla tematów |
> | Akcja | Microsoft.EventGrid/extensionTopics/read | Odczytaj extensionTopic. |
> | Akcja | Microsoft.EventGrid/locations/eventSubscriptions/read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | Akcja | Microsoft.EventGrid/locations/operationResults/read | Przeczytaj wynik operacji regionalnej |
> | Akcja | Microsoft.EventGrid/locations/operationsStatus/read | Odczytywanie stanu operacji regionalnej |
> | Akcja | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | Akcja | Microsoft.EventGrid/operationResults/read | Odczytaj wynik operacji |
> | Akcja | Microsoft.EventGrid/operations/read | Utwórz listę operacji EventGrid. |
> | Akcja | Microsoft.EventGrid/operationsStatus/read | Odczytywanie stanu operacji |
> | Akcja | Microsoft. EventGrid/Register/Action | Rejestruje subskrypcję dostawcy zasobów EventGrid. |
> | Akcja | Microsoft. EventGrid/tematy/Usuń | Usuwanie tematu |
> | Akcja | Microsoft.EventGrid/topics/listKeys/action | Lista kluczy tematu |
> | Akcja | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla tematów |
> | Akcja | Microsoft. EventGrid/tematy/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla tematów |
> | Akcja | Microsoft.EventGrid/topics/read | Zapoznaj się z tematem |
> | Akcja | Microsoft.EventGrid/topics/regenerateKey/action | Wygeneruj ponownie klucz dla tematu |
> | Akcja | Microsoft. EventGrid/tematy/zapis | Utwórz lub zaktualizuj temat |
> | Akcja | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | Akcja | Microsoft.EventGrid/topictypes/eventtypes/read | Odczytaj zdarzenia typu obsługiwane przez element temattype |
> | Akcja | Microsoft. EventGrid/topictypes/odczyt | Odczytaj element tematu |
> | Akcja | Microsoft.EventGrid/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. EventHub/availableClusterRegions/odczyt | Operacja odczytu, aby wyświetlić listę dostępnych wstępnie inicjowanych klastrów według regionów platformy Azure. |
> | Akcja | Microsoft. EventHub/checkNameAvailability/akcja | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft. EventHub/checkNamespaceAvailability/akcja | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Akcja | Microsoft. EventHub/klastry/usuwanie | Usuwa istniejący zasób klastra. |
> | Akcja | Microsoft. EventHub/klastry/przestrzenie nazw/odczyt | Wyświetl listę identyfikatorów ARM obszaru nazw dla przestrzeni nazw w klastrze. |
> | Akcja | Microsoft.EventHub/clusters/operationresults/read | Pobierz stan asynchronicznej operacji klastra. |
> | Akcja | Microsoft. EventHub/klastry/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk klastra |
> | Akcja | Microsoft.EventHub/clusters/read | Pobiera opis zasobu klastra |
> | Akcja | Microsoft. EventHub/klastry/zapis | Tworzy lub modyfikuje istniejący zasób klastra. |
> | Akcja | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Usuwa reguły sieci wirtualnej w dostawcy zasobów EventHub dla określonej sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/usuwanie | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Action | Operacja aktualizowania centrum EventHub. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji EventHub |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do centrum EventHub |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/odczyt |  Pobierz listę reguł autoryzacji EventHub |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji/Write | Utwórz reguły autoryzacji EventHub i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Akcja | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operacja usuwania zasobu w ramach odbiorcy |
> | Akcja | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Pobierz listę opisów zasobów dla odbiorców |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventHubs/consumergroups/Write | Utwórz lub zaktualizuj właściwości odbiorcy. |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/Delete | Operacja usuwania zasobu EventHub |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/read | Pobierz listę opisów zasobów EventHub |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/eventhubs/Write | Utwórz lub zaktualizuj właściwości EventHub. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Delete | Usuń zasób filtru IP |
> | Akcja | Microsoft.EventHub/namespaces/ipFilterRules/read | Pobierz zasób filtru IP |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/ipFilterRules/Write | Utwórz zasób filtru IP |
> | Akcja dataaction | Microsoft. EventHub/przestrzenie nazw/komunikaty/odbieranie/akcja | Odbieranie komunikatów |
> | Akcja dataaction | Microsoft. EventHub/przestrzenie nazw/komunikaty/wysyłanie/akcja | Wysyłanie komunikatów |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/networkruleset/Delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/networkruleset/Read | Pobiera zasób NetworkRuleSet |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/networkruleset/Write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/networkrulesets/delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/networkrulesets/read | Pobiera zasób NetworkRuleSet |
> | Akcja | Microsoft.EventHub/namespaces/networkrulesets/write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/operationresults/Read | Pobierz stan operacji przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Pobierz listę opisów zasobów ustawień diagnostycznych przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/logDefinitions/Read | Pobierz listę opisów zasobów dzienników przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/dostawcy/Microsoft. Insights/metricDefinitions/Read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw ACS |
> | Akcja | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Pobiera zasób reguły sieci wirtualnej |
> | Akcja | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. EventHub/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Akcja | Microsoft.EventHub/operations/read | Pobierz operacje |
> | Akcja | Microsoft. EventHub/rejestr/akcja | Rejestruje subskrypcję dostawcy zasobów centrum zdarzeń i umożliwia tworzenie zasobów EventHub |
> | Akcja | Microsoft.EventHub/sku/read | Pobierz listę opisów zasobów jednostki SKU |
> | Akcja | Microsoft.EventHub/sku/regions/read | Pobierz listę opisów zasobów SkuRegions |
> | Akcja | Microsoft. EventHub/Wyrejestruj/akcja | Rejestruje dostawcę zasobów centrum zdarzeń |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Features/features/read | Pobiera funkcje subskrypcji. |
> | Akcja | Microsoft. Features/Operations/Read | Pobiera listę operacji. |
> | Akcja | Microsoft. Features/Providers/Features/Read | Pobiera funkcję subskrypcji dla danego dostawcy zasobów. |
> | Akcja | Microsoft. Features/Providers/Features/Register/Action | Rejestruje funkcję subskrypcji dla danego dostawcy zasobów. |
> | Akcja | Microsoft. Features/Providers/Features/Unregister/Action | Wyrejestrowuje funkcję subskrypcji w ramach danego dostawcy zasobów. |
> | Akcja | Microsoft. Features/Register/Action | Rejestruje funkcję subskrypcji. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. GuestConfiguration/guestConfigurationAssignments/Delete | Usuń przypisanie konfiguracji gościa. |
> | Akcja | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Pobierz przypisanie konfiguracji gościa. |
> | Akcja | Microsoft. GuestConfiguration/guestConfigurationAssignments/Reports/Read | Pobierz raport dotyczący przypisywania konfiguracji gościa. |
> | Akcja | Microsoft. GuestConfiguration/guestConfigurationAssignments/Write | Utwórz nowe przypisanie konfiguracji gościa. |
> | Akcja | Microsoft. GuestConfiguration/Operations/Read | Pobiera operacje dla dostawcy zasobów Microsoft. GuestConfiguration |
> | Akcja | Microsoft. GuestConfiguration/Register/Action | Rejestruje subskrypcję dostawcy zasobów Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

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

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ImportExport/jobs/delete | Usuwa istniejące zadanie. |
> | Akcja | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Pobiera klucze funkcji BitLocker dla określonego zadania. |
> | Akcja | Microsoft.ImportExport/jobs/read | Pobiera właściwości określonego zadania lub zwraca listę zadań. |
> | Akcja | Microsoft.ImportExport/jobs/write | Tworzy zadanie z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego zadania. |
> | Akcja | Microsoft. ImportExport/lokalizacje/odczyt | Pobiera właściwości dla określonej lokalizacji lub zwraca listę lokalizacji. |
> | Akcja | Microsoft. ImportExport/Operations/Read | Pobiera operacje obsługiwane przez dostawcę zasobów. |
> | Akcja | Microsoft. ImportExport/Register/Action | Rejestruje subskrypcję dostawcy zasobów importu/eksportu i umożliwia tworzenie zadań importu/eksportu. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Insights/ActionGroups/Delete | Usuwanie grupy akcji |
> | Akcja | Microsoft. Insights/ActionGroups/Read | Odczytaj grupę akcji |
> | Akcja | Microsoft. Insights/ActionGroups/Write | Utwórz lub Zaktualizuj grupę akcji |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Uaktywniono alert dziennika aktywności |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Delete | Usuwanie alertu dziennika aktywności |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Read | Odczytaj alert dotyczący dziennika aktywności |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Write | Utwórz lub zaktualizuj alert dotyczący dziennika aktywności |
> | Akcja | Microsoft.Insights/AlertRules/Activated/Action | Uaktywniono alert dotyczący metryki klasycznej |
> | Akcja | Microsoft.Insights/AlertRules/Delete | Usuwanie alertu dotyczącego klasycznej metryki |
> | Akcja | Microsoft.Insights/AlertRules/Incidents/Read | Odczytaj zdarzenie klasycznego alertu dotyczącego metryki |
> | Akcja | Microsoft.Insights/AlertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | Akcja | Microsoft.Insights/AlertRules/Resolved/Action | Rozwiązano alert dotyczący metryki klasycznej |
> | Akcja | Microsoft.Insights/AlertRules/Throttled/Action | Ograniczona reguła alertu dotyczącego metryki klasycznej |
> | Akcja | Microsoft. Insights/AlertRules/Write | Utwórz lub zaktualizuj klasyczny alert dotyczący metryki |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Delete | Usuń ustawienie automatycznego skalowania |
> | Akcja | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Odczytaj definicje dzienników |
> | Akcja | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Read | Odczytaj ustawienie automatycznego skalowania |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Scaledown/Action | Inicjowano skalowanie w poziomie automatycznego skalowania |
> | Akcja | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Ukończono Skalowanie automatyczne w dół |
> | Akcja | Microsoft. Insights/AutoscaleSettings/Scaleup/Action | Zainicjowanie skalowania automatycznego skalowania w górę |
> | Akcja | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Ukończenie skalowania automatycznego skalowania w górę |
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
> | Akcja | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Usuwanie Application Insights osobistego elementu analizy |
> | Akcja | Microsoft. Insights/Components/MyAnalyticsItems/Read | Odczytywanie Application Insights osobistego elementu analizy |
> | Akcja | Microsoft. Insights/Components/MyAnalyticsItems/Write | Pisanie Application Insights osobistego elementu analizy |
> | Akcja | Microsoft. Insights/Components/webulubionych/odczyt | Odczytywanie Application Insights osobistego elementu ulubionego |
> | Akcja | Microsoft. Insights/Components/Operations/Read | Pobieranie stanu długotrwałych operacji w Application Insights |
> | Akcja | Microsoft. Insights/Components/PricingPlans/Read | Odczytywanie planu cenowego składnika Application Insights |
> | Akcja | Microsoft. Insights/Components/PricingPlans/Write | Pisanie planu cenowego składnika Application Insights |
> | Akcja | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Odczytywanie konfiguracji wykrywania proaktywnego Application Insights |
> | Akcja | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Pisanie Application Insights konfiguracji wykrywania proaktywnego |
> | Akcja | Microsoft. Insights/Components/Providers/Microsoft. Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft. Insights/Components/Przeczyść/akcja | Przeczyszczanie danych z Application Insights |
> | Akcja | Microsoft. Insights/Components/Query/Read | Uruchamianie zapytań względem dzienników Application Insights |
> | Akcja | Microsoft. Insights/Components/QuotaStatus/Read | Odczytywanie stanu przydziału składników Application Insights |
> | Akcja | Microsoft. Insights/Components/Read | Odczytywanie konfiguracji składnika usługi Application Insights |
> | Akcja | Microsoft. Insights/Components/SyntheticMonitorLocations/Read | Odczytywanie Application Insights lokalizacji WebTest |
> | Akcja | Microsoft. Insights/Components/webtests/Read | Odczytywanie konfiguracji WebTest |
> | Akcja | Microsoft.Insights/Components/WorkItemConfigs/Delete | Usuwanie Application Insights ALM Integration Configuration |
> | Akcja | Microsoft. Insights/Components/WorkItemConfigs/Read | Odczytywanie konfiguracji integracji Application Insights ALM |
> | Akcja | Microsoft.Insights/Components/WorkItemConfigs/Write | Pisanie Application Insights ALM Integration Configuration |
> | Akcja | Microsoft. Insights/Components/Write | Zapisywanie w konfiguracji składnika usługi Application Insights |
> | Akcja | Microsoft. Insights/DiagnosticSettings/Delete | Usuń ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft. Insights/DiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft. Insights/DiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne zasobu |
> | Akcja | Microsoft.Insights/EventCategories/Read | Odczytaj dostępne kategorie zdarzeń dziennika aktywności |
> | Akcja | Microsoft.Insights/eventtypes/digestevents/Read | Odczytaj podsumowanie typu zdarzenia zarządzania |
> | Akcja | Microsoft.Insights/eventtypes/values/Read | Odczytaj zdarzenia dziennika aktywności |
> | Akcja | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Usuń ustawienie diagnostyczne dziennika przepływu sieci |
> | Akcja | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Odczytaj ustawienie diagnostyczne dziennika przepływu sieci |
> | Akcja | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Utwórz lub zaktualizuj ustawienie diagnostyczne dziennika przepływu sieci |
> | Akcja | Microsoft. Insights/ListMigrationDate/Action | Pobierz datę migracji subskrypcji |
> | Akcja | Microsoft. Insights/ListMigrationDate/Read | Pobierz datę migracji subskrypcji |
> | Akcja | Microsoft.Insights/LogDefinitions/Read | Odczytaj definicje dzienników |
> | Akcja | Microsoft.Insights/LogProfiles/Delete | Usuwanie profilu dziennika aktywności |
> | Akcja | Microsoft.Insights/LogProfiles/Read | Odczytaj profil dziennika aktywności |
> | Akcja | Microsoft. Insights/LogProfiles/Write | Utwórz lub zaktualizuj profil dziennika aktywności |
> | Akcja | Microsoft. Insights/Logs/ADAssessmentRecommendation/Read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/ADReplicationResult/Read | Odczytaj dane z tabeli ADReplicationResult |
> | Akcja | Microsoft. Insights/Logs/ADSecurityAssessmentRecommendation/Read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/alerty/odczyt | Odczytaj dane z tabeli alertów |
> | Akcja | Microsoft. Insights/Logs/AlertHistory/Read | Odczytaj dane z tabeli AlertHistory |
> | Akcja | Microsoft. Insights/Logs/ApplicationInsights/Read | Odczytaj dane z tabeli ApplicationInsights |
> | Akcja | Microsoft.Insights/Logs/AzureActivity/Read | Odczytywanie danych z tabeli platformy Azure |
> | Akcja | Microsoft.Insights/Logs/AzureMetrics/Read | Odczytaj dane z tabeli AzureMetrics |
> | Akcja | Microsoft. Insights/Logs/BoundPort/Read | Odczytaj dane z tabeli BoundPort |
> | Akcja | Microsoft.Insights/Logs/CommonSecurityLog/Read | Odczytaj dane z tabeli CommonSecurityLog |
> | Akcja | Microsoft.Insights/Logs/ComputerGroup/Read | Odczytywanie danych z tabeli komputerów |
> | Akcja | Microsoft. Insights/Logs/Zmianakonfiguracji/Read | Odczytaj dane z tabeli Zmianakonfiguracji |
> | Akcja | Microsoft. Insights/Logs/ConfigurationData/Read | Odczytaj dane z tabeli ConfigurationData |
> | Akcja | Microsoft. Insights/Logs/ContainerImageInventory/Read | Odczytaj dane z tabeli ContainerImageInventory |
> | Akcja | Microsoft. Insights/Logs/ContainerInventory/Read | Odczytaj dane z tabeli ContainerInventory |
> | Akcja | Microsoft. Insights/Logs/ContainerLog/Read | Odczytaj dane z tabeli ContainerLog |
> | Akcja | Microsoft.Insights/Logs/ContainerServiceLog/Read | Odczytaj dane z tabeli ContainerServiceLog |
> | Akcja | Microsoft.Insights/Logs/DeviceAppCrash/Read | Odczytaj dane z tabeli DeviceAppCrash |
> | Akcja | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Odczytaj dane z tabeli DeviceAppLaunch |
> | Akcja | Microsoft.Insights/Logs/DeviceCalendar/Read | Odczytaj dane z tabeli DeviceCalendar |
> | Akcja | Microsoft. Insights/Logs/DeviceCleanup/Read | Odczytaj dane z tabeli DeviceCleanup |
> | Akcja | Microsoft.Insights/Logs/DeviceConnectSession/Read | Odczytaj dane z tabeli DeviceConnectSession |
> | Akcja | Microsoft.Insights/Logs/DeviceEtw/Read | Odczytaj dane z tabeli DeviceEtw |
> | Akcja | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | Akcja | Microsoft.Insights/Logs/DeviceHealth/Read | Odczytaj dane z tabeli zameldowaniem |
> | Akcja | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Odczytaj dane z tabeli DeviceHeartbeat |
> | Akcja | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | Akcja | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | Akcja | Microsoft.Insights/Logs/DeviceSleepState/Read | Odczytaj dane z tabeli DeviceSleepState |
> | Akcja | Microsoft. Insights/Logs/DHAppFailure/Read | Odczytaj dane z tabeli DHAppFailure |
> | Akcja | Microsoft.Insights/Logs/DHAppReliability/Read | Odczytaj dane z tabeli DHAppReliability |
> | Akcja | Microsoft.Insights/Logs/DHDriverReliability/Read | Odczytaj dane z tabeli DHDriverReliability |
> | Akcja | Microsoft. Insights/Logs/DHLogonFailures/Read | Odczytaj dane z tabeli DHLogonFailures |
> | Akcja | Microsoft.Insights/Logs/DHLogonMetrics/Read | Odczytaj dane z tabeli DHLogonMetrics |
> | Akcja | Microsoft. Insights/Logs/DHOSCrashData/Read | Odczytaj dane z tabeli DHOSCrashData |
> | Akcja | Microsoft. Insights/Logs/DHOSReliability/Read | Odczytaj dane z tabeli DHOSReliability |
> | Akcja | Microsoft.Insights/Logs/DHWipAppLearning/Read | Odczytaj dane z tabeli DHWipAppLearning |
> | Akcja | Microsoft.Insights/Logs/DnsEvents/Read | Odczytaj dane z tabeli DnsEvents |
> | Akcja | Microsoft.Insights/Logs/DnsInventory/Read | Odczytaj dane z tabeli DnsInventory |
> | Akcja | Microsoft. Insights/Logs/ETWEvent/Read | Odczytaj dane z tabeli ETWEvent |
> | Akcja | Microsoft. Insights/Logs/Event/Read | Odczytaj dane z tabeli zdarzeń |
> | Akcja | Microsoft. Insights/Logs/ExchangeAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | Akcja | Microsoft. Insights/dzienniki/puls/odczyt | Odczytaj dane z tabeli pulsu |
> | Akcja | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/InboundConnection/Read | Odczytaj dane z tabeli InboundConnection |
> | Akcja | Microsoft. Insights/Logs/KubeNodeInventory/Read | Odczytaj dane z tabeli KubeNodeInventory |
> | Akcja | Microsoft. Insights/Logs/KubePodInventory/Read | Odczytaj dane z tabeli KubePodInventory |
> | Akcja | Microsoft.Insights/Logs/LinuxAuditLog/Read | Odczytaj dane z tabeli LinuxAuditLog |
> | Akcja | Microsoft. Insights/Logs/MAApplication/Read | Odczytaj dane z tabeli MAApplication |
> | Akcja | Microsoft.Insights/Logs/MAApplicationHealth/Read | Odczytaj dane z tabeli MAApplicationHealth |
> | Akcja | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | Akcja | Microsoft. Insights/Logs/MAApplicationHealthIssues/Read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | Akcja | Microsoft. Insights/Logs/MAApplicationInstance/Read | Odczytaj dane z tabeli MAApplicationInstance |
> | Akcja | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | Akcja | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Odczytaj dane z tabeli MAApplicationReadiness |
> | Akcja | Microsoft.Insights/Logs/MADeploymentPlan/Read | Odczytaj dane z tabeli MADeploymentPlan |
> | Akcja | Microsoft.Insights/Logs/MADevice/Read | Odczytaj dane z tabeli MADevice |
> | Akcja | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Odczytaj dane z tabeli MADevicePnPHealth |
> | Akcja | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | Akcja | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | Akcja | Microsoft.Insights/Logs/MADeviceReadiness/Read | Odczytaj dane z tabeli MADeviceReadiness |
> | Akcja | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | Akcja | Microsoft.Insights/Logs/MADriverReadiness/Read | Odczytaj dane z tabeli MADriverReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddin/Read | Odczytaj dane z tabeli MAOfficeAddin |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddinHealth/Read | Odczytaj dane z tabeli MAOfficeAddinHealth |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddinHealthIssues/Read | Odczytaj dane z tabeli MAOfficeAddinHealthIssues |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddinInstance/Read | Odczytaj dane z tabeli MAOfficeAddinInstance |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinInstanceReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAddinReadiness/Read | Odczytaj dane z tabeli MAOfficeAddinReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeApp/Read | Odczytaj dane z tabeli MAOfficeApp |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Odczytaj dane z tabeli MAOfficeAppHealth |
> | Akcja | Microsoft. Insights/Logs/MAOfficeAppInstance/Read | Odczytaj dane z tabeli MAOfficeAppInstance |
> | Akcja | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Odczytaj dane z tabeli MAOfficeAppReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeBuildInfo/Read | Odczytaj dane z tabeli MAOfficeBuildInfo |
> | Akcja | Microsoft. Insights/Logs/MAOfficeCurrencyAssessment/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessment |
> | Akcja | Microsoft. Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAOfficeCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroHealth/Read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroHealthIssues/Read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroIssueReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | Akcja | Microsoft. Insights/Logs/MAOfficeMacroSummary/Read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | Akcja | Microsoft.Insights/Logs/MAOfficeSuite/Read | Odczytaj dane z tabeli MAOfficeSuite |
> | Akcja | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | Akcja | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | Akcja | Microsoft. Insights/Logs/MAWindowsBuildInfo/Read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | Akcja | Microsoft. Insights/Logs/MAWindowsCurrencyAssessment/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | Akcja | Microsoft. Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | Akcja | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | Akcja | Microsoft.Insights/Logs/NetworkMonitoring/Read | Odczytaj dane z tabeli NetworkMonitoring |
> | Akcja | Microsoft.Insights/Logs/OfficeActivity/Read | Odczytaj dane z tabeli pakietu Office |
> | Akcja | Microsoft. Insights/Logs/dzienników/operacji/odczytu | Odczytaj dane z tabeli operacji |
> | Akcja | Microsoft. Insights/Logs/OutboundConnection/Read | Odczytaj dane z tabeli OutboundConnection |
> | Akcja | Microsoft. Insights/Logs/perf/Read | Odczytaj dane z tabeli wydajności |
> | Akcja | Microsoft.Insights/Logs/ProtectionStatus/Read | Odczytaj dane z tabeli ProtectionStatus |
> | Akcja | Microsoft. Insights/Logs/odczyt | Odczytywanie danych ze wszystkich dzienników |
> | Akcja | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | Akcja | Microsoft.Insights/Logs/ReservedCommonFields/Read | Odczytaj dane z tabeli ReservedCommonFields |
> | Akcja | Microsoft. Insights/Logs/SCCMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SCOMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SecurityAlert/Read | Odczytaj dane z tabeli SecurityAlert |
> | Akcja | Microsoft. Insights/Logs/SecurityBaseline/Read | Odczytaj dane z tabeli SecurityBaseline |
> | Akcja | Microsoft. Insights/Logs/SecurityBaselineSummary/Read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | Akcja | Microsoft.Insights/Logs/SecurityDetection/Read | Odczytaj dane z tabeli SecurityDetection |
> | Akcja | Microsoft. Insights/Logs/SecurityEvent/Read | Odczytaj dane z tabeli SecurityEvent |
> | Akcja | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | Akcja | Microsoft. Insights/Logs/ServiceFabricReliableActorEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | Akcja | Microsoft. Insights/Logs/ServiceFabricReliableServiceEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | Akcja | Microsoft. Insights/Logs/SfBAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SPAssessmentRecommendation/Read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | Akcja | Microsoft. Insights/Logs/SQLQueryPerformance/Read | Odczytaj dane z tabeli SQLQueryPerformance |
> | Akcja | Microsoft.Insights/Logs/Syslog/Read | Odczytaj dane z tabeli dziennika systemowego |
> | Akcja | Microsoft. Insights/Logs/SysmonEvent/Read | Odczytaj dane z tabeli SysmonEvent |
> | Akcja | Microsoft. Insights/dzienniki/tabele. Custom/Read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | Akcja | Microsoft.Insights/Logs/UAApp/Read | Odczytaj dane z tabeli UAApp |
> | Akcja | Microsoft. Insights/Logs/UAComputer/Read | Odczytaj dane z tabeli UAComputer |
> | Akcja | Microsoft. Insights/Logs/UAComputerRank/Read | Odczytaj dane z tabeli UAComputerRank |
> | Akcja | Microsoft.Insights/Logs/UADriver/Read | Odczytaj dane z tabeli UADriver |
> | Akcja | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Odczytaj dane z tabeli UADriverProblemCodes |
> | Akcja | Microsoft. Insights/Logs/UAFeedback/Read | Odczytaj dane z tabeli UAFeedback |
> | Akcja | Microsoft. Insights/Logs/UAHardwareSecurity/Read | Odczytaj dane z tabeli UAHardwareSecurity |
> | Akcja | Microsoft. Insights/Logs/UAIESiteDiscovery/Read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | Akcja | Microsoft. Insights/Logs/UAOfficeAddIn/Read | Odczytaj dane z tabeli UAOfficeAddIn |
> | Akcja | Microsoft. Insights/Logs/UAProposedActionPlan/Read | Odczytaj dane z tabeli UAProposedActionPlan |
> | Akcja | Microsoft. Insights/Logs/UASysReqIssue/Read | Odczytaj dane z tabeli UASysReqIssue |
> | Akcja | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Odczytaj dane z tabeli UAUpgradedComputer |
> | Akcja | Microsoft. Insights/Logs/Update/Read | Odczytaj dane z tabeli aktualizacji |
> | Akcja | Microsoft.Insights/Logs/UpdateRunProgress/Read | Odczytaj dane z tabeli UpdateRunProgress |
> | Akcja | Microsoft. Insights/Logs/UpdateSummary/Read | Odczytaj dane z tabeli UpdateSummary |
> | Akcja | Microsoft. Insights/Logs/dzienniki/użycie/odczyt | Odczytywanie danych z tabeli użycia |
> | Akcja | Microsoft. Insights/Logs/W3CIISLog/Read | Odczytaj dane z tabeli W3CIISLog |
> | Akcja | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | Akcja | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | Akcja | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | Akcja | Microsoft. Insights/Logs/WDAVStatus/Read | Odczytaj dane z tabeli WDAVStatus |
> | Akcja | Microsoft. Insights/Logs/WDAVThreat/Read | Odczytaj dane z tabeli WDAVThreat |
> | Akcja | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/WindowsFirewall/Read | Odczytaj dane z tabeli WindowsFirewall |
> | Akcja | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | Akcja | Microsoft.Insights/Logs/WireData/Read | Odczytaj dane z tabeli typowe |
> | Akcja | Microsoft. Insights/Logs/WUDOAggregatedStatus/Read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | Akcja | Microsoft. Insights/Logs/WUDOStatus/Read | Odczytaj dane z tabeli WUDOStatus |
> | Akcja | Microsoft.Insights/MetricAlerts/Delete | Usuwanie alertu metryki |
> | Akcja | Microsoft.Insights/MetricAlerts/Read | Odczytaj alert dotyczący metryki |
> | Akcja | Microsoft. Insights/MetricAlerts/status/Read | Odczytaj stan alertu metryki |
> | Akcja | Microsoft. Insights/MetricAlerts/Write | Utwórz lub zaktualizuj alert dotyczący metryki |
> | Akcja | Microsoft. Insights/MetricBaselines/Read | Odczytaj linie bazowe metryki |
> | Akcja | Microsoft. Insights/MetricDefinitions/Microsoft. Insights/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft. Insights/MetricDefinitions/Providers/Microsoft. Insights/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft.Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Akcja | Microsoft. Insights/Metricnamespaces/Read | Odczytaj przestrzenie nazw metryk |
> | Akcja | Microsoft. Insights/Metrics/Action | Akcja metryki |
> | Akcja | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Odczytaj metryki |
> | Akcja | Microsoft. Insights/Metrics/Providers/Metrics/Read | Odczytaj metryki |
> | Akcja | Microsoft.Insights/Metrics/Read | Odczytaj metryki |
> | Akcja dataaction | Microsoft. Insights/Metrics/Write | Metryki zapisu |
> | Akcja | Microsoft. Insights/MigrateToNewpricingModel/Action | Migruj subskrypcję do nowego modelu cen |
> | Akcja | Microsoft. Insights/Operations/Read | Operacje odczytu |
> | Akcja | Microsoft.Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Akcja | Microsoft. Insights/RollbackToLegacyPricingModel/Action | Wycofywanie subskrypcji do starszego modelu cen |
> | Akcja | Microsoft.Insights/ScheduledQueryRules/Delete | Usuwanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft. Insights/ScheduledQueryRules/Read | Odczytywanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft. Insights/ScheduledQueryRules/Write | Pisanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft. Insights/dzierżawcy/rejestr/akcja | Inicjuje dostawcę usługi Microsoft Insights |
> | Akcja | Microsoft. Insights/Unregister/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Akcja | Microsoft. Insights/webtests/Delete | Usuwanie konfiguracji WebTest |
> | Akcja | Microsoft. Insights/webtests/GetToken/Read | Odczytywanie tokenu WebTest |
> | Akcja | Microsoft.Insights/Webtests/MetricDefinitions/Read | Odczytywanie definicji metryk WebTest |
> | Akcja | Microsoft.Insights/Webtests/Metrics/Read | Odczytywanie metryk WebTest |
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

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.IoTCentral/appTemplates/action | Pobiera wszystkie dostępne szablony aplikacji na platformie Azure IoT Central |
> | Akcja | Microsoft. IoTCentral/checkNameAvailability/Action | Sprawdza, czy nazwa aplikacji IoT Central jest dostępna |
> | Akcja | Microsoft.IoTCentral/checkSubdomainAvailability/action | Sprawdza, czy IoT Central poddomena aplikacji jest dostępna |
> | Akcja | Microsoft.IoTCentral/IoTApps/delete | Usuwa IoT Central aplikacje |
> | Akcja | Microsoft.IoTCentral/IoTApps/read | Pobiera pojedynczą aplikację IoT Central |
> | Akcja | Microsoft.IoTCentral/IoTApps/write | Tworzy lub aktualizuje aplikacje IoT Central |
> | Akcja | Microsoft.IoTCentral/operations/read | Pobiera wszystkie dostępne operacje dotyczące IoT Central aplikacji |
> | Akcja | Microsoft. IoTCentral/Register/Action | Zarejestruj subskrypcję dostawcy zasobów usługi Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.IoTSpaces/Graph/delete | Usuwa zasób programu Microsoft. IoTSpaces Graph |
> | Akcja | Microsoft.IoTSpaces/Graph/read | Pobiera zasoby programu Microsoft. IoTSpaces Graph |
> | Akcja | Microsoft.IoTSpaces/Graph/write | Utwórz zasób programu Microsoft. IoTSpaces Graph |
> | Akcja | Microsoft. IoTSpaces/Register/Action | Zarejestruj subskrypcję dostawcy zasobów Microsoft. IoTSpaces Graph, aby umożliwić tworzenie zasobów |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.KeyVault/checkNameAvailability/read | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i czy nie jest używana |
> | Akcja | Microsoft.KeyVault/deletedVaults/read | Wyświetl właściwości usuniętych nietrwałych magazynów kluczy |
> | Akcja | Microsoft.KeyVault/hsmPools/delete | Usuwanie puli modułów HSM |
> | Akcja | Microsoft.KeyVault/hsmPools/joinVault/action | Dołącz Magazyn kluczy do puli modułów HSM |
> | Akcja | Microsoft.KeyVault/hsmPools/read | Wyświetlanie właściwości puli modułów HSM |
> | Akcja | Microsoft.KeyVault/hsmPools/write | Utwórz nową pulę modułów HSM, aby zaktualizować właściwości istniejącej puli modułów HSM |
> | Akcja | Microsoft.KeyVault/locations/deletedVaults/purge/action | Przeczyść usunięty nietrwale Magazyn kluczy |
> | Akcja | Microsoft.KeyVault/locations/deletedVaults/read | Wyświetl właściwości usuniętego nietrwałego magazynu kluczy |
> | Akcja | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft. kluczy, że sieć wirtualna lub podsieć jest usuwana |
> | Akcja | Microsoft.KeyVault/locations/operationResults/read | Sprawdź wynik długotrwałej operacji uruchamiania |
> | Akcja | Microsoft.KeyVault/operations/read | Wyświetla listę operacji dostępnych w ramach dostawcy zasobów Microsoft. |
> | Akcja | Microsoft.KeyVault/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.KeyVault/unregister/action | Wyrejestrowuje subskrypcję |
> | Akcja | Microsoft.KeyVault/vaults/accessPolicies/write | Aktualizowanie istniejących zasad dostępu przez scalanie lub zastępowanie lub Dodawanie nowych zasad dostępu do magazynu. |
> | Akcja | Microsoft.KeyVault/vaults/delete | Usuń magazyn kluczy |
> | Akcja | Microsoft.KeyVault/vaults/deploy/action | Zapewnia dostęp do wpisów tajnych w magazynie kluczy podczas wdrażania zasobów platformy Azure |
> | Akcja | Microsoft.KeyVault/vaults/eventGridFilters/delete | Powiadamia Microsoft. EventGrid, że jest usuwana subskrypcja usługi dla Key Vault |
> | Akcja | Microsoft.KeyVault/vaults/eventGridFilters/read | Powiadamia Microsoft. EventGrid, że jest wyświetlana subskrypcja usługi dla Key Vault |
> | Akcja | Microsoft.KeyVault/vaults/eventGridFilters/write | Powiadamia Microsoft. kluczy, że jest tworzona nowa subskrypcja EventGrid dla Key Vault |
> | Akcja | Microsoft.KeyVault/vaults/read | Wyświetlanie właściwości magazynu kluczy |
> | Akcja | Microsoft.KeyVault/vaults/secrets/read | Wyświetl właściwości wpisu tajnego, ale nie jego wartość. |
> | Akcja | Microsoft.KeyVault/vaults/secrets/write | Utwórz nowy wpis tajny lub zaktualizuj wartość istniejącego klucza tajnego. |
> | Akcja | Microsoft.KeyVault/vaults/write | Utwórz nowy magazyn kluczy lub zaktualizuj właściwości istniejącego magazynu kluczy |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Kusto/klastry/aktywacja/akcja | Uruchamia klaster. |
> | Akcja | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Usuwa dołączoną resourceCopy konfiguracji bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Odczytuje załączoną konfigurację bazy danych resourceCopy. |
> | Akcja | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Zapisuje dołączoną resourceCopy konfiguracji bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Sprawdza dostępność nazwy klastra. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/addprincipals/Action | Dodaje podmioty zabezpieczeń bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Sprawdza dostępność nazwy dla danego typu. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Usuwa resourceCopy połączeń danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Odczytuje resourceCopy połączeń danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Zapisuje resourceCopy połączeń danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Sprawdza poprawność połączenia danych bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/delete | Usuwa resourceCopy bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/EventHubConnections/usuwanie | Usuwa resourceCopy połączeń centrum zdarzeń. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/EventHubConnections/odczyt | Odczytuje resourceCopy połączeń centrum zdarzeń. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/EventHubConnections/zapis | Zapisuje resourceCopy połączeń centrum zdarzeń. |
> | Akcja | Microsoft. Kusto/klastry/bazy danych/EventHubConnectionValidation/akcja | Sprawdza poprawność połączenia centrum zdarzeń bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Wyświetla listę podmiotów zabezpieczeń bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/read | Odczytuje resourceCopy bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Usuwa podmioty zabezpieczeń bazy danych. |
> | Akcja | Microsoft.Kusto/Clusters/Databases/write | Zapisuje resourceCopy bazy danych. |
> | Akcja | Microsoft. Kusto/klastry/dezaktywowanie/akcja | Zamyka klaster. |
> | Akcja | Microsoft.Kusto/Clusters/delete | Usuwa klaster resourceCopy. |
> | Akcja | Microsoft.Kusto/Clusters/read | Odczytuje resourceCopy klastra. |
> | Akcja | Microsoft.Kusto/Clusters/SKUs/read | Odczytuje jednostkę SKU klastra resourceCopy. |
> | Akcja | Microsoft. Kusto/klastry/uruchamianie/akcja | Uruchamia klaster. |
> | Akcja | Microsoft. Kusto/klastry/zatrzymanie/akcja | Zamyka klaster. |
> | Akcja | Microsoft.Kusto/Clusters/write | Zapisuje resourceCopy klastra. |
> | Akcja | Microsoft. Kusto/DetachFollowerDatabases/Action | Odłącza bazy danych programu. |
> | Akcja | Microsoft. Kusto/ListFollowerDatabases/Action | Wyświetla listę baz danych programu. |
> | Akcja | Microsoft.Kusto/Locations/CheckNameAvailability/action | Sprawdza dostępność nazwy resourceCopy. |
> | Akcja | Microsoft.Kusto/locations/operationresults/read | Odczytuje operacje resourceCopys |
> | Akcja | Microsoft.Kusto/Operations/read | Odczytuje operacje resourceCopys |
> | Akcja | Microsoft. Kusto/Register/Action | Akcja rejestracji subskrypcji |
> | Akcja | Microsoft.Kusto/Register/action | Rejestruje subskrypcję u dostawcy zasobów Kusto. |
> | Akcja | Microsoft.Kusto/SKUs/read | Odczytuje resourceCopy jednostki SKU. |
> | Akcja | Microsoft.Kusto/Unregister/action | Wyrejestrowuje subskrypcję do dostawcy zasobów Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.LabServices/labAccounts/CreateLab/action | Utwórz laboratorium na koncie laboratorium. |
> | Akcja | Microsoft. LabServices/labAccounts/Delete | Usuń konta laboratorium. |
> | Akcja | Microsoft.LabServices/labAccounts/galleryImages/delete | Usuń obrazy galerii. |
> | Akcja | Microsoft.LabServices/labAccounts/galleryImages/read | Odczytuj obrazy galerii. |
> | Akcja | Microsoft.LabServices/labAccounts/galleryImages/write | Dodawanie lub modyfikowanie obrazów galerii. |
> | Akcja | Microsoft. LabServices/labAccounts/GetRegionalAvailability/akcja | Uzyskaj informacje o dostępności regionalnej dla każdej kategorii rozmiaru skonfigurowanej w ramach konta laboratorium |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/ADDUSERS/Action | Dodawanie użytkowników do laboratorium |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/usuwanie | Usuwanie laboratoriów. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Usuń ustawienie środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Usuń środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Odczytuj środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Resetuje hasło użytkownika w środowisku |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Uruchamia środowisko przez uruchomienie wszystkich zasobów w środowisku. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Dodaj lub zmodyfikuj środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Rezerwy/rezerwowe zasoby wymagane dla ustawienia środowiska na podstawie bieżącego stanu ustawienia laboratorium/środowisko. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Odczytaj ustawienie środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Resetuje hasło na maszynie wirtualnej szablonu. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Zapisuje bieżący obraz szablonu do udostępnionej galerii na koncie laboratorium |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Usuwanie harmonogramów. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Odczytaj harmonogramy. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Dodawanie lub modyfikowanie harmonogramów. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Uruchamia szablon przez uruchomienie wszystkich zasobów w szablonie. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Zatrzymuje szablon przez zatrzymanie wszystkich zasobów w szablonie. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Dodaj lub zmodyfikuj ustawienie środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/read | Odczytywanie laboratoriów. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/SendEmail/Action | Wyślij wiadomość e-mail z linkiem rejestracji do laboratorium |
> | Akcja | Microsoft.LabServices/labAccounts/labs/users/delete | Usuń użytkowników. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/users/read | Odczytuj użytkowników. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/użytkownicy/zapis | Dodawanie lub modyfikowanie użytkowników. |
> | Akcja | Microsoft. LabServices/labAccounts/Labs/zapis | Dodawanie lub modyfikowanie laboratoriów. |
> | Akcja | Microsoft.LabServices/labAccounts/read | Odczytuj konta laboratorium. |
> | Akcja | Microsoft. LabServices/labAccounts/sharedGalleries/Delete | Usuń sharedgalleries. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedGalleries/read | Odczytaj sharedgalleries. |
> | Akcja | Microsoft. LabServices/labAccounts/sharedGalleries/Write | Dodaj lub zmodyfikuj sharedgalleries. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedImages/delete | Usuń Sharedimages. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedImages/read | Odczytaj Sharedimages. |
> | Akcja | Microsoft.LabServices/labAccounts/sharedImages/write | Dodaj lub zmodyfikuj Sharedimages. |
> | Akcja | Microsoft. LabServices/labAccounts/Write | Dodawanie lub modyfikowanie kont laboratorium. |
> | Akcja | Microsoft.LabServices/locations/operations/read | Operacje odczytu. |
> | Akcja | Microsoft. LabServices/Register/Action | Rejestruje subskrypcję |
> | Akcja | Microsoft.LabServices/users/GetOperationBatchStatus/action | Pobierz stan operacji wsadowej |
> | Akcja | Microsoft.LabServices/users/GetOperationStatus/action | Pobiera stan długotrwałej operacji |
> | Akcja | Microsoft.LabServices/users/GetPersonalPreferences/action | Uzyskaj osobiste preferencje dla użytkownika |
> | Akcja | Microsoft. LabServices/users/ListAllEnvironments/akcja | Wyświetl listę wszystkich środowisk dla użytkownika |
> | Akcja | Microsoft. LabServices/users/rejestr/akcja | Rejestrowanie użytkownika w zarządzanym laboratorium |
> | Akcja | Microsoft.LabServices/users/ResetPassword/action | Resetuje hasło użytkownika w środowisku |
> | Akcja | Microsoft. LabServices/users/StartEnvironment/akcja | Uruchamia środowisko przez uruchomienie wszystkich zasobów w środowisku. |
> | Akcja | Microsoft. LabServices/users/StopEnvironment/akcja | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |

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
> | Akcja | Microsoft. Logic/integrationAccounts/batchConfigurations/Delete | Usuwa konfigurację partii na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Odczytuje konfigurację partii na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Tworzy lub aktualizuje konfigurację partii na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Certificates/Delete | Usuwa certyfikat na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/certificates/read | Odczytuje certyfikat na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Certificates/Write | Tworzy lub aktualizuje certyfikat na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Delete | Usuwa konto integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Join/Action | Umożliwia dołączenie do konta integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla konta integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Pobiera klucze w magazynie kluczy. |
> | Akcja | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Rejestruje zdarzenia śledzenia na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/maps/delete | Usuwa mapowanie na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości mapy na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/maps/read | Odczytuje mapę na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/maps/write | Tworzy lub aktualizuje mapę na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/partners/delete | Usuwa partnera na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości partnera na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/partners/read | Odczytuje partnera na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/partners/write | Tworzy lub aktualizuje partnera na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Odczytuje definicje dzienników konta integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/read | Odczytuje konto integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/regenerateAccessKey/Action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft. Logic/integrationAccounts/schematy/usuwanie | Usuwa schemat na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla zawartości schematu na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/schemas/read | Odczytuje schemat na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/schematy/zapis | Tworzy lub aktualizuje schemat na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/sessions/delete | Usuwa sesję na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/sessions/read | Odczytuje konfigurację partii na koncie integracji. |
> | Akcja | Microsoft.Logic/integrationAccounts/sessions/write | Tworzy lub aktualizuje sesję na koncie integracji. |
> | Akcja | Microsoft. Logic/integrationAccounts/Write | Tworzy lub aktualizuje konto integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/delete | Usuwa środowisko usługi integracji. |
> | Akcja | Microsoft. Logic/integrationServiceEnvironments/Join/Action | Sprzęga środowisko usługi integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Odczytuje operację zarządzanego interfejsu API środowiska usługi integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Odczytuje zarządzany interfejs API środowiska usługi integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Odczytuje stan operacji środowiska usługi integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje definicje metryk środowiska usługi integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/read | Odczytuje środowisko usługi integracji. |
> | Akcja | Microsoft.Logic/integrationServiceEnvironments/write | Tworzy lub aktualizuje środowisko usługi integracji. |
> | Akcja | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Pobiera grupy operacji zalecane dla przepływu pracy. |
> | Akcja | Microsoft.Logic/locations/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | Akcja | Microsoft.Logic/operations/read | Pobiera operację. |
> | Akcja | Microsoft. Logic/Register/Action | Rejestruje dostawcę zasobów Microsoft. Logic dla danej subskrypcji. |
> | Akcja | Microsoft. Logic/Workflows/accessKeys/Delete | Usuwa klucz dostępu. |
> | Akcja | Microsoft. Logic/Workflows/accessKeys/list/Action | Wyświetla wpisy tajne klucza dostępu. |
> | Akcja | Microsoft.Logic/workflows/accessKeys/read | Odczytuje klucz dostępu. |
> | Akcja | Microsoft. Logic/Workflows/accessKeys/Regenerate/Action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft. Logic/Workflows/accessKeys/Write | Tworzy lub aktualizuje klucz dostępu. |
> | Akcja | Microsoft. Logic/przepływy pracy/usuwanie | Usuwa przepływ pracy. |
> | Akcja | Microsoft. Logic/Workflows/Disable/Action | Wyłącza przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/enable/action | Włącza przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/listSwagger/action | Pobiera definicje struktury Swagger przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/przenoszenie/akcja | Przenosi przepływ pracy z istniejącego identyfikatora subskrypcji, grupy zasobów i/lub nazwy do innego identyfikatora subskrypcji, grupy zasobów i/lub nazwy. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Odczytuje ustawienia diagnostyki przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyki przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Odczytuje definicje dziennika przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/metricDefinitions/Read | Odczytuje definicje metryk przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/read | Odczytuje przepływ pracy. |
> | Akcja | Microsoft. logiki/przepływy pracy/regenerateAccessKey/akcja | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft. Logic/Workflows/Run/Action | Uruchamia przebieg przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Pobiera ślady wyrażenia akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/read | Odczytuje akcję przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Pobiera ślady wyrażenia powtórzenia akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/repetitions/read | Odczytuje powtórzenie akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia/requestHistories/odczyt | Odczytuje historię żądań akcji powtórzenia przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/uruchomienia/Actions/requestHistories/Read | Odczytuje historię żądań akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/Workflows/uruchomienia/Actions/scoperepetitions/Read | Odczytuje powtórzenie zakresu akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft. Logic/przepływy pracy/uruchomienia/Anuluj/akcja | Anuluje uruchomienie przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/delete | Usuwa przebieg przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/operations/read | Odczytuje stan operacji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/read | Odczytuje przebieg przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/suspend/action | Wstrzymuje przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/triggers/histories/read | Odczytuje historie wyzwalacza. |
> | Akcja | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Przesyła ponownie wyzwalacz przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Akcja | Microsoft.Logic/workflows/triggers/read | Odczytuje wyzwalacz. |
> | Akcja | Microsoft.Logic/workflows/triggers/reset/action | Resetuje wyzwalacz. |
> | Akcja | Microsoft. Logic/Workflows/Triggers/Run/Action | Wykonuje wyzwalacz. |
> | Akcja | Microsoft.Logic/workflows/triggers/setState/action | Ustawia stan wyzwalacza. |
> | Akcja | Microsoft.Logic/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/versions/read | Odczytuje wersję przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | Akcja | Microsoft.Logic/workflows/write | Tworzy lub aktualizuje przepływ pracy. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

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
> | Akcja | Microsoft.MachineLearning/skus/read | Pobierz jednostki SKU planu zobowiązania Machine Learning |
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
> | Akcja | Microsoft.MachineLearningServices/locations/usages/read | Raport użycia dla zasobów obliczeniowych AML w ramach subskrypcji |
> | Akcja | Microsoft.MachineLearningServices/locations/vmsizes/read | Pobierz obsługiwane rozmiary maszyn wirtualnych |
> | Akcja | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Pobiera stan określonej operacji obszaru roboczego |
> | Akcja | Microsoft. MachineLearningServices/Register/Action | Rejestruje subskrypcję dla dostawcy zasobów Machine Learning Services |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/obliczenia/usuwanie | Usuwa zasoby obliczeniowe w Machine Learning Services obszarach roboczych |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/obliczenia/listKeys/akcja | Wyświetlanie wpisów tajnych dla zasobów obliczeniowych w obszarze roboczym Machine Learning Services |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/obliczenia/listNodes/akcja | Wyświetlanie listy węzłów dla zasobu obliczeniowego w obszarze roboczym Machine Learning Services |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/read | Pobiera zasoby obliczeniowe w Machine Learning Services obszarach roboczych |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/obliczenia/zapis | Tworzy lub aktualizuje zasoby obliczeniowe w obszarze roboczym Machine Learning Services |
> | Akcja | Microsoft.MachineLearningServices/workspaces/delete | Usuwa obszary robocze Machine Learning Services |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/usuwanie | Usuwa eksperymenty w Machine Learning Services obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/odczyt | Pobiera eksperymenty w Machine Learning Servicesych obszarach roboczych |
> | Akcja dataaction | Microsoft. MachineLearningServices/obszary robocze/eksperymenty/zapis | Tworzy lub aktualizuje eksperymenty w Machine Learning Services obszarach roboczych |
> | Akcja | Microsoft. MachineLearningServices/obszary robocze/listKeys/akcja | Wyświetlanie wpisów tajnych dla obszaru roboczego Machine Learning Services |
> | Akcja | Microsoft.MachineLearningServices/workspaces/read | Pobiera Machine Learning Services obszary robocze |
> | Akcja | Microsoft.MachineLearningServices/workspaces/write | Tworzy lub aktualizuje Machine Learning Services obszary robocze |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ManagedIdentity/tożsamości/odczyt | Pobiera istniejącą tożsamość przypisaną do systemu |
> | Akcja | Microsoft.ManagedIdentity/register/action | Rejestruje subskrypcję dostawcy zasobów tożsamości zarządzanej |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Akcja RBAC do przypisywania istniejącej przypisanej użytkownikowi tożsamości do zasobu |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Usuwa istniejącą tożsamość przypisanego użytkownika |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/read | Pobiera istniejącą tożsamość przypisanego użytkownika |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/write | Tworzy nową tożsamość przypisanego użytkownika lub aktualizuje Tagi skojarzone z istniejącą tożsamością przypisanego użytkownika |

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

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Management/checkNameAvailability/akcja | Sprawdza, czy określona nazwa grupy zarządzania jest prawidłowa i unikatowa. |
> | Akcja | Microsoft. Management/getentities/akcja | Wyświetl listę wszystkich jednostek (Grupy zarządzania, subskrypcje itp.) dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft. Management/managementGroups/Delete | Usuń grupę zarządzania. |
> | Akcja | Microsoft.Management/managementGroups/read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | Akcja | Microsoft. Management/managementGroups/subskrypcje/usuwanie | Anuluj skojarzenie subskrypcji z grupą zarządzania. |
> | Akcja | Microsoft. Management/managementGroups/subskrypcje/zapis | Kojarzy istniejącą subskrypcję z grupą zarządzania. |
> | Akcja | Microsoft. Management/managementGroups/Write | Utwórz lub Zaktualizuj grupę zarządzania. |
> | Akcja | Microsoft. Management/Register/Action | Zarejestruj określoną subskrypcję w usłudze Microsoft. Management |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja dataaction | Microsoft.Maps/accounts/data/read | Przyznaje dostęp do odczytu do konta Maps. |
> | Akcja | Microsoft. Maps/accounts/Delete | Usuń konto Maps. |
> | Akcja | Microsoft.Maps/accounts/eventGridFilters/delete | Usuń filtr Event Grid |
> | Akcja | Microsoft.Maps/accounts/eventGridFilters/read | Pobierz filtr Event Grid |
> | Akcja | Microsoft.Maps/accounts/eventGridFilters/write | Utwórz lub zaktualizuj filtr Event Grid |
> | Akcja | Microsoft.Maps/accounts/listKeys/action | Lista kluczy konta map |
> | Akcja | Microsoft.Maps/accounts/read | Pobierz konto Maps. |
> | Akcja | Microsoft.Maps/accounts/regenerateKey/action | Generuj nowy klucz podstawowy lub pomocniczy konta mapy |
> | Akcja | Microsoft. Maps/accounts/Write | Utwórz lub zaktualizuj konto Maps. |
> | Akcja | Microsoft.Maps/operations/read | Odczytaj operacje dostawcy |
> | Akcja | Microsoft. Maps/Zarejestruj/akcja | Rejestrowanie dostawcy |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Zwraca umowę. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Akceptuje umowę ze znakiem. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importuje obraz do ACR użytkownika końcowego. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Zwraca konfigurację. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Zapisuje konfigurację. |
> | Akcja | Microsoft. Marketplace/rejestr/akcja | Rejestruje dostawcę zasobów Microsoft. Marketplace w ramach subskrypcji. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/delete | Wykonuje operację usuwania dla zasobu klasycznej usługi deweloperskiej. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Pobiera klasyczne klucze zarządzania zasobami usług deweloperskich. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Pobiera adres URL logowania jednokrotnego dla klasycznej usługi deweloperskiej. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/read | Wykonuje operację pobierania na klasycznej usłudze deweloperskiej. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generuje klucze zarządzania zasobami klasycznej usługi deweloperskiej. |
> | Akcja | Microsoft.MarketplaceApps/Operations/read | Przeczytaj operacje dla wszystkich typów zasobów. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. MarketplaceOrdering/umowy/oferty/plany/anulowanie/akcja | Anulowanie umowy dotyczącej danego elementu portalu Marketplace |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Zwróć umowę dla danego elementu portalu Marketplace |
> | Akcja | Microsoft. MarketplaceOrdering/umowy/oferty/plany/Podpisz/akcję | Podpisz umowę dotyczącą danego elementu portalu Marketplace |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/read | Zwróć wszystkie umowy w ramach danej subskrypcji |
> | Akcja | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Pobierz umowę dotyczącą danego elementu maszyny wirtualnej portalu Marketplace |
> | Akcja | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Podpisz lub Anuluj umowę dotyczącą danego elementu maszyny wirtualnej portalu Marketplace |
> | Akcja | Microsoft.MarketplaceOrdering/operations/read | Wyświetl listę wszystkich możliwych operacji w interfejsie API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Media/checknameavailability/Action | Sprawdza, czy nazwa konta Media Services jest dostępna |
> | Akcja | Microsoft.Media/locations/checkNameAvailability/action | Sprawdza, czy nazwa konta Media Services jest dostępna |
> | Akcja | Microsoft. Media/MediaServices/accountfilters/Delete | Usuń filtr konta |
> | Akcja | Microsoft. Media/MediaServices/accountfilters/Read | Odczytaj dowolny filtr konta |
> | Akcja | Microsoft. Media/MediaServices/accountfilters/Write | Utwórz lub zaktualizuj dowolny filtr konta |
> | Akcja | Microsoft. Media/MediaServices/Assets/assetfilters/Delete | Usuń dowolny filtr zasobów |
> | Akcja | Microsoft.Media/mediaservices/assets/assetfilters/read | Odczytaj dowolny filtr zasobów |
> | Akcja | Microsoft.Media/mediaservices/assets/assetfilters/write | Utwórz lub zaktualizuj dowolny filtr zasobów |
> | Akcja | Microsoft. Media/MediaServices/Assets/Delete | Usuń dowolny element zawartości |
> | Akcja | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Pobierz klucz szyfrowania zasobu |
> | Akcja | Microsoft.Media/mediaservices/assets/listContainerSas/action | Wyświetl listę adresów URL SAS kontenera zasobów |
> | Akcja | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Wyświetl listę lokalizatorów przesyłania strumieniowego dla elementu zawartości |
> | Akcja | Microsoft.Media/mediaservices/assets/read | Odczytaj dowolny zasób |
> | Akcja | Microsoft. Media/MediaServices/Assets/Write | Utwórz lub zaktualizuj dowolny element zawartości |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Usuń wszystkie zasady kluczy zawartości |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Pobieranie właściwości zasad przy użyciu wpisów tajnych |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/read | Odczytaj wszystkie zasady dotyczące klucza zawartości |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/write | Tworzenie lub aktualizowanie zasad klucza zawartości |
> | Akcja | Microsoft. Media/MediaServices/Delete | Usuń dowolne konto Media Services |
> | Akcja | Microsoft. Media/MediaServices/eventGridFilters/Delete | Usuń dowolny filtr Event Grid |
> | Akcja | Microsoft.Media/mediaservices/eventGridFilters/read | Odczytaj dowolny filtr Event Grid |
> | Akcja | Microsoft. Media/MediaServices/eventGridFilters/Write | Utwórz lub zaktualizuj dowolny filtr Event Grid |
> | Akcja | Microsoft.Media/mediaservices/liveEventOperations/read | Odczytaj wszystkie operacje na żywo dotyczące zdarzeń |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/Delete | Usuń dowolne wydarzenie na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Delete | Usuń wszystkie dane wyjściowe na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Read | Odczytaj wszystkie dane wyjściowe na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Write | Utwórz lub zaktualizuj wszystkie dane wyjściowe na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/read | Odczytaj dowolne wydarzenie na żywo |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/Reset/Action | Zresetuj wszystkie operacje na żywo dotyczące zdarzeń |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/Start/akcja | Rozpocznij wszystkie operacje na żywo dotyczące zdarzeń |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/akcja | Zatrzymaj wszystkie operacje na żywo dotyczące zdarzeń |
> | Akcja | Microsoft. Media/MediaServices/liveEvents/Write | Utwórz lub zaktualizuj dowolne wydarzenie na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveOutputOperations/read | Odczytaj wszystkie operacje wyjścia na żywo |
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
> | Akcja | Microsoft.Media/mediaservices/transforms/jobs/read | Odczytaj dowolne zadanie |
> | Akcja | Microsoft. Media/MediaServices/transformacje/zadania/zapis | Utwórz lub zaktualizuj dowolne zadanie |
> | Akcja | Microsoft. Media/MediaServices/transformacje/odczyt | Odczytaj dowolne przekształcenia |
> | Akcja | Microsoft. Media/MediaServices/transformacje/zapis | Utwórz lub zaktualizuj dowolne przekształcenie |
> | Akcja | Microsoft. Media/MediaServices/Write | Utwórz lub zaktualizuj dowolne konto Media Services |
> | Akcja | Microsoft.Media/operations/read | Pobierz dostępne operacje |
> | Akcja | Microsoft. Media/Register/Action | Rejestruje subskrypcję dostawcy zasobów Media Services i umożliwia tworzenie kont Media Services |
> | Akcja | Microsoft.Media/unregister/action | Wyrejestrowuje subskrypcję dla dostawcy zasobów Media Services |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/assessmentOptions/odczyt | Pobiera opcje oceny, które są dostępne w danej lokalizacji |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/oceny/odczyt | Wyświetla listę ocen w projekcie |
> | Akcja | Microsoft. Migruj/assessmentprojects/Usuń | Usuwa projekt oceny |
> | Akcja | Microsoft. migruje/assessmentprojects/Groups/Assessments/assessedmachines/Read | Pobierz właściwości ocenianego komputera |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/grupy/oceny/usuwanie | Usuwa ocenę |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/grupy/oceny/downloadurl/akcja | Pobiera adres URL raportu oceny |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Pobiera właściwości oceny |
> | Akcja | Microsoft. Migruj/assessmentprojects/grupy/oceny/zapis | Tworzy nową ocenę lub aktualizuje istniejącą ocenę |
> | Akcja | Microsoft. Migrowanie/assessmentprojects/grupy/usuwanie | Usuwa grupę |
> | Akcja | Microsoft.Migrate/assessmentprojects/groups/read | Pobierz właściwości grupy |
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
> | Akcja | Microsoft.Migrate/locations/assessmentOptions/read | Pobiera opcje oceny, które są dostępne w danej lokalizacji |
> | Akcja | Microsoft. migrowana/Locations/checknameavailability/Action | Sprawdza dostępność nazwy zasobu dla danej subskrypcji w danej lokalizacji |
> | Akcja | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Pobiera właściwości wystąpienia bazy danych |
> | Akcja | Microsoft. migruje/migrateprojects/Databases/Read | Pobiera właściwości bazy danych |
> | Akcja | Microsoft. Migruj/migrateprojects/Usuń | Usuwa projekt migracji |
> | Akcja | Microsoft. migruje/migrateprojects/Machines/Read | Pobiera właściwości maszyny |
> | Akcja | Microsoft. Migrowanie/migrateprojects/MigrateEvents/usuwanie | Usuwa zdarzenie migracji |
> | Akcja | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Pobiera właściwości zdarzenia migracji. |
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
> | Akcja | Microsoft.Migrate/projects/assessments/read | Wyświetla listę ocen w projekcie |
> | Akcja | Microsoft. Migracja/projekty/usuwanie | Usuwa projekt |
> | Akcja | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Pobierz właściwości ocenianego komputera |
> | Akcja | Microsoft. Migrowanie/projekty/grupy/oceny/usuwanie | Usuwa ocenę |
> | Akcja | Microsoft. Migracja/projekty/grupy/oceny/downloadurl/akcja | Pobiera adres URL raportu oceny |
> | Akcja | Microsoft.Migrate/projects/groups/assessments/read | Pobiera właściwości oceny |
> | Akcja | Microsoft. Migrowanie/projekty/grupy/oceny/zapis | Tworzy nową ocenę lub aktualizuje istniejącą ocenę |
> | Akcja | Microsoft. Migracja/projekty/grupy/usuwanie | Usuwa grupę |
> | Akcja | Microsoft.Migrate/projects/groups/read | Pobierz właściwości grupy |
> | Akcja | Microsoft. Migrowanie/projekty/grupy/zapis | Tworzy nową grupę lub aktualizuje istniejącą grupę |
> | Akcja | Microsoft. Migruj/projekty/klucze/akcja | Pobiera klucze wspólne dla projektu |
> | Akcja | Microsoft. Migrowanie/projekty/maszyny/odczyt | Pobiera właściwości maszyny |
> | Akcja | Microsoft. Migracja/projekty/odczyt | Pobiera właściwości projektu |
> | Akcja | Microsoft. Migracja/projekty/zapis | Tworzy nowy projekt lub aktualizuje istniejący projekt |
> | Akcja | Microsoft. Migrowanie/rejestrowanie/akcja | Rejestruje subskrypcję za pomocą programu Microsoft. Migrowanie dostawcy zasobów |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

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

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. NetApp/Locations/checkfilepathavailability/Action | Sprawdź, czy ścieżka pliku jest dostępna |
> | Akcja | Microsoft. NetApp/Locations/checknameavailability/Action | Sprawdź, czy nazwa zasobu jest dostępna |
> | Akcja | Microsoft.NetApp/locations/operationresults/read | Odczytuje zasób wyniku operacji. |
> | Akcja | Microsoft.NetApp/locations/read | Odczytuje zasób sprawdzania dostępności. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Usuwa zasób puli. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/read | Odczytuje zasób puli. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Usuwa zasób woluminu. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Odczytuje zasób docelowy instalacji. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Odczytuje zasób woluminu. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Usuwa zasób migawki. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Odczytuje zasób migawki. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Zapisuje zasób migawki. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Zapisuje zasób woluminu. |
> | Akcja | Microsoft.NetApp/netAppAccounts/capacityPools/write | Zapisuje zasób puli. |
> | Akcja | Microsoft.NetApp/netAppAccounts/delete | Usuwa zasób konta. |
> | Akcja | Microsoft.NetApp/netAppAccounts/read | Odczytuje zasób konta. |
> | Akcja | Microsoft.NetApp/netAppAccounts/write | Zapisuje zasób konta. |
> | Akcja | Microsoft.NetApp/Operations/read | Odczytuje zasoby operacji. |
> | Akcja | Microsoft. NetApp/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. NetApp |
> | Akcja | Microsoft. NetApp/Unregister/Action | Wyrejestrowuje subskrypcję za pomocą dostawcy zasobów Microsoft. NetApp |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Pobierz Application Gateway dostępne nagłówki żądania |
> | Akcja | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Pobierz Application Gateway dostępny nagłówek odpowiedzi |
> | Akcja | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Pobierz Application Gateway dostępne zmienne serwera |
> | Akcja | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway wstępnie zdefiniowanych zasad SSL |
> | Akcja | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway dostępne opcje protokołu SSL |
> | Akcja | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Pobiera Application Gateway dostępne zestawy reguł WAF |
> | Akcja | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Sprzęga pulę adresów zaplecza bramy aplikacji. Brak alertów. |
> | Akcja | Microsoft.Network/applicationGateways/backendhealth/action | Pobiera kondycję zaplecza bramy aplikacji |
> | Akcja | Microsoft. Network/applicationGateways/Delete | Usuwa bramę aplikacji |
> | Akcja | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/akcja | Pobiera kondycję zaplecza bramy aplikacji na żądanie dla danego ustawienia http i puli zaplecza |
> | Akcja | Microsoft.Network/applicationGateways/read | Pobiera bramę aplikacji |
> | Akcja | Microsoft. Network/applicationGateways/Start/akcja | Uruchamia bramę aplikacji |
> | Akcja | Microsoft. Network/applicationGateways/Stop/akcja | Kończy działanie bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/write | Tworzy bramę aplikacji lub aktualizuje bramę aplikacji |
> | Akcja | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Usuwa zasady WAF Application Gateway |
> | Akcja | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Pobiera zasady WAF Application Gateway |
> | Akcja | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Application Gateway tworzy zasady WAFymi lub aktualizuje zasady Application Gateway WAF |
> | Akcja | Microsoft.Network/applicationSecurityGroups/delete | Usuwa grupę zabezpieczeń aplikacji |
> | Akcja | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Przyłącza konfigurację IP do grup zabezpieczeń aplikacji. Brak alertów. |
> | Akcja | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Przyłączanie reguły zabezpieczeń do grup zabezpieczeń aplikacji. Brak alertów. |
> | Akcja | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Wyświetla konfiguracje protokołu IP w ApplicationSecurityGroup |
> | Akcja | Microsoft.Network/applicationSecurityGroups/read | Pobiera identyfikator grupy zabezpieczeń aplikacji. |
> | Akcja | Microsoft.Network/applicationSecurityGroups/write | Tworzy grupę zabezpieczeń aplikacji lub aktualizuje istniejącą grupę zabezpieczeń aplikacji. |
> | Akcja | Microsoft.Network/azureFirewallFqdnTags/read | Pobiera Tagi FQDN zapory platformy Azure |
> | Akcja | Microsoft. Network/azurefirewalls/Delete | Usuwanie zapory platformy Azure |
> | Akcja | Microsoft.Network/azurefirewalls/read | Uzyskiwanie zapory platformy Azure |
> | Akcja | Microsoft. Network/azurefirewalls/Write | Tworzy lub aktualizuje zaporę platformy Azure |
> | Akcja | Microsoft. Network/bastionHosts/Delete | Usuwa hosta bastionu |
> | Akcja | Microsoft.Network/bastionHosts/read | Pobiera hosta bastionu |
> | Akcja | Microsoft. Network/bastionHosts/Write | Tworzenie lub aktualizowanie hosta bastionu |
> | Akcja | Microsoft.Network/bgpServiceCommunities/read | Pobierz społeczności usługi BGP |
> | Akcja | Microsoft.Network/checkFrontDoorNameAvailability/action | Sprawdza, czy nazwa drzwi czołowych jest dostępna |
> | Akcja | Microsoft.Network/checkTrafficManagerNameAvailability/action | Sprawdza dostępność Traffic Manager względnej nazwy DNS. |
> | Akcja | Microsoft. Network/Connections/Delete | Deletes VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Akcja | Microsoft. Network/Connections/odwoływanie/akcja | Oznacza stan połączenia usługi Express Route jako odwołane |
> | Akcja | Microsoft. Network/Connections/SharedKey/Action | Pobierz VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft. Network/Connections/sharedKey/Read | Pobiera VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft. Network/Connections/sharedKey/Write | Tworzy lub aktualizuje istniejące VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Pobiera konfigurację urządzenia sieci VPN VirtualNetworkGatewayConnection |
> | Akcja | Microsoft. Network/Connections/Write | Tworzy lub aktualizuje istniejący VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/ddosCustomPolicies/delete | Usuwa zasady niestandardowe DDoS |
> | Akcja | Microsoft.Network/ddosCustomPolicies/read | Pobiera DDoS dostosowaną definicję zasad |
> | Akcja | Microsoft.Network/ddosCustomPolicies/write | Tworzy dostosowane zasady DDoS lub aktualizuje istniejące dostosowane zasady DDoS |
> | Akcja | Microsoft.Network/ddosProtectionPlans/delete | Usuwa plan DDoS Protection |
> | Akcja | Microsoft.Network/ddosProtectionPlans/join/action | Dołącza do planu DDoS Protection. Brak alertów. |
> | Akcja | Microsoft.Network/ddosProtectionPlans/read | Pobiera plan DDoS Protection |
> | Akcja | Microsoft.Network/ddosProtectionPlans/write | Tworzy plan DDoS Protection lub aktualizuje plan DDoS Protection  |
> | Akcja | Microsoft.Network/dnsoperationresults/read | Pobiera wyniki operacji DNS |
> | Akcja | Microsoft.Network/dnsoperationstatuses/read | Pobiera stan operacji DNS  |
> | Akcja | Microsoft. Network/dnszones/A/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "A" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/A/read | Pobierz zestaw rekordów typu "A" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/A/Write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/AAAA/delete | Usuń zestaw rekordów o danej nazwie i wpisz "AAAA" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/AAAA/read | Pobierz zestaw rekordów typu "AAAA" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/dnszones/AAAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/all/read | Pobiera zestawy rekordów DNS dla różnych typów |
> | Akcja | Microsoft. Network/dnszones/CAA/Delete | Usuń zestaw rekordów o danej nazwie i wpisz "CAA" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/CAA/read | Pobierz zestaw rekordów typu "CAA" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/CAA/Write | Utwórz lub zaktualizuj zestaw rekordów typu "CAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/CNAME/delete | Usuń zestaw rekordów o danej nazwie i wpisz "CNAME" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/CNAME/read | Pobierz zestaw rekordów typu "CNAME" w formacie JSON. Zestaw rekordów zawiera czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/dnszones/CNAME/write | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/delete | Usuń strefę DNS w formacie JSON. Właściwości strefy obejmują Tagi, ETag, numberOfRecordSets i maxNumberOfRecordSets. |
> | Akcja | Microsoft. Network/dnszones/MX/Delete | Usuń zestaw rekordów o danej nazwie i typie "MX" ze strefy DNS. |
> | Akcja | Microsoft. Network/dnszones/MX/odczyt | Pobierz zestaw rekordów typu "MX" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/MX/Write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/NS/delete | Usuwa zestaw rekordów DNS typu NS |
> | Akcja | Microsoft.Network/dnszones/NS/read | Pobiera zestaw rekordów DNS typu NS |
> | Akcja | Microsoft.Network/dnszones/NS/write | Tworzy lub aktualizuje zestaw rekordów DNS typu NS |
> | Akcja | Microsoft. Network/dnszones/PTR/usuwanie | Usuń zestaw rekordów o danej nazwie i typie "PTR" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/PTR/read | Pobierz zestaw rekordów typu "PTR" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft. Network/dnszones/PTR/zapis | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/read | Pobierz strefę DNS w formacie JSON. Właściwości strefy obejmują Tagi, ETag, numberOfRecordSets i maxNumberOfRecordSets. Należy zauważyć, że to polecenie nie pobiera zestawów rekordów zawartych w strefie. |
> | Akcja | Microsoft.Network/dnszones/recordsets/read | Pobiera zestawy rekordów DNS dla różnych typów |
> | Akcja | Microsoft.Network/dnszones/SOA/read | Pobiera zestaw rekordów DNS typu SOA |
> | Akcja | Microsoft.Network/dnszones/SOA/write | Tworzy lub aktualizuje zestaw rekordów DNS typu SOA |
> | Akcja | Microsoft.Network/dnszones/SRV/delete | Usuń zestaw rekordów o danej nazwie i typie "SRV" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/SRV/read | Pobierz zestaw rekordów typu "SRV" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/dnszones/SRV/write | Utwórz lub zaktualizuj zestaw rekordów typu SRV |
> | Akcja | Microsoft.Network/dnszones/TXT/delete | Usuń zestaw rekordów o danej nazwie i typie "TXT" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/TXT/read | Pobierz zestaw rekordów typu "TXT" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/dnszones/TXT/write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/write | Utwórz lub zaktualizuj strefę DNS w grupie zasobów.  Służy do aktualizowania tagów w zasobie strefy DNS. Należy pamiętać, że tego polecenia nie można użyć do tworzenia lub aktualizowania zestawów rekordów w strefie. |
> | Akcja | Microsoft. Network/expressRouteCircuits/autoryzacje/usuwanie | Usuwa autoryzację ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/authorizations/read | Pobiera autoryzację ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/autoryzacje/zapis | Tworzy lub aktualizuje istniejącą autoryzację ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Delete | Usuwa ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Join/Action | Sprzęga obwód trasy Express. Brak alertów. |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/arpTables/Read | Pobiera ExpressRoute komunikacji równorzędnej ArpTable |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Usuwa połączenie ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Pobiera połączenie ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/Connections/Write | Tworzy lub aktualizuje istniejący zasób połączenia usługi ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/delete | Usuwa komunikację równorzędną ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Pobiera połączenie obwodu trasy usługi peer Express |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/read | Pobiera komunikację równorzędną ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/routeTables/Read | Pobiera trasę komunikacji równorzędnej ExpressRoute |
> | Akcja | Microsoft. Network/expressRouteCircuits/Peers/routeTablesSummary/Read | Pobiera podsumowanie tras komunikacji równorzędnej ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Pobiera stat ExpressRoute komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/write | Tworzy lub aktualizuje istniejącą komunikację równorzędną ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/read | Pobierz ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/stats/read | Pobiera ExpressRoute stat |
> | Akcja | Microsoft.Network/expressRouteCircuits/write | Tworzy lub aktualizuje istniejący ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/join/action | Sprzęga połączenie krzyżowe usługi Express Route. Brak alertów. |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Peers/arpTables/Read | Pobiera tabelę ARP komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Usuwa komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/read | Pobiera komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Peers/routeTables/Read | Pobiera tabelę tras komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteCrossConnections/Peers/routeTableSummary/Read | Pobiera podsumowanie tabeli tras komunikacji równorzędnej połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/write | Tworzy komunikację równorzędną połączenia krzyżowego usługi Express Route lub aktualizuje istniejącą komunikację równorzędną połączenia krzyżowego usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/read | Pobierz połączenie krzyżowe usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Usuwa połączenie usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Pobiera połączenie usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Tworzy połączenie usługi Express Route lub aktualizuje istniejące połączenie usługi Express Route |
> | Akcja | Microsoft. Network/expressRouteGateways/Join/Action | Umożliwia dołączenie do bramy Express Route Gateway. Brak alertów. |
> | Akcja | Microsoft.Network/expressRouteGateways/read | Pobierz bramę Express Route Gateway |
> | Akcja | Microsoft. Network/expressRoutePorts/Delete | Usuwa ExpressRoutePorts |
> | Akcja | Microsoft. Network/expressRoutePorts/Join/Action | Sprzęga porty routingu Express. Brak alertów. |
> | Akcja | Microsoft.Network/expressRoutePorts/links/read | Gets ExpressRouteLink |
> | Akcja | Microsoft.Network/expressRoutePorts/read | Gets ExpressRoutePorts |
> | Akcja | Microsoft. Network/expressRoutePorts/Write | Tworzy lub aktualizuje ExpressRoutePorts |
> | Akcja | Microsoft.Network/expressRoutePortsLocations/read | Pobierz lokalizacje portów Express Route |
> | Akcja | Microsoft.Network/expressRouteServiceProviders/read | Pobiera dostawców Route Service ekspresowych |
> | Akcja | Microsoft. Network/firewallPolicies/Delete | Usuwa zasady zapory |
> | Akcja | Microsoft. Network/firewallPolicies/Join/Action | Przyłącza zasady zapory. Brak alertów. |
> | Akcja | Microsoft. Network/firewallPolicies/odczyt | Pobiera zasady zapory |
> | Akcja | Microsoft. Network/firewallPolicies/ruleGroups/Delete | Usuwa grupę reguł zasad zapory |
> | Akcja | Microsoft. Network/firewallPolicies/ruleGroups/odczyt | Pobiera grupę reguł zasad zapory |
> | Akcja | Microsoft. Network/firewallPolicies/ruleGroups/Write | Tworzy grupę reguł zasad zapory lub aktualizuje istniejącą grupę reguł zasad zapory |
> | Akcja | Microsoft. Network/firewallPolicies/Write | Tworzy zasady zapory lub aktualizuje istniejące zasady zapory |
> | Akcja | Microsoft.Network/frontDoors/backendPools/delete | Usuwa pulę zaplecza |
> | Akcja | Microsoft.Network/frontDoors/backendPools/read | Pobiera pulę zaplecza |
> | Akcja | Microsoft.Network/frontDoors/backendPools/write | Tworzy lub aktualizuje pulę zaplecza |
> | Akcja | Microsoft.Network/frontDoors/delete | Usuwa drzwiczki z przodu |
> | Akcja | Microsoft. Network/usługi frontdoor/frontendEndpoints/Delete | Usuwa punkt końcowy frontonu |
> | Akcja | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Wyłącza protokół HTTPS w punkcie końcowym frontonu |
> | Akcja | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Włącza protokół HTTPS w punkcie końcowym frontonu |
> | Akcja | Microsoft.Network/frontDoors/frontendEndpoints/read | Pobiera punkt końcowy frontonu |
> | Akcja | Microsoft.Network/frontDoors/frontendEndpoints/write | Tworzy lub aktualizuje punkt końcowy frontonu |
> | Akcja | Microsoft.Network/frontDoors/healthProbeSettings/delete | Usuwa ustawienia sondy kondycji |
> | Akcja | Microsoft.Network/frontDoors/healthProbeSettings/read | Pobiera ustawienia sondy kondycji |
> | Akcja | Microsoft.Network/frontDoors/healthProbeSettings/write | Tworzy lub aktualizuje ustawienia sondy kondycji |
> | Akcja | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Akcja | Microsoft.Network/frontDoors/loadBalancingSettings/read | Pobiera ustawienia równoważenia obciążenia |
> | Akcja | Microsoft.Network/frontDoors/loadBalancingSettings/write | Tworzy lub aktualizuje ustawienia równoważenia obciążenia |
> | Akcja | Microsoft. Network/usługi frontdoor/przeczyszczanie/akcja | Przeczyszczanie zawartości w pamięci podręcznej z drzwi przednich |
> | Akcja | Microsoft.Network/frontDoors/read | Pobiera tylne drzwi |
> | Akcja | Microsoft.Network/frontDoors/routingRules/delete | Usuwa regułę routingu |
> | Akcja | Microsoft.Network/frontDoors/routingRules/read | Pobiera regułę routingu |
> | Akcja | Microsoft.Network/frontDoors/routingRules/write | Tworzy lub aktualizuje regułę routingu |
> | Akcja | Microsoft.Network/frontDoors/validateCustomDomain/action | Sprawdza poprawność punktu końcowego frontonu dla drzwi z przodu |
> | Akcja | Microsoft.Network/frontDoors/write | Tworzy lub aktualizuje tylne drzwi |
> | Akcja | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Pobiera zestawy reguł zarządzane przez zaporę aplikacji sieci Web |
> | Akcja | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Usuwa zasady zapory aplikacji sieci Web |
> | Akcja | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Pobiera zasady zapory aplikacji sieci Web |
> | Akcja | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Tworzy lub aktualizuje zasady zapory aplikacji sieci Web |
> | Akcja | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | Akcja | Microsoft.Network/loadBalancers/backendAddressPools/read | Pobiera definicję puli adresów zaplecza modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/delete | Usuwa moduł równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Sprzęga konfigurację adresu IP frontonu Load Balancer. Brak alertów. |
> | Akcja | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Pobiera definicję konfiguracji adresu IP frontonu modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Przyłącza do puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia. Brak alertów. |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatPools/read | Pobiera definicję puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/delete | Usuwa regułę NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Przyłącza do przychodzącej reguły NAT modułu równoważenia obciążenia. Brak alertów. |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/read | Pobiera definicję reguły NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/write | Tworzy regułę NAT dla ruchu przychodzącego modułu równoważenia obciążenia lub aktualizuje istniejącą regułę NAT dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/loadBalancingRules/read | Pobiera definicję reguły równoważenia obciążenia modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/networkInterfaces/read | Pobiera odwołania do wszystkich interfejsów sieciowych w ramach modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/outboundRules/read | Pobiera definicję reguły ruchu wychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/probes/join/action | Umożliwia korzystanie z sond modułu równoważenia obciążenia. Na przykład za pomocą tej właściwości uprawnienia healthProbe zestawu skalowania maszyn wirtualnych można odwoływać się do sondy. Brak alertów. |
> | Akcja | Microsoft.Network/loadBalancers/probes/read | Pobiera sondę modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/read | Pobiera definicję modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w ramach modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/write | Tworzy moduł równoważenia obciążenia lub aktualizuje istniejący moduł równoważenia obciążenia |
> | Akcja | Microsoft. Network/localnetworkgateways/Delete | Usuwa LocalNetworkGateway |
> | Akcja | Microsoft.Network/localnetworkgateways/read | Pobiera LocalNetworkGateway |
> | Akcja | Microsoft.Network/localnetworkgateways/write | Tworzy lub aktualizuje istniejący LocalNetworkGateway |
> | Akcja | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Pobiera usługi linków prywatnych z autozatwierdzeniem |
> | Akcja | Microsoft.Network/locations/availableDelegations/read | Pobiera dostępne delegacje |
> | Akcja | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Pobiera dostępne zasoby prywatnego punktu końcowego |
> | Akcja | Microsoft. Network/Locations/availableServiceAliases/Read | Pobiera dostępne aliasy usługi |
> | Akcja | Microsoft. Network/Locations/bareMetalTenants/Action | Przydziela lub sprawdza poprawność dzierżawy systemu operacyjnego |
> | Akcja | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Sprawdza obsługę przyspieszonej sieci |
> | Akcja | Microsoft.Network/locations/checkDnsNameAvailability/read | Sprawdza, czy etykieta DNS jest dostępna w określonej lokalizacji |
> | Akcja | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Sprawdza widoczność usługi link prywatny |
> | Akcja | Microsoft.Network/locations/operationResults/read | Pobiera wynik operacji asynchronicznej operacji POST lub DELETE |
> | Akcja | Microsoft.Network/locations/operations/read | Pobiera zasób operacji reprezentujący stan operacji asynchronicznej |
> | Akcja | Microsoft.Network/locations/serviceTags/read | Pobierz Tagi usługi |
> | Akcja | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Pobiera obsługiwane rozmiary maszyn wirtualnych |
> | Akcja | Microsoft.Network/locations/usages/read | Pobiera metryki użycia zasobów |
> | Akcja | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Pobiera listę dostępnych Virtual Network usług punktu końcowego |
> | Akcja | Microsoft.Network/networkIntentPolicies/delete | Usuwa zasady dotyczące opcji sieci |
> | Akcja | Microsoft.Network/networkIntentPolicies/read | Pobiera opis zasad przeznaczenie sieci |
> | Akcja | Microsoft.Network/networkIntentPolicies/write | Tworzy zasady założeń sieciowych lub aktualizuje istniejące zasady założeń sieci |
> | Akcja | Microsoft.Network/networkInterfaces/delete | Usuwa interfejs sieciowy |
> | Akcja | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Pobieranie sieciowych grup zabezpieczeń skonfigurowanych dla interfejsu sieciowego maszyny wirtualnej |
> | Akcja | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Pobieranie tabeli tras skonfigurowanych dla interfejsu sieciowego maszyny wirtualnej |
> | Akcja | Microsoft. Network/networkInterfaces/ipconfiguration/sprzężenie/akcja | Umożliwia dołączenie do konfiguracji protokołu IP interfejsu sieciowego. Brak alertów. |
> | Akcja | Microsoft.Network/networkInterfaces/ipconfigurations/read | Pobiera definicję konfiguracji protokołu IP interfejsu sieciowego.  |
> | Akcja | Microsoft.Network/networkInterfaces/join/action | Przyłączanie maszyny wirtualnej do interfejsu sieciowego. Brak alertów. |
> | Akcja | Microsoft.Network/networkInterfaces/loadBalancers/read | Pobiera wszystkie usługi równoważenia obciążenia, do których należy interfejs sieciowy |
> | Akcja | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Akcja | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Usuwa konfigurację naciskania interfejsu sieciowego. |
> | Akcja | Microsoft.Network/networkInterfaces/tapConfigurations/read | Pobiera konfigurację naciskania interfejsu sieciowego. |
> | Akcja | Microsoft.Network/networkInterfaces/tapConfigurations/write | Tworzy interfejs sieciowy naciśnij pozycję Konfiguracja lub aktualizuje istniejący interfejs sieciowy naciśnij pozycję Konfiguracja. |
> | Akcja | Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Akcja | Microsoft.Network/networkProfiles/delete | Usuwa profil sieciowy |
> | Akcja | Microsoft.Network/networkProfiles/read | Pobiera profil sieciowy |
> | Akcja | Microsoft.Network/networkProfiles/removeContainers/action | Usuwa kontenery |
> | Akcja | Microsoft.Network/networkProfiles/setContainers/action | Ustawia kontenery |
> | Akcja | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Ustawia interfejsy sieciowe kontenerów |
> | Akcja | Microsoft.Network/networkProfiles/write | Tworzy lub aktualizuje profil sieciowy |
> | Akcja | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Pobiera domyślną definicję reguły zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/join/action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | Akcja | Microsoft.Network/networkSecurityGroups/read | Pobiera definicję sieciowej grupy zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/securityRules/delete | Usuwa regułę zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/securityRules/read | Pobiera definicję reguły zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/securityRules/write | Tworzy regułę zabezpieczeń lub aktualizuje istniejącą regułę zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/write | Tworzy sieciową grupę zabezpieczeń lub aktualizuje istniejącą sieciową grupę zabezpieczeń |
> | Akcja | Microsoft.Network/networkWatchers/availableProvidersList/action | Zwraca wszystkich dostępnych usługodawców internetowych dla określonego regionu świadczenia usługi Azure. |
> | Akcja | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Zwraca wynik względnego opóźnienia dla usługodawców internetowych z określonej lokalizacji do regionów świadczenia usługi Azure. |
> | Akcja | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfiguruje rejestrowanie przepływu dla zasobu docelowego. |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/delete | Usuwa monitor połączeń |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Zbadaj łączność monitorowania między określonymi punktami końcowymi |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/read | Pobierz szczegóły monitora połączeń |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Rozpocznij Monitorowanie łączności między określonymi punktami końcowymi |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Zatrzymaj/Wstrzymaj Monitorowanie łączności między określonymi punktami końcowymi |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/write | Tworzy monitor połączeń |
> | Akcja | Microsoft.Network/networkWatchers/connectivityCheck/action | Weryfikuje możliwość ustanowienia bezpośredniego połączenia TCP z maszyny wirtualnej do danego punktu końcowego, w tym innej maszyny wirtualnej lub dowolnego serwera zdalnego. |
> | Akcja | Microsoft.Network/networkWatchers/delete | Usuwa obserwatora sieciowego |
> | Akcja | Microsoft. Network/networkWatchers/ipFlowVerify/akcja | Zwraca czy pakiet jest dozwolony, czy odrzucany do lub z określonego miejsca docelowego. |
> | Akcja | Microsoft.Network/networkWatchers/lenses/delete | Usuwa soczewkę |
> | Akcja | Microsoft.Network/networkWatchers/lenses/query/action | Zbadaj ruch sieciowy monitorowania w określonym punkcie końcowym |
> | Akcja | Microsoft.Network/networkWatchers/lenses/read | Pobierz szczegóły obiektywu |
> | Akcja | Microsoft.Network/networkWatchers/lenses/start/action | Rozpocznij monitorowanie ruchu sieciowego w określonym punkcie końcowym |
> | Akcja | Microsoft.Network/networkWatchers/lenses/stop/action | Zatrzymaj/Wstrzymaj monitorowanie ruchu sieciowego w określonym punkcie końcowym |
> | Akcja | Microsoft.Network/networkWatchers/lenses/write | Tworzy soczewkę |
> | Akcja | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostyka konfiguracji sieci. |
> | Akcja | Microsoft.Network/networkWatchers/nextHop/action | W przypadku określonego docelowego i docelowego adresu IP Zwróć wartość typu następnego przeskoku i Następny adres IP. |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/delete | Usuwa przechwycenie pakietu |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Pobiera informacje o właściwościach i stanie zasobu przechwytywania pakietów. |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/read | Pobierz definicję przechwytywania pakietów |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Zatrzymaj uruchomioną sesję przechwytywania pakietów. |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/write | Tworzy przechwycenie pakietu |
> | Akcja | Microsoft.Network/networkWatchers/pingMeshes/delete | Usuwa element PingMesh |
> | Akcja | Microsoft.Network/networkWatchers/pingMeshes/read | Pobierz szczegóły PingMesh |
> | Akcja | Microsoft. Network/networkWatchers/pingMeshes/Start/akcja | Rozpocznij PingMesh między określonymi maszynami wirtualnymi |
> | Akcja | Microsoft. Network/networkWatchers/pingMeshes/akcja | Zatrzymaj PingMesh między określonymi maszynami wirtualnymi |
> | Akcja | Microsoft.Network/networkWatchers/pingMeshes/write | Tworzy element PingMesh |
> | Akcja | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Połączenie wsadowe monitorowania zapytań między określonymi punktami końcowymi |
> | Akcja | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Pobiera stan rejestrowania przepływu dla zasobu. |
> | Akcja | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Pobiera wynik rozwiązywania problemów z wcześniej uruchomionej lub aktualnie uruchomionej operacji rozwiązywania problemów. |
> | Akcja | Microsoft.Network/networkWatchers/read | Pobierz definicję obserwatora sieciowego |
> | Akcja | Microsoft.Network/networkWatchers/securityGroupView/action | Wyświetl skonfigurowane i efektywne reguły sieciowej grupy zabezpieczeń zastosowane na maszynie wirtualnej. |
> | Akcja | Microsoft.Network/networkWatchers/topology/action | Pobiera widok poziomu sieci zasobów i ich relacje z grupą zasobów. |
> | Akcja | Microsoft.Network/networkWatchers/troubleshoot/action | Uruchamia rozwiązywanie problemów dotyczących zasobów sieciowych na platformie Azure. |
> | Akcja | Microsoft.Network/networkWatchers/write | Tworzy obserwatora sieciowego lub aktualizuje istniejący obserwator sieci |
> | Akcja | Microsoft.Network/operations/read | Pobierz dostępne operacje |
> | Akcja | Microsoft.Network/p2sVpnGateways/delete | Usuwa element P2SVpnGateway. |
> | Akcja | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generuj profil sieci VPN dla P2SVpnGateway |
> | Akcja | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Pobiera kondycję połączenia sieci VPN P2S dla P2SVpnGateway |
> | Akcja | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/akcja | Pobiera szczegóły kondycji połączenia sieci VPN P2S dla P2SVpnGateway |
> | Akcja | Microsoft.Network/p2sVpnGateways/read | Pobiera P2SVpnGateway. |
> | Akcja | Microsoft.Network/p2sVpnGateways/write | P2SVpnGateway. |
> | Akcja | Microsoft.Network/privateDnsOperationResults/read | Pobiera wyniki operacji Prywatna strefa DNS |
> | Akcja | Microsoft.Network/privateDnsOperationStatuses/read | Pobiera stan operacji Prywatna strefa DNS |
> | Akcja | Microsoft.Network/privateDnsZones/A/delete | Usuń zestaw rekordów o danej nazwie i wpisz "A" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/A/read | Pobierz zestaw rekordów typu "A" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/privateDnsZones/A/write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/AAAA/delete | Usuń zestaw rekordów o danej nazwie i wpisz "AAAA" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/AAAA/read | Pobierz zestaw rekordów typu "AAAA" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/privateDnsZones/AAAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/ALL/read | Pobiera Prywatna strefa DNS zestawy rekordów dla różnych typów |
> | Akcja | Microsoft.Network/privateDnsZones/CNAME/delete | Usuń zestaw rekordów o danej nazwie i wpisz "CNAME" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/CNAME/read | Pobierz zestaw rekordów typu "CNAME" w ramach strefy Prywatna strefa DNS w formacie JSON. |
> | Akcja | Microsoft.Network/privateDnsZones/CNAME/write | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w ramach strefy Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/delete | Usuń strefę Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/MX/delete | Usuń zestaw rekordów o danej nazwie i typie "MX" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/MX/read | Pobierz zestaw rekordów typu "MX" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/privateDnsZones/MX/write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/PTR/delete | Usuń zestaw rekordów o danej nazwie i wpisz "PTR" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/PTR/read | Pobierz zestaw rekordów typu "PTR" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/privateDnsZones/PTR/write | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/read | Pobierz Prywatna strefa DNS właściwości strefy w formacie JSON. Należy zauważyć, że to polecenie nie pobiera sieci wirtualnych, do których jest połączona strefa Prywatna strefa DNS lub zestawy rekordów zawarte w strefie. |
> | Akcja | Microsoft.Network/privateDnsZones/recordsets/read | Pobiera Prywatna strefa DNS zestawy rekordów dla różnych typów |
> | Akcja | Microsoft.Network/privateDnsZones/SOA/read | Pobierz zestaw rekordów typu "SOA" w ramach strefy Prywatna strefa DNS w formacie JSON. |
> | Akcja | Microsoft.Network/privateDnsZones/SOA/write | Zaktualizuj zestaw rekordów typu "SOA" w ramach strefy Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/SRV/delete | Usuń zestaw rekordów o danej nazwie i typie "SRV" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/SRV/read | Pobierz zestaw rekordów typu "SRV" w ramach strefy Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/privateDnsZones/SRV/write | Utwórz lub zaktualizuj zestaw rekordów typu "SRV" w ramach strefy Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/TXT/delete | Usuń zestaw rekordów o danej nazwie i typie "TXT" ze strefy Prywatna strefa DNS. |
> | Akcja | Microsoft.Network/privateDnsZones/TXT/read | Pobierz zestaw rekordów typu "TXT" w strefie Prywatna strefa DNS w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz czas wygaśnięcia, znaczniki i element ETag. |
> | Akcja | Microsoft.Network/privateDnsZones/TXT/write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie Prywatna strefa DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Usuń link strefy Prywatna strefa DNS do sieci wirtualnej. |
> | Akcja | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Pobierz łącze strefy Prywatna strefa DNS do właściwości sieci wirtualnej w formacie JSON. |
> | Akcja | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Utwórz lub zaktualizuj link strefy Prywatna strefa DNS do sieci wirtualnej. |
> | Akcja | Microsoft.Network/privateDnsZones/write | Utwórz lub zaktualizuj strefę Prywatna strefa DNS w grupie zasobów. Należy pamiętać, że tego polecenia nie można użyć do tworzenia lub aktualizowania linków sieci wirtualnej lub zestawów rekordów w strefie. |
> | Akcja | Microsoft. Network/privateEndpointRedirectMaps/odczyt | Pobiera prywatny punkt końcowy RedirectMap |
> | Akcja | Microsoft. Network/privateEndpointRedirectMaps/Write | Tworzy prywatny punkt końcowy RedirectMap lub aktualizuje istniejący prywatny punkt końcowy RedirectMap |
> | Akcja | Microsoft.Network/privateEndpoints/delete | Usuwa prywatny zasób punktu końcowego. |
> | Akcja | Microsoft.Network/privateEndpoints/read | Pobiera prywatny zasób punktu końcowego. |
> | Akcja | Microsoft.Network/privateEndpoints/write | Tworzy nowy prywatny punkt końcowy lub aktualizuje istniejący prywatny punkt końcowy. |
> | Akcja | Microsoft.Network/privateLinkServices/delete | Usuwa zasób usługi link prywatny. |
> | Akcja | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Usuwa połączenie prywatnego punktu końcowego. |
> | Akcja | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Pobiera definicję połączenia z prywatnym punktem końcowym. |
> | Akcja | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Tworzy nowe połączenie prywatnego punktu końcowego lub aktualizuje istniejące połączenie prywatnego punktu końcowego. |
> | Akcja | Microsoft.Network/privateLinkServices/read | Pobiera zasób usługi linku prywatnego. |
> | Akcja | Microsoft.Network/privateLinkServices/write | Tworzy nową usługę linku prywatnego lub aktualizuje istniejącą usługę linku prywatnego. |
> | Akcja | Microsoft.Network/publicIPAddresses/delete | Usuwa publiczny adres IP. |
> | Akcja | Microsoft.Network/publicIPAddresses/join/action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | Akcja | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Akcja | Microsoft.Network/publicIPAddresses/write | Tworzy publiczny adres IP lub aktualizuje istniejący publiczny adres IP.  |
> | Akcja | Microsoft. Network/publicIPPrefixes/Delete | Usuwa prefiks publicznego adresu IP |
> | Akcja | Microsoft. Network/publicIPPrefixes/Join/Action | Sprzęga PublicIPPrefix. Brak alertów. |
> | Akcja | Microsoft.Network/publicIPPrefixes/read | Pobiera definicję prefiksu publicznego adresu IP |
> | Akcja | Microsoft. Network/publicIPPrefixes/Write | Tworzy publiczny prefiks adresu IP lub aktualizuje istniejący prefiks publicznego adresu IP |
> | Akcja | Microsoft. Network/Register/Action | Rejestruje subskrypcję |
> | Akcja | Microsoft.Network/routeFilters/delete | Usuwa definicję filtru tras |
> | Akcja | Microsoft. Network/routeFilters/Join/Action | Sprzęga filtr tras. Brak alertów. |
> | Akcja | Microsoft.Network/routeFilters/read | Pobiera definicję filtru tras |
> | Akcja | Microsoft. Network/routeFilters/routeFilterRules/Delete | Usuwa definicję reguły filtru trasy |
> | Akcja | Microsoft.Network/routeFilters/routeFilterRules/read | Pobiera definicję reguły filtru trasy |
> | Akcja | Microsoft. Network/routeFilters/routeFilterRules/Write | Tworzy regułę filtru tras lub aktualizuje istniejącą regułę filtru tras |
> | Akcja | Microsoft.Network/routeFilters/write | Tworzy filtr trasy lub aktualizuje istniejący filtr tras |
> | Akcja | Microsoft.Network/routeTables/delete | Usuwa definicję tabeli tras |
> | Akcja | Microsoft.Network/routeTables/join/action | Sprzęga tabelę tras. Brak alertów. |
> | Akcja | Microsoft.Network/routeTables/read | Pobiera definicję tabeli tras |
> | Akcja | Microsoft.Network/routeTables/routes/delete | Usuwa definicję trasy |
> | Akcja | Microsoft.Network/routeTables/routes/read | Pobiera definicję trasy |
> | Akcja | Microsoft.Network/routeTables/routes/write | Tworzy trasę lub aktualizuje istniejącą trasę |
> | Akcja | Microsoft.Network/routeTables/write | Tworzy tabelę tras lub aktualizuje istniejącą tabelę tras |
> | Akcja | Microsoft. Network/securegateways/Delete | Usuwanie bramy Secure Gateway |
> | Akcja | Microsoft. Network/securegateways/odczyt | Uzyskaj bezpieczną bramę |
> | Akcja | Microsoft. Network/securegateways/Write | Tworzy lub aktualizuje bramę Secure Gateway |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/Delete | Usuwa zasady punktu końcowego usługi |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/Join/Action | Przyłącza zasady punktu końcowego usługi. Brak alertów. |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Przyłączanie podsieci do zasad punktu końcowego usługi. Brak alertów. |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/read | Pobiera opis zasad punktu końcowego usługi |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Delete | Usuwa definicję zasad punktu końcowego usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Pobiera opis definicji zasad punktu końcowego usługi |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Write | Tworzy definicję zasad punktu końcowego usługi lub aktualizuje istniejącą definicję zasad punktu końcowego usługi |
> | Akcja | Microsoft. Network/serviceEndpointPolicies/Write | Tworzy zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi |
> | Akcja | Microsoft.Network/trafficManagerGeographicHierarchies/read | Pobiera Traffic Manager hierarchię geograficzną zawierającą regiony, które mogą być używane z metodą routingu ruchu geograficznego |
> | Akcja | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Usuwa punkt końcowy platformy Azure z istniejącego profilu Traffic Manager. Traffic Manager przestanie kierować ruch do usuniętego punkt końcowy platformy Azure. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Pobiera punkt końcowy platformy Azure, które należy do profilu Traffic Manager, w tym wszystkie właściwości tego punkt końcowy platformy Azure. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Dodaj nowy punkt końcowy platformy Azure w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącej punkt końcowy platformy Azure w tym profilu Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/delete | Usuń profil Traffic Manager. Wszystkie ustawienia skojarzone z profilem Traffic Manager zostaną utracone i nie będzie można już używać profilu do kierowania ruchem. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Usuwa zewnętrzny punkt końcowy z istniejącego profilu Traffic Manager. Traffic Manager zatrzyma kierowanie ruchu do usuniętego zewnętrznego punktu końcowego. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Pobiera zewnętrzny punkt końcowy, który należy do profilu Traffic Manager, łącznie ze wszystkimi właściwościami tego zewnętrznego punktu końcowego. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Dodaj nowy zewnętrzny punkt końcowy w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącego zewnętrznego punktu końcowego w tym profilu Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Pobiera Traffic Managerową mapę cieplną dla danego profilu Traffic Manager, który zawiera liczby zapytań i dane opóźnienia według lokalizacji i źródłowego adresu IP. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Usuwa zagnieżdżony punkt końcowy z istniejącego profilu Traffic Manager. Traffic Manager przestanie kierować ruch do usuniętego zagnieżdżonego punktu końcowego. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Pobiera zagnieżdżony punkt końcowy, który należy do profilu Traffic Manager, łącznie ze wszystkimi właściwościami tego zagnieżdżonego punktu końcowego. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Dodaj nowy zagnieżdżony punkt końcowy w istniejącym profilu Traffic Manager lub zaktualizuj właściwości istniejącego zagnieżdżonego punktu końcowego w tym Traffic Manager profilu. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/read | Pobierz konfigurację profilu Traffic Manager. Obejmuje to ustawienia DNS, ustawienia routingu ruchu, ustawienia monitorowania punktu końcowego oraz listę punktów końcowych kierowanych przez ten profil Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/write | Utwórz profil Traffic Manager lub zmodyfikuj konfigurację istniejącego profilu Traffic Manager.<br>Obejmuje to Włączanie lub wyłączanie profilu oraz modyfikowanie ustawień DNS, ustawień routingu ruchu lub ustawień monitorowania punktu końcowego.<br>Punkty końcowe kierowane przez profil Traffic Manager można dodawać, usuwać, włączać lub wyłączać. |
> | Akcja | Microsoft. Network/trafficManagerUserMetricsKeys/Delete | Usuwa klucz poziomu subskrypcji używany do zbierania metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft.Network/trafficManagerUserMetricsKeys/read | Pobiera klucz poziomu subskrypcji używany na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft.Network/trafficManagerUserMetricsKeys/write | Tworzy nowy klucz poziomu subskrypcji, który będzie używany na potrzeby zbierania metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft. Network/Unregister/Action | Wyrejestrowuje subskrypcję |
> | Akcja | Microsoft.Network/virtualHubs/delete | Usuwa koncentrator wirtualny |
> | Akcja | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Usuwa element HubVirtualNetworkConnection |
> | Akcja | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | Akcja | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Utwórz lub zaktualizuj HubVirtualNetworkConnection |
> | Akcja | Microsoft.Network/virtualHubs/read | Pobierz koncentrator wirtualny |
> | Akcja | Microsoft. Network/virtualHubs/routeTables/Delete | Usuń VirtualHubRouteTableV2 |
> | Akcja | Microsoft. Network/virtualHubs/routeTables/odczyt | Pobierz VirtualHubRouteTableV2 |
> | Akcja | Microsoft. Network/virtualHubs/routeTables/Write | Utwórz lub zaktualizuj VirtualHubRouteTableV2 |
> | Akcja | Microsoft.Network/virtualHubs/write | Tworzenie lub aktualizowanie koncentratora wirtualnego |
> | Akcja | Microsoft. Network/virtualnetworkgateways/Connections/Read | Get VirtualNetworkGatewayConnection |
> | Akcja | Microsoft. Network/virtualNetworkGateways/Delete | Usuwa element virtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/generatevpnclientpackage/akcja | Generuj pakiet klienta dla virtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Generuj pakiet VpnProfile dla VirtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Pobiera trasy anonsowane przez virtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/getbgppeerstatus/akcja | Pobiera stan elementu równorzędnego BGP virtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/getlearnedroutes/akcja | Pobiera virtualnetworkgatewayne trasy |
> | Akcja | Microsoft. Network/virtualnetworkgateways/getvpnclientconnectionhealth/akcja | Pobierz kondycję połączenia klienta sieci VPN dla VirtualNetworkGateway |
> | Akcja | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Pobierz parametry klienta IPSec dla klienta VirtualNetworkGateway P2S. |
> | Akcja | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Pobiera adres URL wstępnie wygenerowanego pakietu profilu klienta VPN |
> | Akcja | Microsoft.Network/virtualNetworkGateways/read | Pobiera VirtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/Reset/akcja | Resetuje virtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/akcja | Zresetuj klucz współużytkowany klienta dla klienta VirtualNetworkGateway P2S. |
> | Akcja | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Ustaw parametry klienta IPSec dla klienta VirtualNetworkGateway P2S. |
> | Akcja | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Wyświetla listę obsługiwanych urządzeń sieci VPN |
> | Akcja | Microsoft.Network/virtualNetworkGateways/write | Tworzy lub aktualizuje VirtualNetworkGateway |
> | Akcja | Microsoft. Network/virtualNetworks/BastionHosts/akcja | Pobiera odwołania do hostów bastionu w Virtual Network. |
> | Akcja | Microsoft. Network/virtualNetworks/bastionHosts/default/Action | Pobiera odwołania do hostów bastionu w Virtual Network. |
> | Akcja | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Sprawdź, czy adres IP jest dostępny w określonej sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/delete | Usuwa sieć wirtualną |
> | Akcja | Microsoft. Network/virtualNetworks/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Akcja | Microsoft. Network/virtualNetworks/peer/Action | Tworzy równorzędną sieć wirtualną z inną siecią wirtualną |
> | Akcja | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Delete | Usuwa zasady punktu końcowego usługi kontekstowej |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Read | Pobiera zasady punktu końcowego usługi kontekstowej |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Write | Tworzy kontekstowe zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi kontekstowej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/delete | Usuwa podsieć sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Przygotowuje podsieć przez zastosowanie niezbędnych zasad sieciowych |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/Subnets/unprepareNetworkPolicies/Action | Cofnięcie przygotowania podsieci przez usunięcie zastosowanych zasad sieciowych |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w podsieci sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/write | Tworzy podsieć sieci wirtualnej lub aktualizuje istniejącą podsieć sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/usages/read | Uzyskaj użycie adresów IP dla każdej podsieci sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w sieci wirtualnej |
> | Akcja | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Delete | Usuwa komunikację równorzędną sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Pobiera definicję komunikacji równorzędnej sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Tworzy komunikację równorzędną sieci wirtualnej lub aktualizuje istniejącą komunikację równorzędną sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/write | Tworzy sieć wirtualną lub aktualizuje istniejącą sieć wirtualną |
> | Akcja | Microsoft.Network/virtualNetworkTaps/delete | Usuń naciśnięcie Virtual Network |
> | Akcja | Microsoft. Network/virtualNetworkTaps/Join/Action | Umożliwia dołączenie do programu TAP sieci wirtualnej. Brak alertów. |
> | Akcja | Microsoft.Network/virtualNetworkTaps/read | Pobierz Virtual Network naciśnij pozycję |
> | Akcja | Microsoft.Network/virtualNetworkTaps/write | Utwórz lub zaktualizuj Virtual Network TAP |
> | Akcja | Microsoft. Network/virtualRouters/Delete | Usuwa element VirtualRouter |
> | Akcja | Microsoft. Network/virtualRouters/Join/Action | Sprzęga VirtualRouter. Brak alertów. |
> | Akcja | Microsoft. Network/virtualRouters/odczyt | Pobiera VirtualRouter |
> | Akcja | Microsoft. Network/virtualRouters/virtualRouterPeerings/Delete | Usuwa element VirtualRouterPeering |
> | Akcja | Microsoft. Network/virtualRouters/virtualRouterPeerings/odczyt | Pobiera VirtualRouterPeering |
> | Akcja | Microsoft. Network/virtualRouters/virtualRouterPeerings/Write | Tworzy VirtualRouterPeering lub aktualizuje istniejący VirtualRouterPeering |
> | Akcja | Microsoft. Network/virtualRouters/Write | Tworzy VirtualRouter lub aktualizuje istniejący VirtualRouter |
> | Akcja | Microsoft. Network/virtualWans/Delete | Usuwa wirtualną sieć WAN |
> | Akcja | Microsoft. Network/virtualwans/generateVpnProfile/akcja | Generuj VirtualWanVpnServerConfiguration VpnProfile |
> | Akcja | Microsoft.Network/virtualWans/read | Uzyskaj wirtualną sieć WAN |
> | Akcja | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Pobiera obsługiwanych dostawców zabezpieczeń VirtualWan. |
> | Akcja | Microsoft.Network/virtualWans/virtualHubs/read | Pobiera wszystkie centra wirtualne odwołujące się do wirtualnej sieci WAN. |
> | Akcja | Microsoft.Network/virtualwans/vpnconfiguration/action | Pobiera konfigurację sieci VPN |
> | Akcja | Microsoft. Network/virtualwans/vpnServerConfigurations/akcja | Pobierz VirtualWanVpnServerConfigurations |
> | Akcja | Microsoft.Network/virtualWans/vpnSites/read | Pobiera wszystkie lokacje sieci VPN odwołujące się do wirtualnej sieci WAN. |
> | Akcja | Microsoft. Network/virtualWans/Write | Utwórz lub zaktualizuj wirtualną sieć WAN |
> | Akcja | Microsoft.Network/vpnGateways/delete | Usuwa element bramy vpngateway. |
> | Akcja | microsoft.network/vpngateways/listvpnconnectionshealth/action | Pobiera kondycję połączenia dla wszystkich lub podzbioru połączeń na bramy vpngateway |
> | Akcja | Microsoft.Network/vpnGateways/read | Pobiera bramy vpngateway. |
> | Akcja | microsoft.network/vpngateways/reset/action | Resetuje bramy vpngateway |
> | Akcja | microsoft.network/vpnGateways/vpnConnections/delete | Usuwa element VpnConnection. |
> | Akcja | microsoft.network/vpnGateways/vpnConnections/read | Pobiera VpnConnection. |
> | Akcja | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Pobiera połączenie sieci VPN |
> | Akcja | microsoft.network/vpnGateways/vpnConnections/write | VpnConnection. |
> | Akcja | Microsoft.Network/vpnGateways/write | Bramy vpngateway. |
> | Akcja | Microsoft. Network/vpnServerConfigurations/Delete | Usuń VpnServerConfiguration |
> | Akcja | Microsoft. Network/vpnServerConfigurations/odczyt | Pobierz VpnServerConfiguration |
> | Akcja | Microsoft. Network/vpnServerConfigurations/Write | Utwórz lub zaktualizuj VpnServerConfiguration |
> | Akcja | Microsoft.Network/vpnsites/delete | Usuwa zasób witryny sieci VPN. |
> | Akcja | Microsoft.Network/vpnsites/read | Pobiera zasób witryny sieci VPN. |
> | Akcja | Microsoft. Network/vpnSites/vpnSiteLinks/odczyt | Pobiera łącze do witryny sieci VPN |
> | Akcja | Microsoft.Network/vpnsites/write | Tworzy lub aktualizuje zasób witryny sieci VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Sprawdza, czy dana nazwa zasobu przestrzeni nazw jest dostępna w ramach usługi NotificationHub. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia do przestrzeni nazw |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Reguła autoryzacji przestrzeni nazw Wygeneruj ponownie podstawowy/SecondaryKey, określ klucz, który należy ponownie wygenerować |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Sprawdza, czy dana nazwa NotificationHub jest dostępna wewnątrz przestrzeni nazw. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Usuń reguły autoryzacji centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Pobierz parametry połączenia do centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Reguła autoryzacji centrum powiadomień Regenerate Primary/SecondaryKey, określ klucz, który należy ponownie wygenerować |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Utwórz reguły autoryzacji centrum powiadomień i zaktualizuj jego właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Wyślij testowe Powiadomienie wypychane. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Usuwanie zasobu centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Pobierz listę opisów zasobów metryk przestrzeni nazw |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Pobierz wszystkie poświadczenia PNS centrum powiadomień. Obejmuje to, WNS, usługi MPNS, APNS, GCM i Baidu |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Pobierz listę opisów zasobów centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Utwórz centrum powiadomień i zaktualizuj jego właściwości. Jego właściwości obejmują głównie poświadczenia PNS. Reguły autoryzacji i czas wygaśnięcia |
> | Akcja | Microsoft.NotificationHubs/Namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft.NotificationHubs/Namespaces/write | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Akcja | Microsoft.NotificationHubs/operationResults/read | Zwraca wyniki operacji dla dostawcy Notification Hubs |
> | Akcja | Microsoft.NotificationHubs/operations/read | Zwraca listę obsługiwanych operacji dla dostawcy Notification Hubs |
> | Akcja | Microsoft.NotificationHubs/register/action | Rejestruje subskrypcję dostawcy zasobów NotificationHubs i umożliwia tworzenie przestrzeni nazw i NotificationHubs |
> | Akcja | Microsoft.NotificationHubs/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów NotificationHubs i umożliwia tworzenie przestrzeni nazw i NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

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
> | Akcja | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Pobiera właściwości kont Uruchom jako funkcji Hyper-V |
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
> | Akcja | Microsoft.OffAzure/VMwareSites/delete | Usuwa witrynę programu VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/healthsummary/Read | Pobiera podsumowanie kondycji zasobu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/jobs/read | Pobiera właściwości zadań programu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/machines/read | Pobiera właściwości maszyn VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/Machines/Start/Action | Uruchom maszyny VMware |
> | Akcja | Microsoft. OffAzure/VMwareSites/Machines/akcja | Powoduje zatrzymanie maszyn VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Pobiera właściwości stanu operacji VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/read | Pobiera właściwości witryny programu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/refresh/action | Odświeża obiekty w witrynie programu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Pobiera właściwości kont Uruchom jako VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/usage/read | Pobiera użycie witryny programu VMware |
> | Akcja | Microsoft.OffAzure/VMwareSites/vcenters/read | Pobiera właściwości programu VMware vCenter |
> | Akcja | Microsoft.OffAzure/VMwareSites/vcenters/write | Tworzy lub aktualizuje program VMware vCenter |
> | Akcja | Microsoft.OffAzure/VMwareSites/write | Tworzy lub aktualizuje witrynę programu VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.OperationalInsights/linkTargets/read | Wyświetla listę istniejących kont, które nie są skojarzone z subskrypcją platformy Azure. Aby połączyć tę subskrypcję platformy Azure z obszarem roboczym, użyj identyfikatora klienta zwróconego przez tę operację we właściwości identyfikator klienta operacji Utwórz obszar roboczy. |
> | Akcja | Microsoft. operationalinsights/Operations/Read | Wyświetla wszystkie dostępne operacje interfejsu API REST OperationalInsights. |
> | Akcja | Microsoft. operationalinsights/Register/Action | Rergisters subskrypcję. |
> | Akcja | Microsoft. OperationalInsights/Register/Action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Akcja | Microsoft. operationalinsights/Unregister/Action | Wyrejestrowuje subskrypcję. |
> | Akcja | Microsoft.OperationalInsights/workspaces/analytics/query/action | Wyszukaj przy użyciu nowego aparatu. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Analytics/Query/Schema/Read | Pobierz schemat wyszukiwania w wersji 2. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/interfejs API/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Pobierz schemat wyszukiwania w wersji 2. |
> | Akcja | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Usuń zakres konfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Pobierz zakres konfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Ustaw zakres konfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/datasources/delete | Usuń źródła danych w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/datasources/read | Pobierz źródła danych w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/datasources/write | Utwórz/zaktualizuj źródła danych w obszarze roboczym. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/usuwanie | Usuwa obszar roboczy. Jeśli obszar roboczy został połączony z istniejącym obszarem roboczym podczas tworzenia, a obszar roboczy, z którym został on połączony, nie został usunięty. |
> | Akcja | Microsoft.OperationalInsights/workspaces/gateways/delete | Usuwa bramę skonfigurowaną dla obszaru roboczego. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/generateregistrationcertificate/akcja | Generuje certyfikat rejestracji dla obszaru roboczego. Ten certyfikat służy do łączenia programu Microsoft System Center Operations Manager z obszarem roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Disable/Action | Wyłącza pakiet Intelligence Pack dla danego obszaru roboczego. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Enable/Action | Włącza pakiet Intelligence Pack dla danego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Wyświetla listę wszystkich pakietów analizy, które są widoczne dla danego obszaru roboczego, a także listę informacji o tym, czy pakiet jest włączony, czy wyłączony dla tego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Usuń połączone usługi w podanym obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/linkedServices/read | Pobierz połączone usługi w podanym obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/linkedServices/write | Utwórz/zaktualizuj połączone usługi w podanym obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/listKeys/action | Pobiera klucze list dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/listKeys/read | Pobiera klucze list dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/managementGroups/read | Pobiera nazwy i metadane dla System Center Operations Manager grup zarządzania połączonych z tym obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Pobierz definicje metryki w obszarze roboczym |
> | Akcja | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Usuń ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Pobierz ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Ustaw ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft. operationalinsights/obszary robocze/operacje/odczyt | Pobiera stan operacji obszaru roboczego OperationalInsights. |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/przeczyszczanie/akcja | Usuń określone dane z obszaru roboczego |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountLogon/Read | Odczytaj dane z tabeli AADDomainServicesAccountLogon |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountManagement/Read | Odczytaj dane z tabeli AADDomainServicesAccountManagement |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Odczytaj dane z tabeli AADDomainServicesDirectoryServiceAccess |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesLogonLogoff/Read | Odczytaj dane z tabeli AADDomainServicesLogonLogoff |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPolicyChange/Read | Odczytaj dane z tabeli AADDomainServicesPolicyChange |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPrivilegeUse/Read | Odczytaj dane z tabeli AADDomainServicesPrivilegeUse |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesSystemSecurity/Read | Odczytaj dane z tabeli AADDomainServicesSystemSecurity |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Odczytaj dane z tabeli ADAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupAlerts/Read | Odczytaj dane z tabeli AddonAzureBackupAlerts |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupJobs/Read | Odczytaj dane z tabeli AddonAzureBackupJobs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupPolicy/Read | Odczytaj dane z tabeli AddonAzureBackupPolicy |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupProtectedInstance/Read | Odczytaj dane z tabeli AddonAzureBackupProtectedInstance |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupStorage/Read | Odczytaj dane z tabeli AddonAzureBackupStorage |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ADFActivityRun/Read | Odczytaj dane z tabeli ADFActivityRun |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ADFPipelineRun/Read | Odczytaj dane z tabeli ADFPipelineRun |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Odczytaj dane z tabeli ADFTriggerRun |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Odczytaj dane z tabeli ADReplicationResult |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Odczytaj dane z tabeli ADSecurityAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Alert/read | Odczytaj dane z tabeli alertów |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Odczytaj dane z tabeli AlertHistory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterEvent/Read | Odczytaj dane z tabeli AmlComputeClusterEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterNodeEvent/Read | Odczytaj dane z tabeli AmlComputeClusterNodeEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeJobEvent/Read | Odczytaj dane z tabeli AmlComputeJobEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ApiManagementGatewayLogs/Read | Odczytaj dane z tabeli ApiManagementGatewayLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Odczytaj dane z tabeli AppCenterError |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Odczytaj dane z tabeli ApplicationInsights |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AppPlatformLogsforSpring/Read | Odczytaj dane z tabeli AppPlatformLogsforSpring |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AppServiceEnvironmentPlatformLogs/Read | Odczytaj dane z tabeli AppServiceEnvironmentPlatformLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Odczytaj dane z tabeli AuditLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Odczytaj dane z tabeli AutoscaleEvaluationsLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Odczytaj dane z tabeli AutoscaleScaleActionsLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/AWSCloudTrail/Read | Odczytaj dane z tabeli AWSCloudTrail |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/Azure — odczyt | Odczytywanie danych z tabeli platformy Azure |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Odczytaj dane z tabeli AzureAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Odczytaj dane z tabeli AzureMetrics |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterEvent/Read | Odczytaj dane z tabeli BaiClusterEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterNodeEvent/Read | Odczytaj dane z tabeli BaiClusterNodeEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BaiJobEvent/Read | Odczytaj dane z tabeli BaiJobEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BlockchainApplicationLog/Read | Odczytaj dane z tabeli BlockchainApplicationLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BlockchainProxyLog/Read | Odczytaj dane z tabeli BlockchainProxyLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/BoundPort/Read | Odczytaj dane z tabeli BoundPort |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/CommonSecurityLog/Read | Odczytaj dane z tabeli CommonSecurityLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Odczytywanie danych z tabeli komputerów |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/Zmianakonfiguracji/Read | Odczytaj dane z tabeli Zmianakonfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Odczytaj dane z tabeli ConfigurationData |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Odczytaj dane z tabeli ContainerImageInventory |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Odczytaj dane z tabeli ContainerInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerLog/Read | Odczytaj dane z tabeli ContainerLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Odczytaj dane z tabeli ContainerNodeInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryLoginEvents/Read | Odczytaj dane z tabeli ContainerRegistryLoginEvents |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryRepositoryEvents/Read | Odczytaj dane z tabeli ContainerRegistryRepositoryEvents |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Odczytaj dane z tabeli ContainerServiceLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/CoreAzureBackup/Read | Odczytaj dane z tabeli CoreAzureBackup |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Odczytaj dane z tabeli DatabricksAccounts |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksClusters/Read | Odczytaj dane z tabeli DatabricksClusters |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksDBFS/Read | Odczytaj dane z tabeli DatabricksDBFS |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksInstancePools/Read | Odczytaj dane z tabeli DatabricksInstancePools |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Odczytaj dane z tabeli DatabricksJobs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksNotebook/Read | Odczytaj dane z tabeli DatabricksNotebook |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Odczytaj dane z tabeli DatabricksSecrets |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSQLPermissions/Read | Odczytaj dane z tabeli DatabricksSQLPermissions |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSSH/Read | Odczytaj dane z tabeli DatabricksSSH |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DatabricksTables/Read | Odczytaj dane z tabeli DatabricksTables |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Odczytaj dane z tabeli DatabricksWorkspace |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceAppCrash/Read | Odczytaj dane z tabeli DeviceAppCrash |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Odczytaj dane z tabeli DeviceAppLaunch |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Odczytaj dane z tabeli DeviceCalendar |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DeviceCleanup/Read | Odczytaj dane z tabeli DeviceCleanup |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Odczytaj dane z tabeli DeviceConnectSession |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Odczytaj dane z tabeli DeviceEtw |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Odczytaj dane z tabeli DeviceHardwareHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Odczytaj dane z tabeli zameldowaniem |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Odczytaj dane z tabeli DeviceHeartbeat |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Odczytaj dane z tabeli DeviceSkypeHeartbeat |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Odczytaj dane z tabeli DeviceSkypeSignIn |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Odczytaj dane z tabeli DeviceSleepState |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Odczytaj dane z tabeli DHAppFailure |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Odczytaj dane z tabeli DHAppReliability |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHDriverReliability/Read | Odczytaj dane z tabeli DHDriverReliability |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Odczytaj dane z tabeli DHLogonFailures |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Odczytaj dane z tabeli DHLogonMetrics |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Odczytaj dane z tabeli DHOSCrashData |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/DHOSReliability/Read | Odczytaj dane z tabeli DHOSReliability |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Odczytaj dane z tabeli DHWipAppLearning |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Odczytaj dane z tabeli DnsEvents |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Odczytaj dane z tabeli DnsInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ETWEvent/Read | Odczytaj dane z tabeli ETWEvent |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytanie/zdarzenie/odczyt | Odczytaj dane z tabeli zdarzeń |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ExchangeAssessmentRecommendation/Read | Odczytaj dane z tabeli ExchangeAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli ExchangeOnlineAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/FailedIngestion/Read | Odczytaj dane z tabeli FailedIngestion |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/puls/odczyt | Odczytaj dane z tabeli pulsu |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/HuntingBookmark/Read | Odczytaj dane z tabeli HuntingBookmark |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Odczytaj dane z tabeli IISAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Odczytaj dane z tabeli InboundConnection |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Odczytaj dane z tabeli InsightsMetrics |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Odczytaj dane z tabeli IntuneAuditLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Odczytaj dane z tabeli IntuneOperationalLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/KubeEvents/Read | Odczytaj dane z tabeli KubeEvents |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/KubeHealth/Read | Odczytaj dane z tabeli KubeHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Odczytaj dane z tabeli KubeNodeInventory |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/KubePodInventory/Read | Odczytaj dane z tabeli KubePodInventory |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Odczytaj dane z tabeli KubeServices |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Odczytaj dane z tabeli LinuxAuditLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplication/Read | Odczytaj dane z tabeli MAApplication |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealth/Read | Odczytaj dane z tabeli MAApplicationHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthAlternativeVersions/Read | Odczytaj dane z tabeli MAApplicationHealthAlternativeVersions |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthIssues/Read | Odczytaj dane z tabeli MAApplicationHealthIssues |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationInstance/Read | Odczytaj dane z tabeli MAApplicationInstance |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Odczytaj dane z tabeli MAApplicationInstanceReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Odczytaj dane z tabeli MAApplicationReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADeploymentPlan/Read | Odczytaj dane z tabeli MADeploymentPlan |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADevice/Read | Odczytaj dane z tabeli MADevice |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNotEnrolled/Read | Odczytaj dane z tabeli MADeviceNotEnrolled |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNRT/Read | Odczytaj dane z tabeli MADeviceNRT |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Odczytaj dane z tabeli MADevicePnPHealth |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Odczytaj dane z tabeli MADevicePnPHealthAlternativeVersions |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Odczytaj dane z tabeli MADevicePnPHealthIssues |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Odczytaj dane z tabeli MADeviceReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Odczytaj dane z tabeli MADriverInstanceReadiness |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Odczytaj dane z tabeli MADriverReadiness |
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
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Odczytaj dane z tabeli MAOfficeDeploymentStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Odczytaj dane z tabeli MAOfficeDeploymentStatusNRT |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroErrorNRT/Read | Odczytaj dane z tabeli MAOfficeMacroErrorNRT |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroGlobalHealth/Read | Odczytaj dane z tabeli MAOfficeMacroGlobalHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealth/Read | Odczytaj dane z tabeli MAOfficeMacroHealth |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealthIssues/Read | Odczytaj dane z tabeli MAOfficeMacroHealthIssues |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueInstanceReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueInstanceReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueReadiness/Read | Odczytaj dane z tabeli MAOfficeMacroIssueReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroSummary/Read | Odczytaj dane z tabeli MAOfficeMacroSummary |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeSuite/Read | Odczytaj dane z tabeli MAOfficeSuite |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Odczytaj dane z tabeli MAOfficeSuiteInstance |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAProposedPilotDevices/Read | Odczytaj dane z tabeli MAProposedPilotDevices |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsBuildInfo/Read | Odczytaj dane z tabeli MAWindowsBuildInfo |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsCurrencyAssessment/Read | Odczytaj dane z tabeli MAWindowsCurrencyAssessment |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Odczytaj dane z tabeli MAWindowsCurrencyAssessmentDailyCounts |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Odczytaj dane z tabeli MAWindowsDeploymentStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Odczytaj dane z tabeli MAWindowsDeploymentStatusNRT |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Odczytaj dane z tabeli MAWindowsSysReqInstanceReadiness |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/McasShadowItReporting/Read | Odczytaj dane z tabeli McasShadowItReporting |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareShareLog/Read | Odczytaj dane z tabeli MicrosoftDataShareShareLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftHealthcareApisAuditLogs/Read | Odczytaj dane z tabeli MicrosoftHealthcareApisAuditLogs |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftInsightsAzureActivityLog/Read | Odczytaj dane z tabeli MicrosoftInsightsAzureActivityLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebApplicationLog/Read | Odczytaj dane z tabeli MicrosoftWebApplicationLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebFunctionExecutionLogs/Read | Odczytaj dane z tabeli MicrosoftWebFunctionExecutionLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Odczytaj dane z tabeli MicrosoftWebStdOutStdErrLog |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebW3CLog/Read | Odczytaj dane z tabeli MicrosoftWebW3CLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Odczytaj dane z tabeli NetworkMonitoring |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/Office/odczyt | Odczytaj dane z tabeli pakietu Office |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/operacja/odczyt | Odczytaj dane z tabeli operacji |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Odczytaj dane z tabeli OutboundConnection |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/perf/Read | Odczytaj dane z tabeli wydajności |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Odczytaj dane z tabeli ProtectionStatus |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytanie/odczyt | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Odczytaj dane z tabeli ReservedAzureCommonFields |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Odczytaj dane z tabeli ReservedCommonFields |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SCCMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCCMAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SCOMAssessmentRecommendation/Read | Odczytaj dane z tabeli SCOMAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Odczytaj dane z tabeli SecurityAlert |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaseline/Read | Odczytaj dane z tabeli SecurityBaseline |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaselineSummary/Read | Odczytaj dane z tabeli SecurityBaselineSummary |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Odczytaj dane z tabeli SecurityDetection |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SecurityEvent/Read | Odczytaj dane z tabeli SecurityEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Odczytaj dane z tabeli SecurityIoTRawEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Odczytaj dane z tabeli SecurityRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Odczytaj dane z tabeli ServiceFabricOperationalEvent |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Odczytaj dane z tabeli ServiceFabricReliableActorEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricReliableServiceEvent/Read | Odczytaj dane z tabeli ServiceFabricReliableServiceEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SfBAssessmentRecommendation/Read | Odczytaj dane z tabeli SfBAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli SfBOnlineAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Odczytaj dane z tabeli SharePointOnlineAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Odczytaj dane z tabeli SigninLogs |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Odczytaj dane z tabeli SPAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Odczytaj dane z tabeli SQLAssessmentRecommendation |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SQLQueryPerformance/Read | Odczytaj dane z tabeli SQLQueryPerformance |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Odczytaj dane z tabeli SqlThreatProtectionLoginAudits |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Odczytaj dane z tabeli SqlVulnerabilityAssessmentResult |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/SucceededIngestion/Read | Odczytaj dane z tabeli SucceededIngestion |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/dziennik systemowy/odczyt | Odczytaj dane z tabeli dziennika systemowego |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Odczytaj dane z tabeli SysmonEvent |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/tabele. Custom/Read | Odczytywanie danych z dowolnego dziennika niestandardowego |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/ThreatIntelligenceIndicator/Read | Odczytaj dane z tabeli ThreatIntelligenceIndicator |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Odczytaj dane z tabeli UAApp |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAComputer/Read | Odczytaj dane z tabeli UAComputer |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAComputerRank/Read | Odczytaj dane z tabeli UAComputerRank |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UADriver/Read | Odczytaj dane z tabeli UADriver |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Odczytaj dane z tabeli UADriverProblemCodes |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAFeedback/Read | Odczytaj dane z tabeli UAFeedback |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAHardwareSecurity/Read | Odczytaj dane z tabeli UAHardwareSecurity |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Odczytaj dane z tabeli UAIESiteDiscovery |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAOfficeAddIn/Read | Odczytaj dane z tabeli UAOfficeAddIn |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/UAProposedActionPlan/Read | Odczytaj dane z tabeli UAProposedActionPlan |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Odczytaj dane z tabeli UASysReqIssue |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Odczytaj dane z tabeli UAUpgradedComputer |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/Update/read | Odczytaj dane z tabeli aktualizacji |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Odczytaj dane z tabeli UpdateRunProgress |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Odczytaj dane z tabeli UpdateSummary |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/zapytania/użycie/odczyt | Odczytywanie danych z tabeli użycia |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/VMBoundPort/Read | Odczytaj dane z tabeli VMBoundPort |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/VMConnection/Read | Odczytaj dane z tabeli VMConnection |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/W3CIISLog/Read | Odczytaj dane z tabeli W3CIISLog |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Odczytaj dane z tabeli WaaSDeploymentStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Odczytaj dane z tabeli WaaSInsiderStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Odczytaj dane z tabeli WaaSUpdateStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Odczytaj dane z tabeli WDAVStatus |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WDAVThreat/Read | Odczytaj dane z tabeli WDAVThreat |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Odczytaj dane z tabeli WindowsClientAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Odczytaj dane z tabeli WindowsEvent |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WindowsFirewall/Read | Odczytaj dane z tabeli WindowsFirewall |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Odczytaj dane z tabeli WindowsServerAssessmentRecommendation |
> | Akcja | Microsoft.OperationalInsights/workspaces/query/WireData/read | Odczytaj dane z tabeli typowe |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WorkloadMonitoringPerf/Read | Odczytaj dane z tabeli WorkloadMonitoringPerf |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WUDOAggregatedStatus/Read | Odczytaj dane z tabeli WUDOAggregatedStatus |
> | Akcja | Microsoft. OperationalInsights/Workspaces/Query/WUDOStatus/Read | Odczytaj dane z tabeli WUDOStatus |
> | Akcja | Microsoft.OperationalInsights/workspaces/read | Pobiera istniejący obszar roboczy |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/regeneratesharedkey/akcja | Ponownie generuje klucz współużytkowany określonego obszaru roboczego |
> | Akcja | Microsoft. operationalinsights/obszary robocze/reguły/odczyt | Pobierz wszystkie reguły alertów. |
> | Akcja | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Usuwa zapisane zapytanie wyszukiwania |
> | Akcja | Microsoft.OperationalInsights/workspaces/savedSearches/read | Pobiera zapisane zapytanie wyszukiwania |
> | Akcja | microsoft.operationalinsights/workspaces/savedsearches/results/read | Pobierz wyniki zapisanych wyszukiwań. Przestarzałe |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Delete | Usuń zaplanowane akcje wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Read | Pobierz zaplanowane akcje wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Write | Utwórz lub zaktualizuj zaplanowane akcje wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Delete | Usuń zaplanowane wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Read | Pobierz zaplanowane wyszukiwania. |
> | Akcja | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Write | Utwórz lub zaktualizuj zaplanowane wyszukiwania. |
> | Akcja | Microsoft.OperationalInsights/workspaces/savedSearches/write | Tworzy zapytanie zapisanego wyszukiwania |
> | Akcja | Microsoft.OperationalInsights/workspaces/schema/read | Pobiera schemat wyszukiwania dla obszaru roboczego.  Schemat wyszukiwania obejmuje uwidocznione pola i ich typy. |
> | Akcja | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Akcja | Microsoft. operationalinsights/obszary robocze/wyszukiwanie/odczyt | Pobierz wyniki wyszukiwania. Przestarzałe. |
> | Akcja | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Delete | Usuwa konfigurację magazynu. Spowoduje to zatrzymanie przez program Microsoft Operational Insights odczytywania danych z konta magazynu. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Read | Pobiera konfigurację magazynu. |
> | Akcja | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Write | Tworzy nową konfigurację magazynu. Te konfiguracje są używane do ściągania danych z lokalizacji na istniejącym koncie magazynu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Pobierz dziennik błędów tłumaczenia aktualizacji wyszukiwania dla obszaru roboczego |
> | Akcja | Microsoft. OperationalInsights/obszary robocze/użycie/odczyt | Pobiera dane użycia dla obszaru roboczego, w tym ilość danych odczytanych przez obszar roboczy. |
> | Akcja | Microsoft.OperationalInsights/workspaces/write | Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącego obszaru roboczego. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. OperationsManagement/managementAssociations/Delete | Usuń istniejące skojarzenie zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementAssociations/read | Pobierz istniejące skojarzenie zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementAssociations/write | Utwórz nowe skojarzenie zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementConfigurations/delete | Usuń istniejącą konfigurację zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementConfigurations/read | Pobieranie istniejącej konfiguracji zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementConfigurations/write | Utwórz nową konfigurację zarządzania |
> | Akcja | Microsoft.OperationsManagement/register/action | Zarejestruj subskrypcję u dostawcy zasobów. |
> | Akcja | Microsoft.OperationsManagement/solutions/delete | Usuń istniejące rozwiązanie OMS |
> | Akcja | Microsoft.OperationsManagement/solutions/read | Pobierz wyjście z rozwiązania OMS |
> | Akcja | Microsoft.OperationsManagement/solutions/write | Utwórz nowe rozwiązanie OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.PolicyInsights/asyncOperationResults/read | Pobiera wynik operacji asynchronicznej. |
> | Akcja dataaction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Sprawdź stan zgodności danego składnika względem zasad dotyczących danych. |
> | Akcja | Microsoft.PolicyInsights/operations/read | Pobiera obsługiwane operacje w przestrzeni nazw Microsoft. PolicyInsights |
> | Akcja dataaction | Microsoft. PolicyInsights/policyEvents/logDataEvents/akcja | Rejestruj zdarzenia zasad składnika zasobów. |
> | Akcja | Microsoft.PolicyInsights/policyEvents/queryResults/action | Zapytanie informacji o zdarzeniach zasad. |
> | Akcja | Microsoft.PolicyInsights/policyEvents/queryResults/read | Zapytanie informacji o zdarzeniach zasad. |
> | Akcja | Microsoft.PolicyInsights/policyStates/queryResults/action | Zapytanie o informacje dotyczące stanów zasad. |
> | Akcja | Microsoft.PolicyInsights/policyStates/queryResults/read | Zapytanie o informacje dotyczące stanów zasad. |
> | Akcja | Microsoft. PolicyInsights/policyStates/podsumowanie/akcja | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych Stanów zasad. |
> | Akcja | Microsoft. PolicyInsights/policyStates/podsumowanie/odczyt | Wykonaj zapytanie o informacje podsumowujące dotyczące najnowszych Stanów zasad. |
> | Akcja | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Wyzwala nową ocenę zgodności dla wybranego zakresu. |
> | Akcja | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Zapytanie dotyczące zasobów wymaganych przez zasady DeployIfNotExistsymi. |
> | Akcja | Microsoft. PolicyInsights/Register/Action | Rejestruje dostawcę zasobów usługi Microsoft Policy Insights i włącza na nim akcje. |
> | Akcja | Microsoft. PolicyInsights/korygowania/anulowanie/akcja | Anuluj korygowanie zasad firmy Microsoft w toku. |
> | Akcja | Microsoft.PolicyInsights/remediations/delete | Usuń korygowanie zasad. |
> | Akcja | Microsoft.PolicyInsights/remediations/listDeployments/read | Wyświetla listę wdrożeń wymaganych przez korygowanie zasad. |
> | Akcja | Microsoft.PolicyInsights/remediations/read | Pobierz korygowanie zasad. |
> | Akcja | Microsoft.PolicyInsights/remediations/write | Utwórz lub zaktualizuj korekty zasad firmy Microsoft. |
> | Akcja | Microsoft. PolicyInsights/Unregister/Action | Wyrejestrowuje dostawcę zasobów usługi Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft.Portal

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
> | Akcja | Microsoft.Portal/usersettings/delete | Usuwa Cloud Shell ustawienia użytkownika. |
> | Akcja | Microsoft.Portal/usersettings/read | Odczytuje ustawienia użytkownika Cloud Shell. |
> | Akcja | Microsoft.Portal/usersettings/write | Utwórz lub zaktualizuj ustawienie użytkownika Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.PowerBIDedicated/capacities/delete | Usuwa dedykowaną pojemność Power BI. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/read | Pobiera informacje o określonej Power BI dedykowanej pojemności. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/resume/action | Wznawia pojemność. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/skus/read | Pobierz informacje o dostępnych jednostkach SKU dla pojemności |
> | Akcja | Microsoft.PowerBIDedicated/capacities/suspend/action | Wstrzymuje pojemność. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/write | Tworzy lub aktualizuje określony Power BI dedykowanej pojemności. |
> | Akcja | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Sprawdza, czy dana nazwa dedykowanej pojemności Power BI jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft.PowerBIDedicated/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | Akcja | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | Akcja | Microsoft.PowerBIDedicated/operations/read | Pobiera informacje o operacjach |
> | Akcja | Microsoft.PowerBIDedicated/register/action | Rejestruje Power BI dedykowanego dostawcę zasobów. |
> | Akcja | Microsoft.PowerBIDedicated/skus/read | Pobiera informacje o jednostkach SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | Akcja | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Akcja | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Akcja | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Utwórz chroniony element kopii zapasowej |
> | Akcja | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Akcja | Microsoft.RecoveryServices/Locations/backupStatus/action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Akcja | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Weryfikuj funkcje |
> | Akcja | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Sprawdź dostępność nazwy zasobu jest interfejsem API, aby sprawdzić, czy nazwa zasobu jest dostępna |
> | Akcja | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Akcja | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Akcja | Microsoft. RecoveryServices/Register/Action | Rejestruje subskrypcję dla danego dostawcy zasobów |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupconfig/read | Zwraca konfigurację magazynu Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupconfig/write | Aktualizuje konfigurację magazynu Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Usuń zamiar ochrony kopii zapasowej |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Pobierz cel ochrony kopii zapasowej |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Tworzenie opcji ochrony kopii zapasowej |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/operationsStatus/odczyt | Zwraca stan operacji |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery z ochroną |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Usuwa zarejestrowany kontener |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/operationsStatus/Read | Pobiera stan operacji wykonanej na kontenerze ochrony. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Wykonuje kopię zapasową dla chronionego elementu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Usuwa chroniony element |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Zapewnij natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Przywracanie punktów odzyskiwania dla elementów chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odwołaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Utwórz chroniony element kopii zapasowej |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Tworzy zarejestrowany kontener |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Anuluj zadanie |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zadania. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupJobs/operationsStatus/odczyt | Zwraca stan operacji zadania. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadań |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportuj zadania |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Zwraca wynik operacji tworzenia kopii zapasowej dla magazynu Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Akcja | Microsoft. RecoveryServices/magazyny/backupPolicies/usuwanie | Usuwanie zasad ochrony |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Tworzy zasady ochrony |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Zwraca listę wszystkich elementów podlegających ochronie. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Zwraca informacje zabezpieczającego numeru PIN dla magazynu Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Zwraca konfigurację magazynu dla magazynu Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Aktualizuje konfigurację magazynu dla magazynu Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Sprawdź poprawność operacji dla chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/delete | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Pobiera konfigurację powiadomień magazynu usługi Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfiguruje powiadomienia e-mail dla magazynu usługi Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Operacja Wyrejestruj kontener może służyć do wyrejestrowywania kontenera. |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operacji rejestrowania kontenera usługi można użyć do zarejestrowania kontenera z usługą odzyskiwania. |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Odczytaj wszystkie ustawienia alertów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Utwórz lub zaktualizuj ustawienia alertów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczytaj wszystkie zdarzenia |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność sieci szkieletowej |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Usuń wszystkie sieci szkieletowe |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Wdróż obraz serwera przetwarzania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrowanie sieci szkieletowej do usługi AAD |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/operationresults/odczyt | Śledź wyniki operacji asynchronicznej w sieciach szkieletowych zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Odczytaj wszystkie sieci szkieletowe |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skojarz ponownie bramę |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Usuń sieć szkieletową |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Odnów certyfikat sieci szkieletowej |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Odczytaj wszystkie sieci logiczne |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Odczytaj wszystkie sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Usuń wszystkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytaj wszystkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Utwórz lub zaktualizuj dowolne mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Odkryj element objęty ochroną |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/operationresults/Read | Śledź wyniki operacji asynchronicznej w kontenerach ochrony zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytaj wszystkie kontenery ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Usuń kontener ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Usuń wszystkie elementy migracji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrowanie elementu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Odczytaj wszystkie punkty odzyskiwania migracji |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na elementach migracji zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Odczytaj wszystkie elementy migracji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Migracja testowa |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Oczyszczanie migracji testów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Utwórz lub zaktualizuj dowolne elementy migracji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczytaj dowolne elementy podlegające ochronie |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Dodawanie dysków |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Zastosuj punkt odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Usuń wszystkie chronione elementy |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Zatwierdzanie trybu failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na elementach chronionych przez zasoby |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowana praca w trybie failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczytaj wszystkie chronione elementy |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Usuń chroniony element |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Usuń dyski |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Napraw replikację |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Włącz ponownie ochronę chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Prześlij opinię |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Odczytaj wszystkie docelowe rozmiary obliczeń |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test pracy w trybie failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Czyszczenie testu pracy w trybie failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizowanie usługi mobilności |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Utwórz lub zaktualizuj wszystkie chronione elementy |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Usuń wszystkie mapowania kontenerów ochrony |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na mapowaniach kontenerów ochrony zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Usuń mapowanie kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Utwórz lub zaktualizuj wszelkie mapowania kontenerów ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Przełącz kontener ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Tworzenie lub aktualizowanie wszelkich kontenerów ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Usuń wszystkich dostawców Recovery Services |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/operationresults/Read | Śledź wyniki operacji asynchronicznej dla dostawców Recovery Services zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczytaj wszystkich dostawców Recovery Services |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Odśwież dostawcę |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Usuń dostawcę Recovery Services |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Utwórz lub zaktualizuj wszelkich Recovery Services dostawców |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytaj wszystkie klasyfikacje magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Usuń wszystkie mapowania klasyfikacji magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na mapowaniach klasyfikacji magazynu zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Utwórz lub zaktualizuj dowolne mapowania klasyfikacji magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Usuń wszystkie vCenter |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/operationresults/Read | Śledź wyniki operacji asynchronicznej na serwerze vCenter zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Odczytaj dowolne vCenter |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Utwórz lub zaktualizuj dowolne vCenter |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Utwórz lub zaktualizuj wszystkie sieci szkieletowe |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Anuluj zadanie |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationJobs/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na zadaniach zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/read | Odczytaj wszystkie zadania |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationJobs/ponowne uruchomienie/akcja | Uruchom ponownie zadanie |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationJobs/wznowienie/akcja | Wznów zadanie |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Odczytaj wszystkie elementy migracji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Odczytaj wszystkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Odczytaj wszystkie sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Usuń wszystkie zasady |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationPolicies/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na zasadach zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Odczytaj wszystkie zasady |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Tworzenie lub aktualizowanie zasad |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Odczytaj wszystkie chronione elementy |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Odczytaj wszystkie kontenery ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Usuń wszystkie plany odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan odzyskiwania zatwierdzania w trybie failover |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na planach odzyskiwania zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan odzyskiwania planowanej pracy w trybie failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Zapoznaj się z planami odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Ponowne włączanie ochrony planu odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan odzyskiwania testowego trybu failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan odzyskiwania oczyszczania testów w trybie failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania w trybie failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Tworzenie lub aktualizowanie planów odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Odczytaj wszystkich dostawców Recovery Services |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Odczytaj wszystkie klasyfikacje magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Odczytaj wszystkie  |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationUsages/read | Odczytaj wszystkie użycia replikacji magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/operationresults/odczyt | Śledź wyniki operacji asynchronicznej na kondycji replikacji magazynu zasobów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Odczytaj wszystkie kondycje replikacji magazynu |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationVaultHealth/odświeżanie/akcja | Odśwież kondycję magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Odczytaj wszystkie  |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Utwórz lub zaktualizuj dowolne  |
> | Akcja | Microsoft. RecoveryServices/magazyny/replicationvCenters/odczyt | Odczytaj dowolne vCenter |
> | Akcja | Microsoft.RecoveryServices/vaults/usages/read | Odczytaj wszystkie użycia magazynu |
> | Akcja | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Akcja | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |

## <a name="microsoftrelay"></a>Microsoft.Relay

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
> | Akcja | Microsoft.Relay/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Action | Operacja zaktualizowania HybridConnection. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji HybridConnection |
> | Akcja | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do HybridConnection |
> | Akcja | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji HybridConnection |
> | Akcja | Microsoft. Relay/Namespaces/HybridConnections/reguł autoryzacji/regeneratekeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji/Write | Utwórz reguły autoryzacji HybridConnection i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/Delete | Operacja usuwania zasobu HybridConnection |
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
> | Akcja | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw ACS |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Action | Operacja zaktualizowania WcfRelay. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Delete | Operacja usuwania reguł autoryzacji WcfRelay |
> | Akcja | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do WcfRelay |
> | Akcja | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/Read |  Pobierz listę reguł autoryzacji WcfRelay |
> | Akcja | Microsoft. Relay/Namespaces/WcfRelays/reguł autoryzacji/regeneratekeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji/Write | Utwórz reguły autoryzacji WcfRelay i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/Delete | Operacja usuwania zasobu WcfRelay |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/Read | Pobierz listę opisów zasobów WcfRelay |
> | Akcja | Microsoft. Relay/przestrzenie nazw/WcfRelays/Write | Utwórz lub zaktualizuj właściwości WcfRelay. |
> | Akcja | Microsoft. Relay/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Akcja | Microsoft.Relay/operations/read | Pobierz operacje |
> | Akcja | Microsoft. Relay/Register/Action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i umożliwia tworzenie zasobów przekazywania |
> | Akcja | Microsoft. Relay/Unregister/Action | Rejestruje subskrypcję dostawcy zasobów usługi Relay i umożliwia tworzenie zasobów przekazywania |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Pobiera stan dostępności dla określonego zasobu |
> | Akcja | Microsoft.ResourceHealth/AvailabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Akcja | Microsoft.ResourceHealth/events/read | Pobierz zdarzenia Service Health dla danej subskrypcji |
> | Akcja | Microsoft. Resourcehealth/healthevent/Action | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft.Resourcehealth/healthevent/Activated/action | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft.Resourcehealth/healthevent/InProgress/action | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft. Resourcehealth/healthevent/Pending/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft. Resourcehealth/healthevent/rozwiązane/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft. Resourcehealth/healthevent/zaktualizowane/akcja | Wskazuje zmianę stanu kondycji dla określonego zasobu |
> | Akcja | Microsoft.ResourceHealth/impactedResources/read | Pobierz zasoby, których dotyczy problem dla danej subskrypcji |
> | Akcja | Microsoft.ResourceHealth/metadata/read | Pobiera metadane |
> | Akcja | Microsoft.ResourceHealth/Operations/read | Pobierz operacje dostępne dla programu Microsoft ResourceHealthe |
> | Akcja | Microsoft.ResourceHealth/register/action | Rejestruje subskrypcję programu Microsoft ResourceHealthe |
> | Akcja | Microsoft.ResourceHealth/unregister/action | Wyrejestrowuje subskrypcję programu Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. resources/calculateTemplateHash/Action | Oblicz wartość skrótu podanego szablonu. |
> | Akcja | Microsoft. resources/checkPolicyCompliance/Action | Sprawdź stan zgodności danego zasobu względem zasad zasobów. |
> | Akcja | Microsoft.Resources/checkResourceName/action | Sprawdź nazwę zasobu, aby sprawdzić poprawność. |
> | Akcja | Microsoft. resources/Deployments/Cancel/Action | Anuluje wdrożenie. |
> | Akcja | Microsoft. resources/Deployments/Delete | Usuwa wdrożenie. |
> | Akcja | Microsoft. resources/Deployments/exportTemplate/Action | Eksportuj szablon wdrożenia |
> | Akcja | Microsoft.Resources/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Akcja | Microsoft. resources/Deployments/operationstatuses/Read | Pobiera lub wyświetla stan operacji wdrożenia. |
> | Akcja | Microsoft.Resources/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | Akcja | Microsoft. resources/Deployments/Validate/Action | Sprawdza poprawność wdrożenia. |
> | Akcja | Microsoft. resources/Deployments/whatIf/Action | Przewiduje zmiany wdrożenia szablonu. |
> | Akcja | Microsoft. resources/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft.Resources/links/delete | Usuwa łącze zasobu. |
> | Akcja | Microsoft.Resources/links/read | Pobiera linki do zasobów lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/links/write | Tworzy lub aktualizuje łącze zasobu. |
> | Akcja | Microsoft. resources/Marketplace/Purchase/Action | Zakup zasobu z portalu Marketplace. |
> | Akcja | Microsoft.Resources/providers/read | Pobierz listę dostawców. |
> | Akcja | Microsoft.Resources/resources/read | Pobierz listę zasobów na podstawie filtrów. |
> | Akcja | Microsoft.Resources/subscriptions/locations/read | Pobiera listę obsługiwanych lokalizacji. |
> | Akcja | Microsoft.Resources/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | Akcja | Microsoft. resources/subscriptions/Providers/Read | Pobiera dostawców zasobów lub wyświetla ich listę. |
> | Akcja | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/delete | Usuwa grupę zasobów i wszystkie jej zasoby. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Pobiera lub wyświetla stan operacji wdrożenia. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Przenosi zasoby z jednej grupy zasobów do innej. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Pobiera zasoby dla grupy zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Weryfikowanie przenoszenia zasobów z jednej grupy zasobów do innej. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/write | Tworzy lub aktualizuje grupę zasobów. |
> | Akcja | Microsoft. resources/subscriptions/sources/Read | Pobiera zasoby subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/delete | Usuwa tag subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/read | Pobiera lub wyświetla Tagi subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Usuwa wartość tagu subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Pobiera lub wyświetla wartości tagów subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Dodaje wartość tagu subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/write | Dodaje tag subskrypcji. |
> | Akcja | Microsoft.Resources/tags/delete | Usuwa wszystkie znaczniki w zasobie. |
> | Akcja | Microsoft.Resources/tags/read | Pobiera wszystkie znaczniki w zasobie. |
> | Akcja | Microsoft.Resources/tags/write | Aktualizuje Tagi w zasobie, zastępując lub scalając istniejące Tagi nowym zestawem tagów lub usuwając istniejące Tagi. |
> | Akcja | Microsoft. resources/dzierżawcy/odczyt | Pobiera listę dzierżawców. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Scheduler/jobcollections/delete | Usuwa kolekcję zadań. |
> | Akcja | Microsoft. Scheduler/jobcollections/Disable/akcja | Wyłącza kolekcję zadań. |
> | Akcja | Microsoft. Scheduler/jobcollections/Włącz/akcja | Włącza zbieranie zadań. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/delete | Usuwa zadanie. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Generuje definicję aplikacji logiki na podstawie zadania usługi Scheduler. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Pobiera historię zadania. |
> | Akcja | Microsoft.Scheduler/jobcollections/jobs/read | Pobiera zadanie. |
> | Akcja | Microsoft. Scheduler/jobcollections/Jobs/Run/Action | Uruchamia zadanie. |
> | Akcja | Microsoft. Scheduler/jobcollections/Jobs/Write | Tworzy lub aktualizuje zadanie. |
> | Akcja | Microsoft.Scheduler/jobcollections/read | Pobierz kolekcję zadań |
> | Akcja | Microsoft. Scheduler/jobcollections/Write | Tworzy lub aktualizuje kolekcję zadań. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Search/checkNameAvailability/action | Sprawdza dostępność nazwy usługi. |
> | Akcja | Microsoft.Search/operations/read | Wyświetla wszystkie dostępne operacje dostawcy Microsoft. Search. |
> | Akcja | Microsoft. Search/Register/Action | Rejestruje subskrypcję dostawcy zasobów wyszukiwania i umożliwia tworzenie usług wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/createQueryKey/action | Tworzy klucz zapytania. |
> | Akcja | Microsoft.Search/searchServices/delete | Usuwa usługę wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/deleteQueryKey/delete | Usuwa klucz zapytania. |
> | Akcja | Microsoft. Search/searchServices/listAdminKeys/Action | Odczytuje klucze administratora. |
> | Akcja | Microsoft. Search/searchServices/listQueryKeys/Action | Zwraca listę kluczy interfejsu API zapytania dla danej usługi Wyszukiwanie poznawcze platformy Azure. |
> | Akcja | Microsoft.Search/searchServices/listQueryKeys/read | Zwraca listę kluczy interfejsu API zapytania dla danej usługi Wyszukiwanie poznawcze platformy Azure. |
> | Akcja | Microsoft.Search/searchServices/read | Odczytuje usługę wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/regenerateAdminKey/action | Generuje ponownie klucz administratora. |
> | Akcja | Microsoft. Search/searchServices/Start/akcja | Uruchamia usługę wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/stop/action | Powoduje zatrzymanie usługi wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/write | Tworzy lub aktualizuje usługę wyszukiwania. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Wymusza stosowanie zasad ograniczania ruchu przez tworzenie reguł zabezpieczeń zgodnych z podaną grupą zabezpieczeń sieci |
> | Akcja | Microsoft.Security/adaptiveNetworkHardenings/read | Pobiera adaptacyjne zalecenia dotyczące ograniczania przepustowości sieci chronionej przez platformę Azure |
> | Akcja | Microsoft.Security/advancedThreatProtectionSettings/read | Pobiera ustawienia zaawansowanej ochrony przed zagrożeniami dla zasobu |
> | Akcja | Microsoft.Security/advancedThreatProtectionSettings/write | Aktualizuje ustawienia zaawansowanej ochrony przed zagrożeniami dla zasobu |
> | Akcja | Microsoft. Security/Alerts/Read | Pobiera wszystkie dostępne alerty zabezpieczeń |
> | Akcja | Microsoft.Security/applicationWhitelistings/read | Pobiera aplikację whitelistings |
> | Akcja | Microsoft.Security/applicationWhitelistings/write | Tworzy nową aplikację listy dozwolonych lub aktualizuje istniejącą |
> | Akcja | Microsoft. Security/assessmentMetadata/Read | Uzyskaj dostępne metadane oceny zabezpieczeń w ramach subskrypcji |
> | Akcja | Microsoft. Security/assessmentMetadata/Write | Tworzenie lub aktualizowanie metadanych oceny zabezpieczeń |
> | Akcja | Microsoft. Security/Assessments/Read | Uzyskaj oceny zabezpieczeń w ramach subskrypcji |
> | Akcja | Microsoft. Security/Assessments/Write | Utwórz lub zaktualizuj oceny zabezpieczeń w ramach subskrypcji |
> | Akcja | Microsoft. Security/complianceResults/Read | Pobiera wyniki zgodności dla zasobu |
> | Akcja | Microsoft. Security/informationProtectionPolicies/Read | Pobiera zasady usługi Information Protection dla zasobu |
> | Akcja | Microsoft. Security/informationProtectionPolicies/Write | Aktualizuje zasady usługi Information Protection dla zasobu |
> | Akcja | Microsoft. Security/Locations/Alerts/Activate/Action | Aktywuj alert zabezpieczeń |
> | Akcja | Microsoft.Security/locations/alerts/dismiss/action | Odrzuć alert zabezpieczeń |
> | Akcja | Microsoft. Security/Locations/Alerts/Read | Pobiera wszystkie dostępne alerty zabezpieczeń |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Usuwa zasady dostępu just in Time do sieci |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicjuje żądanie zasady dostępu just in Time do sieci |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Pobiera zasady dostępu just in Time do sieci |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Tworzy nowe zasady dostępu just in Time do sieci lub aktualizuje istniejące |
> | Akcja | Microsoft. Security/Locations/Read | Pobiera lokalizację danych zabezpieczeń |
> | Akcja | Microsoft. Security/Locations/Tasks/Activate/Action | Aktywuj zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft.Security/locations/tasks/dismiss/action | Odrzuć zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft. Security/Locations/Tasks/Read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Akcja | Microsoft. zabezpieczenia/lokalizacje/zadania/Rozwiązywanie/akcja | Rozwiązywanie zalecenia dotyczącego zabezpieczeń |
> | Akcja | Microsoft. Security/Locations/Tasks/Start/Action | Rozpocznij zalecenie dotyczące zabezpieczeń |
> | Akcja | Microsoft. Security/Policies/Read | Pobiera zasady zabezpieczeń |
> | Akcja | Microsoft.Security/policies/write | Aktualizuje zasady zabezpieczeń |
> | Akcja | Microsoft. Security/cenniki/usuwanie | Usuwa ustawienia cen dla zakresu |
> | Akcja | Microsoft. zabezpieczenia/cenniki/odczyt | Pobiera ustawienia cen dla zakresu |
> | Akcja | Microsoft. zabezpieczenia/cenniki/zapis | Aktualizuje ustawienia cen dla zakresu |
> | Akcja | Microsoft. Security/Register/Action | Rejestruje subskrypcję dla Azure Security Center |
> | Akcja | Microsoft. Security/securityContacts/Delete | Usuwa kontakt zabezpieczeń |
> | Akcja | Microsoft. Security/securityContacts/Read | Pobiera kontakt zabezpieczeń |
> | Akcja | Microsoft. Security/securityContacts/Write | Aktualizuje kontakt zabezpieczeń |
> | Akcja | Microsoft.Security/securitySolutions/delete | Usuwa rozwiązanie zabezpieczeń |
> | Akcja | Microsoft.Security/securitySolutions/read | Pobiera rozwiązania zabezpieczeń |
> | Akcja | Microsoft.Security/securitySolutions/write | Tworzy nowe rozwiązanie zabezpieczeń lub aktualizuje istniejące |
> | Akcja | Microsoft.Security/securitySolutionsReferenceData/read | Pobiera dane referencyjne rozwiązań zabezpieczeń |
> | Akcja | Microsoft. Security/securityStatuses/Read | Pobiera stan kondycji zabezpieczeń dla zasobów platformy Azure |
> | Akcja | Microsoft. Security/securityStatusesSummaries/Read | Pobiera podsumowania stanu zabezpieczeń dla zakresu |
> | Akcja | Microsoft.Security/settings/read | Pobiera ustawienia dla zakresu |
> | Akcja | Microsoft.Security/settings/write | Aktualizuje ustawienia zakresu |
> | Akcja | Microsoft.Security/tasks/read | Pobiera wszystkie dostępne zalecenia dotyczące zabezpieczeń |
> | Akcja | Microsoft. Security/Unregister/Action | Wyrejestrowuje subskrypcję z Azure Security Center |
> | Akcja | Microsoft. Security/webApplicationFirewalls/Delete | Usuwa zaporę aplikacji sieci Web |
> | Akcja | Microsoft. Security/webApplicationFirewalls/Read | Pobiera zapory aplikacji sieci Web |
> | Akcja | Microsoft. Security/webApplicationFirewalls/Write | Tworzy nową zaporę aplikacji sieci Web lub aktualizuje istniejącą |
> | Akcja | Microsoft.Security/workspaceSettings/connect/action | Zmień ustawienia ponownego połączenia ustawień obszaru roboczego |
> | Akcja | Microsoft.Security/workspaceSettings/delete | Usuwa ustawienia obszaru roboczego |
> | Akcja | Microsoft.Security/workspaceSettings/read | Pobiera ustawienia obszaru roboczego |
> | Akcja | Microsoft.Security/workspaceSettings/write | Aktualizuje ustawienia obszaru roboczego |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettings/delete | Usuwanie ustawień diagnostycznych |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettings/read | Odczytywanie ustawień diagnostycznych |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettings/write | Zapisywanie ustawień diagnostycznych |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Odczytywanie kategorii ustawień diagnostycznych |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. ServiceBus/checkNameAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft. ServiceBus/checkNamespaceAvailability/Action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały zamiast tego użyj CheckNameAvailability. |
> | Akcja | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Usuwa reguły sieci wirtualnej w dostawcy zasobów ServiceBus dla określonej sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Action | Aktualizuje regułę autoryzacji przestrzeni nazw. Ten interfejs API jest przestarzały. Zamiast tego użyj wywołania PUT, aby zaktualizować regułę autoryzacji przestrzeni nazw. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Delete | Usuń regułę autoryzacji przestrzeni nazw. Nie można usunąć domyślnej reguły autoryzacji przestrzeni nazw.  |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/ListKeys/Action | Pobierz parametry połączenia do przestrzeni nazw |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Read | Pobierz Opis reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/regenerateKeys/Action | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. ServiceBus/Namespaces/reguł autoryzacji/Write | Utwórz reguły autoryzacji na poziomie przestrzeni nazw i zaktualizuj jej właściwości. Prawa dostępu do reguł autoryzacji, klucze podstawowe i pomocnicze, można aktualizować. |
> | Akcja | Microsoft.ServiceBus/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobiera klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i uniemożliwia replikację zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Usuwa konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. Tę operację można wywołać tylko za pośrednictwem podstawowej przestrzeni nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje geograficzną tryb failover odzyskiwania po awarii i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Usuwa filtr Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Pobiera Filtr Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Tworzy lub aktualizuje filtr Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft. ServiceBus/Namespaces/eventhubs/Read | Pobierz listę opisów zasobów EventHub |
> | Akcja | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Usuń zasób filtru IP |
> | Akcja | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Pobierz zasób filtru IP |
> | Akcja | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Utwórz zasób filtru IP |
> | Akcja dataaction | Microsoft. ServiceBus/przestrzenie nazw/komunikaty/odbieranie/akcja | Odbieranie komunikatów |
> | Akcja dataaction | Microsoft. ServiceBus/przestrzenie nazw/komunikaty/wysyłanie/akcja | Wysyłanie komunikatów |
> | Akcja | Microsoft. ServiceBus/Namespaces/messagingPlan/Read | Pobiera plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. ServiceBus/Namespaces/messagingPlan/Write | Aktualizuje plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości uwidocznione za pośrednictwem zasobu MessagingPlan są przenoszone do zasobu przestrzeni nazw (nadrzędny) w nowszych wersjach interfejsu API.<br>Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/migracja/akcja | Operacja migracji przestrzeni nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Usuwa konfigurację migracji. |
> | Akcja | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Pobiera konfigurację migracji, która wskazuje stan migracji i oczekujących operacji replikacji |
> | Akcja | Microsoft. ServiceBus/Namespaces/migrationConfigurations/Przywróć/akcja | Przywraca migrację przestrzeni nazw Standard do warstwy Premium |
> | Akcja | Microsoft. ServiceBus/Namespaces/migrationConfigurations/upgrade/Action | Przypisuje serwer DNS skojarzony z przestrzenią nazw w warstwie Premium, która kończy migrację i kończy synchronizowanie zasobów ze standardowego do Premium przestrzeni nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Tworzy lub aktualizuje konfigurację migracji. Spowoduje to rozpoczęcie synchronizowania zasobów ze standardowego do przestrzeni nazw Premium |
> | Akcja | Microsoft. ServiceBus/Namespaces/networkruleset/Delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/Namespaces/networkruleset/Read | Pobiera zasób NetworkRuleSet |
> | Akcja | Microsoft. ServiceBus/Namespaces/networkruleset/Write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/networkrulesets/read | Pobiera zasób NetworkRuleSet |
> | Akcja | Microsoft.ServiceBus/namespaces/networkrulesets/write | Utwórz zasób reguły sieci wirtualnej |
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
> | Akcja | Microsoft.ServiceBus/namespaces/queues/Delete | Operacja usuwania zasobu kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/read | Pobierz listę opisów zasobów kolejki |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/kolejki/zapis | Utwórz lub zaktualizuj właściwości kolejki. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/odczyt | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw ACS |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/akcja | Operacja zaktualizowania tematu. Ta operacja nie jest obsługiwana w interfejsie API w wersji 2017-04-01. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować regułę autoryzacji. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/usuwanie | Operacja usuwania tematu reguły autoryzacji |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/ListKeys/akcja | Pobierz parametry połączenia z tematem |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/odczyt |  Pobierz listę reguł autoryzacji tematów |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/regenerateKeys/akcja | Wygeneruj ponownie klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji/zapis | Tworzenie reguł autoryzacji tematów i aktualizowanie ich właściwości. Prawa dostępu do reguł autoryzacji można aktualizować. |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/Delete | Operacja usuwania zasobu tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/read | Pobierz listę opisów zasobów tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operacja usuwania zasobu TopicSubscription |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/subskrypcje/odczyt | Pobierz listę opisów zasobów TopicSubscription |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operacja usuwania zasobu reguły |
> | Akcja | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/odczyt | Pobierz listę opisów zasobów reguł |
> | Akcja | Microsoft. ServiceBus/obszary nazw/tematy/subskrypcje/reguły/zapis | Utwórz lub zaktualizuj właściwości reguły. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/subskrypcje/zapis | Utwórz lub zaktualizuj właściwości TopicSubscription. |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/tematy/zapis | Utwórz lub zaktualizuj właściwości tematu. |
> | Akcja | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Usuń zasób reguły sieci wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Pobiera zasób reguły sieci wirtualnej |
> | Akcja | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Utwórz zasób reguły sieci wirtualnej |
> | Akcja | Microsoft. ServiceBus/przestrzenie nazw/zapis | Utwórz zasób przestrzeni nazw i zaktualizuj jego właściwości. Tagi i pojemność przestrzeni nazw to właściwości, które można zaktualizować. |
> | Akcja | Microsoft. ServiceBus/Operations/Read | Pobierz operacje |
> | Akcja | Microsoft. ServiceBus/Register/Action | Rejestruje subskrypcję dostawcy zasobów ServiceBus i umożliwia tworzenie zasobów ServiceBus |
> | Akcja | Microsoft.ServiceBus/sku/read | Pobierz listę opisów zasobów jednostki SKU |
> | Akcja | Microsoft.ServiceBus/sku/regions/read | Pobierz listę opisów zasobów SkuRegions |
> | Akcja | Microsoft. ServiceBus/Unregister/Action | Rejestruje subskrypcję dostawcy zasobów ServiceBus i umożliwia tworzenie zasobów ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/delete | Usuń dowolną aplikację |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/read | Odczytaj dowolną aplikację |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/delete | Usuwanie dowolnej usługi |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Odczytaj dowolną partycję |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Odczytaj dowolną replikę |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/read | Odczytaj dowolną usługę |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Odczytaj wszystkie stany usług |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/services/write | Utwórz lub zaktualizuj dowolną usługę |
> | Akcja | Microsoft.ServiceFabric/clusters/applications/write | Utwórz lub zaktualizuj dowolną aplikację |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Usuń dowolny typ aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/read | Odczytaj dowolny typ aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Usuń wszystkie wersje typu aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Odczytaj dowolną wersję typu aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Utwórz lub zaktualizuj dowolną wersję typu aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/applicationTypes/write | Utwórz lub zaktualizuj dowolny typ aplikacji |
> | Akcja | Microsoft.ServiceFabric/clusters/delete | Usuń dowolny klaster |
> | Akcja | Microsoft.ServiceFabric/clusters/nodes/read | Odczytaj dowolny węzeł |
> | Akcja | Microsoft.ServiceFabric/clusters/read | Odczytaj dowolny klaster |
> | Akcja | Microsoft.ServiceFabric/clusters/statuses/read | Odczytaj wszystkie Stany klastra |
> | Akcja | Microsoft.ServiceFabric/clusters/write | Utwórz lub zaktualizuj dowolny klaster |
> | Akcja | Microsoft.ServiceFabric/locations/clusterVersions/read | Odczytaj dowolną wersję klastra |
> | Akcja | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Odczytaj dowolną wersję klastra dla określonego środowiska |
> | Akcja | Microsoft.ServiceFabric/locations/operationresults/read | Odczytaj wszystkie wyniki operacji |
> | Akcja | Microsoft.ServiceFabric/locations/operations/read | Odczytaj wszystkie operacje według lokalizacji |
> | Akcja | Microsoft.ServiceFabric/operations/read | Odczytaj wszystkie dostępne operacje |
> | Akcja | Microsoft.ServiceFabric/register/action | Zarejestruj dowolną akcję |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. SignalRService/Locations/checknameavailability/Action | Sprawdza, czy nazwa jest dostępna do użycia z nową usługą sygnalizującego |
> | Akcja | Microsoft.SignalRService/locations/operationresults/signalr/read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Akcja | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Akcja | Microsoft.SignalRService/locations/usages/read | Pobierz użycie przydziału dla usługi Azure Signal Service |
> | Akcja | Microsoft.SignalRService/operationresults/read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Akcja | Microsoft. SignalRService/Operations/Read | Wyświetl listę operacji usługi Azure Signal Service. |
> | Akcja | Microsoft.SignalRService/operationstatus/read | Wykonywanie zapytania dotyczącego stanu operacji asynchronicznej |
> | Akcja | Microsoft. SignalRService/Register/Action | Rejestruje dostawcę zasobów "Microsoft. SignalRService" z subskrypcją |
> | Akcja | Microsoft.SignalRService/SignalR/delete | Usuwanie całej usługi sygnalizującej |
> | Akcja | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Usuń filtr usługi Event Grid ze sygnalizującego. |
> | Akcja | Microsoft.SignalRService/SignalR/eventGridFilters/read | Pobierz właściwości określonego filtru usługi Event Grid lub Wyświetl listę wszystkich filtrów usługi Event Grid dla określonego sygnalizującego. |
> | Akcja | Microsoft.SignalRService/SignalR/eventGridFilters/write | Utwórz lub zaktualizuj filtr usługi Event Grid dla sygnalizującego o określonych parametrach. |
> | Akcja | Microsoft.SignalRService/SignalR/listkeys/action | Wyświetlanie wartości kluczy dostępu sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Akcja | Microsoft.SignalRService/SignalR/read | Wyświetlanie ustawień i konfiguracji sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Akcja | Microsoft.SignalRService/SignalR/regeneratekey/action | Zmiana wartości kluczy dostępu sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Akcja | Microsoft. SignalRService/sygnalizujący/Uruchom ponownie/akcję | Aby ponownie uruchomić usługę sygnałów platformy Azure w portalu zarządzania lub za pomocą interfejsu API. Zostanie określony przestój. |
> | Akcja | Microsoft. SignalRService/sygnalizujący/Write | Modyfikowanie ustawień i konfiguracji sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | Akcja | Microsoft. SignalRService/Unregister/Action | Wyrejestrowuje dostawcę zasobów "Microsoft. SignalRService" z subskrypcją |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Wyświetla listę artefaktów aplikacji definicji aplikacji. |
> | Akcja | Microsoft.Solutions/applicationDefinitions/delete | Usuwa definicję aplikacji. |
> | Akcja | Microsoft.Solutions/applicationDefinitions/read | Pobiera listę definicji aplikacji. |
> | Akcja | Microsoft.Solutions/applicationDefinitions/write | Dodaj lub zmodyfikuj definicję aplikacji. |
> | Akcja | Microsoft.Solutions/applications/applicationArtifacts/read | Wyświetla listę artefaktów aplikacji. |
> | Akcja | Microsoft.Solutions/applications/delete | Usuwa aplikację. |
> | Akcja | Microsoft.Solutions/applications/read | Pobiera listę aplikacji. |
> | Akcja | Microsoft.Solutions/applications/refreshPermissions/action | Odświeża uprawnienia aplikacji. |
> | Akcja | Microsoft.Solutions/applications/updateAccess/action | Aktualizuje dostęp do aplikacji. |
> | Akcja | Microsoft.Solutions/applications/write | Tworzy aplikację. |
> | Akcja | Microsoft.Solutions/jitRequests/delete | Usuń JitRequest |
> | Akcja | Microsoft.Solutions/jitRequests/read | Pobiera listę JitRequests |
> | Akcja | Microsoft.Solutions/jitRequests/write | Tworzy element JitRequest |
> | Akcja | Microsoft.Solutions/locations/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft. Solutions/Register/Action | Zarejestruj się w rozwiązaniach. |
> | Akcja | Microsoft. Solutions/Unregister/Action | Wyrejestrowuje z rozwiązań. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Sql/checkNameAvailability/action | Sprawdź, czy dana nazwa serwera jest dostępna do aprowizacji na całym świecie dla danej subskrypcji. |
> | Akcja | Microsoft.Sql/instancePools/delete | Usuwa pulę wystąpień |
> | Akcja | Microsoft.Sql/instancePools/read | Pobiera pulę wystąpień |
> | Akcja | Microsoft.Sql/instancePools/usages/read | Pobiera informacje o użyciu puli wystąpień |
> | Akcja | Microsoft.Sql/instancePools/write | Tworzy lub aktualizuje pulę wystąpień |
> | Akcja | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Akcja | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Pobierz wynik operacji ustawiania zasad inspekcji obiektu BLOB serwera |
> | Akcja | Microsoft. SQL/lokalizacje/możliwości/odczyt | Pobiera możliwości dla tej subskrypcji w danej lokalizacji |
> | Akcja | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/locations/databaseOperationResults/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Pobiera operacje w toku na usuniętym serwerze |
> | Akcja | Microsoft.Sql/locations/deletedServerOperationResults/read | Pobiera operacje w toku na usuniętym serwerze |
> | Akcja | Microsoft.Sql/locations/deletedServers/read | Zwróć listę usuniętych serwerów lub pobiera właściwości dla określonego usuniętego serwera. |
> | Akcja | Microsoft.Sql/locations/deletedServers/recover/action | Odzyskiwanie usuniętego serwera |
> | Akcja | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Pobiera asynchroniczną operację platformy Azure dla operacji asynchronicznej puli elastycznej |
> | Akcja | Microsoft.Sql/locations/elasticPoolOperationResults/read | Pobiera wynik operacji puli elastycznej. |
> | Akcja | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania danych transparentu |
> | Akcja | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania danych transparentu |
> | Akcja | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Akcja | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Pobierz wynik operacji ustawiania zasad inspekcji rozszerzonego obiektu BLOB serwera |
> | Akcja | Microsoft. SQL/Locations/firewallRulesAzureAsyncOperation/Read | Pobiera stan operacji reguły zapory. |
> | Akcja | Microsoft.Sql/locations/firewallRulesOperationResults/read | Pobiera stan operacji reguły zapory. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/delete | Usuwa istniejącą grupę trybu failover wystąpienia. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Wykonuje planowaną pracę w trybie failover w istniejącej grupie trybu failover wystąpienia. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszone przejście w tryb failover w istniejącej grupie trybu failover wystąpienia. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/read | Zwraca listę grup trybu failover wystąpienia lub pobiera właściwości dla określonego wystąpienia grupy trybu failover. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/write | Tworzy grupę trybu failover wystąpienia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej grupy trybu failover. |
> | Akcja | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Pobiera stan operacji puli wystąpień |
> | Akcja | Microsoft.Sql/locations/instancePoolOperationResults/read | Pobiera wynik operacji puli wystąpień |
> | Akcja | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Zwraca szczegółowe informacje o określonym interfejsie operacji asynchronicznej platformy Azure |
> | Akcja | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Zwraca szczegóły podanej operacji profilu punktu końcowego interfejsu |
> | Akcja | Microsoft. SQL/Locations/jobAgentAzureAsyncOperation/Read | Pobiera stan operacji agenta zadań. |
> | Akcja | Microsoft.Sql/locations/jobAgentOperationResults/read | Pobiera wynik operacji agenta zadań. |
> | Akcja | Microsoft.Sql/locations/longTermRetentionBackups/read | Wyświetla długoterminowe kopie zapasowe przechowywania dla każdej bazy danych na każdym serwerze w lokalizacji |
> | Akcja | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Wyświetla kopie zapasowe przechowywania długoterminowego dla każdej bazy danych na serwerze |
> | Akcja | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Usuwa długoterminową kopię zapasową przechowywania |
> | Akcja | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Wyświetla kopie zapasowe przechowywania długoterminowego dla bazy danych |
> | Akcja | Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/Action | Kończy operację przywracania zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania wystąpienia zarządzanego szyfrowania danych |
> | Akcja | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Pobiera operacje w toku dotyczące funkcji ochrony szyfrowania wystąpienia zarządzanego szyfrowania danych |
> | Akcja | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Pobiera operacje w toku na przezroczystych kluczach wystąpienia zarządzanego szyfrowania danych |
> | Akcja | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Pobiera operacje w toku na przezroczystych kluczach wystąpienia zarządzanego szyfrowania danych |
> | Akcja | Microsoft. SQL/Locations/managedTransparentDataEncryptionAzureAsyncOperation/Read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Pobiera wynik operacji połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Pobiera wynik operacji serwera proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft.Sql/locations/read | Pobiera dostępne lokalizacje dla danej subskrypcji |
> | Akcja | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Akcja | Microsoft.Sql/locations/serverKeyOperationResults/read | Pobiera operacje w toku dla kluczy serwera przezroczystego szyfrowania danych |
> | Akcja | Microsoft.Sql/locations/syncAgentOperationResults/read | Pobierz wynik operacji zasobu agenta synchronizacji |
> | Akcja | Microsoft.Sql/locations/syncDatabaseIds/read | Pobieranie identyfikatorów bazy danych synchronizacji dla określonego regionu i subskrypcji |
> | Akcja | Microsoft.Sql/locations/syncGroupOperationResults/read | Pobierz wynik operacji zasobu grupy synchronizacji |
> | Akcja | Microsoft.Sql/locations/syncMemberOperationResults/read | Pobierz wynik operacji synchronizacji zasobu elementu członkowskiego |
> | Akcja | Microsoft.Sql/locations/usages/read | Pobiera kolekcję metryk użycia dla tej subskrypcji w lokalizacji |
> | Akcja | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Zwraca szczegóły określonych reguł sieci wirtualnej Azure asynchronicznej operacji  |
> | Akcja | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Zwraca szczegóły określonej operacji dotyczącej reguł sieci wirtualnej  |
> | Akcja | Microsoft. SQL/managedInstances/Administratorzy/usuwanie | Usuwa istniejącego administratora wystąpienia zarządzanego. |
> | Akcja | Microsoft. SQL/managedInstances/Administratorzy/odczyt | Pobiera listę administratorów wystąpienia zarządzanego. |
> | Akcja | Microsoft. SQL/managedInstances/Administratorzy/zapis | Tworzy lub aktualizuje administratora wystąpienia zarządzanego o określonych parametrach. |
> | Akcja | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Pobiera krótkoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Aktualizuje krótkoterminowe zasady przechowywania dla zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/kolumny/odczyt | Zwracanie listy kolumn dla zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/Write | Etykiety czułości aktualizacji partii |
> | Akcja | Microsoft.Sql/managedInstances/databases/delete | Usuwa istniejącą zarządzaną bazę danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/managedInstances/Databases/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla baz danych wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/databases/read | Pobiera istniejącą zarządzaną bazę danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/Write | Etykiety czułości zalecanych aktualizacji wsadowych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/restoreDetails/Read | Zwraca szczegóły przywracania zarządzanej bazy danych, gdy przywracanie jest w toku. |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/read | Pobierz schemat zarządzanej bazy danych. |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Pobieranie kolumny zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Usuń etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Disable/Action | Wyłącz zalecenia dotyczące czułości w danej kolumnie |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Włącz/akcja | Włącz zalecenia dotyczące czułości w danej kolumnie |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Pobierz etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/zapis | Utwórz lub zaktualizuj etykietę czułości danej kolumny |
> | Akcja | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Pobieranie tabeli zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń zarządzanych baz danych skonfigurowanych dla danego serwera |
> | Akcja | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń bazy danych dla danej zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/securityEvents/read | Pobiera zdarzenia zabezpieczeń zarządzanej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/Read | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/Read | Pobierz szczegóły Transparent Data Encryption bazy danych w danej zarządzanej bazie danych |
> | Akcja | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/Write | Zmień Transparent Data Encryption bazy danych dla danej zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Usuń ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Pobieranie zasad oceny luk w zabezpieczeniach na givendatabase |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Pobierz linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Zmień linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Przekonwertuj istniejący wynik skanowania na format możliwy do odczytania przez człowieka. Jeśli już nie istnieje, nic się nie dzieje |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Zwróć listę rekordów skanowania oceny luk w zabezpieczeniach bazy danych lub Pobierz rekord skanowania pod kątem określonego identyfikatora skanowania. |
> | Akcja | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/write | Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych. |
> | Akcja | Microsoft.Sql/managedInstances/delete | Usuwa istniejące wystąpienie zarządzane. |
> | Akcja | Microsoft.Sql/managedInstances/encryptionProtector/read | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/akcja | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft.Sql/managedInstances/encryptionProtector/write | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft.Sql/managedInstances/keys/delete | Usuwa istniejący klucz wystąpienia zarządzanego Azure SQL. |
> | Akcja | Microsoft.Sql/managedInstances/keys/read | Zwróć listę kluczy wystąpienia zarządzanego lub pobiera właściwości dla określonego klucza wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/managedInstances/keys/write | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/managedInstances/metricDefinitions/read | Pobierz definicje metryk wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/metrics/read | Pobierz metryki wystąpienia zarządzanego |
> | Akcja | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/logDefinitions/Read | Pobiera dostępne dzienniki dla wystąpień zarządzanych |
> | Akcja | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla wystąpień zarządzanych |
> | Akcja | Microsoft.Sql/managedInstances/read | Zwróć listę wystąpień zarządzanych lub pobiera właściwości dla określonego wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/managedInstances/recoverableDatabases/read | Zwraca listę możliwych do odzyskania zarządzanych baz danych |
> | Akcja | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Pobiera krótkoterminowe zasady przechowywania dla porzuconej zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Aktualizuje krótkoterminowe zasady przechowywania dla porzuconej zarządzanej bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Zwraca listę dostępnych usuniętych zarządzanych baz danych. |
> | Akcja | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń serwera zarządzanego skonfigurowanych dla danego serwera |
> | Akcja | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera zarządzanego dla danego serwera zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/tdeCertificates/action | Utwórz/zaktualizuj certyfikat TDE |
> | Akcja | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Usuń ocenę luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Pobieranie zasad oceny luk w zabezpieczeniach w danym wystąpieniu zarządzanym |
> | Akcja | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danego wystąpienia zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/write | Tworzy wystąpienie zarządzane o określonych parametrach lub aktualizuje właściwości lub Tagi dla określonego wystąpienia zarządzanego. |
> | Akcja | Microsoft.Sql/operations/read | Pobiera dostępne operacje REST |
> | Akcja | Microsoft. SQL/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft.Sql/register/action | Rejestruje subskrypcję dostawcy zasobów programu Microsoft SQL Database i umożliwia tworzenie baz danych Microsoft SQL. |
> | Akcja | Microsoft.Sql/servers/administrators/delete | Usuwa określony obiekt administratora Azure Active Directory |
> | Akcja | Microsoft.Sql/servers/administrators/read | Pobiera określony obiekt administratora Azure Active Directory |
> | Akcja | Microsoft.Sql/servers/administrators/write | Dodaje lub aktualizuje określony obiekt administratora Azure Active Directory |
> | Akcja | Microsoft.Sql/servers/advisors/read | Zwraca listę doradców dostępnych dla serwera |
> | Akcja | Microsoft.Sql/servers/advisors/recommendedActions/read | Zwraca listę zalecanych akcji określonego klasyfikatora dla serwera |
> | Akcja | Microsoft.Sql/servers/advisors/recommendedActions/write | Zastosuj zalecaną akcję na serwerze |
> | Akcja | Microsoft.Sql/servers/advisors/write | Aktualizuje stan autouruchomienia klasyfikatora na poziomie serwera. |
> | Akcja | Microsoft.Sql/servers/auditingPolicies/read | Pobierz szczegóły domyślnych zasad inspekcji tabeli serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft.Sql/servers/auditingPolicies/write | Zmień domyślną inspekcję tabeli serwera dla danego serwera |
> | Akcja | Microsoft.Sql/servers/auditingSettings/operationResults/read | Pobierz wynik operacji ustawiania zasad inspekcji obiektu BLOB serwera |
> | Akcja | Microsoft.Sql/servers/auditingSettings/read | Pobierz szczegóły zasad inspekcji obiektów BLOB serwera skonfigurowanych na danym serwerze |
> | Akcja | Microsoft.Sql/servers/auditingSettings/write | Zmień inspekcję obiektu BLOB serwera dla danego serwera |
> | Akcja | Microsoft.Sql/servers/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania dla serwera. |
> | Akcja | Microsoft.Sql/servers/automaticTuning/write | Aktualizuje ustawienia dostrajania automatycznego dla serwera i zwraca zaktualizowane ustawienia |
> | Akcja | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Usuwa istniejący magazyn archiwum kopii zapasowych. |
> | Akcja | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Ta operacja służy do pobierania magazynu długoterminowego przechowywania kopii zapasowych. Zwraca informacje o magazynie zarejestrowanym na tym serwerze |
> | Akcja | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ta operacja służy do rejestrowania magazynu długoterminowego przechowywania kopii zapasowej na serwerze programu. |
> | Akcja | Microsoft. SQL/serwery/communicationLinks/Delete | Usuwa istniejące łącze komunikacji z serwerem. |
> | Akcja | Microsoft. SQL/serwery/communicationLinks/odczyt | Zwróć listę linków komunikacyjnych określonego serwera. |
> | Akcja | Microsoft. SQL/serwery/communicationLinks/Write | Utwórz lub zaktualizuj łącze do komunikacji z serwerem. |
> | Akcja | Microsoft.Sql/servers/connectionPolicies/read | Zwróć listę zasad połączenia serwera określonego serwera. |
> | Akcja | Microsoft.Sql/servers/connectionPolicies/write | Utwórz lub zaktualizuj zasady połączenia z serwerem. |
> | Akcja | Microsoft.Sql/servers/databases/advisors/read | Zwraca listę doradców dostępnych dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Zwraca listę zalecanych akcji określonego klasyfikatora dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Zastosuj zalecaną akcję w bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/advisors/write | Aktualizowanie stanu autouzupełniania usługi Advisor na poziomie bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/auditingPolicies/read | Pobierz szczegóły zasad inspekcji tabeli skonfigurowanych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/auditingPolicies/write | Zmień zasady inspekcji tabeli dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/auditingSettings/read | Pobierz szczegóły zasad inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/auditingSettings/write | Zmień zasady inspekcji obiektów BLOB dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualizuje ustawienia dostrajania automatycznego dla bazy danych i zwraca zaktualizowane ustawienia |
> | Akcja | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Zwróć listę zasad archiwizacji kopii zapasowych określonej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Utwórz lub zaktualizuj Zasady archiwizacji kopii zapasowej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Pobiera krótkoterminowe zasady przechowywania dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Aktualizuje krótkoterminowe zasady przechowywania dla bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/kolumny/odczyt | Zwracanie listy kolumn dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/connectionPolicies/read | Pobierz szczegóły zasad połączenia skonfigurowanych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/connectionPolicies/write | Zmień zasady połączenia dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Etykiety czułości aktualizacji partii |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Zwróć listę zasad maskowania danych bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Usuń regułę zasad maskowania danych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Pobierz szczegóły reguły zasad maskowania danych skonfigurowanej w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Zmień regułę zasad maskowania danych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Zmień zasady maskowania danych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Zwraca informacje o kroku zapytania rozproszonego dotyczące zapytania hurtowni danych dla wybranego identyfikatora kroku |
> | Akcja | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Zwraca informacje o zapytaniu dystrybucji magazynu danych dla wybranego identyfikatora zapytania |
> | Akcja | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Pobiera działania użytkownika wystąpienia SQL Data Warehouse, w tym uruchomione i wstrzymane zapytania |
> | Akcja | Microsoft.Sql/servers/databases/delete | Usuwa istniejącą bazę danych. |
> | Akcja | Microsoft.Sql/servers/databases/export/action | Eksportuj Azure SQL Database |
> | Akcja | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Pobierz szczegóły zasad rozszerzonej inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Zmiana zasad rozszerzonej inspekcji obiektów BLOB dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/extensions/read | Pobiera kolekcję rozszerzeń dla bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/rozszerzenia/zapis | Zmień rozszerzenie dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/tryb failover/akcja | Klient przełączenia w tryb failover bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Pobierz zasady geograficznej kopii zapasowej dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Tworzenie lub aktualizowanie zasad tworzenia kopii zapasowej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/importExportOperationResults/read | Pobiera operacje importu/eksportu w toku |
> | Akcja | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Pobiera listę dostępnych okien obsługi dla wybranej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/maintenanceWindows/read | Pobiera ustawienia okien obsługi dla wybranej bazy danych. |
> | Akcja | Microsoft. SQL/serwery/bazy danych/maintenanceWindows/Write | Ustawia ustawienia okien obsługi dla wybranej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/metrics/read | Metryki zwracane dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/move/action | Zmień nazwę istniejącej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/operationResults/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/operations/cancel/action | Anuluje Azure SQL Database oczekującej operacji asynchronicznej, która nie została jeszcze zakończona. |
> | Akcja | Microsoft.Sql/servers/databases/operations/read | Zwróć listę operacji wykonanych na bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/pause/action | Wstrzymywanie bazy danych usługi Azure SQL DataWarehouse |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla baz danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Zwraca kolekcję tekstów zapytania, które odpowiadają określonym parametrom. |
> | Akcja | Microsoft.Sql/servers/databases/queryStore/read | Zwraca bieżące wartości ustawień magazynu zapytań dla bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/queryStore/write | Aktualizuje ustawienie magazynu zapytań dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/read | Zwróć listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/recommendedSensitivityLabels/Write | Etykiety czułości zalecanych aktualizacji wsadowych |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/delete | Zakończenie wymuszonej relacji replikacji z potencjalną utratą danych |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Przełączenie w tryb failover po zsynchronizowaniu wszystkich zmian z poziomu podstawowego, co sprawia, że ta baza danych jest podzielona na relationship\u0027s replikacji i udostępnianie zdalnego elementu głównego do pomocniczego |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Natychmiastowe przejście w tryb failover z potencjalną utratą danych, dzięki czemu ta baza danych jest relationship\u0027s replikacją podstawową i udostępniająca ją jako pomocniczy. |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/read | Zwróć listę linków replikacji lub pobiera właściwości dla określonych linków replikacji. |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Wymuszone zakończenie relacji replikacji lub po zsynchronizowaniu z partnerem |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Aktualizowanie trybu replikacji dla łącza do trybu synchronicznego lub asynchronicznego |
> | Akcja | Microsoft.Sql/servers/databases/restorePoints/action | Tworzy nowy punkt przywracania |
> | Akcja | Microsoft.Sql/servers/databases/restorePoints/delete | Usuwa punkt przywracania bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/restorePoints/read | Zwraca punkty przywracania bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/resume/action | Wznów bazę danych usługi Azure SQL DataWarehouse |
> | Akcja | Microsoft.Sql/servers/databases/schemas/read | Pobierz schemat bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Pobierz kolumnę bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Usuń etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Disable/Action | Wyłącz zalecenia dotyczące czułości w danej kolumnie |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Włącz/akcja | Włącz zalecenia dotyczące czułości w danej kolumnie |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Pobierz etykietę czułości danej kolumny |
> | Akcja | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/Write | Utwórz lub zaktualizuj etykietę czułości danej kolumny |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/read | Pobierz tabelę bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Pobierz listę zaleceń dotyczących indeksów w bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Zastosuj zalecenie dotyczące indeksu |
> | Akcja | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń bazy danych skonfigurowanych dla danego serwera |
> | Akcja | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń bazy danych dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/securityMetrics/read | Pobiera kolekcję metryk zabezpieczeń bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/sensitivityLabels/read | Wyświetl etykiety czułości danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Zwróć sugestię dotyczącą skalowania bazy danych w górę lub w dół na podstawie statystyk wykonywania zapytań w celu zwiększenia wydajności lub obniżenia kosztów |
> | Akcja | Microsoft.Sql/servers/databases/skus/read | Pobiera kolekcję jednostek SKU dostępnych dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Anuluj synchronizację grupy synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/delete | Usuwa istniejącą grupę synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Zwróć listę schematów bazy danych Centrum synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/logs/read | Zwróć listę dzienników grup synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/read | Zwróć listę grup synchronizacji lub pobiera właściwości dla określonej grupy synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Odśwież schemat bazy danych Centrum synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu narzędzia Sync Hub |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Usuwa istniejący element członkowski synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Zwróć listę elementów członkowskich synchronizacji lub pobiera właściwości dla określonego elementu członkowskiego synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Odśwież schemat elementu członkowskiego synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu elementu członkowskiego synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Zwróć listę schematów bazy danych elementu członkowskiego synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Tworzy element członkowski synchronizacji z określonymi parametrami lub aktualizuje właściwości określonego elementu członkowskiego synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Wyzwalanie synchronizacji grupy synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/write | Tworzy grupę synchronizacji z określonymi parametrami lub aktualizuje właściwości określonej grupy synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/topQueries/queryText/action | Zwraca tekst Transact-SQL dla wybranego identyfikatora zapytania |
> | Akcja | Microsoft.Sql/servers/databases/topQueries/read | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Akcja | Microsoft.Sql/servers/databases/topQueries/statistics/read | Zwraca zagregowane statystyki środowiska uruchomieniowego dla wybranego zapytania w wybranym okresie |
> | Akcja | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Pobiera operacje w toku dotyczące przezroczystego szyfrowania danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/odczyt | Pobierz stan i szczegóły funkcji zabezpieczenia szyfrowania danych przezroczystych dla danej bazy danych |
> | Akcja | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/Write | Zmień stan przezroczystego szyfrowania danych |
> | Akcja | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Uaktualnianie bazy danych usługi Azure SQL DataWarehouse |
> | Akcja | Microsoft.Sql/servers/databases/usages/read | Pobiera informacje o użyciach Azure SQL Database |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Usuń ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Pobieranie zasad oceny luk w zabezpieczeniach na givendatabase |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Pobierz linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Zmień linię bazową reguły oceny luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Przekonwertuj istniejący wynik skanowania na format możliwy do odczytania przez człowieka. Jeśli już nie istnieje, nic się nie dzieje |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Zwróć listę rekordów skanowania oceny luk w zabezpieczeniach bazy danych lub Pobierz rekord skanowania pod kątem określonego identyfikatora skanowania. |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Wykonaj skanowanie bazy danych ocen luk w zabezpieczeniach. |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Pobieranie wyniku operacji skanowania oceny luk w zabezpieczeniach bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Pobierz szczegóły oceny luk w zabezpieczeniach skonfigurowanej w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Zmień ocenę luk w zabezpieczeniach dla danej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/write | Tworzy bazę danych z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej bazy danych. |
> | Akcja | Microsoft.Sql/servers/delete | Usuwa istniejący serwer. |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Usuwa istniejące konfiguracje odzyskiwania po awarii dla danego serwera |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Przełączenie w tryb failover DisasterRecoveryConfiguration |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Wymuś tryb failover DisasterRecoveryConfiguration |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Pobiera kolekcję konfiguracji odzyskiwania po awarii, która obejmuje ten serwer |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Zmień konfigurację odzyskiwania po awarii serwera |
> | Akcja | Microsoft.Sql/servers/elasticPoolEstimates/read | Zwraca listę oszacowań elastycznej puli już utworzonych dla tego serwera |
> | Akcja | Microsoft.Sql/servers/elasticPoolEstimates/write | Tworzy nowe szacowanie puli elastycznej dla listy dostarczonych baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/read | Zwraca listę doradców dostępnych dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Zwraca listę zalecanych akcji określonego klasyfikatora dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Zastosuj zalecaną akcję w puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/write | Aktualizacja stanu autouruchomienia klasyfikatora na poziomie puli elastycznej. |
> | Akcja | Microsoft.Sql/servers/elasticPools/databases/read | Pobiera listę baz danych dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/delete | Usuń istniejącą pulę elastyczną |
> | Akcja | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Pobieranie działań i szczegółów w danej elastycznej puli baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Pobierz działania i szczegóły dotyczące danej bazy danych będącej częścią elastycznej puli baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/failover/action | Klient zainicjował pracę awaryjną puli elastycznej. |
> | Akcja | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Zwracaj typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/metrics/read | Metryki zwracane dla elastycznych pul baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Anuluje oczekującą operację asynchroniczną w puli elastycznej SQL platformy Azure, która nie została jeszcze zakończona. |
> | Akcja | Microsoft.Sql/servers/elasticPools/operations/read | Zwróć listę operacji wykonywanych w puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/read | Pobierz szczegóły puli elastycznej na danym serwerze |
> | Akcja | Microsoft.Sql/servers/elasticPools/skus/read | Pobiera kolekcję jednostek SKU dostępnych dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/write | Utwórz nowe lub zmień właściwości istniejącej puli elastycznej |
> | Akcja | Microsoft.Sql/servers/encryptionProtector/read | Zwraca listę funkcji ochrony szyfrowania serwera lub pobiera właściwości dla określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft. SQL/serwery/encryptionProtector/ponownie Zweryfikuj/akcja | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft.Sql/servers/encryptionProtector/write | Zaktualizuj właściwości określonej ochrony szyfrowania serwera. |
> | Akcja | Microsoft.Sql/servers/extendedAuditingSettings/read | Pobierz szczegóły zasad inspekcji rozszerzonego serwera obiektów BLOB skonfigurowanych na danym serwerze |
> | Akcja | Microsoft.Sql/servers/extendedAuditingSettings/write | Zmień rozszerzoną inspekcję obiektów BLOB serwera dla danego serwera |
> | Akcja | Microsoft.Sql/servers/failoverGroups/delete | Usuwa istniejącą grupę trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/failover/action | Wykonuje planowaną pracę w trybie failover w istniejącej grupie trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszone przejście w tryb failover w istniejącej grupie trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/read | Zwraca listę grup trybu failover lub pobiera właściwości dla określonej grupy trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/write | Tworzy grupę trybu failover z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej grupy trybu failover. |
> | Akcja | Microsoft. SQL/serwery/firewallRules/Delete | Usuwa istniejącą regułę zapory serwera. |
> | Akcja | Microsoft. SQL/serwery/firewallRules/odczyt | Zwróć listę reguł zapory serwera lub pobiera właściwości dla określonej reguły zapory serwera. |
> | Akcja | Microsoft. SQL/serwery/firewallRules/Write | Tworzy regułę zapory serwera z określonymi parametrami, aktualizuje właściwości określonej reguły lub zastępuje wszystkie istniejące reguły przy użyciu nowych reguł zapory serwera. |
> | Akcja | Microsoft. SQL/serwery/import/akcja | Utwórz nową bazę danych na serwerze i Wdróż schemat oraz dane z pakietu DacPac |
> | Akcja | Microsoft.Sql/servers/importExportOperationResults/read | Pobiera operacje importu/eksportu w toku |
> | Akcja | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Usuwa określony profil punktu końcowego interfejsu |
> | Akcja | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Zwraca właściwości dla określonego profilu punktu końcowego interfejsu |
> | Akcja | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Tworzy profil punktu końcowego interfejsu z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego punktu końcowego interfejsu |
> | Akcja | Microsoft.Sql/servers/jobAgents/delete | Usuwa agenta zadań usługi Azure SQL DB |
> | Akcja | Microsoft.Sql/servers/jobAgents/read | Pobiera agenta zadań usługi Azure SQL DB |
> | Akcja | Microsoft.Sql/servers/jobAgents/write | Tworzy lub aktualizuje agenta zadań usługi Azure SQL DB |
> | Akcja | Microsoft.Sql/servers/keys/delete | Usuwa istniejący klucz serwera. |
> | Akcja | Microsoft.Sql/servers/keys/read | Zwróć listę kluczy serwera lub pobiera właściwości dla określonego klucza serwera. |
> | Akcja | Microsoft.Sql/servers/keys/write | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego klucza serwera. |
> | Akcja | Microsoft.Sql/servers/operationResults/read | Pobiera operacje serwera w toku |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Usuwa istniejący serwer proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Zwraca listę proxy połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Sprawdza poprawność wywołania połączenia z prywatnym punktem końcowym po stronie NRP |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Tworzy prywatny serwer proxy połączenia z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera proxy połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnections/delete | Usuwa istniejące połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnections/read | Zwraca listę połączeń prywatnych punktów końcowych lub pobiera właściwości dla określonego połączenia prywatnego punktu końcowego. |
> | Akcja | Microsoft.Sql/servers/privateEndpointConnections/write | Zatwierdza lub odrzuca istniejące połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. SQL/serwery/privateEndpointConnectionsApproval/akcja | Określa, czy użytkownik może zatwierdzić połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft.Sql/servers/privateLinkResources/read | Pobierz zasoby linku prywatnego dla odpowiedniego programu SQL Server |
> | Akcja | Microsoft. SQL/serwery/dostawcy/Microsoft. Insights/metricDefinitions/Read | Zwracaj typy metryk, które są dostępne dla serwerów |
> | Akcja | Microsoft.Sql/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Pobierz metryki dla zalecanych elastycznych pul baz danych dla danego serwera |
> | Akcja | Microsoft.Sql/servers/recommendedElasticPools/read | Pobieranie zalecenia dla elastycznych pul baz danych w celu ograniczenia kosztów lub zwiększenia wydajności w oparciu o historyczne wykorzystanie zasobów |
> | Akcja | Microsoft.Sql/servers/recoverableDatabases/read | Ta operacja służy do odzyskiwania po awarii usługi Live Database w celu przywrócenia bazy danych do ostatniego znanego dobrego punktu kopii zapasowej. Zwraca informacje o ostatniej dobrej kopii zapasowej, ale doesn\u0027t w rzeczywistości przywraca bazę danych. |
> | Akcja | Microsoft.Sql/servers/replicationLinks/read | Zwróć listę linków replikacji lub pobiera właściwości dla określonych linków replikacji. |
> | Akcja | Microsoft.Sql/servers/restorableDroppedDatabases/read | Pobierz listę baz danych, które zostały porzucone na danym serwerze, które są nadal w ramach zasad przechowywania. |
> | Akcja | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Pobierz wyniki operacji zapisu zasad wykrywania zagrożeń serwera |
> | Akcja | Microsoft.Sql/servers/securityAlertPolicies/read | Pobieranie listy zasad wykrywania zagrożeń serwera skonfigurowanych dla danego serwera |
> | Akcja | Microsoft.Sql/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.Sql/servers/serviceObjectives/read | Pobierz listę celów poziomu usług (nazywanych również warstwami wydajności) dostępnych na danym serwerze |
> | Akcja | Microsoft. SQL/serwery/syncAgents/Delete | Usuwa istniejącego agenta synchronizacji. |
> | Akcja | Microsoft.Sql/servers/syncAgents/generateKey/action | Generuj klucz rejestracji agenta synchronizacji |
> | Akcja | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Zwróć listę połączonych baz danych agenta synchronizacji |
> | Akcja | Microsoft.Sql/servers/syncAgents/read | Zwróć listę agentów synchronizacji lub pobiera właściwości dla określonego agenta synchronizacji. |
> | Akcja | Microsoft. SQL/serwery/syncAgents/Write | Tworzy agenta synchronizacji z określonymi parametrami lub aktualizuje właściwości określonego agenta synchronizacji. |
> | Akcja | Microsoft.Sql/servers/tdeCertificates/action | Utwórz/zaktualizuj certyfikat TDE |
> | Akcja | Microsoft.Sql/servers/usages/read | Limit przydziału jednostek DTU serwera i bieżące użycie jednostek DTU przez wszystkie bazy danych na serwerze |
> | Akcja | Microsoft.Sql/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę Virtual Network |
> | Akcja | Microsoft.Sql/servers/virtualNetworkRules/read | Zwróć listę reguł sieci wirtualnej lub pobiera właściwości dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft.Sql/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonej reguły sieci wirtualnej. |
> | Akcja | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Usuń ocenę luk w zabezpieczeniach dla danego serwera |
> | Akcja | Microsoft.Sql/servers/vulnerabilityAssessments/read | Pobierz zasady oceny luk w zabezpieczeniach na danym serwerze |
> | Akcja | Microsoft.Sql/servers/vulnerabilityAssessments/write | Zmiana oceny luk w zabezpieczeniach dla danego serwera |
> | Akcja | Microsoft.Sql/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości lub Tagi dla określonego serwera. |
> | Akcja | Microsoft.Sql/unregister/action | Wyrejestrowuje subskrypcję dostawcy zasobów SQL Database firmy Microsoft i umożliwia tworzenie baz danych Microsoft SQL. |
> | Akcja | Microsoft. SQL/virtualClusters/Delete | Usuwa istniejący klaster wirtualny. |
> | Akcja | Microsoft. SQL/virtualClusters/odczyt | Zwróć listę klastrów wirtualnych lub pobiera właściwości dla określonego klastra wirtualnego. |
> | Akcja | Microsoft. SQL/virtualClusters/Write | Aktualizuje Tagi klastra wirtualnego. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Storage/checknameavailability/Read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft.Storage/locations/checknameavailability/read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft. Storage, że sieć wirtualna lub podsieć jest usuwana |
> | Akcja | Microsoft.Storage/locations/usages/read | Zwraca limit i bieżącą liczbę użycia dla zasobów w określonej subskrypcji |
> | Akcja | Microsoft. Storage/Operations/Read | Sonduje stan operacji asynchronicznej. |
> | Akcja | Microsoft. Storage/Register/Action | Rejestruje subskrypcję dostawcy zasobów magazynu i umożliwia tworzenie kont magazynu. |
> | Akcja | Microsoft.Storage/skus/read | Wyświetla listę jednostek SKU obsługiwanych przez firmę Microsoft. Storage. |
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
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ustaw blokadę prawną kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik poprawki kontenera obiektów BLOB |
> | Akcja | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik umieszczenia kontenera obiektów BLOB. |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla usługi BLOB Service |
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
> | Akcja dataaction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/zapis | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | Akcja | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Akcja | Microsoft.Storage/storageAccounts/fileServices/read | Pobierz właściwości usługi plików |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/udziały/usuwanie |  |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/udziały/odczyt |  |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/udziały/odczyt |  |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/udziały/zapis |  |
> | Akcja | Microsoft. Storage/storageAccounts/fileServices/Write |  |
> | Akcja | Microsoft. Storage/storageAccounts/listAccountSas/akcja | Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/listServiceSas/akcja | Zwraca token sygnatury dostępu współdzielonego usługi dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/managementPolicies/delete | Usuwanie zasad zarządzania kontami magazynu |
> | Akcja | Microsoft.Storage/storageAccounts/managementPolicies/read | Pobierz Zasady konta zarządzania magazynem |
> | Akcja | Microsoft. Storage/storageAccounts/managementPolicies/Write | Umieść zasady zarządzania kontami magazynu |
> | Akcja | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Delete |  |
> | Akcja | Microsoft. Storage/storageAccounts/objectReplicationPolicies/odczyt |  |
> | Akcja | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Usuwanie prywatnych serwerów proxy połączeń punktu końcowego |
> | Akcja | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/odczyt | Pobierz serwer proxy połączenia prywatnego punktu końcowego |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Umieść proxy połączeń prywatnych punktów końcowych |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Usuń połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Pobierz połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Umieść połączenie prywatnego punktu końcowego |
> | Akcja | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/akcja | Zatwierdzanie połączeń prywatnych punktów końcowych |
> | Akcja | Microsoft.Storage/storageAccounts/privateLinkResources/read | Pobierz StorageAccount groupids |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/Queues/Delete | Zwraca wynik usunięcia kolejki. |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Add/Action | Zwraca wynik dodania komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Delete | Zwraca wynik usunięcia komunikatu |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Process/Action | Zwraca wynik przetwarzania komunikatu |
> | Akcja dataaction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Zwraca komunikat |
> | Akcja dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Write | Zwraca wynik zapisania komunikatu |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/Queues/Write | Zwraca wynik zapisania kolejki |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/odczyt | Pobierz usługa kolejki właściwości |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/odczyt | Zwraca właściwości usługi kolejki lub dane statystyczne. |
> | Akcja | Microsoft. Storage/storageAccounts/queueServices/Write | Zwraca wynik ustawienia właściwości usługi kolejki |
> | Akcja | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/regeneratekey/action | Generuje ponownie klucze dostępu dla określonego konta magazynu. |
> | Akcja | Microsoft. Storage/storageAccounts/restoreBlobRanges/akcja | Przywróć zakresy obiektów BLOB do stanu określonego czasu |
> | Akcja | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Odwołuje wszystkie klucze delegowania użytkowników dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Utwórz/zaktualizuj ustawienia diagnostyczne konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/tableServices/read | Pobierz Table service właściwości |
> | Akcja | Microsoft. Storage/storageAccounts/Write | Tworzy konto magazynu z określonymi parametrami lub aktualizuje właściwości lub Tagi albo dodaje niestandardową domenę dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/usages/read | Zwraca limit i bieżącą liczbę użycia dla zasobów w określonej subskrypcji |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | microsoft.storagesync/locations/checkNameAvailability/action | Sprawdza, czy nazwa usługi synchronizacji magazynu jest prawidłowa i czy nie jest używana. |
> | Akcja | microsoft.storagesync/locations/workflows/operations/read | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft. storagesync/Operations/Read | Pobiera listę obsługiwanych operacji |
> | Akcja | Microsoft. storagesync/Register/Action | Rejestruje subskrypcję dostawcy synchronizacji magazynu |
> | Akcja | microsoft.storagesync/storageSyncServices/delete | Usuń wszystkie usługi synchronizacji magazynu |
> | Akcja | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. Insights/metricDefinitions/Read | Pobiera dostępne metryki dla usług synchronizacji magazynu |
> | Akcja | microsoft.storagesync/storageSyncServices/read | Odczytaj wszystkie usługi synchronizacji magazynu |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/delete | Usuń wszystkie zarejestrowane serwery |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla zarejestrowanego serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/read | Odczytaj wszystkie zarejestrowane serwery |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/write | Utwórz lub zaktualizuj dowolny zarejestrowany serwer |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Usuń wszystkie punkty końcowe chmury |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Pobiera stan asynchronicznej operacji tworzenia kopii zapasowej/przywracania |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Wywołaj tę akcję po utworzeniu kopii zapasowej |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Wywołaj tę akcję po przywróceniu |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Wywołaj tę akcję przed wykonaniem kopii zapasowej |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Wywołaj tę akcję przed przywróceniem |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Odczytaj wszystkie punkty końcowe w chmurze |
> | Akcja | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/Action | Przywróć puls |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Utwórz lub zaktualizuj wszystkie punkty końcowe w chmurze |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/delete | Usuń wszystkie grupy synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla grup synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/read | Odczytaj wszystkie grupy synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Usuń wszystkie punkty końcowe serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla punktów końcowych serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Odczytaj wszystkie punkty końcowe serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Wywołaj tę akcję, aby odwołać pliki do serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Utwórz lub zaktualizuj wszystkie punkty końcowe serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/write | Utwórz lub zaktualizuj wszystkie grupy synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Pobiera stan operacji asynchronicznej |
> | Akcja | microsoft.storagesync/storageSyncServices/workflows/operations/read | Pobiera stan operacji asynchronicznej |
> | Akcja | microsoft.storagesync/storageSyncServices/workflows/read | Odczytaj przepływy pracy |
> | Akcja | microsoft.storagesync/storageSyncServices/write | Utwórz lub zaktualizuj wszystkie usługi synchronizacji magazynu |
> | Akcja | Microsoft. storagesync/Unregister/Action | Wyrejestrowuje subskrypcję dostawcy synchronizacji magazynu |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/delete | Usuwa rekordy Access Control |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/read | Wyświetla lub pobiera Access Control rekordy |
> | Akcja | Microsoft. StorSimple/kierownicy/accessControlRecords/Write | Tworzenie lub aktualizowanie rekordów Access Control |
> | Akcja | Microsoft.StorSimple/managers/alerts/read | Wyświetla lub pobiera alerty |
> | Akcja | Microsoft.StorSimple/managers/backups/read | Wyświetla lub Pobiera zestaw kopii zapasowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/bandwidthSettings/Usuń | Usuwa istniejące ustawienia przepustowości (tylko seria 8000) |
> | Akcja | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft.StorSimple/managers/bandwidthSettings/read | Wyświetl listę ustawień przepustowości (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/kierownicy/bandwidthSettings/Write | Tworzy nowe lub aktualizuje ustawienia przepustowości (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/certyfikaty/zapis | Utwórz lub zaktualizuj certyfikaty |
> | Akcja | Microsoft. StorSimple/menedżerowie/certyfikaty/zapis | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Akcja | Microsoft. StorSimple/kierownicy/clearAlerts/akcja | Wyczyść wszystkie alerty skojarzone z menedżerem urządzeń. |
> | Akcja | Microsoft. StorSimple/kierownicy/cloudApplianceConfigurations/odczyt | Wyświetl listę obsługiwanych konfiguracji urządzenia w chmurze |
> | Akcja | Microsoft.StorSimple/managers/configureDevice/action | Konfiguruje urządzenie |
> | Akcja | Microsoft. StorSimple/menedżerowie/usuwanie | Usuwa menedżerów urządzeń |
> | Akcja | Microsoft. StorSimple/menedżerowie/usuwanie | Operacja Usuń magazyn usuwa określony zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/alertSettings/read | Wyświetla lub pobiera ustawienia alertu |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/alertSettings/zapis | Utwórz lub zaktualizuj ustawienia alertu |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/authorizeForServiceEncryptionKeyRollover/akcja | Autoryzuj do przerzucania kluczy szyfrowania usługi |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/kopia zapasowa/akcja | Wykonaj ręczną kopię zapasową, aby utworzyć kopię zapasową wszystkich woluminów chronionych przez zasady. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/Usuń | Usuwa istniejące zasady tworzenia kopii zapasowych (tylko seria 8000) |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/odczyt | Utwórz listę zasad tworzenia kopii zapasowych (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/harmonogramy/usuwanie | Usuwa istniejące harmonogramy |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/harmonogramy/odczyt | Wyświetlanie listy harmonogramów |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/backupPolicies/harmonogramy/zapis | Tworzy harmonogramy nowych lub aktualizacji |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/backupPolicies/zapis | Tworzy nowe lub aktualizuje zasady tworzenia kopii zapasowych (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/usuwanie | Usuwa zestaw kopii zapasowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/elementy/klon/akcja | Klonowanie udziału lub woluminu przy użyciu elementu kopii zapasowej. |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/read | Wyświetla lub Pobiera zestaw kopii zapasowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/kopie zapasowe/przywracanie/akcja | Przywróć wszystkie woluminy z zestawu kopii zapasowych. |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Usuwa grupy harmonogramów kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Wyświetla lub pobiera grupy harmonogramów kopii zapasowych |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/backupScheduleGroups/zapis | Tworzenie lub aktualizowanie grup harmonogramów kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/delete | Usuwa ustawienia protokołu CHAP |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/read | Wyświetla lub pobiera ustawienia protokołu CHAP |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/chapSettings/zapis | Utwórz lub zaktualizuj ustawienia protokołu CHAP |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/dezaktywowanie/akcja | Dezaktywuje urządzenie. |
> | Akcja | Microsoft.StorSimple/managers/devices/delete | Usuwa urządzenia |
> | Akcja | Microsoft.StorSimple/managers/devices/disks/read | Wyświetla lub pobiera dyski |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/pobieranie/akcja | Pobierz aktualizacje dla urządzenia. |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/tryb failover/akcja | Przełączenie urządzenia w tryb failover. |
> | Akcja | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/failoverTargets/read | Wyświetla lub Pobiera elementy docelowe trybu failover urządzeń |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/kopia zapasowa/akcja | Utwórz kopię zapasową serwera plików. |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/delete | Usuwa serwery plików |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/read | Wyświetla lub pobiera serwery plików |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/usuwanie | Usuwa udziały |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/fileservers/udziały/zapis | Utwórz lub zaktualizuj udziały |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/fileservers/zapis | Utwórz lub zaktualizuj serwery plików |
> | Akcja | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Zmień stan elektrowni sprzętowych grup składników |
> | Akcja | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/hardwareComponentGroups/odczyt | Wyświetl listę grup składników sprzętowych |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/instalacja/akcja | Zainstaluj aktualizacje na urządzeniu. |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/installUpdates/akcja | Instaluje aktualizacje na urządzeniach (tylko seria 8000). |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/kopia zapasowa/akcja | Utwórz kopię zapasową serwera iSCSI. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/Usuń | Usuwa serwery iSCSI |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/dyski/usuwanie | Usuwa dyski |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/iscsiservers/dyski/odczyt | Wyświetla lub pobiera dyski |
> | Akcja | Microsoft. StorSimple/kierownicy/Devices/iscsiservers/disks/Write | Utwórz lub zaktualizuj dyski |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/metricsDefinitions/odczyt | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/operationResults/odczyt | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/iscsiservers/odczyt | Wyświetla lub pobiera serwery iSCSI |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/iscsiservers/zapis | Utwórz lub zaktualizuj serwery iSCSI |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/zadania/Anuluj/akcja | Anuluj uruchomione zadanie |
> | Akcja | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/jobs/read | Wyświetla lub pobiera zadania |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/listFailoverSets/akcja | Wyświetl listę zestawów trybu failover dla istniejącego urządzenia (tylko seria 8000). |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/listFailoverTargets/akcja | Utwórz listę elementów docelowych trybu failover dla urządzeń (tylko seria 8000). |
> | Akcja | Microsoft.StorSimple/managers/devices/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Potwierdza pomyślną migrację i jej zatwierdzenie. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Wyświetlenie listy Potwierdź stan migracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Pobierz potwierdzenie stanu migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Pobierz stan zadania szacowania migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Pobierz stan migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importuj konfiguracje źródeł do migracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Wystaw oszacowanie migracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Wyświetl stan migracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/migrationSourceConfigurations/startMigration/akcja | Rozpocznij migrację przy użyciu konfiguracji źródła |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Rozpocznij zadanie, aby oszacować czas trwania procesu migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/networkSettings/read | Wyświetla lub pobiera ustawienia sieci |
> | Akcja | Microsoft.StorSimple/managers/devices/networkSettings/write | Tworzy nowe lub aktualizuje ustawienia sieci |
> | Akcja | Microsoft.StorSimple/managers/devices/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Wyświetl publiczny klucz szyfrowania Menedżera urządzeń |
> | Akcja | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Opublikuj pakiet pomocy technicznej dla istniejącego urządzenia. Pakiet pomocy technicznej StorSimple to łatwy w użyciu mechanizm, który gromadzi wszystkie odpowiednie dzienniki, aby ułatwić pomoc techniczna firmy Microsoft Rozwiązywanie problemów z urządzeniami StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/devices/read | Wyświetla lub pobiera urządzenia |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/scanForUpdates/akcja | Skanuj w poszukiwaniu aktualizacji na urządzeniu. |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/read | Wyświetl listę ustawień zabezpieczeń |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Zsynchronizuj certyfikat zarządzania zdalnego dla urządzenia. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/securitySettings/aktualizacja/akcja | Zaktualizuj ustawienia zabezpieczeń. |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/write | Tworzy nowe lub aktualizuje ustawienia zabezpieczeń |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/sendTestAlertEmail/akcja | Wyślij wiadomość e-mail z alertem testowym do skonfigurowanych adresatów wiadomości e-mail. |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/udziały/odczyt | Wyświetla lub pobiera udziały |
> | Akcja | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft.StorSimple/managers/devices/timeSettings/read | Wyświetla lub pobiera ustawienia czasu |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/timeSettings/zapis | Tworzy nowe lub zaktualizowane ustawienia czasu |
> | Akcja | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/updateSummary/odczyt | Wyświetla lub pobiera podsumowanie aktualizacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/Usuń | Usuwa istniejące kontenery woluminów (tylko seria 8000) |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Wyświetl metryki |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/metricsDefinitions/odczyt | Wyświetl listę definicji metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/odczyt | Utwórz listę kontenerów woluminów (tylko seria 8000) |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/usuwanie | Usuwa istniejące woluminy |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Wyświetl metryki |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/metricsDefinitions/odczyt | Wyświetl listę definicji metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Wyświetl listę wyników operacji |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/odczyt | Wyświetl listę woluminów |
> | Akcja | Microsoft. StorSimple/menedżerowie/urządzenia/volumeContainers/woluminy/zapis | Tworzy nowe lub aktualizuje woluminy |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/volumeContainers/zapis | Tworzy nowe lub zaktualizowane kontenery woluminów (tylko seria 8000) |
> | Akcja | Microsoft.StorSimple/managers/devices/volumes/read | Wyświetl listę woluminów |
> | Akcja | Microsoft. StorSimple/kierownicy/urządzenia/zapis | Utwórz lub zaktualizuj urządzenia |
> | Akcja | Microsoft.StorSimple/managers/encryptionSettings/read | Wyświetla lub pobiera ustawienia szyfrowania |
> | Akcja | Microsoft. StorSimple/menedżerowie/extendedInformation/Usuń | Usuwa informacje o rozszerzonym magazynie |
> | Akcja | Microsoft. StorSimple/menedżerowie/extendedInformation/Usuń | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft. StorSimple/kierownicy/extendedInformation/odczyt | Wyświetla lub pobiera rozszerzone informacje o magazynie |
> | Akcja | Microsoft. StorSimple/kierownicy/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft. StorSimple/kierownicy/extendedInformation/Write | Utwórz lub zaktualizuj informacje o rozszerzonym magazynie |
> | Akcja | Microsoft. StorSimple/kierownicy/extendedInformation/Write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Akcja | Microsoft. StorSimple/menedżerowie/funkcje/odczyt | Wyświetl listę funkcji |
> | Akcja | Microsoft.StorSimple/managers/fileservers/read | Wyświetla lub pobiera serwery plików |
> | Akcja | Microsoft.StorSimple/managers/getEncryptionKey/action | Pobierz klucz szyfrowania dla Menedżera urządzeń. |
> | Akcja | Microsoft. StorSimple/kierownicy/iscsiservers/odczyt | Wyświetla lub pobiera serwery iSCSI |
> | Akcja | Microsoft.StorSimple/managers/jobs/read | Wyświetla lub pobiera zadania |
> | Akcja | Microsoft.StorSimple/managers/listActivationKey/action | Pobiera klucz aktywacji Menedżer urządzeń StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Wyświetl listę publicznych kluczy szyfrowania StorSimple Menedżer urządzeń. |
> | Akcja | Microsoft.StorSimple/managers/metrics/read | Wyświetla lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/metricsDefinitions/read | Wyświetla lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrowanie klasyczne do Menedżer zasobów menedżerów |
> | Akcja | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Wyświetl listę konfiguracji źródła migracji (tylko seria 8000) |
> | Akcja | Microsoft.StorSimple/managers/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft. StorSimple/kierownicy/provisionCloudAppliance/akcja | Utwórz nowe urządzenie w chmurze. |
> | Akcja | Microsoft.StorSimple/managers/read | Wyświetla lub pobiera menedżerów urządzeń |
> | Akcja | Microsoft.StorSimple/Managers/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft.StorSimple/managers/regenerateActivationKey/action | Wygeneruj ponownie klucz aktywacji dla istniejących Menedżer urządzeń StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Usuwa poświadczenia konta magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/read | Wyświetla lub Pobiera poświadczenia konta magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/write | Utwórz lub zaktualizuj poświadczenia konta magazynu |
> | Akcja | Microsoft. StorSimple/menedżerowie/storageDomains/Usuń | Usuwa domeny magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Wyświetla lub pobiera wyniki operacji |
> | Akcja | Microsoft.StorSimple/managers/storageDomains/read | Wyświetla lub pobiera domeny magazynu |
> | Akcja | Microsoft. StorSimple/kierownicy/storageDomains/Write | Utwórz lub zaktualizuj domeny magazynu |
> | Akcja | Microsoft. StorSimple/menedżerowie/zapis | Utwórz lub zaktualizuj menedżerów urządzeń |
> | Akcja | Microsoft. StorSimple/menedżerowie/zapis | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |
> | Akcja | Microsoft.StorSimple/operations/read | Wyświetla lub pobiera operacje |
> | Akcja | Microsoft. StorSimple/Register/Action | Zarejestruj dostawcę Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.StreamAnalytics/locations/quotas/Read | Odczytaj Stream Analytics przydziału subskrypcji |
> | Akcja | Microsoft. StreamAnalytics/Operations/Read | Odczyt Stream Analytics operacji |
> | Akcja | Microsoft. StreamAnalytics/Register/Action | Zarejestruj subskrypcję za pomocą dostawcy zasobów Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Delete | Usuwanie zadania Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Usuwanie funkcji zadania Stream Analytics |
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
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Usuń dane wyjściowe zadania Stream Analytics |
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
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Usuń transformację zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Transformations/Read | Odczytaj Stream Analytics przekształcenie zadania |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Transformations/Write | Zapisz transformację zadania Stream Analytics |
> | Akcja | Microsoft. StreamAnalytics/streamingjobs/Write | Stream Analytics zadania zapisu |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Subscription/Anuluj/akcja | Anuluje subskrypcję |
> | Akcja | Microsoft. subskrypcja/subskrypcja/akcja | Tworzenie subskrypcji platformy Azure |
> | Akcja | Microsoft. Subscription/rejestr/akcja | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. Subscription |
> | Akcja | Microsoft. Subscription/zmiana nazwy/akcji | Zmienia nazwę subskrypcji |
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
> | Akcja | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Delete | Usuwa zasady dostępu. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Pobierz właściwości zasad dostępu. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Tworzy nowe zasady dostępu dla środowiska lub aktualizuje istniejące zasady dostępu. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/Delete | Usuwa środowisko. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/eventsources/Delete | Usuwa Źródło zdarzenia. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/eventsources/Read | Pobierz właściwości źródła zdarzeń. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/eventsources/Write | Tworzy nowe źródło zdarzenia dla środowiska lub aktualizuje istniejące źródło zdarzeń. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/read | Pobierz właściwości środowiska. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Usuwa zestaw danych referencyjnych. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Pobierz właściwości zestawu danych referencyjnych. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Tworzy nowy zestaw danych referencyjnych dla środowiska lub aktualizuje istniejący zestaw danych referencyjnych. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/status/read | Pobierz stan środowiska, stan skojarzonych operacji, takich jak ruch przychodzący. |
> | Akcja | Microsoft. TimeSeriesInsights/Environments/Write | Tworzy nowe środowisko lub aktualizuje istniejące środowisko. |
> | Akcja | Microsoft. TimeSeriesInsights/Register/Action | Rejestruje subskrypcję dostawcy zasobów Time Series Insights i umożliwia tworzenie środowisk Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.VisualStudio/Account/Delete | Usuń konto |
> | Akcja | Microsoft. VisualStudio/Account/Extension/Read | Odczytaj konto/rozszerzenie |
> | Akcja | Microsoft. VisualStudio/Account/Project/Read | Odczytaj konto/projekt |
> | Akcja | Microsoft.VisualStudio/Account/Project/Write | Ustaw konto/projekt |
> | Akcja | Microsoft. VisualStudio/Account/Read | Odczytaj konto |
> | Akcja | Microsoft. VisualStudio/Account/Write | Ustaw konto |
> | Akcja | Microsoft. VisualStudio/rozszerzenie/usuwanie | Usuń rozszerzenie |
> | Akcja | Microsoft. VisualStudio/rozszerzenie/odczyt | Odczyt rozszerzenia |
> | Akcja | Microsoft. VisualStudio/rozszerzenie/zapis | Ustaw rozszerzenie |
> | Akcja | Microsoft.VisualStudio/Project/Delete | Usuń projekt |
> | Akcja | Microsoft.VisualStudio/Project/Read | Odczytaj projekt |
> | Akcja | Microsoft.VisualStudio/Project/Write | Ustaw projekt |
> | Akcja | Microsoft. VisualStudio/Register/Action | Zarejestruj subskrypcję platformy Azure przy użyciu dostawcy Microsoft. VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | microsoft.web/apimanagementaccounts/apiacls/read | Pobierz Apiacls kont usługi API Management. |
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
> | Akcja | microsoft.web/apimanagementaccounts/connectionacls/read | Pobierz Connectionacls kont usługi API Management. |
> | Akcja | Microsoft. Web/availablestacks/odczyt | Pobierz dostępne stosy. |
> | Akcja | Microsoft. Web/certyfikaty/usuwanie | Usuń istniejący certyfikat. |
> | Akcja | Microsoft. Web/Certificates/Read | Pobierz listę certyfikatów. |
> | Akcja | Microsoft. Web/Certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejący. |
> | Akcja | Microsoft. Web/checknameavailability/odczyt | Sprawdź, czy nazwa zasobu jest dostępna. |
> | Akcja | Microsoft. Web/classicmobileservices/odczyt | Pobierz klasyczne Mobile Services. |
> | Akcja | Microsoft. Web/connectionGateways/Delete | Usuwa bramę połączenia. |
> | Akcja | Microsoft. Web/connectionGateways/Join/Action | Sprzęga bramę połączenia. |
> | Akcja | Microsoft.Web/connectionGateways/ListStatus/Action | Wyświetla stan bramy połączenia. |
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
> | Akcja | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Wyodrębnia definicję interfejsu API z WSDL. |
> | Akcja | Microsoft. Web/customApis/Join/Action | Sprzęga niestandardowy interfejs API. |
> | Akcja | Microsoft.Web/customApis/listWsdlInterfaces/Action | Wyświetla listę interfejsów WSDL dla niestandardowego interfejsu API. |
> | Akcja | Microsoft. Web/customApis/Przenieś/akcja | Przenosi niestandardowy interfejs API. |
> | Akcja | Microsoft. Web/customApis/odczyt | Pobierz listę niestandardowych interfejsów API. |
> | Akcja | Microsoft. Web/customApis/Write | Tworzy lub aktualizuje niestandardowy interfejs API. |
> | Akcja | Microsoft. Web/deletedSites/odczyt | Pobierz właściwości usuniętej aplikacji sieci Web |
> | Akcja | microsoft.web/deploymentlocations/read | Pobierz lokalizacje wdrożenia. |
> | Akcja | Microsoft. Web/georegions/Read | Pobierz listę regionów geograficznych. |
> | Akcja | Microsoft. Web/hostingenvironments/pojemności/odczyt | Uzyskaj możliwości środowiska hostingu. |
> | Akcja | Microsoft. Web/hostingEnvironments/Delete | Usuwanie App Service Environment |
> | Akcja | Microsoft. Web/hostingenvironments/detektory/odczyt | Pobierz detektory środowisk hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/Diagnostyka/odczyt | Uzyskaj diagnostykę środowisk hostingu. |
> | Akcja | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Pobierz punkty końcowe sieci wszystkich zależności przychodzących. |
> | Akcja | Microsoft. Web/hostingEnvironments/Join/Action | Sprzęga App Service Environment |
> | Akcja | Microsoft. Web/hostingenvironments/metricdefinitions/odczyt | Pobierz definicje metryk środowisk hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/multirolepools/metricdefinitions/odczyt | Pobierz definicje metryk pul wielu ról środowiska hostingu. |
> | Akcja | microsoft.web/hostingenvironments/multirolepools/metrics/read | Uzyskaj metryki pul wielu ról. |
> | Akcja | Microsoft. Web/hostingEnvironments/multiRolePools/odczyt | Pobierz właściwości puli frontonu w App Service Environment |
> | Akcja | Microsoft. Web/hostingenvironments/multirolepools/SKU/odczyt | Uzyskaj środowiska hostingu pule wieloról jednostek SKU. |
> | Akcja | Microsoft. Web/hostingenvironments/multirolepools/Usages/Read | Uzyskaj środowiska hostingu użycia pul wielorolach. |
> | Akcja | Microsoft. Web/hostingEnvironments/multiRolePools/zapis | Utwórz nową pulę frontonu w App Service Environment lub zaktualizuj istniejącą |
> | Akcja | Microsoft. Web/hostingenvironments/Operations/Read | Pobierz operacje środowiska hostingu. |
> | Akcja | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Pobierz punkty końcowe sieci wszystkich zależności wychodzących. |
> | Akcja | Microsoft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/akcja | Zatwierdzanie połączeń prywatnych punktów końcowych |
> | Akcja | Microsoft. Web/hostingEnvironments/odczyt | Pobierz właściwości App Service Environment |
> | Akcja | Microsoft. Web/hostingEnvironments/ponowny rozruch/akcja | Uruchom ponownie wszystkie maszyny w App Service Environment |
> | Akcja | Microsoft. Web/hostingenvironments/Resume/akcja | Wznów środowiska hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/dopuszczalna/odczyt | Pobierz środowiska hostingu App Service plany. |
> | Akcja | Microsoft. Web/hostingenvironments/sites/odczyt | Pobierz środowiska hostingu Web Apps. |
> | Akcja | Microsoft. Web/hostingenvironments/Suspend/akcja | Wstrzymywanie środowisk hostingu. |
> | Akcja | Microsoft. Web/hostingenvironments/Usages/Read | Uzyskaj użycie środowisk hostingu. |
> | Akcja | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Pobierz środowiska hostingu Workerpools definicje metryki. |
> | Akcja | microsoft.web/hostingenvironments/workerpools/metrics/read | Uzyskaj środowiska hostingu Workerpools metryki. |
> | Akcja | Microsoft.Web/hostingEnvironments/workerPools/Read | Pobierz właściwości puli procesów roboczych w App Service Environment |
> | Akcja | microsoft.web/hostingenvironments/workerpools/skus/read | Uzyskaj środowiska hostingu Workerpools jednostki SKU. |
> | Akcja | microsoft.web/hostingenvironments/workerpools/usages/read | Uzyskaj środowiska hostingu Workerpools użycia. |
> | Akcja | Microsoft.Web/hostingEnvironments/workerPools/Write | Utwórz nową pulę procesów roboczych w App Service Environment lub zaktualizuj istniejącą |
> | Akcja | Microsoft. Web/hostingEnvironments/Write | Utwórz nowy App Service Environment lub zaktualizuj istniejący |
> | Akcja | Microsoft. Web/ishostingenvironmentnameavailable/odczyt | Pobierz, jeśli nazwa środowiska hostingu jest dostępna. |
> | Akcja | Microsoft. Web/ishostnameavailable/odczyt | Sprawdź, czy nazwa hosta jest dostępna. |
> | Akcja | Microsoft. Web/isusernameavailable/odczyt | Sprawdź, czy nazwa użytkownika jest dostępna. |
> | Akcja | Microsoft. Web/listSitesAssignedToHostName/odczyt | Pobieranie nazw lokacji przypisanych do nazwy hosta. |
> | Akcja | Microsoft. Web/Locations/apioperations/Read | Pobieranie lokalizacji operacji interfejsu API. |
> | Akcja | microsoft.web/locations/connectiongatewayinstallations/read | Pobierz lokalizacje instalacji bramy połączeń. |
> | Akcja | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Powiadomienie o usunięciu sieci wirtualnej lub podsieci dla lokalizacji. |
> | Akcja | microsoft.web/locations/extractapidefinitionfromwsdl/action | Wyodrębnij definicję interfejsu API z WSDL dla lokalizacji. |
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
> | Akcja | microsoft.web/serverfarms/firstpartyapps/settings/delete | Usuń ustawienia App Service plany aplikacji pierwszej strony. |
> | Akcja | microsoft.web/serverfarms/firstpartyapps/settings/read | Pobierz App Service plany. |
> | Akcja | microsoft.web/serverfarms/firstpartyapps/settings/write | Aktualizacja App Service planuje ustawienia aplikacji innych firm. |
> | Akcja | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/Relay/usuwanie | Usuwanie App Service planów przestrzenie nazw połączeń hybrydowych przekaźników. |
> | Akcja | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/Relay/odczyt | Pobierz App Service plany przestrzeni nazw połączenia hybrydowego. |
> | Akcja | Microsoft. Web/dopuszczalna/hybridconnectionnamespaces/przekaźniki/witryny/odczyt | Pobierz App Service plany przestrzeń nazw połączenia hybrydowego przekazuje Web Apps. |
> | Akcja | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Pobierz limity planu połączeń hybrydowych planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/hybridconnectionrelays/odczyt | Pobierz App Service plany połączeń hybrydowych. |
> | Akcja | microsoft.web/serverfarms/metricdefinitions/read | Pobierz definicje metryk planów App Service. |
> | Akcja | microsoft.web/serverfarms/metrics/read | Pobierz metryki planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/operationresults/odczyt | Pobierz wyniki operacji planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/odczyt | Pobierz właściwości planu App Service |
> | Akcja | Microsoft. Web/dopuszczalna/restartSites/akcja | Uruchom ponownie wszystkie Web Apps w planie App Service |
> | Akcja | Microsoft. Web/dopuszczalna/sites/odczyt | Pobierz Web Apps planów App Service. |
> | Akcja | microsoft.web/serverfarms/skus/read | Pobierz jednostki SKU planów App Service. |
> | Akcja | Microsoft. Web/dopuszczalna/Usages/Read | Pobierz użycia planów App Service. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | App Service plany Virtual Network połączeń z bramą. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/read | Pobierz App Service plany Virtual Network połączeń. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Usuń App Service plany Virtual Network połączeń. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Pobierz App Service plany Virtual Network połączeń. |
> | Akcja | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Aktualizowanie App Service planów Virtual Network połączeń. |
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
> | Akcja | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Pobierz Nieudane żądania diagnostyki Web Apps na identyfikator URI. |
> | Akcja | microsoft.web/sites/diagnostics/frebanalysis/read | Web Apps Pobierz analizę FREB diagnostyki. |
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
> | Akcja | microsoft.web/sites/metrics/read | Pobierz metryki Web Apps. |
> | Akcja | Microsoft. Web/Sites/metricsdefinitions/odczyt | Pobierz definicje metryk Web Apps. |
> | Akcja | Microsoft. Web/Sites/migratemysql/akcja | Migrowanie Web Apps MySql. |
> | Akcja | Microsoft. Web/Sites/migratemysql/odczyt | Get Web Apps Migrate MySql. |
> | Akcja | microsoft.web/sites/networktrace/action | Web Apps śledzenia sieci. |
> | Akcja | microsoft.web/sites/networktraces/operationresults/read | Pobierz wyniki operacji śledzenia sieci Web Apps. |
> | Akcja | Microsoft. Web/Sites/NoweHasło/akcja | NoweHasło Web Apps. |
> | Akcja | Microsoft. Web/Sites/operationresults/odczyt | Pobierz wyniki operacji Web Apps. |
> | Akcja | Microsoft. Web/Sites/Operations/Read | Pobierz Web Apps operacji. |
> | Akcja | Microsoft. Web/Sites/perfcounters/odczyt | Pobierz Web Apps liczniki wydajności. |
> | Akcja | microsoft.web/sites/premieraddons/delete | Usuń Web Apps Dodatki Premier. |
> | Akcja | microsoft.web/sites/premieraddons/read | Uzyskaj Web Apps Dodatki Premier. |
> | Akcja | microsoft.web/sites/premieraddons/write | Aktualizacja dodatków do Web Apps Premier. |
> | Akcja | microsoft.web/sites/privateaccess/read | Uzyskaj dane dotyczące włączania dostępu do lokacji prywatnej i autoryzowanych sieci wirtualnych, które mogą uzyskać dostęp do witryny. |
> | Akcja | Microsoft. Web/Sites/PrivateEndpointConnectionsApproval/akcja | Zatwierdzanie połączeń prywatnych punktów końcowych |
> | Akcja | Microsoft. Web/Sites/processs/modules/Read | Pobierz moduły Web Apps procesów. |
> | Akcja | microsoft.web/sites/processes/read | Pobierz Web Apps procesy. |
> | Akcja | Microsoft. Web/Sites/processs/Threads/Read | Pobierz wątki procesów Web Apps. |
> | Akcja | Microsoft. Web/Sites/publiccertificates/Delete | Usuń Web Apps certyfikaty publiczne. |
> | Akcja | Microsoft. Web/Sites/publiccertificates/odczyt | Pobierz Web Apps certyfikaty publiczne. |
> | Akcja | Microsoft. Web/Sites/publiccertificates/Write | Aktualizowanie Web Apps certyfikatów publicznych. |
> | Akcja | Microsoft. Web/Sites/Publikuj/akcja | Publikowanie aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/publishxml/akcja | Pobieranie pliku XML profilu publikowania dla aplikacji sieci Web |
> | Akcja | microsoft.web/sites/publishxml/read | Pobierz Web Apps publikowania XML. |
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
> | Akcja | microsoft.web/sites/slots/diagnostics/analyses/read | Pobierz analizę diagnostyki miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/aspnetcore/Read | Pobierz diagnostykę Web Appsych miejsc dla aplikacji ASP.NET Core. |
> | Akcja | Microsoft. Web/Sites/szczeliny/Diagnostyka/autozabliźnione/odczyt | Pobierz Web Apps autozabliźnione diagnostyki miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/Deployment/Read | Pobierz wdrożenie diagnostyki miejsc Web Apps. |
> | Akcja | microsoft.web/sites/slots/diagnostics/deployments/read | Pobierz Web Apps wdrożenia diagnostyczne. |
> | Akcja | Microsoft. Web/Sites/szczeliny/Diagnostyka/detektory/wykonaj/akcję | Uruchom detektor diagnostyki Web Appsych gniazd. |
> | Akcja | Microsoft. Web/Sites/szczeliny/Diagnostyka/detektory/odczyt | Pobierz detektor diagnostyki Web Apps miejsc. |
> | Akcja | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Pobierz Web Apps FREB analiza diagnostyki gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/loganalyzer/Read | Pobierz Analizator dziennika diagnostyki gniazd Web Apps. |
> | Akcja | microsoft.web/sites/slots/diagnostics/read | Pobierz diagnostykę Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/runtimeavailability/Read | Pobierz dostępność w czasie wykonywania diagnostyki Web Apps gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/servicehealth/Read | Pobierz Service Health Web Apps diagnostyki miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/sitecpuanalysis/Read | Pobierz przeanalizowanie procesora CPU lokacji diagnostyki Web Apps gniazd. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/sitecrashes/Read | Pobierz awarie lokacji diagnostyki Web Apps miejsc. |
> | Akcja | microsoft.web/sites/slots/diagnostics/sitelatency/read | Pobierz opóźnienie witryny diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/sitememoryanalysis/Read | Pobierz analizę pamięci lokacji diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/siterestartsettingupdate/Read | Pobierz aktualizację ustawień ponownego uruchamiania lokacji diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/siterestartuserinitiated/Read | Pobierz lokację diagnostyki Web Apps miejsc ponownie zainicjowane przez użytkownika. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/siteswap/Read | Pobierz zamianę lokacji diagnostyki Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/ThreadCount/Read | Pobierz liczbę wątków diagnostyki Web Apps gniazd. |
> | Akcja | microsoft.web/sites/slots/diagnostics/workeravailability/read | Pobierz Web Apps diagnostyki Workeravailability miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Diagnostics/workerprocessrecycle/Read | Pobierz procedurę odzyskiwania procesu roboczego diagnostyki gniazd Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/domainownershipidentifiers/Read | Pobierz identyfikatory własności domeny miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/Functions/listsecrets/Action | Wyświetl klucze tajne Web Apps funkcji miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/Functions/Read | Pobierz funkcje Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/hostnamebindings/Delete | Usuń powiązania nazwy hosta Web Apps miejsc. |
> | Akcja | microsoft.web/sites/slots/hostnamebindings/read | Pobierz powiązania nazwy hosta Web Apps miejsc. |
> | Akcja | microsoft.web/sites/slots/hostnamebindings/write | Aktualizowanie powiązań nazwy hosta Web Apps miejsc. |
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
> | Akcja | microsoft.web/sites/slots/metrics/read | Pobierz metryki Web Apps gniazd. |
> | Akcja | microsoft.web/sites/slots/migratemysql/read | Pobierz Web Apps gniazda migracji bazy danych MySql. |
> | Akcja | microsoft.web/sites/slots/networktrace/action | Gniazda Web Apps śledzenia sieci. |
> | Akcja | microsoft.web/sites/slots/networktraces/operationresults/read | Pobierz wyniki operacji śledzenia sieci Web Apps gniazd. |
> | Akcja | Microsoft. Web/Sites/szczeliny/NoweHasło/akcja | NoweHasło Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/operationresults/Read | Pobierz wyniki operacji dla miejsc Web Apps. |
> | Akcja | Microsoft. Web/Sites/Slots/Operations/Read | Pobierz operacje na Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/perfcounters/Read | Pobierz liczniki wydajności Web Apps miejsc. |
> | Akcja | Microsoft. Web/Sites/Slots/phplogging/Read | Pobierz Web Apps Phplogging miejsc. |
> | Akcja | microsoft.web/sites/slots/premieraddons/delete | Usuń dodatki Web Apps miejsc premier. |
> | Akcja | microsoft.web/sites/slots/premieraddons/read | Uzyskaj Web Apps miejsca na dodatki Premier. |
> | Akcja | microsoft.web/sites/slots/premieraddons/write | Aktualizowanie dodatków do Web Apps miejsc premier. |
> | Akcja | microsoft.web/sites/slots/processes/read | Pobierz procesy Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/publiccertificates/Delete | Usuwanie Web Apps miejscowych certyfikatów publicznych. |
> | Akcja | Microsoft. Web/Sites/Slots/publiccertificates/Read | Pobierz Web Apps gniazda certyfikatów publicznych. |
> | Akcja | Microsoft. Web/Sites/Slots/publiccertificates/Write | Utwórz lub zaktualizuj certyfikaty publiczne na Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/szczeliny/publikowanie/akcja | Publikowanie miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/publishxml/Action | Pobierz plik XML profilu publikowania dla miejsca aplikacji sieci Web |
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
> | Akcja | Microsoft.Web/sites/slots/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/sourcecontrols/Read | Pobierz ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/szczeliny/uruchomienie/akcja | Uruchom miejsce aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Slots/akcja | Zatrzymaj miejsce aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/szczeliny/synchronizacja/akcja | Zsynchronizuj Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/Slots/triggeredwebjobs/Delete | Usuwanie Web Appsych gniazd wyzwalanych przez Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/Slots/triggeredwebjobs/Read | Pobierz Web Apps gniazda wyzwolone Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/Slots/triggeredwebjobs/Run/Action | Uruchom uruchomione Web Apps gniazda wyzwalane przez Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/Slots/Usages/Read | Uzyskaj Web Apps użycia miejsc. |
> | Akcja | microsoft.web/sites/slots/virtualnetworkconnections/delete | Usuwanie Web Apps miejsc Virtual Network połączeń. |
> | Akcja | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Aktualizowanie Web Apps miejsc Virtual Network połączeń bram. |
> | Akcja | microsoft.web/sites/slots/virtualnetworkconnections/read | Pobierz Web Apps gniazda Virtual Network połączeń. |
> | Akcja | microsoft.web/sites/slots/virtualnetworkconnections/write | Aktualizacja Virtual Network połączeń Web Apps. |
> | Akcja | Microsoft. Web/Sites/szczeliny/Zadania WebJob/odczyt | Pobierz Zadania WebJob na Web Apps gniazda. |
> | Akcja | Microsoft. Web/Sites/szczeliny/zapis | Utwórz nowe miejsce aplikacji sieci Web lub zaktualizuj istniejące |
> | Akcja | Microsoft. Web/Sites/slotsdiffs/akcja | Poznaj różnice między konfiguracją aplikacji sieci Web i miejsc |
> | Akcja | Microsoft. Web/Sites/slotsswap/akcja | Wymiana miejsc wdrożenia aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/snapshots/Read | Pobierz migawki Web Apps. |
> | Akcja | Microsoft.Web/sites/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/sourcecontrols/odczyt | Pobierz ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/Start/akcja | Uruchamianie aplikacji sieci Web |
> | Akcja | Microsoft. Web/Sites/zatrzymywanie/akcja | Zatrzymaj aplikację internetową |
> | Akcja | Microsoft. Web/Sites/synchronizacja/akcja | Web Apps synchronizacji. |
> | Akcja | Microsoft. Web/Sites/syncfunctiontriggers/akcja | Wyzwalacze funkcji synchronizacji. |
> | Akcja | Microsoft. Web/Sites/triggeredwebjobs/Delete | Usuń Web Apps wyzwolone Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/triggeredwebjobs/historia/odczyt | Pobierz Web Apps wyzwoloną historię zadań WebJob. |
> | Akcja | Microsoft. Web/Sites/triggeredwebjobs/odczyt | Pobierz Web Apps wyzwolone Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/triggeredwebjobs/Run/Action | Uruchom Web Apps wyzwolone Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites//użycia/odczyt | Pobierz Web Apps użycia. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/delete | Usuń połączenia Virtual Network Web Apps. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/gateways/read | Pobierz Web Apps bram Virtual Network połączeń. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/gateways/write | Aktualizowanie bram Web Apps Virtual Network połączeń. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/read | Uzyskaj Web Apps połączenia Virtual Network. |
> | Akcja | microsoft.web/sites/virtualnetworkconnections/write | Aktualizowanie Web Apps Virtual Network połączeń. |
> | Akcja | Microsoft. Web/Sites/WebJobs/odczyt | Pobierz Web Apps Zadania WebJob. |
> | Akcja | Microsoft. Web/Sites/Write | Utwórz nową aplikację sieci Web lub zaktualizuj istniejącą |
> | Akcja | microsoft.web/skus/read | Pobierz jednostki SKU. |
> | Akcja | microsoft.web/sourcecontrols/read | Pobierz kontrolę źródła. |
> | Akcja | Microsoft. Web/sourcecontrols/Write | Aktualizowanie kontroli źródła. |
> | Akcja | Microsoft. Web/Unregister/akcja | Wyrejestruj dostawcę zasobów Microsoft. Web dla subskrypcji. |
> | Akcja | Microsoft. Web/Validate/akcja | Legalizacj. |
> | Akcja | Microsoft. Web/verifyhostingenvironmentvnet/akcja | Sprawdź sieć wirtualną środowiska hostingu. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft. Monitor obciążenia został/Components/Read | Pobiera składniki dla zasobu |
> | Akcja | Microsoft. Monitor obciążenia został/componentsSummary/odczyt | Pobiera podsumowanie składników |
> | Akcja | Microsoft.WorkloadMonitor/monitorInstances/read | Pobiera wystąpienia monitorów dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Pobiera podsumowanie wystąpień monitora |
> | Akcja | Microsoft.WorkloadMonitor/monitors/read | Pobiera monitory dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/monitors/write | Konfigurowanie monitora dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/notificationSettings/read | Pobiera ustawienia powiadomień dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/notificationSettings/write | Konfigurowanie ustawień powiadomień dla zasobu |
> | Akcja | Microsoft.WorkloadMonitor/operations/read | Pobiera obsługiwane operacje |

## <a name="next-steps"></a>Kolejne kroki

- [Dopasuj dostawcę zasobów do usługi](../azure-resource-manager/azure-services-resource-providers.md)
- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
