---
title: Operacje dostawcy zasobów do platformy Azure Resource Manager | Dokumentacja firmy Microsoft
description: Wyświetla operacje dostępne dla dostawców zasobów Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: 08059da95baa9e70b8dba2dd847d0b28669778b7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operacje dostawcy zasobów Menedżera zasobów platformy Azure

W tym artykule wymieniono operacje dostępne dla każdego dostawcy zasobów usługi Azure Resource Manager. Te operacje mogą być używane w [role niestandardowe](custom-roles.md) zapewnienie szczegółowego [kontroli dostępu opartej na rolach (RBAC)](overview.md) do zasobów na platformie Azure. Operacja ciągi mają następujący format: `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

Zawsze ewoluuje operacji dostawcy zasobów. Aby uzyskać najnowsze operacji, należy użyć [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) lub [listy operacji dostawcy az](/cli/azure/provider/operation#az-provider-operation-list).

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AAD/domainServices/delete | Usuwa usług domenowych w usłudze. |
> | Akcja | Microsoft.AAD/domainServices/read | Odczytuje usług domenowych w usłudze. |
> | Akcja | Microsoft.AAD/domainServices/write | Pisanie usług domenowych w usłudze |
> | Akcja | Microsoft.AAD/locations/operationresults/read | Przeczytaj stan operacji asynchronicznej. |
> | Akcja | Microsoft.AAD/Operations/read | Zlokalizowane przyjazny opis dla tej operacji, w jakiej powinny być wyświetlane dla użytkownika. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.aadiam/diagnosticsettings/DELETE | Usuwanie ustawienie diagnostyczne |
> | Akcja | Microsoft.aadiam/diagnosticsettings/Read | Odczytywanie ustawienie diagnostyczne |
> | Akcja | Microsoft.aadiam/diagnosticsettings/Write | Zapisywanie ustawień diagnostycznych |
> | Akcja | Microsoft.aadiam/diagnosticsettingscategories/Read | Odczytywanie kategorii ustawienie diagnostyczne |
> | Akcja | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla dzierżawców |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Addons/operations/read | Pobiera obsługiwane operacje planu odzyskiwania. |
> | Akcja | Microsoft.Addons/register/action | Zarejestruj Microsoft.Addons określonej subskrypcji |
> | Akcja | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Wyświetla listę bieżących informacji planu pomocy technicznej dla określonej subskrypcji. |
> | Akcja | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Usuwa plan określony Canonical pomocy technicznej |
> | Akcja | Microsoft.Addons/supportProviders/supportPlanTypes/read | Pobierz stan planu określonego Canonical pomocy technicznej. |
> | Akcja | Microsoft.Addons/supportProviders/supportPlanTypes/write | Dodaje określony typ planu obsługi kanonicznej. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/action | Utwórz nowy las dla dzierżawcy. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Pobiera wszystkie serwery dla określonej nazwy usługi. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/alerts/read | Pobiera szczegóły alertów dla lasu, takich jak identyfikatorem alertu alert wywoływane Data, ostatni wykryte, alertów opis alertu, ostatniego alertu, zaktualizowaną poziomu, alert stanu, alertów, rozwiązywanie problemów z łączami itp. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/configuration/read | Pobiera konfigurację usługi dla tego lasu. Przykład — nazwa lasu, poziom Functionla, nazewnictwa domeny głównego roli FSMO, roli FSMO wzorca schematu itp. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/delete | Usuwa usługę i jej serwery oraz dane kondycji. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Pobiera szczegóły lokacji i domen w lesie. Stan kondycji przykład, aktywne alerty rozwiązane alerty, właściwości, takie jak poziom funkcjonalności domeny, lasu, wzorzec infrastruktury, podstawowego kontrolera domeny, RID wzorca itp.  |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Pobiera ustawienia preferencji użytkownika dla tego lasu.<br>Przykład - MetricCounterName ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Ustawienia interfejsu użytkownika wykresy itd. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Pobiera lasu podsumowania dla danego lasu, takie jak nazwa lasu, liczby domen w tym lesie, liczba witryn i witryn szczegółów etc. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Pobiera listę obsługiwanych metryki dla danej usługi.<br>Przykład ekstranetu blokady konta, łączna liczba nieudanych żądań oczekujących żądań tokenów (Proxy), Token żądania/s itp dla usługi AD FS.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślnie na sekundę, czas wiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę itp ATQ wątków całkowita dla ADDomainService.<br>Uruchom opóźnienia profilu, ustanowić połączenia TCP, Bajty prywatne agenta wgląd, Statystyka eksportu do usługi Azure AD dla usługi ADSync. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Podana usługa, ten interfejs API pobiera informacje metryki.<br>Na przykład, aby uzyskać informacje dotyczące może służyć tego interfejsu API: ekstranetu blokady konta, łączna liczba nieudanych żądań, zaległych żądań tokenów (Proxy), Token żądania/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślnie na sekundę, czas wiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę itp ATQ wątków całkowita ADDomain usługi.<br>Uruchom profilu opóźnienia, połączeń TCP, Bajty prywatne agenta wgląd, eksportowanie statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Ten interfejs API pobiera listę wszystkich został załadowany ADDomainServices dla dzierżawy premium. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/read | Pobiera szczegóły usługi dla określonej nazwy usługi. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Pobiera szczegóły replikacji dla wszystkich serwerów dla określonej nazwy usługi. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Pobiera liczbę kontrolerów domeny i ich błędy replikacji, jeśli istnieje. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Pobiera Pełna lista kontrolera domeny oraz szczegóły replikacji w danym lesie. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Dodaj wystąpienie serwera do usługi. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Podczas rejestracji serwera ADDomainService Aby uzyskać poświadczenia do dołączenia do nowych serwerów nosi nazwę tego interfejsu api. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Usuwa serwer dla danej usługi i dzierżawcy. |
> | Akcja | Microsoft.ADHybridHealthService/addsservices/write | Tworzy lub aktualizuje wystąpienia ADDomainService dla dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/configuration/action | Aktualizacje konfiguracji dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/configuration/read | Odczytuje konfiguracji dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/configuration/write | Tworzy konfiguracji dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/logs/contents/read | Pobiera zawartość instalacji agenta i dzienniki rejestracji, przechowywane w obiekcie blob. |
> | Akcja | Microsoft.ADHybridHealthService/logs/read | Pobiera instalacji agenta i dzienniki rejestracji dla dzierżawcy. |
> | Akcja | Microsoft.ADHybridHealthService/operations/read | Pobiera listę operacji obsługiwane przez system. |
> | Akcja | Microsoft.ADHybridHealthService/register/action | Rejestruje dostawcę zasobów usługi kondycji ADHybrid i umożliwia tworzenie zasobów ADHybrid kondycji usługi. |
> | Akcja | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Pobiera listę dostępnych regionów, używane przez opracowywania oprogramowania do obsługi zdarzeń klienta. |
> | Akcja | Microsoft.ADHybridHealthService/reports/badpassword/read | Pobiera listę prób podania nieprawidłowego hasła dla wszystkich użytkowników w usługi federacyjne Active Directory. |
> | Akcja | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Pobiera identyfikator URI SAS obiektu Blob zawierającego stanu i próbuje ostatecznego wyniku nowo umieszczonych w kolejce zadania raportu częstotliwości zły nazwy użytkownika i hasła na UserId na adres IP na dzień dla danej dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Pobiera listę DevOps zgodę dzierżaw. Zazwyczaj używane do obsługi klienta. |
> | Akcja | Microsoft.ADHybridHealthService/reports/isdevops/read | Pobiera wartość wskazującą, czy wheather teannt jest DevOps zgodę, czy nie. |
> | Akcja | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Aktualizuje userid(objectid) dla dzierżawy ops wybranego deweloperów. |
> | Akcja | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Pobiera wybrane wdrożenie do danej dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Podany identyfikator dzierżawcy pobiera dzierżawy lokalizacji magazynu. |
> | Akcja | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Pobiera lokalizację geograficzną, z którego będzie można uzyskać dostępu do danych. |
> | Akcja | Microsoft.ADHybridHealthService/services/action | Aktualizuje wystąpienie usługi, w dzierżawie. |
> | Akcja | Microsoft.ADHybridHealthService/services/alerts/read | Odczytuje alerty dla usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/alerts/read | Odczytuje alerty dla usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Podany funkcji nazwa sprawdza, jeśli usługa zawiera wszystko, co jest wymagane do używania tej funkcji. |
> | Akcja | Microsoft.ADHybridHealthService/services/delete | Usuwa wystąpienie usługi, w dzierżawie. |
> | Akcja | Microsoft.ADHybridHealthService/services/exporterrors/read | Pobiera błędy eksportu dla usługi synchronizacji danego. |
> | Akcja | Microsoft.ADHybridHealthService/services/exportstatus/read | Pobiera stan eksportu dla danej usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Pobiera opinii alertów dla danej usługi i serwera. |
> | Akcja | Microsoft.ADHybridHealthService/services/metricmetadata/read | Pobiera listę obsługiwanych metryki dla danej usługi.<br>Przykład ekstranetu blokady konta, łączna liczba nieudanych żądań oczekujących żądań tokenów (Proxy), Token żądania/s itp dla usługi AD FS.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślnie na sekundę, czas wiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę itp ATQ wątków całkowita dla ADDomainService.<br>Uruchom opóźnienia profilu, ustanowić połączenia TCP, Bajty prywatne agenta wgląd, Statystyka eksportu do usługi Azure AD dla usługi ADSync. |
> | Akcja | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Ten interfejs API podana usługa, pobiera średnia dla metryki dla danej usługi.<br>Na przykład, aby uzyskać informacje dotyczące może służyć tego interfejsu API: ekstranetu blokady konta, łączna liczba nieudanych żądań, zaległych żądań tokenów (Proxy), Token żądania/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślnie na sekundę, czas wiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę itp ATQ wątków całkowita ADDomain usługi.<br>Uruchom profilu opóźnienia, połączeń TCP, Bajty prywatne agenta wgląd, eksportowanie statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/metrics/groups/read | Podana usługa, ten interfejs API pobiera informacje metryki.<br>Na przykład, aby uzyskać informacje dotyczące może służyć tego interfejsu API: ekstranetu blokady konta, łączna liczba nieudanych żądań, zaległych żądań tokenów (Proxy), Token żądania/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślnie na sekundę, czas wiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę itp ATQ wątków całkowita ADDomain usługi.<br>Uruchom profilu opóźnienia, połączeń TCP, Bajty prywatne agenta wgląd, eksportowanie statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Ten interfejs API, biorąc pod uwagę usługę, pobiera zagregowany widok dla metryki dla danej usługi.<br>Na przykład, aby uzyskać informacje dotyczące może służyć tego interfejsu API: ekstranetu blokady konta, łączna liczba nieudanych żądań, zaległych żądań tokenów (Proxy), Token żądania/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślnie na sekundę, czas wiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę itp ATQ wątków całkowita ADDomain usługi.<br>Uruchom profilu opóźnienia, połączeń TCP, Bajty prywatne agenta wgląd, eksportowanie statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Dodawanie lub aktualizacji konfiguracji monitorowania dla usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Pobiera konfiguracji monitorowania dla danej usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Dodawanie lub aktualizacji konfiguracji monitorowania dla usługi. |
> | Akcja | Microsoft.ADHybridHealthService/services/premiumcheck/read | Ten interfejs API pobiera listę wszystkich usług został załadowany dla dzierżawy premium. |
> | Akcja | Microsoft.ADHybridHealthService/services/read | Odczytuje wystąpień usługi w dzierżawie. |
> | Akcja | Microsoft.ADHybridHealthService/services/reports/details/read | Raport pobiera top 50 użytkowników z błędami nieprawidłowego hasła z ostatnich 7 dni |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/action | Tworzy wystąpienie serwera w usłudze. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Odczytuje alerty dla serwera. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Podczas rejestracji serwera tego interfejsu api nosi nazwę można uzyskać poświadczenia dołączania nowych serwerów. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Dla danego serwera ten interfejs API pobiera listę typów danych, które są przekazywane przez serwery i Najpóźniejsza godzina dla każdej operacji przekazywania. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/delete | Usuwa wystąpienie serwera w usłudze. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Pobiera szczegóły błędu eksportu synchronizacji dla danej usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Podana usługa, ten interfejs API pobiera informacje metryki.<br>Na przykład, aby uzyskać informacje dotyczące może służyć tego interfejsu API: ekstranetu blokady konta, łączna liczba nieudanych żądań, zaległych żądań tokenów (Proxy), Token żądania/s itp ADFederation usługi.<br>Uwierzytelnienia NTLM na sekundę, wiązania LDAP pomyślnie na sekundę, czas wiązania LDAP, Aktywne wątki LDAP, uwierzytelnienia Kerberos na sekundę itp ATQ wątków całkowita ADDomain usługi.<br>Uruchom profilu opóźnienia, połączeń TCP, Bajty prywatne agenta wgląd, eksportowanie statystyki do usługi Azure AD dla usługi synchronizacji. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/read | Odczytuje wystąpienie serwera w usłudze. |
> | Akcja | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Pobiera konfigurację usługi dla danej dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Pobiera stan listę dozwolonych podobnej funkcji dla danej dzierżawy. |
> | Akcja | Microsoft.ADHybridHealthService/services/write | Tworzy wystąpienie usługi w dzierżawie. |
> | Akcja | Microsoft.ADHybridHealthService/unregister/action | Wyrejestrowuje subskrypcji dla dostawcy zasobów usługi kondycji ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Advisor/configurations/read | Uzyskiwanie konfiguracji |
> | Akcja | Microsoft.Advisor/configurations/write | Tworzy aktualizacji konfiguracji |
> | Akcja | Microsoft.Advisor/generateRecommendations/action | Generuje zalecenia |
> | Akcja | Microsoft.Advisor/generateRecommendations/read | Pobiera Generowanie zaleceń stanu |
> | Akcja | Microsoft.Advisor/operations/read | Pobiera operacje dla Advisor firmy Microsoft |
> | Akcja | Microsoft.Advisor/recommendations/read | Odczytuje zalecenia |
> | Akcja | Microsoft.Advisor/recommendations/suppressions/delete | Usuwa pominięć |
> | Akcja | Microsoft.Advisor/recommendations/suppressions/read | Pobiera pominięć |
> | Akcja | Microsoft.Advisor/recommendations/suppressions/write | Tworzy aktualizacje pominięć |
> | Akcja | Microsoft.Advisor/register/action | Rejestruje subskrypcję Microsoft Advisor |
> | Akcja | Microsoft.Advisor/suppressions/delete | Usuwa pominięć |
> | Akcja | Microsoft.Advisor/suppressions/read | Pobiera pominięć |
> | Akcja | Microsoft.Advisor/suppressions/write | Tworzy aktualizacje pominięć |
> | Akcja | Microsoft.Advisor/unregister/action | Wyrejestrowuje subskrypcję Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AlertsManagement/alerts/changestate/action | Zmień stan alertu. |
> | Akcja | Microsoft.AlertsManagement/alerts/read | Pobierz wszystkie alerty dla filtrów wejściowych. |
> | Akcja | Microsoft.AlertsManagement/alertsSummary/read | Pobierz podsumowanie alertów |
> | Akcja | Microsoft.AlertsManagement/Operations/read | Odczytuje działania określone |
> | Akcja | Microsoft.AlertsManagement/smartGroups/changestate/action | Zmień stan grupy inteligentne |
> | Akcja | Microsoft.AlertsManagement/smartGroups/read | Pobierz wszystkie inteligentne grupy dla filtrów wejściowych |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Sprawdza, czy dany serwer analiz nazwa jest prawidłowa, a nie korzystać. |
> | Akcja | Microsoft.AnalysisServices/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | Akcja | Microsoft.AnalysisServices/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | Akcja | Microsoft.AnalysisServices/operations/read | Pobiera informacje o operacji |
> | Akcja | Microsoft.AnalysisServices/register/action | Rejestruje dostawcę zasobów usług Analysis Services. |
> | Akcja | Microsoft.AnalysisServices/servers/delete | Usuwa Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Lista stanu skojarzonym z serwerem bramy. |
> | Akcja | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla serwera analiz |
> | Akcja | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla serwera analiz |
> | Akcja | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów |
> | Akcja | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla serwera analiz |
> | Akcja | Microsoft.AnalysisServices/servers/read | Pobiera informacje o określonym Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/servers/resume/action | Wznawia Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/servers/skus/read | Pobierz dostępne jednostki SKU informacje dotyczące serwera |
> | Akcja | Microsoft.AnalysisServices/servers/suspend/action | Wstrzymuje Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/servers/write | Tworzy lub aktualizuje określony Analysis Server. |
> | Akcja | Microsoft.AnalysisServices/skus/read | Pobiera informacje jednostki SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ApiManagement/checkNameAvailability/read | Sprawdza, czy pod warunkiem, że nazwa usługi jest niedostępna |
> | Akcja | Microsoft.ApiManagement/operations/read | Dostępne wszystkie operacje interfejsu API dla zasobu Microsoft.ApiManagement odczytu |
> | Akcja | Microsoft.ApiManagement/register/action | Zarejestruj subskrypcję dostawcy zasobów Microsoft.ApiManagement |
> | Akcja | Microsoft.ApiManagement/reports/read | Pobierz raporty w okresach, regionu geograficznego, deweloperów, produktów, interfejsów API, operacje, subskrypcji i byRequest. |
> | Akcja | Microsoft.ApiManagement/service/apis/delete | Usuń istniejące interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/delete | Usuń istniejące diagnostyki |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/loggers/delete | Usuń mapowanie rejestratora przy użyciu ustawienia diagnostyczne |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/loggers/read | Pobierz listę istniejących diagnostycznych rejestratorów |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/loggers/write | Mapowanie rejestratora na ustawienie diagnostyczne |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/read | Pobierz listę diagnostyki lub pobranie szczegółów diagnostyki |
> | Akcja | Microsoft.ApiManagement/service/apis/diagnostics/write | Dodaj nowe diagnostyki lub zaktualizować istniejący szczegóły diagnostyczne |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Usuwa istniejące załącznika |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/attachments/read | Pobieranie załączników problem lub problem pobiera zarządzanie interfejsami API szczegóły załącznika |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/attachments/write | Dodaj załącznik problem interfejsu api |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/comments/delete | Usuwa istniejące komentarza |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/comments/read | Pobiera komentarze lub problem pobiera zarządzanie interfejsami API komentarz szczegółów problemu |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/comments/write | Dodaj komentarz problem interfejsu api |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/delete | Usuwa istniejące problem |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/read | Pobierz problemów związanych z interfejsu API lub pobiera zarządzanie interfejsami API szczegóły problemu |
> | Akcja | Microsoft.ApiManagement/service/apis/issues/write | Dodaj problem interfejsu api lub zaktualizuj problem interfejsu api |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/delete | Usuń istniejące operacji interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policies/delete | Usuń konfigurację zasad od zasad operacji interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policies/read | Pobrania zasad dla operacji interfejsu API lub pobranie szczegółów konfiguracji zasad dla operacji interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policies/write | Ustaw szczegóły konfiguracji zasad dla operacji interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policy/delete | Usuń konfigurację zasad z operacji |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policy/read | Uzyskiwanie szczegółów konfiguracji zasad dla operacji |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/policy/write | Ustaw szczegóły konfiguracji zasad dla operacji |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/read | Pobierz listę istniejących operacji interfejsu API lub uzyskać szczegółowe informacje o operacji interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/tags/delete | Usuń powiązanie istniejącego znacznika z istniejącą operację |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/tags/read | Pobierz Znaczniki skojarzone ze szczegółami operacji lub pobrać tagu |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/tags/write | Skojarz istniejącego znacznika z istniejącą operację |
> | Akcja | Microsoft.ApiManagement/service/apis/operations/write | Tworzenie nowej operacji interfejsu API lub zaktualizować istniejący operacji interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/operationsByTags/read | Pobierz listę operacji/Tag skojarzenia |
> | Akcja | Microsoft.ApiManagement/service/apis/policies/delete | Usuń konfigurację zasad od zasad interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/policies/read | Pobierz zasady dla interfejsu API lub Get szczegóły konfiguracji zasad interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/policies/write | Ustaw szczegóły konfiguracji zasad interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/policy/delete | Usuń konfigurację zasad z interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/policy/read | Uzyskiwanie szczegółów konfiguracji zasad interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/policy/write | Ustaw szczegóły konfiguracji zasad interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/products/read | Pobierz wszystkie produkty, które interfejs API jest częścią |
> | Akcja | Microsoft.ApiManagement/service/apis/read | Pobierz listę wszystkich zarejestrowanych szczegółów interfejsów API lub Get interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/releases/delete | Usuwa wszystkie wersje wersji interfejsu API lub usuń interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/releases/read | Zainstalowane wersje dla interfejsu API lub Get szczegóły reelase interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/releases/write | Utwórz nową wersję interfejsu API lub zaktualizuj istniejącą wersji interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/revisions/delete | Usuwa wszystkie wersje interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/revisions/read | Pobierz poprawki należących do interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/delete | Usuwa istniejące schematu |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/document/read | Pobierz dokument zawierający schematu |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/document/write | Aktualizacja schematu opisujący |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/read | Pobiera wszystkich schematów dla danego interfejsu API lub pobiera schematów używany przez interfejs API |
> | Akcja | Microsoft.ApiManagement/service/apis/schemas/write | Ustawia schematów używany przez interfejs API |
> | Akcja | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Usuń opis tagu z interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Pobierz opisy tagów w zakresie opisu interfejsu API lub pobrać tagu w zakresie interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Opis Tag utworzyć zmiany w zakresie interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apis/tags/delete | Usuń istniejące skojarzenie interfejsu API/Tag |
> | Akcja | Microsoft.ApiManagement/service/apis/tags/read | Pobierz wszystkie skojarzenia interfejsu API/tagu dla interfejsu API lub Get szczegółów skojarzenia interfejsu API/Tag |
> | Akcja | Microsoft.ApiManagement/service/apis/tags/write | Dodaj nowe skojarzenie interfejsu API/Tag |
> | Akcja | Microsoft.ApiManagement/service/apis/write | Utwórz nowy interfejs API lub zaktualizuj istniejącą szczegóły interfejsu API |
> | Akcja | Microsoft.ApiManagement/service/apisByTags/read | Pobierz listę skojarzenia interfejsu API/Tag |
> | Akcja | Microsoft.ApiManagement/service/api-version-sets/delete | Usuń istniejące VersionSet |
> | Akcja | Microsoft.ApiManagement/service/api-version-sets/read | Pobierz listę wersji grupy jednostek lub pobiera szczegóły VersionSet |
> | Akcja | Microsoft.ApiManagement/service/api-version-sets/versions/read | Pobierz listę wersji jednostki |
> | Akcja | Microsoft.ApiManagement/service/api-version-sets/write | Utwórz VersionSet nowego lub istniejącego VersionSet szczegóły aktualizacji |
> | Akcja | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Aktualizuje zasoby Microsoft.ApiManagement systemem w sieci wirtualnej w celu zastosowania zaktualizowanych ustawień sieciowych. |
> | Akcja | Microsoft.ApiManagement/service/authorizationServers/delete | Usuń istniejący serwer autoryzacji |
> | Akcja | Microsoft.ApiManagement/service/authorizationServers/read | Pobierz listę serwerów autoryzacji lub pobrać szczegółów serwera autoryzacji |
> | Akcja | Microsoft.ApiManagement/service/authorizationServers/write | Tworzenie nowego serwera autoryzacji lub szczegóły aktualizacji istniejącego serwera autoryzacji |
> | Akcja | Microsoft.ApiManagement/service/backends/delete | Usuń istniejące wewnętrznej bazy danych |
> | Akcja | Microsoft.ApiManagement/service/backends/read | Pobierz listę zapleczy lub uzyskać szczegółowe informacje o wewnętrznej bazy danych |
> | Akcja | Microsoft.ApiManagement/service/backends/reconnect/action | Utwórz żądanie ponownego nawiązania połączenia |
> | Akcja | Microsoft.ApiManagement/service/backends/write | Dodać nowego zaplecza lub zaktualizować istniejący szczegóły wewnętrznej bazy danych |
> | Akcja | Microsoft.ApiManagement/service/backup/action | Usługi zarządzania interfejsu API tworzenia kopii zapasowej do określonego kontenera użytkownika podane konto magazynu |
> | Akcja | Microsoft.ApiManagement/service/certificates/delete | Usuń istniejący certyfikat |
> | Akcja | Microsoft.ApiManagement/service/certificates/read | Pobierz listę certyfikatów lub Pobierz szczegóły certyfikatu |
> | Akcja | Microsoft.ApiManagement/service/certificates/write | Dodaj nowy certyfikat |
> | Akcja | Microsoft.ApiManagement/service/delete | Usuń wystąpienia usługi interfejsu API zarządzania |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/delete | Usuń istniejące diagnostyki |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/loggers/delete | Usuń mapowanie rejestratora przy użyciu ustawienia diagnostyczne |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/loggers/read | Pobierz listę istniejących diagnostycznych rejestratorów |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/loggers/write | Mapowanie rejestratora na ustawienie diagnostyczne |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/read | Pobierz listę diagnostyki lub pobranie szczegółów diagnostyki |
> | Akcja | Microsoft.ApiManagement/service/diagnostics/write | Dodaj nowe diagnostyki lub zaktualizować istniejący szczegóły diagnostyczne |
> | Akcja | Microsoft.ApiManagement/service/getssotoken/action | Pobiera token logowania jednokrotnego, który może służyć do logowania do portalu starsza wersja usługi zarządzania interfejsu API z uprawnieniami administratora |
> | Akcja | Microsoft.ApiManagement/service/groups/delete | Usuń istniejącą grupę |
> | Akcja | Microsoft.ApiManagement/service/groups/read | Pobierz listę grup lub pobiera szczegóły grupy |
> | Akcja | Microsoft.ApiManagement/service/groups/users/delete | Usuwanie istniejącego użytkownika z istniejącej grupy |
> | Akcja | Microsoft.ApiManagement/service/groups/users/read | Pobierz listę grupy użytkowników |
> | Akcja | Microsoft.ApiManagement/service/groups/users/write | Dodaj istniejącego użytkownika do istniejącej grupy |
> | Akcja | Microsoft.ApiManagement/service/groups/write | Utwórz nową grupę lub zaktualizować istniejący szczegóły grupy |
> | Akcja | Microsoft.ApiManagement/service/identityProviders/delete | Usuń istniejące dostawcy tożsamości |
> | Akcja | Microsoft.ApiManagement/service/identityProviders/read | Pobierz listę dostawców tożsamości lub Uzyskaj szczegóły dostawcy tożsamości |
> | Akcja | Microsoft.ApiManagement/service/identityProviders/write | Utwórz nowe szczegóły dostawcy tożsamości lub aktualizację istniejącego dostawcy tożsamości |
> | Akcja | Microsoft.ApiManagement/service/locations/networkstatus/read | Pobiera stan dostępu do sieci, zasobów, na których zależy od usługi w lokalizacji. |
> | Akcja | Microsoft.ApiManagement/service/loggers/delete | Usuń istniejące rejestratora |
> | Akcja | Microsoft.ApiManagement/service/loggers/read | Pobierz listę rejestratorów lub pobrać szczegółów rejestratora |
> | Akcja | Microsoft.ApiManagement/service/loggers/write | Dodać nowego rejestratora lub zaktualizować istniejący szczegóły rejestratora |
> | Akcja | Microsoft.ApiManagement/service/managedeployments/action | Zmień jednostki SKU/jednostki, dodawania i usuwania wdrożenia regionalnych interfejsu API usługi zarządzania |
> | Akcja | Microsoft.ApiManagement/service/networkstatus/read | Pobiera stan dostępu do sieci, zasobów, na których zależy od usługi. |
> | Akcja | Microsoft.ApiManagement/service/notifications/action | Wysyła powiadomienie do określonego użytkownika |
> | Akcja | Microsoft.ApiManagement/service/notifications/read | Pobiera wszystkie powiadomienia wydawcy interfejsu API zarządzania lub uzyskać zarządzanie interfejsami API wydawcy szczegóły powiadomienia |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Usuwa istniejące adresu E-mail skojarzonego z powiadomienia |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Pobierz adresatów wiadomości E-mail skojarzony z interfejsu API zarządzania wydawcy powiadomień |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Utwórz nowy adres E-mail odbiorcy powiadomienia |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Usuwa użytkownika skojarzonego z adresatów powiadomień |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Pobierz odbiorcy użytkownicy skojarzeni z tym powiadomienie |
> | Akcja | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Dodaj użytkownika do adresatów powiadomień |
> | Akcja | Microsoft.ApiManagement/service/notifications/write | Tworzenie lub aktualizacja interfejsu API zarządzania wydawcy powiadomień |
> | Akcja | Microsoft.ApiManagement/service/openidConnectProviders/delete | Usuń istniejące OpenID Connect dostawcy |
> | Akcja | Microsoft.ApiManagement/service/openidConnectProviders/read | Pobierz listę dostawców OpenID Connect lub pobranie szczegółów OpenID Connect dostawcy |
> | Akcja | Microsoft.ApiManagement/service/openidConnectProviders/write | Utwórz nowe szczegóły OpenID Connect dostawcy lub aktualizację istniejącego dostawcy uwierzytelniania OpenID Connect |
> | Akcja | Microsoft.ApiManagement/service/operationresults/read | Pobiera bieżący stan długotrwałej operacji |
> | Akcja | Microsoft.ApiManagement/service/policies/delete | Usuń konfigurację zasad od dzierżawcy zasad |
> | Akcja | Microsoft.ApiManagement/service/policies/read | Pobierz zasady dla dzierżawy lub Get szczegóły konfiguracji zasad dla dzierżawcy |
> | Akcja | Microsoft.ApiManagement/service/policies/write | Ustaw szczegóły konfiguracji zasad dla dzierżawcy |
> | Akcja | Microsoft.ApiManagement/service/policySnippets/read | Pobierz wszystkie fragmenty zasad |
> | Akcja | Microsoft.ApiManagement/service/portalsettings/read | Tworzenie konta ustawienia uzyskać portalu lub Get podpisywania w ustawieniach portalu lub pobrać ustawień delegowania dla portalu |
> | Akcja | Microsoft.ApiManagement/service/portalsettings/write | Zaktualizować ustawień Utwórz konto lub ustawienia aktualizacji Utwórz konto lub ustawienia aktualizacji logowania lub ustawienia aktualizacji logowania lub ustawienia delegowania aktualizacji lub ustawienia delegowania aktualizacji |
> | Akcja | Microsoft.ApiManagement/service/products/apis/delete | Usuń istniejące interfejsu API z istniejącego produktu |
> | Akcja | Microsoft.ApiManagement/service/products/apis/read | Pobierz listę dodane do istniejących interfejsów API |
> | Akcja | Microsoft.ApiManagement/service/products/apis/write | Dodaj istniejący interfejs API do istniejącego produktu |
> | Akcja | Microsoft.ApiManagement/service/products/delete | Usuń istniejące produkt |
> | Akcja | Microsoft.ApiManagement/service/products/groups/delete | Usuń skojarzenie istniejąca grupa deweloperów z istniejącego produktu |
> | Akcja | Microsoft.ApiManagement/service/products/groups/read | Pobierz listę grup developer skojarzonych z produktu |
> | Akcja | Microsoft.ApiManagement/service/products/groups/write | Skojarz istniejąca grupa deweloperów z istniejącego produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policies/delete | Usuń konfigurację zasad od zasad produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policies/read | Pobierz zasady produktu lub Get zasad szczegółowe informacje dotyczące konfiguracji produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policies/write | Ustaw szczegóły konfiguracji zasad produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policy/delete | Usuń zasady konfiguracji z istniejącego produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policy/read | Uzyskiwanie konfiguracji zasad istniejącego produktu |
> | Akcja | Microsoft.ApiManagement/service/products/policy/write | Ustaw konfigurację zasad dla istniejącego produktu |
> | Akcja | Microsoft.ApiManagement/service/products/read | Pobierz listę produktów lub uzyskać szczegółowe informacje o produkcie |
> | Akcja | Microsoft.ApiManagement/service/products/subscriptions/read | Pobierz listę subskrypcji produktu |
> | Akcja | Microsoft.ApiManagement/service/products/tags/delete | Usuń powiązanie istniejącego znacznika z istniejącego produktu |
> | Akcja | Microsoft.ApiManagement/service/products/tags/read | Pobierz Znaczniki skojarzone z szczegóły produktu lub pobrać tagu |
> | Akcja | Microsoft.ApiManagement/service/products/tags/write | Skojarz istniejącego znacznika z istniejącego produktu |
> | Akcja | Microsoft.ApiManagement/service/products/write | Tworzenie nowego produktu lub zaktualizować istniejące informacje szczegółowe |
> | Akcja | Microsoft.ApiManagement/service/properties/delete | Usuwa istniejące właściwości |
> | Akcja | Microsoft.ApiManagement/service/properties/read | Pobiera listę właściwości wszystkich lub pobiera szczegóły określonej właściwości |
> | Akcja | Microsoft.ApiManagement/service/properties/write | Tworzy nową właściwość lub aktualizuje wartość dla określonej właściwości |
> | Akcja | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla usługi Zarządzanie interfejsami API |
> | Akcja | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla usługi Zarządzanie interfejsami API |
> | Akcja | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla usługi Zarządzanie interfejsami API |
> | Akcja | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla usługi Zarządzanie interfejsami API |
> | Akcja | Microsoft.ApiManagement/service/quotas/periods/read | Pobierz wartość licznika przydziału dla okresu |
> | Akcja | Microsoft.ApiManagement/service/quotas/periods/write | Ustaw bieżącą wartość licznika przydziału |
> | Akcja | Microsoft.ApiManagement/service/quotas/read | Pobiera wartości dla limitu przydziału |
> | Akcja | Microsoft.ApiManagement/service/quotas/write | Ustaw bieżącą wartość licznika przydziału |
> | Akcja | Microsoft.ApiManagement/service/read | Odczytać metadanych dla wystąpienia interfejsu API usługi zarządzania |
> | Akcja | Microsoft.ApiManagement/service/reports/read | Pobierz raport w okresach lub raportu Get w regionie geograficznym lub raportu Get agregowana przez deweloperów.<br>lub Załóż raportu w produktów.<br>lub Załóż agregowane przez interfejsy API lub Get raportu w operacji lub Get raportu w subskrypcji raportu.<br>lub pobrać żądań danych raportowania |
> | Akcja | Microsoft.ApiManagement/service/restore/action | Przywracanie usługi interfejsu API zarządzania z kontenera określonej w użytkownika podane konto magazynu |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/delete | Usuń subskrypcję. Ta operacja może służyć do usuwania subskrypcji |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/read | Pobiera listę subskrypcji produktu lub uzyskać szczegółów subskrypcji produktu |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Ponowne generowanie klucza podstawowego subskrypcji |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Ponowne wygenerowanie klucza pomocniczego subskrypcji |
> | Akcja | Microsoft.ApiManagement/service/subscriptions/write | Subskrypcja istniejącego użytkownika do istniejącego produktu lub zaktualizować istniejący Szczegóły subskrypcji. Ta operacja może służyć do odnowienia subskrypcji |
> | Akcja | Microsoft.ApiManagement/service/tagResources/read | Pobierz listę tagów ze skojarzonymi zasobami |
> | Akcja | Microsoft.ApiManagement/service/tags/delete | Usuń istniejące Tag |
> | Akcja | Microsoft.ApiManagement/service/tags/read | Pobierz listę tagów lub pobrać szczegółów znacznika |
> | Akcja | Microsoft.ApiManagement/service/tags/write | Dodaj nowy znacznik lub zaktualizować istniejący Szczegóły tagu |
> | Akcja | Microsoft.ApiManagement/service/templates/delete | Resetuj domyślny szablon wiadomości e-mail zarządzanie interfejsami API |
> | Akcja | Microsoft.ApiManagement/service/templates/read | Pobiera wszystkie szablony wiadomości e-mail lub wiadomości e-mail pobiera zarządzanie interfejsami API Szczegóły szablonu |
> | Akcja | Microsoft.ApiManagement/service/templates/write | Utwórz lub zaktualizuj zarządzanie interfejsami API szablon wiadomości e-mail lub aktualizuje zarządzanie interfejsami API szablon wiadomości e-mail |
> | Akcja | Microsoft.ApiManagement/service/tenant/delete | Usuń konfigurację zasad dla dzierżawcy |
> | Akcja | Microsoft.ApiManagement/service/tenant/deploy/action | Uruchamia zadanie wdrażania można zastosować zmian z gałęzi git określony w konfiguracji w bazie danych. |
> | Akcja | Microsoft.ApiManagement/service/tenant/operationResults/read | Pobierz listę wyników operacji lub uzyskanie wyniku operacji |
> | Akcja | Microsoft.ApiManagement/service/tenant/read | Pobierz konfigurację zasad dla Get lub dzierżawy szczegółowe informacje dotyczące dostępu |
> | Akcja | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Ponownie wygenerować podstawowy klucz dostępu |
> | Akcja | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Wygeneruj ponownie pomocniczy klucz dostępu |
> | Akcja | Microsoft.ApiManagement/service/tenant/save/action | Tworzy zatwierdzenia z konfiguracji migawek dla określonej gałęzi w repozytorium |
> | Akcja | Microsoft.ApiManagement/service/tenant/syncState/read | Pobierz stan ostatniej synchronizacji git |
> | Akcja | Microsoft.ApiManagement/service/tenant/validate/action | Weryfikuje zmian z gałęzi git określony |
> | Akcja | Microsoft.ApiManagement/service/tenant/write | Ustaw konfigurację zasad dla dzierżawy lub szczegółowe informacje dotyczące aktualizacji dzierżawy dostępu |
> | Akcja | Microsoft.ApiManagement/service/updatecertificate/action | Przekaż certyfikat protokołu SSL dla interfejsu API usługi zarządzania |
> | Akcja | Microsoft.ApiManagement/service/updatehostname/action | Konfiguracja, zaktualizować lub usunąć nazwy domeny niestandardowej dla usługi interfejsu API zarządzania |
> | Akcja | Microsoft.ApiManagement/service/users/action | Zarejestruj nowy użytkownik |
> | Akcja | Microsoft.ApiManagement/service/users/applications/attachments/delete | Usuwa załącznika |
> | Akcja | Microsoft.ApiManagement/service/users/applications/attachments/read | Pobiera załączniki aplikacji lub pobiera załącznika |
> | Akcja | Microsoft.ApiManagement/service/users/applications/attachments/write | Dodaj załącznik do aplikacji |
> | Akcja | Microsoft.ApiManagement/service/users/applications/delete | Usuwa istniejące aplikacji |
> | Akcja | Microsoft.ApiManagement/service/users/applications/read | Pobierz listę wszystkich aplikacji użytkownika lub szczegóły aplikacji pobiera zarządzanie interfejsami API |
> | Akcja | Microsoft.ApiManagement/service/users/applications/write | Rejestruje aplikację interfejsu API zarządzania lub aktualizacje szczegóły aplikacji |
> | Akcja | Microsoft.ApiManagement/service/users/delete | Usuń konto użytkownika |
> | Akcja | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Generowania adresu URL logowania jednokrotnego. Adres URL można uzyskać dostęp do portalu administratora |
> | Akcja | Microsoft.ApiManagement/service/users/groups/read | Pobierz listę grup użytkowników |
> | Akcja | Microsoft.ApiManagement/service/users/keys/read | Pobierz listę kluczy użytkownika |
> | Akcja | Microsoft.ApiManagement/service/users/read | Pobierz listę zarejestrowanych użytkowników lub pobrać szczegółów konta użytkownika |
> | Akcja | Microsoft.ApiManagement/service/users/subscriptions/read | Pobierz listę subskrypcji użytkownika |
> | Akcja | Microsoft.ApiManagement/service/users/token/action | Uzyskaj token dostępu token dla użytkownika |
> | Akcja | Microsoft.ApiManagement/service/users/write | Zarejestrować nowego użytkownika lub szczegóły konta aktualizacji istniejącego użytkownika |
> | Akcja | Microsoft.ApiManagement/service/write | Utwórz nowe wystąpienie interfejsu API usługi zarządzania |
> | Akcja | Microsoft.ApiManagement/unregister/action | Wyrejestrowanie subskrypcji dla dostawcy zasobów Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Authorization/checkAccess/action | Sprawdza, czy element wywołujący ma autoryzację do wykonania określonej akcji |
> | Akcja | Microsoft.Authorization/classicAdministrators/delete | Usuwa administratora z subskrypcji. |
> | Akcja | Microsoft.Authorization/classicAdministrators/read | Odczytuje administratorów dla subskrypcji. |
> | Akcja | Microsoft.Authorization/classicAdministrators/write | Dodaje lub modyfikuje administratora dla subskrypcji. |
> | Akcja | Microsoft.Authorization/elevateAccess/action | Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy |
> | Akcja | Microsoft.Authorization/locks/delete | Usuwa blokady w podanym zakresie. |
> | Akcja | Microsoft.Authorization/locks/read | Pobiera blokady w podanym zakresie. |
> | Akcja | Microsoft.Authorization/locks/write | Dodaje blokady w podanym zakresie. |
> | Akcja | Microsoft.Authorization/permissions/read | Wyświetla wszystkie uprawnienia obiektu wywołującego w danym zakresie. |
> | Akcja | Microsoft.Authorization/policyAssignments/delete | Usuń przypisanie zasad w podanym zakresie. |
> | Akcja | Microsoft.Authorization/policyAssignments/read | Pobierz informacje o przypisaniu zasad. |
> | Akcja | Microsoft.Authorization/policyAssignments/write | Utwórz przypisanie zasad w podanym zakresie. |
> | Akcja | Microsoft.Authorization/policyDefinitions/delete | Usuń definicję zasad. |
> | Akcja | Microsoft.Authorization/policyDefinitions/read | Pobierz informacje o definicji zasad. |
> | Akcja | Microsoft.Authorization/policyDefinitions/write | Utwórz niestandardową definicję zasad. |
> | Akcja | Microsoft.Authorization/policySetDefinitions/delete | Usuń definicję zestawu zasad. |
> | Akcja | Microsoft.Authorization/policySetDefinitions/read | Pobierz informacje o definicji zestawu zasad. |
> | Akcja | Microsoft.Authorization/policySetDefinitions/write | Utwórz niestandardową definicję zestawu zasad. |
> | Akcja | Microsoft.Authorization/providerOperations/read | Pobiera operacje dla wszystkich dostawców zasobów do użycia w definicjach ról. |
> | Akcja | Microsoft.Authorization/roleAssignments/delete | Usuwa przypisanie roli w podanym zakresie. |
> | Akcja | Microsoft.Authorization/roleAssignments/read | Pobiera informacje o przypisaniu roli. |
> | Akcja | Microsoft.Authorization/roleAssignments/write | Tworzy przypisanie roli w podanym zakresie. |
> | Akcja | Microsoft.Authorization/roleDefinitions/delete | Usuwa określoną niestandardową definicję roli. |
> | Akcja | Microsoft.Authorization/roleDefinitions/read | Pobiera informacje o definicji roli. |
> | Akcja | Microsoft.Authorization/roleDefinitions/write | Tworzy lub aktualizuje niestandardową definicję roli z określonymi uprawnieniami i zakresami możliwymi do przypisania. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Przeczytaj informacje rejestracyjne DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Zapisuje żądanie, aby ponownie wygenerować klucze Konfiguracja DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/certificates/delete | Usuwa zasób certyfikatu usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/certificates/getCount/action | Odczytuje liczba certyfikatów |
> | Akcja | Microsoft.Automation/automationAccounts/certificates/read | Pobiera zasób certyfikatu usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/certificates/write | Tworzy lub aktualizuje zasób certyfikatu usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/compilationjobs/read | Odczytuje kompilacji DSC usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/compilationjobs/read | Odczytuje kompilacji DSC usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapisuje kompilacji DSC usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapisuje kompilacji DSC usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/content/read | Odczytuje zawartość nośnika konfiguracji |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/delete | Usuwa zawartość DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/getCount/action | Odczytuje liczba zawartości DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/read | Pobiera zawartość DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/configurations/write | Zapisuje zawartość DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/connections/delete | Usuwa zasób połączenia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/connections/getCount/action | Odczytuje liczba połączeń |
> | Akcja | Microsoft.Automation/automationAccounts/connections/read | Pobiera zasób połączenia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/connections/write | Tworzy lub aktualizuje zasób połączenia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/connectionTypes/delete | Usuwa zasób typu połączenia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/connectionTypes/read | Pobiera zasób typu połączenia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/connectionTypes/write | Tworzy zasób typu połączenia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/delete | Usuwa zasób poświadczenia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/getCount/action | Odczytuje liczba poświadczeń |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/read | Pobiera zasób poświadczenia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/credentials/write | Tworzy lub aktualizuje zasób poświadczenia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/delete | Usuwa konto usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Automation/automationAccounts/diagnosticSettings/write | Konfiguruje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Usuwa zasoby hybrydowego procesu roboczego elementu Runbook |
> | Akcja | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/output/read | Pobiera dane wyjściowe zadania |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Pobiera zawartość elementu runbook usługi Automatyzacja Azure w czasie wykonywania zadania |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/stop/action | Zatrzymuje zadanie usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/jobSchedules/delete | Usuwa harmonogram zadań usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/jobSchedules/read | Pobiera harmonogram zadań usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/jobSchedules/write | Tworzy harmonogram zadań usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem automatyzacji |
> | Akcja | Microsoft.Automation/automationAccounts/listKeys/action | Odczytuje kluczy dla konta automatyzacji |
> | Akcja | Microsoft.Automation/automationAccounts/logDefinitions/read | Pobiera dostępne dzienniki dla konta automatyzacji |
> | Akcja | Microsoft.Automation/automationAccounts/modules/activities/read | Pobiera działania usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/modules/delete | Usuwa moduł usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/modules/getCount/action | Pobiera liczbę modułów w ramach konta automatyzacji |
> | Akcja | Microsoft.Automation/automationAccounts/modules/read | Pobiera moduł usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/modules/write | Tworzy lub aktualizuje moduł usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Usuwa konfiguracji węzła DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Odczytuje zawartość konfiguracji węzła DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Odczytuje konfiguracji węzła DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Zapisuje konfiguracji węzła DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodecounts/read | Odczytuje podsumowania liczba węzłów dla określonego typu |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/delete | Usuwa węzłów DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/read | Odczytuje węzłów DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Odczytuje zawartość raportu konfiguracji DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/reports/read | Odczytuje raporty konfiguracji DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/nodes/write | Tworzy lub aktualizuje węzłów DSC automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Pobiera TypeFields usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Pobiera definicje metryk automatyzacji |
> | Akcja | Microsoft.Automation/automationAccounts/read | Pobiera konto usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/content/read | Pobiera zawartość elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/delete | Usuwa element runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Tworzy zawartość wersji roboczej elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Pobiera wyniki operacji wersję roboczą elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/publish/action | Publikuje wersję roboczą elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/read | Pobiera wersję roboczą elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Pobiera zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Wznawia zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Zatrzymuje zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Wstrzymuje zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Tworzy zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Cofnij operacje edycji próbnej wersji elementu runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Pobiera liczbę elementów runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/runbooks/write | Tworzy lub aktualizuje element runbook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/delete | Usuwa zasób harmonogramu usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/getCount/action | Pobiera liczbę harmonogramy automatyzacji Azure |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/read | Pobiera zasób harmonogramu usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/schedules/write | Tworzy lub aktualizuje zasób harmonogramu usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/statistics/read | Pobiera statystyki usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Pobierz maszyny wdrażania aktualizacji usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Pobiera zadanie poprawki zarządzania aktualizacji usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/usages/read | Pobiera użycia usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/variables/delete | Usuwa zasób zmiennej usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/variables/read | Odczytuje zasób zmiennej usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/variables/write | Tworzy lub aktualizuje zasób zmiennej usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/delete | Usunąć obserwatora zadanie usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/read | Pobiera zadanie usługi Automatyzacja Azure obserwatora |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/start/action | Uruchomienie zadania obserwatora usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/stop/action | Zatrzymaj zadanie usługi Automatyzacja Azure obserwatora |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/streams/read | Pobiera strumień zadań usługi Automatyzacja Azure obserwatora |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Usuwanie akcji zadania obserwatora usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Pobiera akcje zadania obserwatora usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Tworzenie akcji zadania obserwatora usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/watchers/write | Tworzy zadanie obserwatora usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/webhooks/action | Generuje identyfikator URI dla elementu webhook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/webhooks/delete | Usuwa element webhook usługi Automatyzacja Azure  |
> | Akcja | Microsoft.Automation/automationAccounts/webhooks/read | Odczytuje element webhook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/webhooks/write | Tworzy lub aktualizuje element webhook usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/automationAccounts/write | Tworzy lub aktualizuje konto usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/operations/read | Pobiera dostępne operacje dla zasobów usługi Automatyzacja Azure |
> | Akcja | Microsoft.Automation/register/action | Rejestruje subskrypcję usługi Automatyzacja Azure |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Usuń zasób katalogu B2C |
> | Akcja | Microsoft.AzureActiveDirectory/b2cDirectories/read | Wyświetl zasób katalogu B2C |
> | Akcja | Microsoft.AzureActiveDirectory/b2cDirectories/write | Utwórz lub zaktualizuj zasób katalogu B2C |
> | Akcja | Microsoft.AzureActiveDirectory/operations/read | Odczytuj wszystkie operacje interfejsu API dostępne dla dostawcy zasobów Microsoft.AzureActiveDirectory |
> | Akcja | Microsoft.AzureActiveDirectory/register/action | Rejestruj subskrypcję dostawcy zasobów Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.AzureStack/Operations/read | Pobiera właściwości operacji dostawcy zasobów |
> | Akcja | Microsoft.AzureStack/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.AzureStack |
> | Akcja | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Usuwa subskrypcji platformy Azure stosu klienta |
> | Akcja | Microsoft.AzureStack/registrations/customerSubscriptions/read | Pobiera właściwości subskrypcji platformy Azure stosu klienta |
> | Akcja | Microsoft.AzureStack/registrations/customerSubscriptions/write | Tworzy lub aktualizuje subskrypcji platformy Azure stosu klienta |
> | Akcja | Microsoft.AzureStack/registrations/delete | Usuwa rejestrację Azure stosu |
> | Akcja | Microsoft.AzureStack/registrations/getActivationKey/action | Pobiera najnowsze klucza aktywacji Azure stosu |
> | Akcja | Microsoft.AzureStack/registrations/products/listDetails/action | Pobiera rozszerzony szczegóły produktu Azure Marketplace stosu |
> | Akcja | Microsoft.AzureStack/registrations/products/read | Pobiera właściwości produktu Azure Marketplace stosu |
> | Akcja | Microsoft.AzureStack/registrations/read | Pobiera właściwości rejestracji Azure stosu |
> | Akcja | Microsoft.AzureStack/registrations/write | Tworzy lub aktualizuje rejestracji Azure stosu |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Batch/batchAccounts/applications/delete | Usuwa aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/read | Wyświetla listę aplikacji lub pobiera właściwości aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktywuje pakietu aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/delete | Usuwa pakiet aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/read | Pobiera właściwości pakietu aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/versions/write | Tworzy nowy pakiet aplikacji lub aktualizuje istniejący pakiet aplikacji |
> | Akcja | Microsoft.Batch/batchAccounts/applications/write | Tworzy nową aplikację lub aktualizuje istniejącą aplikację |
> | Akcja | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Pobiera wyniki długotrwałej operacji certyfikatu na konto usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Anuluje nie powiodło się usunięcie certyfikatu na konto usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/delete | Usuwa certyfikat z konta usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/read | Wyświetla listę certyfikatów na konto usługi partia zadań lub pobiera właściwości certyfikatu |
> | Akcja | Microsoft.Batch/batchAccounts/certificates/write | Tworzy nowy certyfikat na konto usługi partia zadań lub aktualizuje istniejący certyfikat |
> | Akcja | Microsoft.Batch/batchAccounts/delete | Usuwa konto usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/listkeys/action | Wyświetla uzyskać dostęp do kluczy dla konta usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/operationResults/read | Pobiera wyniki długotrwałej operacji konto usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/poolOperationResults/read | Pobiera wyniki długotrwałej operacji puli na konto usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/pools/delete | Usuwa pulę z konta usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Powoduje wyłączenie automatycznego skalowania puli konta usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/pools/read | Wyświetla listę pul na konto usługi partia zadań lub pobiera właściwości puli |
> | Akcja | Microsoft.Batch/batchAccounts/pools/stopResize/action | Zatrzymuje jako bieżące zmiany rozmiaru operacji w puli konta usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Uaktualnia system operacyjny puli konta usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/pools/write | Tworzy nową pulę na konto usługi partia zadań lub aktualizuje istniejącą pulę |
> | Akcja | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/read | Lista kont usługi partia zadań lub pobiera właściwości konta usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/regeneratekeys/action | Odtwarza uzyskać dostęp do kluczy dla konta usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchronizuje klucze dostępu dla konta magazynu automatycznie skonfigurowany dla konta usługi partia zadań |
> | Akcja | Microsoft.Batch/batchAccounts/write | Tworzy nowe konto wsadowe lub aktualizuje istniejące konto usługi partia zadań |
> | Akcja | Microsoft.Batch/locations/checkNameAvailability/action | Sprawdza, czy nazwa konta jest nieprawidłowa i nie w użyciu. |
> | Akcja | Microsoft.Batch/locations/quotas/read | Pobiera limity przydziału partii określonej subskrypcji w wybranym regionie Azure |
> | Akcja | Microsoft.Batch/register/action | Rejestruje subskrypcję dostawcy zasobów partii i umożliwia tworzenie konta usługi partia zadań |
> | Akcja | Microsoft.Batch/unregister/action | Wyrejestrowuje subskrypcji dla dostawcy zasobów partii uniemożliwia tworzenie kont usługi partia zadań |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.BatchAI/clusters/delete | Usuwa klastra partii AI |
> | Akcja | Microsoft.BatchAI/clusters/read | Wyświetla listę klastrów AI partii lub pobiera właściwości klastra partii AI |
> | Akcja | Microsoft.BatchAI/clusters/remoteLoginInformation/action | Wyświetla informacje logowania zdalnego dla klastra partii AI |
> | Akcja | Microsoft.BatchAI/clusters/write | Tworzy nowy klaster AI partii lub aktualizuje istniejący klaster AI partii |
> | Akcja | Microsoft.BatchAI/fileservers/delete | Usuwa plików wsadowych AI |
> | Akcja | Microsoft.BatchAI/fileservers/read | Wyświetla listę fileservers AI partii lub pobiera właściwości plików wsadowych AI |
> | Akcja | Microsoft.BatchAI/fileservers/resume/action | Wznawia plików wsadowych AI |
> | Akcja | Microsoft.BatchAI/fileservers/suspend/action | Wstrzymuje plików wsadowych AI |
> | Akcja | Microsoft.BatchAI/fileservers/write | Tworzy nowy plików AI partii lub aktualizuje istniejące plików AI partii |
> | Akcja | Microsoft.BatchAI/jobs/delete | Usuwa zadanie wsadowe AI |
> | Akcja | Microsoft.BatchAI/jobs/read | Zawiera listę zadań wsadowych AI lub pobiera właściwości zadania wsadowego AI |
> | Akcja | Microsoft.BatchAI/jobs/remoteLoginInformation/action | Wyświetla informacje logowania zdalnego zadania wsadowego AI |
> | Akcja | Microsoft.BatchAI/jobs/terminate/action | Kończy zadanie wsadowe AI |
> | Akcja | Microsoft.BatchAI/jobs/write | Tworzy nowe zadanie wsadowe AI lub aktualizuje istniejące zadanie wsadowe AI |
> | Akcja | Microsoft.BatchAI/register/action | Rejestruje subskrypcję dostawcy zasobów AI partii i umożliwia tworzenie zasobów partii AI |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Billing/billingPeriods/read | Wyświetla listę dostępnych okresów rozliczeń |
> | Akcja | Microsoft.Billing/invoices/read | Wyświetla dostępne faktury |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.BingMaps/mapApis/Delete | Operacja usuwania |
> | Akcja | Microsoft.BingMaps/mapApis/listSecrets/action | Wyświetl klucze tajne |
> | Akcja | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Odczyt tokenu autoryzacji logowania jednokrotnego dla zasobu |
> | Akcja | Microsoft.BingMaps/mapApis/Read | Operacja odczytu |
> | Akcja | Microsoft.BingMaps/mapApis/regenerateKey/action | Generuje ponownie klucz |
> | Akcja | Microsoft.BingMaps/mapApis/Write | Operacja zapisu |
> | Akcja | Microsoft.BingMaps/Operations/read | Opis operacji. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Cache/checknameavailability/action | Sprawdza, czy nazwa jest dostępna do użycia z nową pamięcią podręczną Redis Cache |
> | Akcja | Microsoft.Cache/locations/operationresults/read | Pobiera wynik długotrwałej operacji, dla której nagłówek „Location” został wcześniej zwrócony do klienta |
> | Akcja | Microsoft.Cache/operations/read | Wyświetla listę operacji obsługiwanych przez dostawcę „Microsoft.Cache”. |
> | Akcja | Microsoft.Cache/redis/delete | Usuń całą pamięć podręczną Redis |
> | Akcja | Microsoft.Cache/redis/export/action | Wyeksportuj dane usługi Redis do określonych prefiksem obiektów blob magazynu w podanym formacie |
> | Akcja | Microsoft.Cache/redis/firewallRules/delete | Usuń reguły zapory IP pamięci podręcznej Redis Cache |
> | Akcja | Microsoft.Cache/redis/firewallRules/read | Pobierz reguły zapory IP pamięci podręcznej Redis Cache |
> | Akcja | Microsoft.Cache/redis/firewallRules/write | Edytuj reguły zapory IP pamięci podręcznej Redis Cache |
> | Akcja | Microsoft.Cache/redis/forceReboot/action | Wymuś ponowny rozruch wystąpienia pamięci podręcznej, nawet z utratą danych. |
> | Akcja | Microsoft.Cache/redis/import/action | Zaimportuj dane w podanym formacie z wielu obiektów blob do usługi Redis |
> | Akcja | Microsoft.Cache/redis/linkedservers/delete | Usuń podłączony serwer z usługi Redis Cache |
> | Akcja | Microsoft.Cache/redis/linkedservers/read | Pobierz połączone serwery skojarzona z usługą Redis Cache. |
> | Akcja | Microsoft.Cache/redis/linkedservers/write | Dodaj połączony serwer do usługi Redis Cache |
> | Akcja | Microsoft.Cache/redis/listKeys/action | Wyświetl wartości kluczy dostępu pamięci podręcznej Redis w portalu zarządzania |
> | Akcja | Microsoft.Cache/redis/listUpgradeNotifications/read | Lista najnowszych powiadomień o uaktualnieniach dla dzierżawy pamięci podręcznej. |
> | Akcja | Microsoft.Cache/redis/metricDefinitions/read | Pobiera dostępne metryki pamięci podręcznej Redis |
> | Akcja | Microsoft.Cache/redis/patchSchedules/delete | Usuń harmonogram stosowania poprawek pamięci podręcznej Redis Cache |
> | Akcja | Microsoft.Cache/redis/patchSchedules/read | Pobiera harmonogram stosowania poprawek pamięci podręcznej Redis Cache |
> | Akcja | Microsoft.Cache/redis/patchSchedules/write | Modyfikuj harmonogram stosowania poprawek pamięci podręcznej Redis Cache |
> | Akcja | Microsoft.Cache/redis/read | Wyświetl ustawienia i konfigurację pamięci podręcznej Redis w portalu zarządzania |
> | Akcja | Microsoft.Cache/redis/regenerateKey/action | Zmień wartość kluczy dostępu pamięci podręcznej Redis w portalu zarządzania |
> | Akcja | Microsoft.Cache/redis/start/action | Uruchom wystąpienie pamięci podręcznej. |
> | Akcja | Microsoft.Cache/redis/stop/action | Zatrzymaj wystąpienie pamięci podręcznej. |
> | Akcja | Microsoft.Cache/redis/write | Zmodyfikuj ustawienia i konfigurację pamięci podręcznej Redis w portalu zarządzania |
> | Akcja | Microsoft.Cache/register/action | Rejestruje dostawcę zasobów „Microsoft.Cache” z subskrypcją |
> | Akcja | Microsoft.Cache/unregister/action | Wyrejestrowuje dostawcę zasobów „Microsoft.Cache” z subskrypcją |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Capacity/register/action | Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie pojemności zasobów. |
> | Akcja | Microsoft.Capacity/reservationorders/action | Zaktualizuj zastrzeżenie |
> | Akcja | Microsoft.Capacity/reservationorders/delete | Usuń zastrzeżenie |
> | Akcja | Microsoft.Capacity/reservationorders/read | Wszystkie rezerwacje do odczytu |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/action | Zaktualizuj zastrzeżenie |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/delete | Usuń zastrzeżenie |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/read | Wszystkie rezerwacje do odczytu |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/revisions/read | Wszystkie rezerwacje do odczytu |
> | Akcja | Microsoft.Capacity/reservationorders/reservations/write | Utwórz zastrzeżenie |
> | Akcja | Microsoft.Capacity/reservationorders/write | Utwórz zastrzeżenie |

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
> | Akcja | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyki dla zasobu |
> | Akcja | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyki dla zasobu |
> | Akcja | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla Microsoft.Cdn |
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
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/Delete | Usuń istniejące AppServiceCertificate |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/Read | Pobierz listę CertificateOrders |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Wydaj ponownie istniejących certificateorder |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Odnów istniejący certificateorder |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Ponowne wysyłanie wiadomości e-mail certyfikatu |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Ponowne wysyłanie wiadomości e-mail żądania do innego adresu e-mail |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Pobrać zamknięcia lokacji w celu wystawiony certyfikat usługi aplikacji |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Pobierz listę akcji certyfikatu |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Pobieranie certyfikatu historii wiadomości e-mail |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Weryfikowanie własności domeny |
> | Akcja | Microsoft.CertificateRegistration/certificateOrders/Write | Dodaj nowy certificateOrder lub zaktualizuj istniejącą |
> | Akcja | Microsoft.CertificateRegistration/operations/Read | Wyświetl listę wszystkich działań z rejestracji certyfikatu usługi aplikacji |
> | Akcja | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Zainicjuj obsługę nazwy głównej usługi dla nazwy głównej usługi aplikacji |
> | Akcja | Microsoft.CertificateRegistration/register/action | Rejestrowanie dostawcy zasobów Certificates firmy Microsoft dla subskrypcji |
> | Akcja | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Sprawdzanie poprawności obiektu zakupu certyfikatu bez przesyłania |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ClassicCompute/capabilities/read | Pokazuje możliwości |
> | Akcja | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Sprawdza dostępność danej nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/active/write | Ustawia nazwę aktywnej domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Pokazuje zestaw dostępności dla zasobu. |
> | Akcja | Microsoft.ClassicCompute/domainNames/capabilities/read | Pokazuje możliwości nazwy domeny |
> | Akcja | Microsoft.ClassicCompute/domainNames/delete | Usuwa nazwy domen dla zasobów. |
> | Akcja | Microsoft.ClassicCompute/domainNames/extensions/delete | Usuwa rozszerzenia nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Odczytuje stan operacji dla rozszerzeń nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/extensions/read | Zwraca rozszerzenia nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/extensions/write | Dodaje rozszerzenia nazwy domeny. |
> | Akcja | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Usuwa nową wewnętrzną usługę równoważenia obciążenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Odczytuje stan operacji dla wewnętrznych modułów równoważenia obciążenia nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Pobiera wewnętrzne usługi równoważenia obciążenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Tworzy nową wewnętrzną usługę równoważenia obciążenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Odczytuje stan operacji dla zestawów punktów końcowych z równoważeniem obciążenia nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Pokazuje zestawy punktów końcowych ze zrównoważonym obciążeniem |
> | Akcja | Microsoft.ClassicCompute/domainNames/read | Zwraca nazwy domen dla zasobów. |
> | Akcja | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Usuwa używane certyfikaty usługi. |
> | Akcja | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Odczytuje stan operacji dla certyfikatów usługi nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Zwraca używane certyfikaty usługi. |
> | Akcja | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Dodaje lub modyfikuje używane certyfikaty usługi. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/delete | Usuwa dane miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Odczytuje stan operacji dla gniazd nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/read | Pokazuje miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Usuwa odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Odczytuje stan operacji dla odwołań rozszerzenia ról w ramach gniazd nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Zwraca odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Dodaje lub modyfikuje odwołanie do rozszerzenia dla roli miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Pobiera definicje metryk. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/read | Pobiera rolę dla miejsca wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Odczytuje stan operacji dla wystąpień roli w ramach ról gniazd nazw domen. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Pobiera wystąpienie roli. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Ponownie kompiluje wystąpienie roli. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Odtwarza z obrazu wystąpienie roli. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Uruchamia ponownie wystąpienia roli. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/start/action | Uruchamia miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Zmienia stan miejsca wdrożenia na Zatrzymane. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Zmienia stan miejsca wdrożenia na Uruchomione. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/stop/action | Wstrzymuje miejsce wdrożenia. |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Uaktualnia domenę (z przeszukaniem). |
> | Akcja | Microsoft.ClassicCompute/domainNames/slots/write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft.ClassicCompute/domainNames/swap/action | Zamienia miejsce przejściowe z miejscem produkcyjnym. |
> | Akcja | Microsoft.ClassicCompute/domainNames/write | Dodaje lub modyfikuje nazwy domen dla zasobów. |
> | Akcja | Microsoft.ClassicCompute/moveSubscriptionResources/action | Przenieś wszystkie zasoby klasyczne do innej subskrypcji. |
> | Akcja | Microsoft.ClassicCompute/operatingSystemFamilies/read | Generuje listę rodzin systemów operacyjnych gościa dostępnych na platformie Microsoft Azure, a dodatkowo pokazuje wersje systemu operacyjnego dostępne w ramach każdej rodziny. |
> | Akcja | Microsoft.ClassicCompute/operatingSystems/read | Generuje listę wersji systemów operacyjnych gościa dostępnych obecnie na platformie Microsoft Azure. |
> | Akcja | Microsoft.ClassicCompute/quotas/read | Pobiera przydział dla subskrypcji. |
> | Akcja | Microsoft.ClassicCompute/register/action | Zarejestruj się w usłudze Classic Compute |
> | Akcja | Microsoft.ClassicCompute/resourceTypes/skus/read | Pobiera listę jednostek Sku dla obsługiwanych typów zasobów. |
> | Akcja | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Weryfikuj dostępność subskrypcji dla klasycznej operacji przenoszenia. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Pobiera dostępne operacje asynchroniczne |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Dołącza dysk z danymi do maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/delete | Usuwa maszyny wirtualne. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Odłącza dysk z danymi od maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/disks/read | Pobiera listę dysków z danymi |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Pobiera plik RDP dla maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Odczytuje stan operacji dla rozszerzeń maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/extensions/read | Pobiera rozszerzenie maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/extensions/write | Umieszcza rozszerzenie maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/metrics/read | Pobiera metryki. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszyną wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Odczytuje stan operacji dla maszyn wirtualnych. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Wykonuje konserwację maszyny wirtualnej. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Pobiera definicje metryk. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/read | Pobiera listę maszyn wirtualnych. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Ponownie wdraża maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/restart/action | Uruchamia ponownie maszyny wirtualne. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Zamyka maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/start/action | Uruchamia maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/stop/action | Zatrzymuje maszynę wirtualną. |
> | Akcja | Microsoft.ClassicCompute/virtualMachines/write | Dodaje lub modyfikuje maszyny wirtualne. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Pobiera listę obsługiwanych urządzeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grupy zabezpieczeń sieci. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Usuwa regułę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Odczytuje stan operacji dla reguł zabezpieczeń grupy zabezpieczeń sieci. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Pobiera regułę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Dodaje lub aktualizuje regułę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/networkSecurityGroups/write | Dodaje nową sieciową grupę zabezpieczeń. |
> | Akcja | Microsoft.ClassicNetwork/quotas/read | Pobiera przydział dla subskrypcji. |
> | Akcja | Microsoft.ClassicNetwork/register/action | Zarejestruj się w usłudze Classic Network |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/delete | Usuwa zastrzeżony adres IP. |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/join/action | Dołącz zarezerwowany adres IP |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/link/action | Połącz zarezerwowany adres IP |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Odczytuje stan operacji dla zarezerwowanych adresów IP. |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/read | Pobiera zastrzeżone adresy IP |
> | Akcja | Microsoft.ClassicNetwork/reservedIps/write | Dodaje nowy zastrzeżony adres IP |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Pokazuje możliwości |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Sprawdza dostępność danego adresu IP w sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/delete | Usuwa sieć wirtualną. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Odwołuje cofnięcie certyfikatu klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Odczytuje cofnięte certyfikaty klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Cofa certyfikat klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Usuwa certyfikat klienta bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Pobiera certyfikat na podstawie odcisku palca. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Wyświetla pakiet certyfikatu bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Znajduje certyfikaty główne klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Przekazuje nowy certyfikat główny klienta. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Tworzy połączenie bramy między lokacjami. |
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
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Uruchamia diagnostykę bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Zatrzymuje diagnostykę bramy sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Dodaje bramę sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/join/action | Umożliwia dołączenie do sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Odczytuje stan operacji dla sieci wirtualnych. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Łączy równorzędnie sieć wirtualną z inną siecią wirtualną. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/read | Pobiera sieć wirtualną. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Odczytuje stan operacji dla grupy zabezpieczeń sieci skojarzonej z podsiecią sieci wirtualnej. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Pobiera sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Dodaje sieciową grupę zabezpieczeń skojarzoną z podsiecią. |
> | Akcja | Microsoft.ClassicNetwork/virtualNetworks/write | Dodaje nową sieć wirtualną. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ClassicStorage/capabilities/read | Pokazuje możliwości |
> | Akcja | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Sprawdza dostępność konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/disks/read | Zwraca dysk konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/images/read | Zwraca obraz. |
> | Akcja | Microsoft.ClassicStorage/osImages/read | Zwraca obraz systemu operacyjnego. |
> | Akcja | Microsoft.ClassicStorage/publicImages/read | Pobiera publiczny obraz maszyny wirtualnej |
> | Akcja | Microsoft.ClassicStorage/quotas/read | Pobiera przydział dla subskrypcji. |
> | Akcja | Microsoft.ClassicStorage/register/action | Zarejestruj w magazynie klasycznym |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/delete | Usuwa konto magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/delete | Usuwa dany dysk konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/read | Zwraca dysk konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/disks/write | Dodaje dysk konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/images/delete | Usuwa określony obraz konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/images/read | Zwraca obraz konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla klucze dostępu dla kont magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Usuwa określony obraz systemu operacyjnego konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/osImages/read | Zwraca obraz systemu operacyjnego konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/read | Zwraca konto magazynu z podanym kontem. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Pobiera ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Dodaje lub modyfikuje ustawienia diagnostyczne. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Pobiera definicje metryk. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Pobiera metryki. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/services/read | Pobiera dostępne usługi. |
> | Akcja | Microsoft.ClassicStorage/storageAccounts/write | Dodaje nowe konto magazynu. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.CognitiveServices/accounts/delete | Usuwa konta interfejsów API |
> | Akcja | Microsoft.CognitiveServices/accounts/listKeys/action | Wylicz klucze |
> | Akcja | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla usług Cognitive Services. |
> | Akcja | Microsoft.CognitiveServices/accounts/read | Odczytuje konta interfejsów API. |
> | Akcja | Microsoft.CognitiveServices/accounts/regenerateKey/action | Wygeneruj ponownie klucz |
> | Akcja | Microsoft.CognitiveServices/accounts/skus/read | Odczytuje jednostki SKU dostępne dla istniejącego zasobu. |
> | Akcja | Microsoft.CognitiveServices/accounts/usages/read | Pobierz użycie przydziału dla istniejącego zasobu. |
> | Akcja | Microsoft.CognitiveServices/accounts/write | Zapisuje konta interfejsów API. |
> | Akcja | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Odczytuje dostępne jednostki SKU na potrzeby subskrypcji. |
> | Akcja | Microsoft.CognitiveServices/Operations/read | Lista wszystkich dostępnych operacji |
> | Akcja | Microsoft.CognitiveServices/register/action | Rejestruje subskrypcję dla usług Cognitive Services |
> | Akcja | Microsoft.CognitiveServices/skus/read | Odczytuje dostępne jednostki SKU dla usług Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Commerce/RateCard/read | Zwraca oferują danych, metadane Miernik/zasobów i szybkości dla danej subskrypcji. |
> | Akcja | Microsoft.Commerce/UsageAggregates/read | Pobiera Microsoft Azure — użytek przez subskrypcję. Wynik zawiera agreguje dane dotyczące użycia, subskrypcji i zasobu powiązane informacje, w określonym czasie. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Compute/availabilitySets/delete | Usuwa zestaw dostępności |
> | Akcja | Microsoft.Compute/availabilitySets/read | Pobierz właściwości zestawu dostępności |
> | Akcja | Microsoft.Compute/availabilitySets/vmSizes/read | Wyświetl dostępne rozmiary na potrzeby tworzenia lub aktualizowania maszyny wirtualnej w zestawie dostępności |
> | Akcja | Microsoft.Compute/availabilitySets/write | Tworzy nowy zestaw dostępności lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Compute/disks/beginGetAccess/action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dysku na potrzeby uzyskiwania dostępu do obiektu blob |
> | Akcja | Microsoft.Compute/disks/delete | Usuwa dysk |
> | Akcja | Microsoft.Compute/disks/endGetAccess/action | Wycofaj identyfikator URI sygnatury dostępu współdzielonego dysku |
> | Akcja | Microsoft.Compute/disks/read | Pobierz właściwości dysku |
> | Akcja | Microsoft.Compute/disks/write | Tworzy nowy dysk lub aktualizuje istniejący |
> | Akcja | Microsoft.Compute/images/delete | Usuwa obraz |
> | Akcja | Microsoft.Compute/images/read | Pobierz właściwości obrazu |
> | Akcja | Microsoft.Compute/images/write | Tworzy nowy obraz lub aktualizuje istniejący obraz |
> | Akcja | Microsoft.Compute/locations/capsOperations/read | Pobiera stan operacji asynchronicznej Caps |
> | Akcja | Microsoft.Compute/locations/diskOperations/read | Pobiera stan operacji asynchronicznej dysku |
> | Akcja | Microsoft.Compute/locations/operations/read | Pobiera stan operacji asynchronicznej |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Pobrać właściwości oferty obrazu platformy |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Pobrać właściwości jednostki SKU obrazu platformy |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Pobrać właściwości wersji obrazu platformy |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Pobierz właściwości typu VMExtension |
> | Akcja | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Pobierz właściwości wersji VMExtension |
> | Akcja | Microsoft.Compute/locations/publishers/read | Pobierz właściwości wydawcy |
> | Akcja | Microsoft.Compute/locations/runCommands/read | Wyświetla listę dostępnych poleceń uruchamiania w lokalizacji |
> | Akcja | Microsoft.Compute/locations/usages/read | Pobiera limity usług i bieżące dane użycia zasobów obliczeniowych subskrypcji w danej lokalizacji |
> | Akcja | Microsoft.Compute/locations/vmSizes/read | Wyświetla listę dostępnych rozmiarów maszyny wirtualnej w danej lokalizacji |
> | Akcja | Microsoft.Compute/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.Compute |
> | Akcja | Microsoft.Compute/register/action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft.Compute |
> | Akcja | Microsoft.Compute/restorePointCollections/delete | Usuwa kolekcję punktów przywracania i należące do niej punkty przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/read | Pobierz właściwości kolekcji punktów przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/restorePoints/delete | Usuwa punkt przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/restorePoints/read | Pobierz właściwości punktu przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Pobierz właściwości punktu przywracania z identyfikatorami URI SAS obiektów blob |
> | Akcja | Microsoft.Compute/restorePointCollections/restorePoints/write | Tworzy nowy punkt przywracania |
> | Akcja | Microsoft.Compute/restorePointCollections/write | Tworzy nową kolekcję punktów przywracania lub aktualizuje istniejącą |
> | Akcja | Microsoft.Compute/sharedVMImages/delete | Usuwa element SharedVMImage |
> | Akcja | Microsoft.Compute/sharedVMImages/read | Pobierz właściwości elementu SharedVMImage |
> | Akcja | Microsoft.Compute/sharedVMImages/versions/delete | Usuń element SharedVMImageVersion |
> | Akcja | Microsoft.Compute/sharedVMImages/versions/read | Pobierz właściwości elementu SharedVMImageVersion |
> | Akcja | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replikuj element SharedVMImageVersion do regionów docelowych |
> | Akcja | Microsoft.Compute/sharedVMImages/versions/write | Utwórz nowy element SharedVMImageVersion lub zaktualizuj istniejący |
> | Akcja | Microsoft.Compute/sharedVMImages/write | Tworzy nowy element SharedVMImage lub aktualizuje istniejący |
> | Akcja | Microsoft.Compute/skus/read | Pobiera listę jednostek SKU Microsoft.Compute dostępnych dla subskrypcji |
> | Akcja | Microsoft.Compute/snapshots/beginGetAccess/action | Pobierz identyfikator URI sygnatury dostępu Współdzielonego migawki dla dostępu do obiektów blob |
> | Akcja | Microsoft.Compute/snapshots/delete | Usuń migawkę |
> | Akcja | Microsoft.Compute/snapshots/endGetAccess/action | Wycofaj identyfikator URI sygnatury dostępu Współdzielonego migawki |
> | Akcja | Microsoft.Compute/snapshots/read | Pobierz właściwości migawki |
> | Akcja | Microsoft.Compute/snapshots/write | Utwórz nową migawkę lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Compute/virtualMachines/capture/action | Przechwytuje maszynę wirtualną przez skopiowanie wirtualnych dysków twardych oraz generuje szablon umożliwiający tworzenie podobnych maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Konwertuje oparte na obiektach blob dyski maszyny wirtualnej na dyski zarządzane |
> | Akcja | Microsoft.Compute/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Akcja | Microsoft.Compute/virtualMachines/delete | Usuwa maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/extensions/delete | Usuwa rozszerzenie maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/extensions/read | Pobierz właściwości rozszerzenia maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/extensions/write | Tworzy nowe rozszerzenie maszyny wirtualnej lub aktualizuje istniejące rozszerzenie |
> | Akcja | Microsoft.Compute/virtualMachines/generalize/action | Ustawia stan maszyny wirtualnej „Uogólniono” i przygotowuje maszynę wirtualną do przechwycenia |
> | Akcja | Microsoft.Compute/virtualMachines/instanceView/read | Pobiera szczegółowy stan środowiska uruchomieniowego dla maszyny wirtualnej i jej zasobów |
> | Akcja | Microsoft.Compute/virtualMachines/performMaintenance/action | Wykonuje operację konserwacji maszyny wirtualnej. |
> | Akcja | Microsoft.Compute/virtualMachines/powerOff/action | Wyłącza maszynę wirtualną. Należy pamiętać, że maszyna wirtualna nadal będą naliczane opłaty. |
> | Akcja | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje definicje metryk maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/redeploy/action | Ponownie wdraża maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/reimage/action | Odtwarza z obrazu maszynę wirtualną używającą dysku różnicowego. |
> | Akcja | Microsoft.Compute/virtualMachines/restart/action | Uruchamia ponownie maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/runCommand/action | Wykonuje wstępnie zdefiniowany skrypt na maszynie wirtualnej |
> | Akcja | Microsoft.Compute/virtualMachines/start/action | Uruchamia maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/vmSizes/read | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachines/write | Tworzy nową maszynę wirtualną lub aktualizuje istniejącą maszynę wirtualną |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Wyłącza i zwalnia zasoby obliczeniowe dla wystąpień zestawu skalowania maszyn wirtualnych  |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/delete | Usuwa zestaw skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/delete/action | Usuwa wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Usuwa rozszerzenie zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Pobiera właściwości rozszerzenia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Tworzy nowe rozszerzenie zestawu skalowania maszyn wirtualnych lub aktualizuje już istniejące |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Ręcznie skontroluj domeny aktualizacji platformy w sieci szkieletowej zestawu skalowania maszyn wirtualnych, aby ukończyć oczekującą aktualizację, która się zablokowała |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Pobiera widok wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Ręcznie aktualizuje wystąpienia do najnowszego modelu zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Pobierz właściwości wszystkich interfejsów sieciowych zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Pobiera historię uaktualniania systemu operacyjnego dla zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Przeprowadza planowaną konserwację na wystąpieniach zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Wyłącza wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje definicje metryk zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Pobierz właściwości wszystkich publicznych adresów IP zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/read | Pobiera właściwości zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Wdróż ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Odtwarza z obrazu wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/restart/action | Uruchamia ponownie wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Anuluje operację stopniowego uaktualniania zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Pobierz najnowszy stan stopniowego uaktualniania dla zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/scale/action | Sprawdź, czy dla istniejącego zestawu skalowania maszyn wirtualnych można przeprowadzić skalowanie do wewnątrz / na zewnątrz do określonej liczby wystąpień |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/skus/read | Wyświetla prawidłowe jednostki SKU dla istniejącego zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/start/action | Uruchamia wystąpienia zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Wyłącza i zwalnia zasoby obliczeniowe dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Usuń konkretną maszynę wirtualną z zestawu skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Pobiera widok wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Pobierz właściwości publicznego adresu IP utworzonych przy użyciu zestawu skalowania maszyn wirtualnych. Zestaw skalowania maszyn wirtualnych można utworzyć co najwyżej jeden publiczny adres IP dla elementu ipconfiguration (prywatnego adresu IP) |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Pobierz właściwości jednego lub wszystkich konfiguracji IP interfejsu sieciowego utworzone za pomocą zestawu skalowania maszyn wirtualnych. Konfiguracje adresów IP reprezentują prywatnych adresów IP |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Pobierz właściwości jednego lub wszystkich interfejsów sieciowych maszyny wirtualnej utworzonej za pomocą zestawu skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Przeprowadza planowaną konserwację na wystąpieniu maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Wyłącza wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje definicje metryk maszyny wirtualnej w zestawie skalowania |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Pobiera właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Wdraża ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Odtwarza z obrazu wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Uruchamia ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Uruchamia wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Aktualizuje właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych |
> | Akcja | Microsoft.Compute/virtualMachineScaleSets/write | Tworzy nowy zestaw skalowania maszyn wirtualnych lub aktualizuje istniejący zestaw |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Consumption/balances/read | Wyświetl użycie podsumowania okresie rozliczeniowym dla grupy zarządzania. |
> | Akcja | Microsoft.Consumption/budgets/read | Wyświetl listę budżetów subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/budgets/write | Tworzy, aktualizować i usuwać budżetów subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/marketplaces/read | Wyświetl szczegóły użycia zasobów marketplace dla zakresu EA i WebDirect subskrypcji. |
> | Akcja | Microsoft.Consumption/operations/read | Wyświetl listę operacji wszystkie obsługiwane przez dostawcę zasobów Microsoft.Consumption. |
> | Akcja | Microsoft.Consumption/pricesheets/read | Wyświetlanie danych Pricesheets dla subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/reservationDetails/read | Wyświetl szczegóły użycia dla wystąpień zastrzeżone przez grupy zarządzania lub kolejności rezerwacji. Szczegóły danych jest dla każdego wystąpienia na poziomie dnia. |
> | Akcja | Microsoft.Consumption/reservationRecommendations/read | Lista jednego lub udostępnionego zalecenia dotyczące wystąpień zarezerwowane dla subskrypcji. |
> | Akcja | Microsoft.Consumption/reservationSummaries/read | Wyświetl podsumowanie zastrzeżone wystąpienia według grup zarządzania lub kolejności rezerwacji wykorzystania. Podsumowanie danych jest na poziomie miesiąc lub codziennie. |
> | Akcja | Microsoft.Consumption/reservationTransactions/read | Listy historii transakcji dla wystąpień zastrzeżone przez grupy zarządzania. |
> | Akcja | Microsoft.Consumption/terms/read | Lista warunków dla subskrypcji lub grupy zarządzania. |
> | Akcja | Microsoft.Consumption/usageDetails/read | Wyświetl szczegóły użycia dla zakresu EA i WebDirect subskrypcji. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Pobierz dzienniki dla określonego kontenera. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/delete | Usuń określoną grupę kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/read | Pobierz wszystkie grupy kontenerów. |
> | Akcja | Microsoft.ContainerInstance/containerGroups/write | Utwórz lub zaktualizuj określoną grupę kontenerów. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContainerRegistry/checkNameAvailability/read | Sprawdza, czy nazwa rejestru kontenera jest dostępne do użycia. |
> | Akcja | Microsoft.ContainerRegistry/locations/operationResults/read | Pobiera wynik operacji asynchronicznej |
> | Akcja | Microsoft.ContainerRegistry/operations/read | Wyświetla listę wszystkich dostępnych operacji interfejsu API REST Azure kontenera rejestru |
> | Akcja | Microsoft.ContainerRegistry/register/action | Rejestruje subskrypcję dostawcy zasobów rejestru kontenera i umożliwia tworzenie kontenera rejestrów. |
> | Akcja | Microsoft.ContainerRegistry/registries/delete | Usuwa rejestru kontenera. |
> | Akcja | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Usuwa filtr siatki zdarzeń z rejestru kontenera. |
> | Akcja | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Pobiera właściwości siatki filtru określonego zdarzenia lub wyświetla listę wszystkich filtrów siatki zdarzeń rejestru określonego kontenera. |
> | Akcja | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Tworzy lub aktualizuje filtr zdarzeń siatki dla kontenera rejestru z określonymi parametrami. |
> | Akcja | Microsoft.ContainerRegistry/registries/importImage/action | Obraz importowania do kontenera rejestru z określonymi parametrami. |
> | Akcja | Microsoft.ContainerRegistry/registries/listCredentials/action | Wyświetla listę poświadczenia logowania dla określonego kontenera rejestru. |
> | Akcja | Microsoft.ContainerRegistry/registries/listUsages/read | Wyświetla listę użycia przydziału dla określonego kontenera rejestru. |
> | Akcja | Microsoft.ContainerRegistry/registries/operationStatuses/read | Pobiera stan operacji asynchronicznej rejestru |
> | Akcja | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla ContainerRegistry firmy Microsoft |
> | Akcja | Microsoft.ContainerRegistry/registries/read | Pobiera właściwości rejestru określonego kontenera lub wyświetla ich listę wszystkich rejestrów kontenera w ramach określonej grupy zasobów lub subskrypcji. |
> | Akcja | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Generuje ponownie jednego poświadczenia logowania dla określonego kontenera rejestru. |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/delete | Usuwa replikacji z rejestru kontenera. |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Pobiera stan operacji asynchronicznej dla replikacji |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/read | Pobiera właściwości określonej replikacji lub wyświetla ich listę wszystkich replikacje rejestru określonego kontenera. |
> | Akcja | Microsoft.ContainerRegistry/registries/replications/write | Tworzy lub aktualizuje Replikacja rejestru kontenera z określonymi parametrami. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/delete | Usuwa elementu webhook z rejestru kontenera. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Pobiera konfiguracji identyfikator URI usługi i niestandardowe nagłówki dla elementu webhook. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Wyświetla listę ostatnio zdarzeń dla określonego elementu webhook. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Pobiera stan operacji asynchronicznej dla elementu webhook |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Wyzwala zdarzenie typu ping do wysłania do elementu webhook. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/read | Pobiera właściwości określonego elementu webhook lub wyświetla ich listę wszystkich elementów webhook rejestru określonego kontenera. |
> | Akcja | Microsoft.ContainerRegistry/registries/webhooks/write | Tworzy lub aktualizuje elementu webhook dla rejestru kontenera z określonymi parametrami. |
> | Akcja | Microsoft.ContainerRegistry/registries/write | Tworzy lub aktualizuje rejestr kontenera z określonymi parametrami. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContainerService/containerServices/delete | Usuwa usługę kontenera |
> | Akcja | Microsoft.ContainerService/containerServices/read | Pobierz usługę kontenera |
> | Akcja | Microsoft.ContainerService/containerServices/write | Tworzy nową usługę kontenera lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Uzyskać profilu dostępu do zarządzanego klastra za pomocą nazwy roli przy użyciu poświadczeń listy |
> | Akcja | Microsoft.ContainerService/managedClusters/accessProfiles/read | Uzyskać profilu dostępu do zarządzanego klastra za pomocą nazwy roli |
> | Akcja | Microsoft.ContainerService/managedClusters/delete | Usuwa zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienie diagnostyczne dla zasobu zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla klastra zarządzania |
> | Akcja | Microsoft.ContainerService/managedClusters/read | Pobierz zarządzanego klastra |
> | Akcja | Microsoft.ContainerService/managedClusters/write | Tworzy nowy klaster zarządzanego lub aktualizuje istniejący zestaw |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ContentModerator/applications/delete | Operacja usuwania |
> | Akcja | Microsoft.ContentModerator/applications/listSecrets/action | Wyświetl klucze tajne |
> | Akcja | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Odczyt jednokrotnego tokenów |
> | Akcja | Microsoft.ContentModerator/applications/read | Operacja odczytu |
> | Akcja | Microsoft.ContentModerator/applications/write | Operacja zapisu |
> | Akcja | Microsoft.ContentModerator/applications/write | Operacja zapisu |
> | Akcja | Microsoft.ContentModerator/listCommunicationPreference/action | Lista komunikacji preferencji |
> | Akcja | Microsoft.ContentModerator/operations/read | operacje odczytu |
> | Akcja | Microsoft.ContentModerator/updateCommunicationPreference/action | Preferencji komunikacji aktualizacji |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.CustomerInsights/hubs/adobemetadata/action | Utwórz lub zaktualizuj wszystkie metadane Adobe Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/adobemetadata/read | Odczytać metadanych Adobe Insights dowolnego klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Usuń dowolne zasady sygnatury dostępu współdzielonego Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Przeczytaj zasady podpisu dostępu udostępniony żadnych informacji szczegółowych Azure klienta |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Ponowne wygenerowanie klucza podstawowego Azure klienta Insights zasad dostępu współużytkowanego podpisu |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Ponowne wygenerowanie klucza pomocniczego Azure klienta Insights zasad dostępu współużytkowanego podpisu |
> | Akcja | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Utwórz lub zaktualizuj dowolne zasady sygnatury dostępu współdzielonego Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktywowanie wszystkich łączników Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktywowanie wszystkich łączników Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/delete | Usunięcie łącznika Insights dowolnego klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Pobierz stan czasu wykonywania żadnych łącznika Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Aktywuj żadnego mapowania łącznika Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Usunąć wszystkie mapowania łącznika Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Odczytaj dowolny wynik operacji mapowania łącznika Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Przeczytaj żadnego mapowania łącznika Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Utwórz lub zaktualizuj żadnego mapowania łącznika Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/operations/read | Odczytaj dowolny wynik operacji łącznika Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/read | Każdy łącznik Insights Azure klienta do odczytu |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Utwórz lub zaktualizuj bramą uwierzytelniania dowolnego klienta Azure Insights łącznika |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/update/action | Aktualizacja wszystkich łączników Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/connectors/write | Utwórz lub zaktualizuj każdy łącznik Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/crmmetadata/action | Utwórz lub zaktualizuj wszystkie metadane Crm Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/crmmetadata/read | Odczytać metadanych Crm Insights dowolnego klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/delete | Usunąć wszelkie Centrum Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/gdpr/delete | Usuń wszelkie Gdpr Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/gdpr/read | Przeczytaj Gdpr Insights dowolnego klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/gdpr/write | Utwórz lub zaktualizuj Gdpr Insights dowolnego klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Pobierz klienta platformy Azure Insights Centrum rozliczeń środków |
> | Akcja | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Pobierz historię rozliczeń Centrum Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/images/delete | Usuń obraz Insights dowolnego klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/images/read | Przeczytaj żadnego obrazu Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/images/write | Utwórz lub zaktualizuj żadnego obrazu Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/delete | Usuń wszelkie Insights klientami azure |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/operations/read | Odczytaj dowolny wynik operacji interakcji Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/read | Przeczytaj interakcji Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Sugeruj łącza relacji dla dowolnego Insights klientami Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/interactions/write | Utwórz lub zaktualizuj interakcji Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/delete | Usuń wszelkie klienta Azure Insights kluczowy wskaźnik wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/operations/read | Odczytaj dowolny wynik operacji Azure klienta Insights kluczowych wskaźników wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/read | Przeczytaj dowolnego klienta Azure Insights kluczowy wskaźnik wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Ponownie przetworzyć dowolnego klienta Azure Insights kluczowe wskaźniki wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/kpi/write | Utwórz lub zaktualizuj dowolnego klienta Azure Insights kluczowy wskaźnik wydajności |
> | Akcja | Microsoft.CustomerInsights/hubs/links/delete | Usuń wszystkie linki Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/links/operations/read | Odczytaj dowolny wynik operacji Azure klienta Insights łączy |
> | Akcja | Microsoft.CustomerInsights/hubs/links/read | Przeczytaj łącza Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/links/write | Utwórz lub zaktualizuj łącza klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/msemetadata/action | Utwórz lub zaktualizuj wszystkie metadane Mse Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/msemetadata/read | Odczytać metadanych Mse Insights dowolnego klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/operationresults/read | Wyniki operacji centrum Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictions/delete | Usuń wszelkie prognoz Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictions/operations/read | Odczytaj dowolny wynik operacji prognoz Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictions/read | Przeczytaj prognoz Insights dowolnego klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictions/write | Utwórz lub zaktualizuj wszystkie operacje przewidywania dla klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Usuń wszystkie zasady predykcyjnej dopasowania Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Odczytaj dowolny wynik operacji Azure klienta Insights predykcyjnej dopasowanie zasad |
> | Akcja | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Odczytu żadnych zasad predykcyjnej dopasowania Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Utwórz lub zaktualizuj wszystkie zasady predykcyjnej dopasowania Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/profiles/delete | Usuń profil Insights dowolnego klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/profiles/operations/read | Odczytaj dowolny wynik operacji profil Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/profiles/read | Przeczytaj dowolnego profilu Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/profiles/write | Zapis dowolnego profilu Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla zasobu |
> | Akcja | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla zasobu |
> | Akcja | Microsoft.CustomerInsights/hubs/read | Przeczytaj dowolnego Centrum Insights Azure klienta |
> | Akcja | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Usuń wszystkie linki relacji Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Odczytaj dowolny wynik operacji łączy relacja Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Odczytywanie dowolnego klienta Azure Insights relacji |
> | Akcja | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Utwórz lub zaktualizuj łącza relacji Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationships/delete | Usuń wszystkie relacje Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationships/operations/read | Odczytaj dowolny wynik operacji Insights z klientami Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/relationships/read | Wszystkie relacje Insights Azure klienta do odczytu |
> | Akcja | Microsoft.CustomerInsights/hubs/relationships/write | Utwórz lub zaktualizuj wszystkie relacje Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Usuń wszelkie przydział Rbac Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Odczytaj dowolny wynik operacji Azure klienta Insights Rbac przypisania |
> | Akcja | Microsoft.CustomerInsights/hubs/roleAssignments/read | Przeczytaj przydziału Rbac Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/roleAssignments/write | Utwórz lub zaktualizuj wszystkie przypisania Rbac Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/roles/read | Wszystkie role Rbac Insights Azure klienta do odczytu |
> | Akcja | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Utwórz lub zaktualizuj wszystkie metadane SalesForce Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Odczytać metadanych SalesForce Insights dowolnego klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/delete | Usuń segmentów Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Segmenty dynamicznie szczegółowe informacje o wszelkich klienta usługi Azure Management |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/read | Przeczytaj segmentów Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/static/action | Segmenty wszystkie statyczne wglądu Azure klienta zarządzania |
> | Akcja | Microsoft.CustomerInsights/hubs/segments/write | Utwórz lub zaktualizuj segmentów Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Usuń wszelkie SqlConnectionStrings Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Przeczytaj SqlConnectionStrings Insights dowolnego klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Utwórz lub zaktualizuj SqlConnectionStrings Insights dowolnego klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Generowanie wskazujące typ schematu z przykładowych danych |
> | Akcja | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Zarządzanie ustawieniami Centrum żadnych Insights klienta Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/views/delete | Usuń wszelkie widok aplikacji Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/views/read | Przeczytaj dowolnym widoku App Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/views/write | Utwórz lub zaktualizuj dowolnym widoku App Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/widgettypes/read | Przeczytaj żadnych typów App Widget Insights klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/hubs/write | Utwórz lub zaktualizuj Centrum Insights dowolnego klienta usługi Azure |
> | Akcja | Microsoft.CustomerInsights/operations/read | Przeczytaj Metadatas interfejsu Api Insights klienta platformy Azure |
> | Akcja | Microsoft.CustomerInsights/register/action | Rejestruje subskrypcję dostawcy zasobów Insights klientów i umożliwia tworzenie zasobów Insights klienta |
> | Akcja | Microsoft.CustomerInsights/unregister/action | Wyrejestrowuje subskrypcji dla dostawcy zasobów Insights klienta |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Databricks/workspaces/delete | Usuwa obszarem roboczym. |
> | Akcja | Microsoft.Databricks/workspaces/read | Pobiera listę obszarów roboczych. |
> | Akcja | Microsoft.Databricks/workspaces/write | Tworzy obszarem roboczym. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataCatalog/catalogs/delete | Usuwa katalogu. |
> | Akcja | Microsoft.DataCatalog/catalogs/read | Pobierz właściwości dla katalogu lub katalogów w ramach subskrypcji lub grupy zasobów. |
> | Akcja | Microsoft.DataCatalog/catalogs/write | Tworzy katalog lub aktualizuje znaczniki i właściwości dla katalogu. |
> | Akcja | Microsoft.DataCatalog/checkNameAvailability/action | Służy do sprawdzania dostępności nazwy katalogu dla dzierżawcy. |
> | Akcja | Microsoft.DataCatalog/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.DataCatalog. |
> | Akcja | Microsoft.DataCatalog/register/action | Rejestruje subskrypcję dostawcy zasobów Microsoft.DataCatalog. |
> | Akcja | Microsoft.DataCatalog/unregister/action | Wyrejestrowuje subskrypcji z Microsoft.DataCatalog dostawcy zasobów. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataFactory/datafactories/activitywindows/read | Odczytuje okien działania w fabryce danych z określonymi parametrami. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Odczytuje okien działania dla działania potoku z określonymi parametrami. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Odczytuje okien działania dla potoku z określonymi parametrami. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/delete | Usuwa wszystkie potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Wstrzymuje żadnych potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/read | Odczytuje wszystkie potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Wznawia żadnych potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/update/action | Aktualizuje wszystkie potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datapipelines/write | Tworzy lub aktualizuje wszystkie potoku. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Odczytuje okien działania dla zestawu danych z określonymi parametrami. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/delete | Usuwa wszystkie zestawu danych. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/read | Odczytuje wszystkie zestawu danych. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Odczytuje uruchomić wycinka danych do danego zestawu danych o czasie rozpoczęcia danego. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/slices/read | Pobiera wycinków danych w danym okresie. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/slices/write | Aktualizowanie stanu wycinka danych. |
> | Akcja | Microsoft.DataFactory/datafactories/datasets/write | Tworzy lub aktualizuje wszystkie zestawu danych. |
> | Akcja | Microsoft.DataFactory/datafactories/delete | Usuwa fabryki danych. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Odczytuje informacje o połączeniu dla dowolnego bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/delete | Usuwa wszystkie bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Wyświetla listę kluczy uwierzytelniania dla dowolnego bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/read | Odczytuje wszystkie bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Generuje ponownie klucze uwierzytelniania, wszystkie bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/gateways/write | Tworzy lub aktualizuje wszystkie bramy. |
> | Akcja | Microsoft.DataFactory/datafactories/linkedServices/delete | Usuwa wszystkie połączonej usługi. |
> | Akcja | Microsoft.DataFactory/datafactories/linkedServices/read | Odczytuje wszystkie połączonej usługi. |
> | Akcja | Microsoft.DataFactory/datafactories/linkedServices/write | Tworzy lub aktualizuje połączonej usługi. |
> | Akcja | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla datafactories |
> | Akcja | Microsoft.DataFactory/datafactories/read | Odczytuje fabryki danych. |
> | Akcja | Microsoft.DataFactory/datafactories/runs/loginfo/read | Odczytuje identyfikator URI sygnatury dostępu Współdzielonego do kontenera obiektów blob zawierający dzienniki. |
> | Akcja | Microsoft.DataFactory/datafactories/tables/delete | Usuwa wszystkie zestawu danych. |
> | Akcja | Microsoft.DataFactory/datafactories/tables/read | Odczytuje wszystkie zestawu danych. |
> | Akcja | Microsoft.DataFactory/datafactories/tables/write | Tworzy lub aktualizuje wszystkie zestawu danych. |
> | Akcja | Microsoft.DataFactory/datafactories/write | Tworzy lub aktualizuje fabryki danych. |
> | Akcja | Microsoft.DataFactory/factories/cancelpipelinerun/action | Anuluje potoku Uruchom określony przez identyfikator działania. |
> | Akcja | Microsoft.DataFactory/factories/datasets/delete | Usuwa wszystkie zestawu danych. |
> | Akcja | Microsoft.DataFactory/factories/datasets/read | Odczytuje wszystkie zestawu danych. |
> | Akcja | Microsoft.DataFactory/factories/datasets/write | Tworzy lub aktualizuje wszystkie zestawu danych. |
> | Akcja | Microsoft.DataFactory/factories/delete | Usuwa fabryki danych. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/delete | Usuwa wszystkie środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Odczytuje informacje o połączeniu środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Odczytuje stan środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Wyświetla listę kluczy uwierzytelniania dla dowolnego środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Pobiera dane monitorowania dla dowolnego środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Usuwa węzła dla określonego środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Zwraca adres IP dla określonego węzła środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Aktualizuje siebie węzła środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/read | Odczytuje wszystkie środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Generuje ponownie klucze uwierzytelniania dla określonego środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/start/action | Uruchamia wszystkie środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Zatrzymuje wszystkie środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synchronizuje poświadczenia dla określonego środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Uaktualnia wykonawcze integracji. |
> | Akcja | Microsoft.DataFactory/factories/integrationruntimes/write | Tworzy lub aktualizuje wszystkie środowiska uruchomieniowego integracji. |
> | Akcja | Microsoft.DataFactory/factories/linkedServices/delete | Usuwa połączonej usługi. |
> | Akcja | Microsoft.DataFactory/factories/linkedServices/read | Odczyty połączonej usługi. |
> | Akcja | Microsoft.DataFactory/factories/linkedServices/write | Utwórz lub zaktualizuj połączone usługi |
> | Akcja | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Odczytuje działanie zostanie uruchomione dla określonego potoku Uruchom identyfikatora. |
> | Akcja | Microsoft.DataFactory/factories/pipelineruns/read | Odczytuje uruchamia potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/createrun/action | Tworzy Uruchom dla potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/delete | Usuwa potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/read | Odczytuje potoku. |
> | Akcja | Microsoft.DataFactory/factories/pipelines/write | Utwórz lub zaktualizuj potoku |
> | Akcja | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla fabryki |
> | Akcja | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla fabryki |
> | Akcja | Microsoft.DataFactory/factories/read | Odczytuje fabryki danych. |
> | Akcja | Microsoft.DataFactory/factories/triggers/delete | Usuwa wszystkie wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/read | Odczytuje wszystkie wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/start/action | Uruchamia wszystkie wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/stop/action | Zatrzymuje wszystkie wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/triggers/triggerruns/read | Odczytuje uruchamia wyzwalacz. |
> | Akcja | Microsoft.DataFactory/factories/triggers/write | Tworzy lub aktualizuje wszystkie wyzwalacza. |
> | Akcja | Microsoft.DataFactory/factories/write | Utwórz lub zaktualizuj fabryki danych |
> | Akcja | Microsoft.DataFactory/register/action | Rejestruje subskrypcję dostawcy zasobów fabryki danych. |
> | Akcja | Microsoft.DataFactory/unregister/action | Wyrejestrowuje subskrypcji dla dostawcy zasobów fabryki danych. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Usuń zasady obliczeń. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Pobierz informacje o zasadach obliczeń. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Utwórz lub zaktualizuj zasady obliczeń. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Odłącz od konta DataLakeAnalytics przy użyciu konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Pobiera informacje o połączonego konta DataLakeStore DataLakeAnalytics konta. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Utwórz lub zaktualizuj połączonego konta DataLakeStore DataLakeAnalytics konta. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/delete | Usuwanie konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Usuwanie reguły zapory. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Pobierz informacje dotyczące reguły zapory. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Utwórz lub zaktualizuj regułę zapory. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Pobierz wynik operacji DataLakeAnalytics konta. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienia diagnostyki dla konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Utwórz lub zaktualizuj ustawienia diagnostyki dla konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | Pobierz dostępne dzienniki dla konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | Pobierz dostępne metryki dla konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/read | Pobiera informacje o istniejącym koncie DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Wyświetl listę tokeny sygnatury dostępu Współdzielonego dla kontenerów magazynów konta DataLakeAnalytics połączonego konta magazynu. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Pobierz kontenery konta DataLakeAnalytics połączonego konta magazynu. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Odłączanie konta magazynu z konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Pobiera informacje o połączonego konta magazynu DataLakeAnalytics konta. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Utwórz lub zaktualizuj konto magazynu połączonego konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Przyznaj uprawnienia, aby anulować zadania przesłane przez innych użytkowników. |
> | Akcja | Microsoft.DataLakeAnalytics/accounts/write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/capability/read | Uzyskiwanie informacji o subskrypcji dotyczące przy użyciu DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/locations/operationResults/read | Pobierz wynik operacji DataLakeAnalytics konta. |
> | Akcja | Microsoft.DataLakeAnalytics/operations/read | Pobiera dostępne operacje z DataLakeAnalytics. |
> | Akcja | Microsoft.DataLakeAnalytics/register/action | Zarejestruj subskrypcję DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DataLakeStore/accounts/delete | Usuwanie konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Włącz konto DataLakeStore KeyVault. |
> | Akcja | Microsoft.DataLakeStore/accounts/firewallRules/delete | Usuwanie reguły zapory. |
> | Akcja | Microsoft.DataLakeStore/accounts/firewallRules/read | Pobierz informacje dotyczące reguły zapory. |
> | Akcja | Microsoft.DataLakeStore/accounts/firewallRules/write | Utwórz lub zaktualizuj regułę zapory. |
> | Akcja | Microsoft.DataLakeStore/accounts/operationResults/read | Pobierz wynik operacji DataLakeStore konta. |
> | Akcja | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienia diagnostyki dla konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Utwórz lub zaktualizuj ustawienia diagnostyki dla konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | Pobierz dostępne dzienniki dla konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | Pobierz dostępne metryki dla konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/accounts/read | Pobiera informacje o istniejącym koncie DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/accounts/Superuser/action | Przyznaj administratora w usłudze Data Lake Store po udzieleniu z Microsoft.Authorization/roleAssignments/write. |
> | Akcja | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Usuń zaufanego dostawcę tożsamości. |
> | Akcja | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Pobiera informacje o zaufanego dostawcę tożsamości. |
> | Akcja | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Utwórz lub zaktualizuj zaufanego dostawcę tożsamości. |
> | Akcja | Microsoft.DataLakeStore/accounts/write | Utwórz lub zaktualizuj konto DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/locations/capability/read | Uzyskiwanie informacji o subskrypcji dotyczące przy użyciu DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Sprawdź dostępność nazwy konta DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/locations/operationResults/read | Pobierz wynik operacji DataLakeStore konta. |
> | Akcja | Microsoft.DataLakeStore/operations/read | Pobiera dostępne operacje z DataLakeStore. |
> | Akcja | Microsoft.DataLakeStore/register/action | Zarejestruj subskrypcję DataLakeStore. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DBforMySQL/locations/performanceTiers/read | Zwraca listę dostępnych poziomów wydajności. |
> | Akcja | Microsoft.DBforMySQL/performanceTiers/read | Zwraca listę dostępnych poziomów wydajności. |
> | Akcja | Microsoft.DBforMySQL/servers/delete | Usuwa istniejącego serwera. |
> | Akcja | Microsoft.DBforMySQL/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft.DBforMySQL/servers/firewallRules/read | Zwraca listę zapory zasady serwera lub pobiera właściwości dla reguły zapory określony. |
> | Akcja | Microsoft.DBforMySQL/servers/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.DBforMySQL/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforMySQL/servers/recoverableServers/read | Powrót do odzyskania informacji o serwerze MySQL |
> | Akcja | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | Akcja | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguły lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub zaktualizować właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforMySQL/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Zwraca listę dostępnych poziomów wydajności. |
> | Akcja | Microsoft.DBforPostgreSQL/performanceTiers/read | Zwraca listę dostępnych poziomów wydajności. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/delete | Usuwa istniejącego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Usuwa istniejącą regułę zapory. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Zwraca listę zapory zasady serwera lub pobiera właściwości dla reguły zapory określony. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera disagnostic ustawienie dla zasobu |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla serwerów Postgres |
> | Akcja | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.DBforPostgreSQL/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Powrót do odzyskania informacji o serwerze PostgreSQL |
> | Akcja | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Pobieranie szczegółów serwera zasad wykrywania zagrożeń skonfigurowane na danym serwerze |
> | Akcja | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | Akcja | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguły lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub zaktualizować właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.DBforPostgreSQL/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Devices/checkNameAvailability/Action | Sprawdź, czy jest dostępna nazwa Jeśli Centrum IotHub |
> | Akcja | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Sprawdź, czy jest dostępna nazwa Jeśli Centrum IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Usuń zasób dzierżawy Centrum IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Usuwanie grupy konsumentów EventHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Tworzenie grupy konsumentów EventHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eksportuj urządzeń |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Pobiera zasób Statystyka dzierżawy Centrum IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importuj urządzeń |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Pobiera klucz dzierżawy Centrum IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Pobierz zadania szczegóły przesyłane na podany Centrum IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Pobiera klucze dzierżawy Centrum IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Pobiera definicje dzienników dostępnych dla usługi Centrum IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Pobiera dostępne metryki dla usługi Centrum IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Pobieraj metryki przydziału |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/Read | Pobiera zasób dzierżawy Centrum IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testowa wiadomość przed wszystkie istniejące trasy |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testowa wiadomość przed podany test trasy |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Pobiera stan wszystkich punktów końcowych routingu dla Centrum IotHub |
> | Akcja | Microsoft.Devices/elasticPools/iotHubTenants/Write | Utwórz lub zaktualizuj zasób dzierżawy Centrum IotHub |
> | Akcja | Microsoft.Devices/ElasticPools/metricDefinitions/read | Pobiera dostępne metryki dla usługi Centrum IotHub |
> | Akcja | Microsoft.Devices/iotHubs/certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft.Devices/iotHubs/certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft.Devices/iotHubs/certificates/verify/Action | Sprawdź zasobów certyfikatu |
> | Akcja | Microsoft.Devices/iotHubs/certificates/Write | Tworzenie lub aktualizowanie certyfikatów |
> | Akcja | Microsoft.Devices/iotHubs/Delete | Usuń zasób z Centrum IotHub |
> | Akcja | Microsoft.Devices/IotHubs/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/IotHubs/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Usuwa filtr zdarzeń siatki |
> | Akcja | Microsoft.Devices/iotHubs/eventGridFilters/Read | Pobiera filtr zdarzeń siatki |
> | Akcja | Microsoft.Devices/iotHubs/eventGridFilters/Write | Utworzyć lub zaktualizować istniejący filtr zdarzeń siatki |
> | Akcja | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Usuwanie grupy konsumentów EventHub |
> | Akcja | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Pobierz grupy konsumentów EventHub |
> | Akcja | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Tworzenie grupy konsumentów EventHub |
> | Akcja | Microsoft.Devices/iotHubs/exportDevices/Action | Eksportuj urządzeń |
> | Akcja | Microsoft.Devices/iotHubs/importDevices/Action | Importuj urządzeń |
> | Akcja | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Pobierz klucz Centrum IotHub dla podanej nazwie |
> | Akcja | Microsoft.Devices/iotHubs/iotHubStats/Read | Uzyskać statystyki Centrum IotHub |
> | Akcja | Microsoft.Devices/iotHubs/jobs/Read | Pobierz zadania szczegóły przesyłane na podany Centrum IotHub |
> | Akcja | Microsoft.Devices/iotHubs/listkeys/Action | Pobierz wszystkie klucze Centrum IotHub |
> | Akcja | Microsoft.Devices/IotHubs/logDefinitions/read | Pobiera definicje dzienników dostępnych dla usługi Centrum IotHub |
> | Akcja | Microsoft.Devices/IotHubs/metricDefinitions/read | Pobiera dostępne metryki dla usługi Centrum IotHub |
> | Akcja | Microsoft.Devices/iotHubs/quotaMetrics/Read | Pobieraj metryki przydziału |
> | Akcja | Microsoft.Devices/iotHubs/Read | Pobiera zasoby Centrum IotHub |
> | Akcja | Microsoft.Devices/iotHubs/routing/$testall/Action | Testowa wiadomość przed wszystkie istniejące trasy |
> | Akcja | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testowa wiadomość przed podany test trasy |
> | Akcja | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Pobiera stan wszystkich punktów końcowych routingu dla Centrum IotHub |
> | Akcja | Microsoft.Devices/iotHubs/skus/Read | Pobierz prawidłowe jednostki SKU z Centrum IotHub |
> | Akcja | Microsoft.Devices/iotHubs/Write | Utwórz lub zaktualizuj zasób z Centrum IotHub |
> | Akcja | Microsoft.Devices/operations/Read | Pobierz wszystkie operacje ResourceProvider |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/Delete | Usuwa certyfikat |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generuj kod weryfikacyjny |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/Read | Pobiera certyfikat |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/verify/Action | Sprawdź zasobów certyfikatu |
> | Akcja | Microsoft.Devices/provisioningServices/certificates/Write | Tworzenie lub aktualizowanie certyfikatów |
> | Akcja | Microsoft.Devices/provisioningServices/Delete | Usuń zasób IotDps |
> | Akcja | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Devices/provisioningServices/listkeys/Action | Pobierz wszystkie klucze IotDps |
> | Akcja | Microsoft.Devices/provisioningServices/logDefinitions/read | Pobiera definicje dzienników dostępnych dla inicjowania obsługi usługi |
> | Akcja | Microsoft.Devices/provisioningServices/metricDefinitions/read | Pobiera dostępne metryki dla usługi udostępniania |
> | Akcja | Microsoft.Devices/provisioningServices/ProvisioningServiceKeys/listkeys/Action | Pobieranie kluczy IotDps dla nazwy klucza |
> | Akcja | Microsoft.Devices/provisioningServices/Read | Uzyskanie IotDps zasobu |
> | Akcja | Microsoft.Devices/provisioningServices/skus/Read | Pobierz prawidłowe IotDps jednostki SKU |
> | Akcja | Microsoft.Devices/provisioningServices/Write | Utwórz zasób IotDps |
> | Akcja | Microsoft.Devices/register/action | Zarejestruj subskrypcję dostawcy zasobów Centrum IotHub i umożliwia tworzenie zasobów Centrum IotHub |
> | Akcja | Microsoft.Devices/register/action | Zarejestruj subskrypcję dostawcy zasobów Centrum IotHub i umożliwia tworzenie zasobów Centrum IotHub |
> | Akcja | Microsoft.Devices/usages/Read | Uzyskanie subskrypcji Szczegóły obciążenia dla tego dostawcy. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DevTestLab/labCenters/delete | Usuń centrów laboratorium. |
> | Akcja | Microsoft.DevTestLab/labCenters/read | Przeczytaj centrów laboratorium. |
> | Akcja | Microsoft.DevTestLab/labCenters/write | Dodaje lub modyfikuje centrów laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Przeczytaj szablony Menedżera zasobów platformy azure. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generuje szablonu usługi ARM dla danego artefaktu, przekazuje wymaganych plików do konta magazynu i weryfikuje wygenerowanego artefaktu. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Przeczytaj artefaktów. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/delete | Usuń źródła artefaktu. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/read | Przeczytaj źródeł artefaktu. |
> | Akcja | Microsoft.DevTestLab/labs/artifactSources/write | Dodaje lub modyfikuje źródeł artefaktu. |
> | Akcja | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Oświadczenia losowe claimable maszynę wirtualną w laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/costs/read | Przeczytaj kosztów. |
> | Akcja | Microsoft.DevTestLab/labs/costs/write | Dodaje lub modyfikuje kosztów. |
> | Akcja | Microsoft.DevTestLab/labs/CreateEnvironment/action | Tworzenie maszyn wirtualnych w laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/customImages/delete | Usuwanie obrazów niestandardowych. |
> | Akcja | Microsoft.DevTestLab/labs/customImages/read | Niestandardowe obrazy. |
> | Akcja | Microsoft.DevTestLab/labs/customImages/write | Dodaje lub modyfikuje niestandardowych obrazów. |
> | Akcja | Microsoft.DevTestLab/labs/delete | Usuń labs. |
> | Akcja | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Eksportuje użycia zasobów laboratorium do konta magazynu |
> | Akcja | Microsoft.DevTestLab/labs/formulas/delete | Usuń formuły. |
> | Akcja | Microsoft.DevTestLab/labs/formulas/read | Przeczytaj formuły. |
> | Akcja | Microsoft.DevTestLab/labs/formulas/write | Dodaje lub modyfikuje formuły. |
> | Akcja | Microsoft.DevTestLab/labs/galleryImages/read | Przeczytaj obrazów w galerii. |
> | Akcja | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generuj identyfikator URI dla przesyłania obrazów dysku niestandardowych do laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Zaimportuj maszynę wirtualną do innego laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/ListVhds/action | Listy obrazów dysku dostępne na potrzeby tworzenia niestandardowego obrazu. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/delete | Usuń notificationchannels. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Wyślij powiadomienia do podanego kanału. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/read | Przeczytaj notificationchannels. |
> | Akcja | Microsoft.DevTestLab/labs/notificationChannels/write | Dodaje lub modyfikuje notificationchannels. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Ocenia zasady laboratorium. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/policies/delete | Usuwanie zasad. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/policies/read | Przeczytaj zasady. |
> | Akcja | Microsoft.DevTestLab/labs/policySets/policies/write | Dodaje lub modyfikuje zasady. |
> | Akcja | Microsoft.DevTestLab/labs/read | Laboratoria odczytu. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/delete | Usuń harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/Execute/action | Wykonanie zgodnie z harmonogramem. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Wyświetla listę wszystkich odpowiednich harmonogramów |
> | Akcja | Microsoft.DevTestLab/labs/schedules/read | Przeczytaj harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/schedules/write | Dodaje lub modyfikuje harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/serviceRunners/delete | Usuń używanych modułów uruchamiających usługi. |
> | Akcja | Microsoft.DevTestLab/labs/serviceRunners/read | Przeczytaj używanych modułów uruchamiających usługi. |
> | Akcja | Microsoft.DevTestLab/labs/serviceRunners/write | Dodaje lub modyfikuje używanych modułów uruchamiających usługi. |
> | Akcja | Microsoft.DevTestLab/labs/users/delete | Usuń profile użytkowników. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/Attach/action | Dołącz i tworzenia dzierżawy dysku do maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/delete | Usuń dyski. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/Detach/action | Odłącz i przerwie dzierżawy dysków dołączonych do maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/read | Przeczytaj dysków. |
> | Akcja | Microsoft.DevTestLab/labs/users/disks/write | Dodaje lub modyfikuje dysków. |
> | Akcja | Microsoft.DevTestLab/labs/users/environments/delete | Usuwanie środowiska. |
> | Akcja | Microsoft.DevTestLab/labs/users/environments/read | Przeczytaj środowisk. |
> | Akcja | Microsoft.DevTestLab/labs/users/environments/write | Dodaje lub modyfikuje środowisk. |
> | Akcja | Microsoft.DevTestLab/labs/users/read | Przeczytaj profile użytkowników. |
> | Akcja | Microsoft.DevTestLab/labs/users/secrets/delete | Usuwanie kluczy tajnych. |
> | Akcja | Microsoft.DevTestLab/labs/users/secrets/read | Odczyt kluczy tajnych. |
> | Akcja | Microsoft.DevTestLab/labs/users/secrets/write | Dodaje lub modyfikuje kluczy tajnych. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Usunięcie sieci szkieletowej usług. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Wyświetla listę wszystkich odpowiednich harmonogramów |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Sieci szkieletowe odczytu usługi. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Usuń harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Wykonanie zgodnie z harmonogramem. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Przeczytaj harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Dodaje lub modyfikuje harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Uruchom usługi sieć szkieletowa. |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zatrzymaj usługi sieć szkieletowa |
> | Akcja | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Dodaje lub modyfikuje sieci szkieletowej usług. |
> | Akcja | Microsoft.DevTestLab/labs/users/write | Dodaje lub modyfikuje profile użytkowników. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Dołączenie dysku danych nowej lub istniejącej maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Zastosuj artefakty do maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Przejmowanie na własność istniejącej maszyny wirtualnej |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/delete | Usuń maszyny wirtualne. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Odłączyć określonego dysku od maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Wyświetla listę wszystkich odpowiednich harmonogramów |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/read | Maszyny wirtualne do odczytu. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Wdróż ponownie maszynę wirtualną |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Zmień rozmiar maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Uruchom ponownie maszynę wirtualną. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Usuń harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Wykonanie zgodnie z harmonogramem. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Przeczytaj harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Dodaje lub modyfikuje harmonogramów. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Uruchom maszynę wirtualną. |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Zatrzymaj maszynę wirtualną |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Przenieś własność dysków danych maszyny wirtualnej do siebie |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Własność wersji istniejącej maszyny wirtualnej |
> | Akcja | Microsoft.DevTestLab/labs/virtualMachines/write | Dodaje lub modyfikuje maszyny wirtualne. |
> | Akcja | Microsoft.DevTestLab/labs/virtualNetworks/delete | Usuwanie sieci wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/virtualNetworks/read | Przeczytaj sieci wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/virtualNetworks/write | Dodaje lub modyfikuje sieci wirtualnych. |
> | Akcja | Microsoft.DevTestLab/labs/vmPools/delete | Usunąć pul maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/vmPools/read | Przeczytaj pule maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/vmPools/write | Dodaje lub modyfikuje pule maszyny wirtualnej. |
> | Akcja | Microsoft.DevTestLab/labs/write | Dodaje lub modyfikuje labs. |
> | Akcja | Microsoft.DevTestLab/locations/operations/read | Operacje odczytu. |
> | Akcja | Microsoft.DevTestLab/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.DevTestLab/schedules/delete | Usuń harmonogramów. |
> | Akcja | Microsoft.DevTestLab/schedules/Execute/action | Wykonanie zgodnie z harmonogramem. |
> | Akcja | Microsoft.DevTestLab/schedules/read | Przeczytaj harmonogramów. |
> | Akcja | Microsoft.DevTestLab/schedules/Retarget/action | Aktualizuje zasób docelowy zgodnie z harmonogramem identyfikatora. |
> | Akcja | Microsoft.DevTestLab/schedules/write | Dodaje lub modyfikuje harmonogramów. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DocumentDB/databaseAccountNames/read | Sprawdza dostępność nazwy. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Zmiana grupy zasobów dla konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Odczytuje kolekcję definicji metryk. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Odczytuje metryki kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Przeczytaj kluczowych metryk poziomu bazy danych konta partycji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Przeczytaj metryki poziomu partycji konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Przeczytaj partycjach konta bazy danych w kolekcji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Poziom użycia partycji do odczytu konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Odczytuje użycia kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Odczytuje bazę danych definicji metryk |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Odczytuje metryki bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Odczytuje użycia bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/delete | Usuwa konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Zmiany priorytetów trybu failover regionów konta bazy danych. To jest używana do wykonywania operacji ręcznego przełączania trybu failover |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Pobrać parametry połączenia dla konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Listy kluczy konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Odczytuje definicje metryk konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/metrics/read | Odczytuje metryki konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Odczytanie stanu operacji asynchronicznych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Metryki opóźnienie odczytu |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/read | Przeczytaj percentylu opóźnienia replikacji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Odczytać metryki opóźnienie dla konkretnego regionu źródłowa i docelowa |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Metryki opóźnienie odczytu dla konkretnego regionu |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | Pobiera catageries dostępnych dzienników dla konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla bazy danych konta |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/read | Odczytuje konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Obróć klucze konta bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Odczytuje metryki regionalnych kolekcji. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Przeczytaj kluczowych metryk poziomu regionalnych bazy danych konta partycji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Odczytać metryki poziomu partycji konta regionalnych bazy danych |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Przeczytaj regionalnych bazy danych konta partycji w kolekcji |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Odczytuje metryki konta regionu i bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/usages/read | Odczytuje użycia konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/databaseAccounts/write | Aktualizacja konta bazy danych. |
> | Akcja | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia Microsoft.DocumentDB, że została usunięta VirtualNetwork lub podsieci |
> | Akcja | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Przeczytaj deleteVirtualNetworkOrSubnets stan operacji asynchronicznej |
> | Akcja | Microsoft.DocumentDB/operationResults/read | Odczytanie stanu operacji asynchronicznych |
> | Akcja | Microsoft.DocumentDB/operations/read | Dostępne operacje odczytu dla Microsoft DocumentDB  |
> | Akcja | Microsoft.DocumentDB/register/action |  Rejestrowanie dostawcy zasobów Microsoft DocumentDB dla subskrypcji |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DomainRegistration/checkDomainAvailability/Action | Sprawdź, czy domeny jest dostępne do zakupu |
> | Akcja | Microsoft.DomainRegistration/domains/Delete | Usuń istniejącą domenę. |
> | Akcja | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Usuń identyfikator własności |
> | Akcja | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Lista identyfikatorów własności |
> | Akcja | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Pobierz identyfikator własności |
> | Akcja | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Utwórz lub zaktualizuj identyfikator |
> | Akcja | Microsoft.DomainRegistration/domains/operationresults/Read | Operacja domeny pobierania |
> | Akcja | Microsoft.DomainRegistration/domains/Read | Pobierz listę domen |
> | Akcja | Microsoft.DomainRegistration/domains/Read | Pobierz domeny |
> | Akcja | Microsoft.DomainRegistration/domains/renew/Action | Odnów istniejącej domeny. |
> | Akcja | Microsoft.DomainRegistration/domains/Write | Dodaj nową domenę lub zaktualizuj istniejącą |
> | Akcja | Microsoft.DomainRegistration/generateSsoRequest/Action | Generuje żądanie podpisywania domeny control Center. |
> | Akcja | Microsoft.DomainRegistration/listDomainRecommendations/Action | Pobieranie listy zaleceń domeny na podstawie słów kluczowych |
> | Akcja | Microsoft.DomainRegistration/operations/Read | Wyświetl listę wszystkich działań z rejestracji domen usługi aplikacji |
> | Akcja | Microsoft.DomainRegistration/register/action | Rejestrowanie dostawcy zasobów Domains firmy Microsoft dla subskrypcji |
> | Akcja | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Wyświetl listę akcji umowy |
> | Akcja | Microsoft.DomainRegistration/topLevelDomains/Read | Pobierz toplevel domen |
> | Akcja | Microsoft.DomainRegistration/topLevelDomains/Read | Pobierz toplevel domeny |
> | Akcja | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Sprawdzanie poprawności obiektu zakupu domeny bez przesyłania |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | Wyświetl Microsoft Dynamics AX 2012 R3 oceny wdrożenia w projekcie Microsoft Dynamics cyklu życia usług, które należą do użytkownika |
> | Akcja | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | Utwórz Microsoft Dynamics AX 2012 R3 oceny wdrożenia w projekcie Microsoft Dynamics cyklu życia usług, które należą do użytkownika. Wdrożeń można zarządzać za pomocą portalu zarządzania Azure |
> | Akcja | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Łączniki odczytu, które należą do usług Microsoft Dynamics cyklu życia projektu |
> | Akcja | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Tworzenie i aktualizowanie łączniki, które należą do usług Microsoft Dynamics cyklu życia projektu |
> | Akcja | Microsoft.DynamicsLcs/lcsprojects/delete | Usuwanie usług programu Microsoft Dynamics cyklu życia projektów, które należą do użytkownika |
> | Akcja | Microsoft.DynamicsLcs/lcsprojects/read | Wyświetl projekty usług Microsoft Dynamics cyklu życia, które należą do użytkownika |
> | Akcja | Microsoft.DynamicsLcs/lcsprojects/write | Tworzenie i aktualizowanie projektów usług Microsoft Dynamics cyklu życia, które należą do użytkownika. Można aktualizować tylko nazwę i opis właściwości. Nie można zaktualizować subskrypcji i lokalizacji skojarzony z projektem po utworzeniu |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/delete | Usuń eventSubscription |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Pobierz pełny adres url dla subskrypcji zdarzeń |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla subskrypcji zdarzeń |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyki dla subskrypcji zdarzeń |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla eventSubscriptions |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/read | Przeczytaj eventSubscription |
> | Akcja | Microsoft.EventGrid/eventSubscriptions/write | Utwórz lub zaktualizuj eventSubscription |
> | Akcja | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla — tematy |
> | Akcja | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla — tematy |
> | Akcja | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla — tematy |
> | Akcja | Microsoft.EventGrid/register/action | Rejestruje dostawcę zasobów EventGrid eventSubscription i umożliwia tworzenie subskrypcji zdarzeń siatki. |
> | Akcja | Microsoft.EventGrid/topics/delete | Usunięcie tematu |
> | Akcja | Microsoft.EventGrid/topics/listKeys/action | Lista kluczy dla tematu |
> | Akcja | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla — tematy |
> | Akcja | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla — tematy |
> | Akcja | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla — tematy |
> | Akcja | Microsoft.EventGrid/topics/read | Przeczytaj temat |
> | Akcja | Microsoft.EventGrid/topics/regenerateKey/action | Regenerate klucz dla tematu |
> | Akcja | Microsoft.EventGrid/topics/write | Utwórz lub zaktualizuj tematu |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.EventHub/checkNameAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.EventHub/checkNamespaceAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailabiltiy. |
> | Akcja | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metrykę klastra opisów zasobów |
> | Akcja | Microsoft.EventHub/clusters/read | Pobiera opis zasobu klastra |
> | Akcja | Microsoft.EventHub/clusters/write | Pobiera opis zasobu klastra |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/action | Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest depricated. Można zaktualizować reguły autoryzacji Namespace zamiast tego użyj wywołania PUT. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć reguły autoryzacji Namespace domyślnej.  |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia z przestrzenią nazw |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft.EventHub/namespaces/authorizationRules/write | Tworzenie reguł autoryzacji z poziomu Namespace i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze. |
> | Akcja | Microsoft.EventHub/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobierz klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Usuwa konfiguracji odzyskiwania po awarii, skojarzone z przestrzenią nazw. Tę operację można wywołać tylko za pomocą głównej przestrzeni nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje tryb pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operacja aktualizowania EventHub. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operację można usunąć reguły autoryzacji EventHub |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Pobierz ciąg połączenia EventHub |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Pobierz listę reguł autoryzacji EventHub |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Tworzenie reguł autoryzacji EventHub i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia. |
> | Akcja | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operację można usunąć grupy konsumentów zasobów |
> | Akcja | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Pobierz listę opisów zasobów grupy konsumentów |
> | Akcja | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Utwórz lub właściwości grupy konsumentów aktualizacji. |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/Delete | Operacja usunąć EventHub zasobu |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/read | Pobierz listę opisów zasobów EventHub |
> | Akcja | Microsoft.EventHub/namespaces/eventhubs/write | Tworzenie lub aktualizacja EventHub właściwości. |
> | Akcja | Microsoft.EventHub/namespaces/messagingPlan/read | Pobiera Plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego).<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.EventHub/namespaces/messagingPlan/write | Aktualizuje wiadomości Plan dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego).<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.EventHub/namespaces/operationresults/read | Pobierz stan operacji przestrzeni nazw |
> | Akcja | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz listę Namespace ustawień diagnostycznych zasobu opisów |
> | Akcja | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Pobierz listę Namespace ustawień diagnostycznych zasobu opisów |
> | Akcja | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Pobierz listę dzienników Namespace opisów zasobów |
> | Akcja | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryki Namespace opisów zasobów |
> | Akcja | Microsoft.EventHub/namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw usługi ACS |
> | Akcja | Microsoft.EventHub/namespaces/write | Utwórz zasób Namespace i zaktualizuj jego właściwości. Znaczniki i pojemności Namespace są właściwości, które mogą być aktualizowane. |
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

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.HDInsight/clusters/changerdpsetting/action | Zmień ustawienie protokołu RDP dla klastra usługi HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/configurations/action | Zaktualizuj konfigurację klastra usługi HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/configurations/read | Pobierz konfiguracje klastrów usługi HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/delete | Usuwanie klastra usługi HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu klastra usługi HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu klastra usługi HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla klastra usługi HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/read | Uzyskiwanie szczegółowych informacji dotyczących klastra usługi HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/roles/resize/action | Zmień rozmiar klastra usługi HDInsight |
> | Akcja | Microsoft.HDInsight/clusters/write | Utwórz lub zaktualizuj klastra usługi HDInsight |
> | Akcja | Microsoft.HDInsight/locations/capabilities/read | Pobrać możliwości subskrypcji |
> | Akcja | Microsoft.HDInsight/locations/checkNameAvailability/read | Sprawdź dostępność nazwy |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ImportExport/jobs/delete | Usuwa istniejące zadanie. |
> | Akcja | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Pobiera klucze funkcji BitLocker dla określonego zadania. |
> | Akcja | Microsoft.ImportExport/jobs/read | Pobiera właściwości dla określonego zadania lub zwraca listę zadań. |
> | Akcja | Microsoft.ImportExport/jobs/write | Tworzy zadanie z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego zadania. |
> | Akcja | Microsoft.ImportExport/locations/read | Pobiera właściwości dla określonej lokalizacji lub zwraca listę lokalizacji. |
> | Akcja | Microsoft.ImportExport/register/action | Rejestruje subskrypcję dostawcy zasobów importu/eksportu i umożliwia tworzenie zadania importu/eksportu. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Insights/ActionGroups/Delete | Usuwanie grupy akcji |
> | Akcja | Microsoft.Insights/ActionGroups/Read | Odczytywanie grupy akcji |
> | Akcja | Microsoft.Insights/ActionGroups/Write | Zapisywanie grupy akcji |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Wyzwolony alert dziennika aktywności |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Delete | Usuwanie alertu dziennika aktywności |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Read | Odczytywanie alertu dziennika aktywności |
> | Akcja | Microsoft.Insights/ActivityLogAlerts/Write | Odczytywanie alertu dziennika aktywności |
> | Akcja | Microsoft.Insights/AlertRules/Activated/Action | Aktywowana reguła alertu |
> | Akcja | Microsoft.Insights/AlertRules/Delete | Usuwanie konfiguracji reguły alertu |
> | Akcja | Microsoft.Insights/AlertRules/Incidents/Read | Odczytywanie konfiguracji zdarzenia reguły alertu |
> | Akcja | Microsoft.Insights/AlertRules/Read | Odczytywanie konfiguracji reguły alertu |
> | Akcja | Microsoft.Insights/AlertRules/Resolved/Action | Rozwiązana reguła alertu |
> | Akcja | Microsoft.Insights/AlertRules/Throttled/Action | Reguła alertu jest ograniczana |
> | Akcja | Microsoft.Insights/AlertRules/Write | Zapisywanie w konfiguracji reguły alertu |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Delete | Usuwanie konfiguracji ustawienia autoskalowania |
> | Akcja | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Read | Odczytywanie konfiguracji ustawienia autoskalowania |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Autoskalowanie — operacja skalowania w dół |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Autoskalowanie — operacja skalowania w górę |
> | Akcja | Microsoft.Insights/AutoscaleSettings/Write | Zapisywanie w konfiguracji ustawienia autoskalowania |
> | Akcja | Microsoft.Insights/Components/AnalyticsItems/Delete | Usuwanie elementu analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsItems/Read | Odczytywanie elementu analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsItems/Write | Zapisywanie elementu analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsTables/Action | Akcja tabeli analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsTables/Delete | Usuwanie schematu tabeli analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsTables/Read | Odczytywanie schematu tabeli analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/AnalyticsTables/Write | Zapisywanie schematu tabeli analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/Annotations/Delete | Usuwanie adnotacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Annotations/Read | Odczytywanie adnotacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Annotations/Write | Zapisywanie adnotacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Api/Read | Odczytywanie interfejsu API danych składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ApiKeys/Action | Generowanie klucza interfejsu API usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ApiKeys/Delete | Usuwanie klucza interfejsu API usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ApiKeys/Read | Odczytywanie klucza interfejsu API usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/BillingPlanForComponent/Read | Odczytywanie planu rozliczeniowego dla składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Odczytywanie bieżących funkcji rozliczeń dla składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Zapisywanie bieżących funkcji rozliczeń dla składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Odczytywanie domyślnej konfiguracji integracji z zarządzaniem cyklem życia aplikacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Delete | Usuwanie konfiguracji składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ExportConfiguration/Action | Akcja ustawień eksportu usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ExportConfiguration/Delete | Usuwanie ustawień eksportu usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ExportConfiguration/Read | Odczytywanie ustawień eksportu usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ExportConfiguration/Write | Zapisywanie ustawień eksportu usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ExtendQueries/Read | Odczytywanie rozszerzonych wyników zapytania składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/Favorites/Delete | Usuwanie ulubionego elementu w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Favorites/Read | Odczytywanie ulubionego elementu w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Favorites/Write | Zapisywanie ulubionego elementu w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/FeatureCapabilities/Read | Odczytywanie możliwości funkcji składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Odczytywanie dostępnych funkcji rozliczeń składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/GetToken/Read | Odczytywanie tokenu składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/MetricDefinitions/Read | Odczytywanie definicji metryk składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/Metrics/Read | Odczytywanie metryk składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/Move/Action | Przenieś składnik usługi Application Insights do innej grupy zasobów lub subskrypcji |
> | Akcja | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Usuwanie osobistego elementu analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/MyAnalyticsItems/Read | Odczytywanie osobistego elementu analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/MyAnalyticsItems/Write | Zapisywanie osobistego elementu analizy usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/MyFavorites/Read | Odczytywanie osobistego ulubionego elementu w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/PricingPlans/Read | Odczytywanie planu cenowego składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/PricingPlans/Write | Zapisywanie planu cenowego składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Odczytywanie konfiguracji wykrywania proaktywnego usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Zapisywanie konfiguracji wykrywania proaktywnego usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/Components/QuotaStatus/Read | Odczytywanie stanu przydziału składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/Read | Odczytywanie konfiguracji składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Odczytywanie lokalizacji testu internetowego usługi Application Insights |
> | Akcja | Microsoft.Insights/Components/WorkItemConfigs/Delete | Usuwanie konfiguracji integracji z zarządzaniem cyklem życia aplikacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/WorkItemConfigs/Read | Odczytywanie konfiguracji integracji z zarządzaniem cyklem życia aplikacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/WorkItemConfigs/Write | Zapisywanie konfiguracji integracji z zarządzaniem cyklem życia aplikacji w usłudze Application Insights |
> | Akcja | Microsoft.Insights/Components/Write | Zapisywanie w konfiguracji składnika usługi Application Insights |
> | Akcja | Microsoft.Insights/DiagnosticSettings/Delete | Usuwanie konfiguracji ustawień diagnostycznych |
> | Akcja | Microsoft.Insights/DiagnosticSettings/Read | Odczytywanie konfiguracji ustawień diagnostycznych |
> | Akcja | Microsoft.Insights/DiagnosticSettings/Write | Zapisywanie w konfiguracji ustawień diagnostycznych |
> | Akcja | Microsoft.Insights/EventCategories/Read | Odczytywanie kategorii zdarzenia |
> | Akcja | Microsoft.Insights/eventtypes/digestevents/Read | Typ zdarzeń zarządzania — odczytaj podsumowanie |
> | Akcja | Microsoft.Insights/eventtypes/values/Read | Typ zdarzeń zarządzania — odczytaj wartości |
> | Akcja | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Usuwanie konfiguracji ustawień diagnostyki rozszerzonej |
> | Akcja | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Odczytywanie konfiguracji ustawień diagnostyki rozszerzonej |
> | Akcja | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Zapisywanie w konfiguracji ustawień diagnostyki rozszerzonej |
> | Akcja | Microsoft.Insights/ListMigrationDate/Action | Odzyskaj datę migracji subskrypcji |
> | Akcja | Microsoft.Insights/ListMigrationDate/Read | Odzyskaj datę migracji subskrypcji |
> | Akcja | Microsoft.Insights/LogDefinitions/Read | Przeczytaj definicje dzienników |
> | Akcja | Microsoft.Insights/LogProfiles/Delete | Usuń konfigurację profilów dzienników |
> | Akcja | Microsoft.Insights/LogProfiles/Read | Odczyt profilów dzienników |
> | Akcja | Microsoft.Insights/LogProfiles/Write | Zapisywanie w konfiguracji profilu dziennika |
> | Akcja | Microsoft.Insights/MetricAlerts/Delete | Usuwanie alertu metryki |
> | Akcja | Microsoft.Insights/MetricAlerts/Read | Odczytywanie alertu metryki |
> | Akcja | Microsoft.Insights/MetricAlerts/Status/Read | Odczytywanie stanu alertu metryki |
> | Akcja | Microsoft.Insights/MetricAlerts/Write | Zapisywanie alertu metryki |
> | Akcja | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/MetricDefinitions/Read | Przeczytaj definicje metryk |
> | Akcja | Microsoft.Insights/Metrics/providers/Metrics/Read | Odczytaj metryki |
> | Akcja | Microsoft.Insights/Metrics/Read | Odczytaj metryki |
> | Akcja | Microsoft.Insights/Metrics/Write | Metryki zapisu |
> | Akcja | Microsoft.Insights/MigrateToNewpricingModel/Action | Migruj subskrypcję do nowego modelu cen |
> | Akcja | Microsoft.Insights/Operations/Read | Odczytywanie operacji |
> | Akcja | Microsoft.Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Akcja | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Wycofaj subskrypcję do starszego modelu cen |
> | Akcja | Microsoft.Insights/ScheduledQueryRules/Delete | Usuwanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft.Insights/ScheduledQueryRules/Read | Odczytywanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft.Insights/ScheduledQueryRules/Write | Zapisywanie reguły zaplanowanego zapytania |
> | Akcja | Microsoft.Insights/Tenants/Register/Action | Inicjuje dostawcę usługi Microsoft Insights |
> | Akcja | Microsoft.Insights/Unregister/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Akcja | Microsoft.Insights/Webtests/Delete | Usuwanie konfiguracji testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/GetToken/Read | Odczytywanie tokenu testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/MetricDefinitions/Read | Odczytywanie definicji metryk testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/Metrics/Read | Odczytywanie metryk testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/Read | Odczytywanie konfiguracji testu internetowego |
> | Akcja | Microsoft.Insights/Webtests/Write | Zapisywanie w konfiguracji testu internetowego |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.KeyVault/checkNameAvailability/read | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa oraz czy została już użyta |
> | Akcja | Microsoft.KeyVault/deletedVaults/read | Wyświetl właściwości usuniętych nietrwale magazynów kluczy |
> | Akcja | Microsoft.KeyVault/hsmPools/delete | Usuń pulę modułów HSM |
> | Akcja | Microsoft.KeyVault/hsmPools/joinVault/action | Dołącz magazyn kluczy do puli modułów HSM |
> | Akcja | Microsoft.KeyVault/hsmPools/read | Wyświetl właściwości puli modułów HSM |
> | Akcja | Microsoft.KeyVault/hsmPools/write | Utwórz nową pulę modułów HSM lub zaktualizuj właściwości istniejącej puli modułów HSM |
> | Akcja | Microsoft.KeyVault/locations/deletedVaults/purge/action | Przeczyść usunięty nietrwale magazyn kluczy |
> | Akcja | Microsoft.KeyVault/locations/deletedVaults/read | Wyświetl właściwości usuniętego nietrwale magazynu kluczy |
> | Akcja | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia element Microsoft.KeyVault o usuwaniu podsieci lub sieci wirtualnej |
> | Akcja | Microsoft.KeyVault/locations/operationResults/read | Sprawdź wynik operacji długiego uruchamiania |
> | Akcja | Microsoft.KeyVault/operations/read | Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.KeyVault |
> | Akcja | Microsoft.KeyVault/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.KeyVault/unregister/action | Wyrejestrowuje subskrypcję |
> | Akcja | Microsoft.KeyVault/vaults/accessPolicies/write | Aktualizowanie istniejących zasad dostępu przez scalanie lub zastępowanie albo dodawanie nowych zasad dostępu do magazynu. |
> | Akcja | Microsoft.KeyVault/vaults/delete | Usuń magazyn kluczy |
> | Akcja | Microsoft.KeyVault/vaults/deploy/action | Zapewnia dostęp do wpisów tajnych w magazynie kluczy podczas wdrażania zasobów platformy Azure |
> | Akcja | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla magazynu kluczy |
> | Akcja | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla magazynu kluczy |
> | Akcja | Microsoft.KeyVault/vaults/read | Wyświetl właściwości magazynu kluczy |
> | Akcja | Microsoft.KeyVault/vaults/secrets/read | Wyświetl właściwości wpisu tajnego, ale nie jego wartość |
> | Akcja | Microsoft.KeyVault/vaults/secrets/write | Utwórz nowy wpis tajny lub zaktualizuj wartość istniejącego wpisu tajnego |
> | Akcja | Microsoft.KeyVault/vaults/write | Utwórz nowy magazyn kluczy lub zaktualizuj właściwości istniejącego magazynu kluczy |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.LabServices/labAccounts/CreateLab/action | Tworzenie laboratorium w ramach konta laboratorium. |
> | Akcja | Microsoft.LabServices/labAccounts/delete | Usuwanie kont laboratorium. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/delete | Usuń labs. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Oświadczenia losowe środowiska użytkownika w ustawieniach środowiska |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Usuń ustawienie środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Oświadczeń środowiska i przypisuje go do użytkownika |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Usuwanie środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Przeczytaj środowisk. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Uruchamia środowisko przez uruchomienie wszystkich zasobów w środowisku. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Dodaje lub modyfikuje środowisk. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Przepisy/deprovisions wymaganych zasobów dla ustawienia środowiska na podstawie bieżącego stanu ustawienia/w środowisku laboratoryjnym. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Ustawienie środowiska odczytu. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Uruchamia szablonu przez uruchomienie wszystkich zasobów w szablonie. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Uruchamia szablonu przez uruchomienie wszystkich zasobów w szablonie. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Dodaje lub modyfikuje ustawienia środowiska. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/read | Laboratoria odczytu. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/Register/action | Zarejestruj się w laboratorium zarządzanych. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/users/delete | Usuwanie użytkowników. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/users/read | Do odczytu użytkowników. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/users/write | Dodaje lub modyfikuje użytkowników. |
> | Akcja | Microsoft.LabServices/labAccounts/labs/write | Dodaje lub modyfikuje labs. |
> | Akcja | Microsoft.LabServices/labAccounts/read | Odczytywanie kont laboratorium. |
> | Akcja | Microsoft.LabServices/labAccounts/sizes/read | Przeczytaj rozmiary. |
> | Akcja | Microsoft.LabServices/labAccounts/write | Dodaje lub modyfikuje konta laboratorium. |
> | Akcja | Microsoft.LabServices/locations/operations/read | Operacje odczytu. |
> | Akcja | Microsoft.LabServices/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.LabServices/users/GetEnvironment/action | Pobiera szczegóły maszyny wirtualnej |
> | Akcja | Microsoft.LabServices/users/ListEnvironments/action | Lista środowiska użytkownika |
> | Akcja | Microsoft.LabServices/users/ListLabs/action | Lista labs dla użytkownika. |
> | Akcja | Microsoft.LabServices/users/Register/action | Zarejestruj użytkownika do zarządzanego laboratorium |
> | Akcja | Microsoft.LabServices/users/StartEnvironment/action | Uruchamia środowisko przez uruchomienie wszystkich zasobów w środowisku. |
> | Akcja | Microsoft.LabServices/users/StopEnvironment/action | Zatrzymuje środowisko przez zatrzymanie wszystkich zasobów w środowisku |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.LocationBasedServices/accounts/delete | (Przestarzałe: Użyj /providers/Microsoft.Maps) Usuń lokalizacji na podstawie konto usług. |
> | Akcja | Microsoft.LocationBasedServices/accounts/listKeys/action | (Przestarzałe: Użyj /providers/Microsoft.Maps) Klucze konta usługi na podstawie lokalizacji listy |
> | Akcja | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Przestarzałe: Użyj /providers/Microsoft.Maps) Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Przestarzałe: Użyj /providers/Microsoft.Maps) Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Przestarzałe: Użyj /providers/Microsoft.Maps) Pobiera dostępne metryki dla kont usług na podstawie lokalizacji |
> | Akcja | Microsoft.LocationBasedServices/accounts/read | (Przestarzałe: Użyj /providers/Microsoft.Maps) Uzyskiwanie lokalizacji na podstawie konto usług. |
> | Akcja | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Przestarzałe: Użyj /providers/Microsoft.Maps) Generuj nowy klucz podstawowy lub pomocniczy konta usługi na podstawie lokalizacji |
> | Akcja | Microsoft.LocationBasedServices/accounts/write | (Przestarzałe: Użyj /providers/Microsoft.Maps) Utwórz lub zaktualizuj konto usługi na podstawie lokalizacji. |
> | Akcja | Microsoft.LocationBasedServices/register/action | (Przestarzałe: Użyj /providers/Microsoft.Maps) Zarejestruj dostawcę |

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
> | Akcja | Microsoft.Logic/workflows/listSwagger/action | Pobiera definicje struktury Swagger przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/move/action | Przenosi przepływ pracy z jego obecnego identyfikatora subskrypcji, grupy zasobów i/lub nazwy do innego identyfikatora subskrypcji, grupy zasobów i/lub nazwy. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Odczytuje ustawienia diagnostyki przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub modyfikuje ustawienie diagnostyki przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Odczytuje definicje dzienników przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Odczytuje definicje metryk przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/read | Odczytuje przepływ pracy. |
> | Akcja | Microsoft.Logic/workflows/regenerateAccessKey/action | Generuje ponownie wpisy tajne klucza dostępu. |
> | Akcja | Microsoft.Logic/workflows/run/action | Uruchamia przebieg przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Pobiera ślady wyrażenia akcji uruchamiania przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/read | Odczytuje akcję przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Pobiera ślady wyrażenia powtórzenia akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/repetitions/read | Odczytuje powtórzenie akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Odczytuje powtórzenie zakresu akcji przebiegu przepływu pracy. |
> | Akcja | Microsoft.Logic/workflows/runs/cancel/action | Anuluje przebieg przepływu pracy. |
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
> | Akcja | Microsoft.Logic/workflows/write | Tworzy lub modyfikuje przepływ pracy. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Przenieś wszystkie Machine Learning skojarzenia planu zobowiązań |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Przeczytaj maszyn zobowiązań skojarzenia Plan nauki |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/delete | Usuń wszystkie maszyny zobowiązań Plan nauki |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/join/action | Dołącz do dowolnej maszyny zobowiązań Plan nauki |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/read | Przeczytaj maszyn zobowiązań Plan nauki |
> | Akcja | Microsoft.MachineLearning/commitmentPlans/write | Utwórz lub zaktualizuj żadnych zobowiązań Plan nauki maszyny |
> | Akcja | Microsoft.MachineLearning/locations/operationresults/read | Pobierz wynik operacji Machine Learning |
> | Akcja | Microsoft.MachineLearning/locations/operationsstatus/read | Pobierz stan trwającą operacją Machine Learning |
> | Akcja | Microsoft.MachineLearning/operations/read | Pobierz operacje uczenia maszynowego |
> | Akcja | Microsoft.MachineLearning/register/action | Rejestruje subskrypcję dostawcy zasobów usługi sieci web uczenia maszynowego i umożliwia tworzenie usług sieci web. |
> | Akcja | Microsoft.MachineLearning/skus/read | Pobierz jednostki SKU planu zobowiązań uczenia maszynowego |
> | Akcja | Microsoft.MachineLearning/webServices/action | Utwórz regionalnych właściwości obsługiwanych regionów usługi sieci Web |
> | Akcja | Microsoft.MachineLearning/webServices/delete | Usuń usługi sieci Web Machine Learning |
> | Akcja | Microsoft.MachineLearning/webServices/listkeys/read | Pobieranie kluczy do usługi sieci Web Machine Learning |
> | Akcja | Microsoft.MachineLearning/webServices/read | Usługi sieci Web Machine Learning do odczytu |
> | Akcja | Microsoft.MachineLearning/webServices/write | Utwórz lub zaktualizuj usługi sieci Web Machine Learning |
> | Akcja | Microsoft.MachineLearning/Workspaces/delete | Usuń wszelkie Machine Learning obszaru roboczego |
> | Akcja | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Lista kluczy dla obszaru roboczego Machine Learning |
> | Akcja | Microsoft.MachineLearning/Workspaces/read | Wszelkie Machine Learning obszaru roboczego do odczytu |
> | Akcja | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Zsynchronizowanie klucze konta magazynu skonfigurowanego dla obszaru roboczego Machine Learning |
> | Akcja | Microsoft.MachineLearning/Workspaces/write | Utwórz lub zaktualizuj wszystkie Machine Learning obszaru roboczego |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Sprawdź, czy aktualizacje są dostępne dla systemu usług dla klastra operationalization |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Usuń dowolne konto hostingu |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Utwórz listę kluczy skojarzony z klastrem operationalization |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/read | Przeczytaj dowolne konto hostingu |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Aktualizacja usługi systemowe w klastrze operationalization |
> | Akcja | Microsoft.MachineLearningCompute/operationalizationClusters/write | Utwórz lub zaktualizuj dowolne konto hostingu |
> | Akcja | Microsoft.MachineLearningCompute/register/action | Rejestruje identyfikator subskrypcji dostawcy zasobów i umożliwia tworzenie zasoby obliczeniowe uczenia maszynowego |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MachineLearningModelManagement/accounts/delete | Usuń dowolne konto hostingu |
> | Akcja | Microsoft.MachineLearningModelManagement/accounts/read | Przeczytaj dowolne konto hostingu |
> | Akcja | Microsoft.MachineLearningModelManagement/accounts/write | Utwórz lub zaktualizuj dowolne konto hostingu |
> | Akcja | Microsoft.MachineLearningModelManagement/register/action | Rejestruje identyfikator subskrypcji dostawcy zasobów i umożliwia tworzenie konta hostingu |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MachineLearningServices/register/action | Rejestruje subskrypcję dostawcy zasobów Machine Learning usług |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/delete | Usuwa zasoby obliczeniowe w obszarów roboczych usługi uczenie maszyny |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Lista kluczy tajnych dla zasobów obliczeniowych w Machine Learning usług w obszarze roboczym |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/read | Pobiera zasoby obliczeniowe w obszarów roboczych usługi uczenie maszyny |
> | Akcja | Microsoft.MachineLearningServices/workspaces/computes/write | Tworzy lub aktualizuje zasobów obliczeniowych w obszarów roboczych usługi uczenie maszyny |
> | Akcja | Microsoft.MachineLearningServices/workspaces/delete | Usuwa obszarów roboczych usługi uczenia maszynowego |
> | Akcja | Microsoft.MachineLearningServices/workspaces/listKeys/action | Utwórz listę kluczy tajnych obszaru roboczego usług Machine Learning |
> | Akcja | Microsoft.MachineLearningServices/workspaces/read | Pobiera obszarów roboczych usługi uczenia maszynowego |
> | Akcja | Microsoft.MachineLearningServices/workspaces/write | Tworzy lub aktualizuje obszarów roboczych usługi Machine Learning |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Akcja RBAC przypisywania istniejącego użytkownika do zasobu przydzielono tożsamości |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Usuwa istniejącego użytkownika przypisane tożsamości |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/read | Pobiera istniejącego użytkownika przypisane tożsamości |
> | Akcja | Microsoft.ManagedIdentity/userAssignedIdentities/write | Tworzy nowego użytkownika przypisane tożsamości lub aktualizuje do tagów skojarzonych z istniejącego użytkownika przypisane tożsamości |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ManagedLab/labAccounts/CreateLab/action | Tworzenie laboratorium w ramach konta laboratorium. |
> | Akcja | Microsoft.ManagedLab/labAccounts/delete | Usuwanie kont laboratorium. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/delete | Usuń labs. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Usuń ustawienie środowiska. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Usuwanie środowiska. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Przeczytaj środowisk. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Dodaje lub modyfikuje środowisk. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Ustawienie środowiska odczytu. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Dodaje lub modyfikuje ustawienia środowiska. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Usuń maszyny wirtualne laboratorium. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Przeczytaj maszyn wirtualnych laboratorium. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Dodaje lub modyfikuje maszyny wirtualne laboratorium. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/read | Laboratoria odczytu. |
> | Akcja | Microsoft.ManagedLab/labAccounts/labs/write | Dodaje lub modyfikuje labs. |
> | Akcja | Microsoft.ManagedLab/labAccounts/read | Odczytywanie kont laboratorium. |
> | Akcja | Microsoft.ManagedLab/labAccounts/write | Dodaje lub modyfikuje konta laboratorium. |
> | Akcja | Microsoft.ManagedLab/locations/operations/read | Operacje odczytu. |
> | Akcja | Microsoft.ManagedLab/register/action | Rejestruje subskrypcję |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Management/checkNameAvailability/action | Sprawdza, czy nazwa grupy zarządzania określony jest prawidłowa i unikatowe. |
> | Akcja | Microsoft.Management/getEntities/action | Wyświetl listę wszystkich jednostek (grup zarządzania, subskrypcje, itp.) dla tego uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.Management/managementGroups/delete | Usuwanie grupy zarządzania. |
> | Akcja | Microsoft.Management/managementGroups/read | Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika. |
> | Akcja | Microsoft.Management/managementGroups/subscriptions/delete | Kojarzy usuwania subskrypcji z grupy zarządzania. |
> | Akcja | Microsoft.Management/managementGroups/subscriptions/write | Stowarzyszonych istniejącej subskrypcji z grupą zarządzania. |
> | Akcja | Microsoft.Management/managementGroups/write | Utwórz lub zaktualizuj grupy zarządzania. |
> | Akcja | Microsoft.Management/register/action | Zarejestruj Microsoft.Management określonej subskrypcji |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Maps/accounts/delete | Usuwanie konta mapy. |
> | Akcja | Microsoft.Maps/accounts/listKeys/action | Lista kluczy konta mapy |
> | Akcja | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla kont mapy |
> | Akcja | Microsoft.Maps/accounts/read | Uzyskaj konto mapy. |
> | Akcja | Microsoft.Maps/accounts/regenerateKey/action | Generuj nowy klucz podstawowy lub pomocniczy konta mapy |
> | Akcja | Microsoft.Maps/accounts/write | Utwórz lub zaktualizuj konto mapy. |
> | Akcja | Microsoft.Maps/register/action | Zarejestruj dostawcę |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Zwraca umowy. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Akceptuje podpisanej umowy. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importuje obraz do ACR przez użytkownika końcowego. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Zwraca konfiguracji. |
> | Akcja | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Zapisuje konfiguracji. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/delete | Wykonuje operację usuwania zasobu usługi klasycznego deweloperów. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Pobiera klucze zarządzania zasobów usługi klasycznego deweloperów. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Pobiera pojedynczy znak na adres URL dla usługi klasycznego deweloperów. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/read | Wykonuje operację GET w usłudze klasycznego deweloperów. |
> | Akcja | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generuje klucze zarządzania zasobów klasycznych deweloperów usługi. |
> | Akcja | Microsoft.MarketplaceApps/Operations/read | Operacje dla wszystkich typów zasobów odczytu. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Anuluj umowę dla elementu danej witryny marketplace |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Zwraca umowę dla elementu danej witryny marketplace |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Podpisać umowę dla elementu danej witryny marketplace |
> | Akcja | Microsoft.MarketplaceOrdering/agreements/read | Zwróć wszystkie umowy w podanej subskrypcji |
> | Akcja | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Pobierz umowę dla elementu marketplace danej maszyny wirtualnej |
> | Akcja | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Zaloguj się, lub Anuluj umowę dla elementu marketplace danej maszyny wirtualnej |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Media/checknameavailability/action | Sprawdza, czy nazwa konta usługi Media Services jest dostępna |
> | Akcja | Microsoft.Media/locations/checkNameAvailability/action | Sprawdza, czy nazwa konta usługi Media Services jest dostępna |
> | Akcja | Microsoft.Media/mediaservices/assets/delete | Usuń zasób |
> | Akcja | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Pobierz klucz szyfrowania zasobów |
> | Akcja | Microsoft.Media/mediaservices/assets/listContainerSas/action | Zasobów kontenera SAS adresy URL |
> | Akcja | Microsoft.Media/mediaservices/assets/read | Zasób, do odczytu |
> | Akcja | Microsoft.Media/mediaservices/assets/write | Utwórz lub zaktualizuj zasób |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Usuń dowolne zasady klucza zawartości |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Pobierz właściwości zasad z kluczy tajnych |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/read | Odczytaj dowolne zasady klucza zawartości |
> | Akcja | Microsoft.Media/mediaservices/contentKeyPolicies/write | Utwórz lub zaktualizuj wszystkie zasady klucza zawartości |
> | Akcja | Microsoft.Media/mediaservices/delete | Usuń wszystkie konta usługi Media Services |
> | Akcja | Microsoft.Media/mediaservices/eventGridFilters/delete | Usuń wszystkie filtry siatki zdarzeń |
> | Akcja | Microsoft.Media/mediaservices/eventGridFilters/read | Dowolny filtr siatki zdarzeń do odczytu |
> | Akcja | Microsoft.Media/mediaservices/eventGridFilters/write | Utwórz lub zaktualizuj filtr siatki żadnych zdarzeń |
> | Akcja | Microsoft.Media/mediaservices/liveEventOperations/read | Wszystkie wydarzenia na żywo operacja odczytu |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/delete | Usuń wszystkie wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Usuń wszelkie na żywo dane wyjściowe |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Wszelkie na żywo dane wyjściowe do odczytu |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Utwórz lub zaktualizuj żadnego wyniku na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/read | Przeczytaj wszystkie wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/reset/action | Resetuj żadnej operacji wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/start/action | Uruchamianie operacji wszystkie wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/stop/action | Zatrzymaj operację wszystkie wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveEvents/write | Utwórz lub zaktualizuj wszystkie wydarzenia na żywo |
> | Akcja | Microsoft.Media/mediaservices/liveOutputOperations/read | Wszystkie dane wyjściowe na żywo operacja odczytu |
> | Akcja | Microsoft.Media/mediaservices/read | Wszystkie konta usługi Media Services do odczytu |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Przeczytaj żadnej operacji przesyłania strumieniowego punktu końcowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/delete | Usuń wszelkie punktu końcowego przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/read | Przeczytaj dowolnego punktu końcowego przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Skalowanie żadnej operacji przesyłania strumieniowego punktu końcowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Uruchom żadnej operacji przesyłania strumieniowego punktu końcowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Zatrzymaj wszelkie operacje przesyłania strumieniowego punktu końcowego |
> | Akcja | Microsoft.Media/mediaservices/streamingEndpoints/write | Utwórz lub zaktualizuj dowolnego punktu końcowego przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/delete | Usuń wszelkie Lokalizator przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Lista kluczy zawartości |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Lista ścieżek |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/read | Przeczytaj żadnych Lokalizator przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingLocators/write | Utwórz lub zaktualizuj wszystkie Lokalizator przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingPolicies/delete | Usuń dowolne zasady przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingPolicies/read | Odczytaj dowolne zasady przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/streamingPolicies/write | Utwórz lub zaktualizuj wszystkie zasady przesyłania strumieniowego |
> | Akcja | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronizowanie klucza magazynu dołączonego konta usługi Azure Storage |
> | Akcja | Microsoft.Media/mediaservices/transforms/delete | Usuń wszelkie przekształcenia |
> | Akcja | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Anuluj zadanie |
> | Akcja | Microsoft.Media/mediaservices/transforms/jobs/delete | Usuń wszystkie zadania |
> | Akcja | Microsoft.Media/mediaservices/transforms/jobs/read | Wszystkie zadania do odczytu |
> | Akcja | Microsoft.Media/mediaservices/transforms/jobs/write | Utwórz lub zaktualizuj wszystkie zadania |
> | Akcja | Microsoft.Media/mediaservices/transforms/read | Przeczytaj transformację |
> | Akcja | Microsoft.Media/mediaservices/transforms/write | Utwórz lub zaktualizuj transformację |
> | Akcja | Microsoft.Media/mediaservices/write | Utwórz lub zaktualizuj wszystkie konta usługi Media Services |
> | Akcja | Microsoft.Media/operations/read | Pobiera dostępne operacje |
> | Akcja | Microsoft.Media/register/action | Rejestruje subskrypcję dostawcy zasobów usługi Media Services i umożliwia tworzenie konta usługi Media Services |
> | Akcja | Microsoft.Media/unregister/action | Wyrejestrowuje subskrypcji dla dostawcy zasobów usługi Media Services |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Migrate/Operations/read | Odczytuje ujawnione operacje |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Ssl bramy aplikacji wstępnie zdefiniowane zasady |
> | Akcja | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Dostępne opcje Ssl bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Pobiera bramę aplikacji, ustawia dostępne reguły zapory aplikacji sieci Web |
> | Akcja | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Dołącza do puli adresów zaplecza bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/backendhealth/action | Pobiera kondycji zaplecza bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/delete | Usuwa bramę aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Pobierz tabelę tras skonfigurowany dla bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Pobierz tabelę tras skonfigurowany dla bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Pobiera zdarzenia bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/read | Pobiera bramę aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Centrum zabezpieczeń bramy aplikacji zestawów konfiguracji |
> | Akcja | Microsoft.Network/applicationGateways/start/action | Uruchamia bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/stop/action | Zatrzymuje bramy aplikacji |
> | Akcja | Microsoft.Network/applicationGateways/write | Tworzy bramę aplikacji lub aktualizuje istniejącą bramę aplikacji |
> | Akcja | Microsoft.Network/applicationSecurityGroups/delete | Usuwa grupę zabezpieczeń aplikacji |
> | Akcja | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Tworzy sprzężenie konfigurację protokołu IP dla grup zabezpieczeń aplikacji. |
> | Akcja | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Tworzy sprzężenie reguły zabezpieczeń dla grup zabezpieczeń aplikacji. |
> | Akcja | Microsoft.Network/applicationSecurityGroups/read | Pobiera identyfikator grupy zabezpieczeń aplikacji. |
> | Akcja | Microsoft.Network/applicationSecurityGroups/write | Tworzy grupę zabezpieczeń aplikacji, lub aktualizuje istniejącą grupę zabezpieczeń aplikacji. |
> | Akcja | Microsoft.Network/bgpServiceCommunities/read | Pobierz społeczności usługi protokołu Bgp |
> | Akcja | Microsoft.Network/checkTrafficManagerNameAvailability/action | Sprawdza dostępność nazwy DNS względem usługi Traffic Manager. |
> | Akcja | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/connections/sharedkey/action | Pobierz element VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft.Network/connections/sharedKey/read | Pobiera element VirtualNetworkGatewayConnection SharedKey |
> | Akcja | Microsoft.Network/connections/sharedKey/write | Tworzy lub aktualizuje istniejącą SharedKey element VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Pobiera element VirtualNetworkGatewayConnection konfiguracji urządzenia sieci Vpn |
> | Akcja | Microsoft.Network/connections/write | Tworzy lub aktualizuje istniejący element VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | Usuwa serwer Proxy planu ochrony przed atakami DDoS |
> | Akcja | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | Pobiera definicję ochrony przed atakami DDoS Planowanie serwera Proxy |
> | Akcja | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | Tworzy serwer Proxy Plan ochrony przed atakami DDoS lub aktualizacji i serwer Proxy Plan ochrony przed atakami DDoS istniejących |
> | Akcja | Microsoft.Network/ddosProtectionPlans/delete | Usuwa Plan ochrony przed atakami DDoS |
> | Akcja | Microsoft.Network/ddosProtectionPlans/join/action | Tworzy sprzężenie Plan ochrony przed atakami DDoS |
> | Akcja | Microsoft.Network/ddosProtectionPlans/read | Pobiera Plan ochrony przed atakami DDoS |
> | Akcja | Microsoft.Network/ddosProtectionPlans/write | Tworzy Plan ochrony przed atakami DDoS lub aktualizuje Plan ochrony przed atakami DDoS  |
> | Akcja | Microsoft.Network/dnsoperationresults/read | Pobiera wyniki operacji DNS |
> | Akcja | Microsoft.Network/dnsoperationstatuses/read | Pobiera stan operacji DNS  |
> | Akcja | Microsoft.Network/dnszones/A/delete | Usuń zestaw rekordów o danej nazwie i typu "A" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/A/read | Pobierz zestaw rekordów typu "A", w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag. |
> | Akcja | Microsoft.Network/dnszones/A/write | Utwórz lub zaktualizuj zestaw rekordów typu "A" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/AAAA/delete | Usuń zestaw rekordów o danej nazwie i typu "AAAA" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/AAAA/read | Pobierz zestaw rekordów typu "AAAA" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag. |
> | Akcja | Microsoft.Network/dnszones/AAAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/all/read | Pobiera różnych typów zestawów rekordów DNS |
> | Akcja | Microsoft.Network/dnszones/CAA/delete | Usuń zestaw rekordów o danej nazwie i wpisz "CAA" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/CAA/read | Pobierz zestaw rekordów typu "CAA" w formacie JSON. Zestaw rekordów zawiera TTL, tag i etag. |
> | Akcja | Microsoft.Network/dnszones/CAA/write | Utwórz lub zaktualizuj zestaw rekordów typu "CAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/CNAME/delete | Usuń zestaw rekordów o danej nazwie i typu "CNAME" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/CNAME/read | Pobierz zestaw rekordów typu "CNAME" w formacie JSON. Zestaw rekordów zawiera TTL, tag i etag. |
> | Akcja | Microsoft.Network/dnszones/CNAME/write | Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/delete | Usuń strefę DNS, w formacie JSON. Właściwości strefy obejmują tag, etag, numberOfRecordSets i maxNumberOfRecordSets. |
> | Akcja | Microsoft.Network/dnszones/MX/delete | Usuń zestaw rekordów o danej nazwie i typu "MX" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/MX/read | Pobierz zestaw rekordów typu "MX" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag. |
> | Akcja | Microsoft.Network/dnszones/MX/write | Utwórz lub zaktualizuj zestaw rekordów typu "MX" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/NS/delete | Usuwa zestaw rekordów DNS typu NS |
> | Akcja | Microsoft.Network/dnszones/NS/read | Pobiera zestaw rekordów DNS typu NS |
> | Akcja | Microsoft.Network/dnszones/NS/write | Tworzy lub aktualizuje zestaw rekordów DNS typu NS |
> | Akcja | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyki strefy DNS |
> | Akcja | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyki strefy DNS |
> | Akcja | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | Pobiera definicje metryk strefy DNS |
> | Akcja | Microsoft.Network/dnszones/PTR/delete | Usuń zestaw rekordów o danej nazwie i typu "PTR" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/PTR/read | Pobierz zestaw rekordów typu "PTR" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag. |
> | Akcja | Microsoft.Network/dnszones/PTR/write | Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/read | Pobierz strefę DNS, w formacie JSON. Właściwości strefy obejmują tag, etag, numberOfRecordSets i maxNumberOfRecordSets. Należy pamiętać, że to polecenie nie pobiera zestawów rekordów znajdujących się w strefie. |
> | Akcja | Microsoft.Network/dnszones/recordsets/read | Pobiera różnych typów zestawów rekordów DNS |
> | Akcja | Microsoft.Network/dnszones/SOA/read | Pobiera zestaw rekordów DNS typu SOA |
> | Akcja | Microsoft.Network/dnszones/SOA/write | Tworzy lub aktualizuje zestaw rekordów DNS typu SOA |
> | Akcja | Microsoft.Network/dnszones/SRV/delete | Usuń zestaw rekordów o danej nazwie i typu "SRV" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/SRV/read | Pobierz zestaw rekordów typu "SRV" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag. |
> | Akcja | Microsoft.Network/dnszones/SRV/write | Utwórz lub zaktualizuj zestaw rekordów typu SRV |
> | Akcja | Microsoft.Network/dnszones/TXT/delete | Usuń zestaw rekordów o danej nazwie i typu "TXT" ze strefy DNS. |
> | Akcja | Microsoft.Network/dnszones/TXT/read | Pobierz zestaw rekordów typu "TXT" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag. |
> | Akcja | Microsoft.Network/dnszones/TXT/write | Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów. |
> | Akcja | Microsoft.Network/dnszones/write | Utwórz lub zaktualizuj strefę DNS w ramach grupy zasobów.  Używane do aktualizowania elementów tag w zasobie strefy DNS. Należy pamiętać, że to polecenie nie może służyć do utworzenia ani zaktualizowania zestawów rekordów w strefie. |
> | Akcja | Microsoft.Network/expressRouteCircuits/authorizations/delete | Usuwa ExpressRouteCircuit autoryzacji |
> | Akcja | Microsoft.Network/expressRouteCircuits/authorizations/read | Pobiera autoryzacji ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/authorizations/write | Tworzy lub aktualizuje istniejącą autoryzacji ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/delete | Usuwa ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/join/action | Tworzy sprzężenie obwodu Expressroute |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Pobiera ExpressRouteCircuit ArpTable komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Usuwa połączenie ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Pobiera połączenie ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Tworzy lub aktualizuje istniejący zasób połączenia ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/delete | Usuwa ExpressRouteCircuit komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/read | Pobiera ExpressRouteCircuit komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Pobiera stan równorzędna ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Pobiera ExpressRouteCircuit równorzędna stan podsumowanie |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Pobiera ExpressRouteCircuit równorzędna Stat |
> | Akcja | Microsoft.Network/expressRouteCircuits/peerings/write | Tworzy lub aktualizuje istniejącą ExpressRouteCircuit komunikacji równorzędnej |
> | Akcja | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyki dla obwody usługi ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyki dla obwody usługi ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | Uzyskać zdarzenia obwody usługi ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | Pobiera metryki definicje obwody usługi ExpressRoute |
> | Akcja | Microsoft.Network/expressRouteCircuits/read | Pobierz ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/stats/read | Pobiera stan ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCircuits/write | Tworzy lub aktualizuje istniejące ExpressRouteCircuit |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/delete | Usuń Express Route Cross połączenia |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/join/action | Połączenie między sprzężenia usługi Express Route |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Pobiera Express Route Cross tabeli protokołu Arp równorzędna połączenia |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Usuwa Cross połączenia komunikacji równorzędnej Expressroute |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/read | Pobiera Cross połączenia komunikacji równorzędnej Expressroute |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Pobiera Express Route Cross połączenia komunikacji równorzędnej tabeli tras |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Pobiera Cross podsumowanie tabeli tras połączenia komunikacji równorzędnej Expressroute |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/peerings/write | Tworzy trasę dla wielu połączeń równorzędnej lub aktualizuje istniejącą Cross połączenia komunikacji równorzędnej Express Route |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/read | Pobierz Express Route Cross połączenia |
> | Akcja | Microsoft.Network/expressRouteCrossConnections/write | Utwórz lub zaktualizuj Express Route Cross połączenia |
> | Akcja | Microsoft.Network/expressRouteServiceProviders/read | Pobiera dostawców usługi Expressroute |
> | Akcja | Microsoft.Network/intendedPolicies/delete | Usuwa określone zasady |
> | Akcja | Microsoft.Network/intendedPolicies/read | Pobiera opis określone zasady |
> | Akcja | Microsoft.Network/intendedPolicies/write | Tworzy zasady przeznaczone lub aktualizuje istniejące zasady przeznaczone |
> | Akcja | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Dołącza do puli adresów zaplecza modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/backendAddressPools/read | Pobiera definicję puli adresów zaplecza modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/delete | Usuwa usługę równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Pobiera definicję konfiguracji IP frontonu modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Tworzy sprzężenie modułu równoważenia obciążenia ruchu przychodzącego pulę translacji nat |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatPools/read | Pobiera moduł równoważenia obciążenia ruchu przychodzącego definicję puli translacji nat |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/delete | Usuwa regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Tworzy sprzężenie reguły nat dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/read | Pobiera moduł równoważenia obciążenia definicję przychodzącej reguły nat |
> | Akcja | Microsoft.Network/loadBalancers/inboundNatRules/write | Tworzy regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia lub aktualizuje istniejącą regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/loadBalancingRules/read | Pobiera definicję modułu równoważenia obciążenia obciążenia równoważenia reguły |
> | Akcja | Microsoft.Network/loadBalancers/networkInterfaces/read | Pobiera odwołania do wszystkich interfejsów sieciowych należących do modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/outboundNatRules/read | Pobiera definicję reguły nat ruchu wychodzącego modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/probes/join/action | Zezwala przy użyciu sondy modułu równoważenia obciążenia. Na przykład z tą właściwością healthProbe uprawnienia skali maszyny Wirtualnej zestawu można odwoływać się sondy. |
> | Akcja | Microsoft.Network/loadBalancers/probes/read | Pobiera sondę modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyki usługi równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyki usługi równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | Pobiera zdarzenia do modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla usługi równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/read | Pobiera definicję modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych należących do modułu równoważenia obciążenia |
> | Akcja | Microsoft.Network/loadBalancers/write | Tworzy moduł równoważenia obciążenia lub aktualizuje istniejący moduł równoważenia obciążenia |
> | Akcja | Microsoft.Network/localnetworkgateways/delete | Usuwa LocalNetworkGateway |
> | Akcja | Microsoft.Network/localnetworkgateways/read | Pobiera LocalNetworkGateway |
> | Akcja | Microsoft.Network/localnetworkgateways/write | Tworzy lub aktualizuje istniejące LocalNetworkGateway |
> | Akcja | Microsoft.Network/locations/availableDelegations/read | Pobiera dostępne delegowania |
> | Akcja | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Obsługa sieci przyspieszony kontroli |
> | Akcja | Microsoft.Network/locations/checkDnsNameAvailability/read | Sprawdza, czy etykieta dns jest dostępna w określonej lokalizacji |
> | Akcja | Microsoft.Network/locations/operationResults/read | Pobiera wynik operacji asynchronicznej POST lub operacja usuwania |
> | Akcja | Microsoft.Network/locations/operations/read | Pobiera zasób operacji reprezentujący stan operacji asynchronicznej |
> | Akcja | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Pobiera obsługiwane rozmiary maszyn wirtualnych |
> | Akcja | Microsoft.Network/locations/usages/read | Pobiera metryki użycia zasobów |
> | Akcja | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Pobiera listę dostępnych usług punktu końcowego sieci wirtualnej |
> | Akcja | Microsoft.Network/networkInterfaces/delete | Usuwa interfejs sieciowy |
> | Akcja | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | Pobiera tożsamość diagnostycznych zasobu |
> | Akcja | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Pobieranie grup zabezpieczeń sieci skonfigurowane w sieci interfejsu z maszyny wirtualnej |
> | Akcja | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Pobierz tabelę tras skonfigurowane dla interfejsu sieciowego maszyny wirtualnej |
> | Akcja | Microsoft.Network/networkInterfaces/ipconfigurations/read | Pobiera definicję konfiguracji adresu ip interfejsu sieciowego.  |
> | Akcja | Microsoft.Network/networkInterfaces/join/action | Dołącza maszynę wirtualną do interfejsu sieciowego |
> | Akcja | Microsoft.Network/networkInterfaces/joinViaPrivateIp/action | Tworzy sprzężenie zasobu do interfejsu sieciowego za pośrednictwem skojarzenie usługi |
> | Akcja | Microsoft.Network/networkInterfaces/loadBalancers/read | Pobiera wszystkich usług równoważenia obciążenia interfejsu sieciowego jest częścią |
> | Akcja | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki interfejsu sieciowego |
> | Akcja | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Akcja | Microsoft.Network/networkInterfaces/serviceAssociations/delete | Usuwa skojarzenie usługi |
> | Akcja | Microsoft.Network/networkInterfaces/serviceAssociations/read | Pobiera definicję skojarzenia usługi |
> | Akcja | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | Weryfikuje skojarzenie usługi |
> | Akcja | Microsoft.Network/networkInterfaces/serviceAssociations/write | Powoduje utworzenie nowego skojarzenia usługi lub modyfikuje istniejące skojarzenie usługi |
> | Akcja | Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Akcja | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Pobiera domyślną definicję reguły zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/delete | Usuwa sieciową grupę zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń |
> | Akcja | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne sieciowych grup zabezpieczeń |
> | Akcja | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyki sieciowych grup zabezpieczeń. Ta operacja jest uzupełniana przez dostawcę zasobów usługi Insights. |
> | Akcja | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Pobiera zdarzenia dla sieciowej grupy zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/read | Pobiera definicję grupy zabezpieczeń sieci |
> | Akcja | Microsoft.Network/networkSecurityGroups/securityRules/delete | Usuwa regułę zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/securityRules/read | Pobiera definicję reguły zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/securityRules/write | Tworzy regułę zabezpieczeń lub aktualizuje istniejącą regułę zabezpieczeń |
> | Akcja | Microsoft.Network/networkSecurityGroups/write | Tworzy sieciową grupę zabezpieczeń lub aktualizuje istniejącą sieciową grupę zabezpieczeń |
> | Akcja | Microsoft.Network/networkWatchers/availableProvidersList/action | Zwraca wszystkie dostępne internet dostawcy usług dla określonego regionu platformy Azure. |
> | Akcja | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Zwraca wynik względną opóźnienia Internetu dostawcy usług z określonej lokalizacji do regiony platformy Azure. |
> | Akcja | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfiguruje przepływu rejestrowanie dla zasobu docelowego. |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/delete | Usuwa Monitor połączenia |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienia diagnostyki monitora połączenia |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyki Monitor połączenia |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla połączenia monitora |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Monitorowanie łączności między punktami końcowymi określonego zapytania |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/read | Uzyskiwanie szczegółowych informacji Monitor połączenia |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Rozpocznij monitorowanie łączność między określonym punkty końcowe |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Monitorowanie łączności między punktami końcowymi określonego Stop/Wstrzymaj |
> | Akcja | Microsoft.Network/networkWatchers/connectionMonitors/write | Tworzy Monitor połączenia |
> | Akcja | Microsoft.Network/networkWatchers/connectivityCheck/action | Sprawdza, czy możliwość nawiązywania bezpośredniego połączenia TCP z maszyny wirtualnej do danego punktu końcowego, łącznie z innej maszyny Wirtualnej lub dowolnego serwera zdalnego. |
> | Akcja | Microsoft.Network/networkWatchers/delete | Usuwa obserwatora sieciowego |
> | Akcja | Microsoft.Network/networkWatchers/ipFlowVerify/action | Zwraca czy pakiet jest dozwolony lub odmowa dostępu do lub z danego przeznaczenia. |
> | Akcja | Microsoft.Network/networkWatchers/lenses/delete | Usuwa obiektyw |
> | Akcja | Microsoft.Network/networkWatchers/lenses/query/action | Monitorowanie ruchu w sieci w określonym punkcie końcowym zapytania |
> | Akcja | Microsoft.Network/networkWatchers/lenses/read | Uzyskiwanie szczegółowych informacji obiektyw |
> | Akcja | Microsoft.Network/networkWatchers/lenses/start/action | Rozpocznij monitorowanie ruchu sieciowego na określony punkt końcowy |
> | Akcja | Microsoft.Network/networkWatchers/lenses/stop/action | Monitorowanie ruchu w sieci w określonym punkcie końcowym Stop/Wstrzymaj |
> | Akcja | Microsoft.Network/networkWatchers/lenses/write | Tworzy obiektyw |
> | Akcja | Microsoft.Network/networkWatchers/nextHop/action | Określony obiekt docelowy oraz docelowy adres IP zwracany typ następnego przeskoku i dalej nadzieję, że adres IP. |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/delete | Usuwa przechwytywania pakietów |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Pobiera informacje o właściwościach i stan zasobu przechwytywania pakietów. |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/read | Pobierz definicję przechwytywania pakietów |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Zatrzymaj uruchomiona sesja przechwytywania pakietów. |
> | Akcja | Microsoft.Network/networkWatchers/packetCaptures/write | Tworzy przechwytywania pakietów |
> | Akcja | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Pobiera stan przepływu rejestrowania w zasobie. |
> | Akcja | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Pobiera wynik rozwiązywania problemów z poprzednio uruchomionymi lub aktualnie uruchomiona operacja rozwiązywania problemów. |
> | Akcja | Microsoft.Network/networkWatchers/read | Pobierz definicję obserwatora sieciowego |
> | Akcja | Microsoft.Network/networkWatchers/securityGroupView/action | Wyświetl reguły grupy zabezpieczeń sieci skonfigurowane i skuteczne, stosowane na maszynie Wirtualnej. |
> | Akcja | Microsoft.Network/networkWatchers/topology/action | Pobiera widok poziomu sieci, zasobów i ich relacje w grupie zasobów. |
> | Akcja | Microsoft.Network/networkWatchers/troubleshoot/action | Uruchamia Rozwiązywanie problemów w zasobie sieci na platformie Azure. |
> | Akcja | Microsoft.Network/networkWatchers/write | Tworzy obserwatora sieciowego lub aktualizuje istniejącą obserwatora sieciowego |
> | Akcja | Microsoft.Network/operations/read | Pobiera dostępne operacje |
> | Akcja | Microsoft.Network/publicIPAddresses/delete | Usuwa publiczny adres Ip. |
> | Akcja | Microsoft.Network/publicIPAddresses/join/action | Dołącza do publicznego adresu ip |
> | Akcja | Microsoft.Network/publicIPAddresses/loadBalancerPools/delete | Usuwa publiczny puli zaplecza modułu równoważenia obciążenia adresu IP |
> | Akcja | Microsoft.Network/publicIPAddresses/loadBalancerPools/join/action | Tworzy sprzężenie puli zaplecza modułu równoważenia obciążenia w usłudze publicznego adresu IP |
> | Akcja | Microsoft.Network/publicIPAddresses/loadBalancerPools/read | Pobiera publiczny adres IP definicję modułu równoważenia obciążenia zaplecza puli |
> | Akcja | Microsoft.Network/publicIPAddresses/loadBalancerPools/write | Tworzy puli zaplecza modułu równoważenia obciążenia w usłudze publiczny adres IP lub aktualizuje istniejący publiczny adres IP puli modułu równoważenia obciążenia wewnętrznej bazy danych |
> | Akcja | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienia diagnostyki publicznego adresu IP |
> | Akcja | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | Utwórz lub zaktualizuj ustawienia diagnostyki publicznego adresu IP |
> | Akcja | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | Pobierz definicje dziennika publicznego adresu IP |
> | Akcja | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | Pobierz definicje metryk publicznego adresu IP |
> | Akcja | Microsoft.Network/publicIPAddresses/read | Pobiera definicję adres publiczny adres ip. |
> | Akcja | Microsoft.Network/publicIPAddresses/write | Tworzy publiczny adres Ip lub aktualizuje istniejący publiczny adres Ip.  |
> | Akcja | Microsoft.Network/register/action | Rejestruje subskrypcję |
> | Akcja | Microsoft.Network/routeFilters/delete | Usuwa definicję filtru tras |
> | Akcja | Microsoft.Network/routeFilters/join/action | Dołącza filtr tras |
> | Akcja | Microsoft.Network/routeFilters/read | Pobiera definicję filtru tras |
> | Akcja | Microsoft.Network/routeFilters/routeFilterRules/delete | Usuwa definicję reguły filtru trasy |
> | Akcja | Microsoft.Network/routeFilters/routeFilterRules/read | Pobiera definicję reguły filtru trasy |
> | Akcja | Microsoft.Network/routeFilters/routeFilterRules/write | Tworzy regułę filtru tras lub aktualizuje istniejącą regułę filtru trasy |
> | Akcja | Microsoft.Network/routeFilters/write | Tworzy filtr tras lub aktualizuje istniejący filtr tras |
> | Akcja | Microsoft.Network/routeTables/delete | Usuwa definicję tabeli tras |
> | Akcja | Microsoft.Network/routeTables/join/action | Tworzy sprzężenie tabeli tras |
> | Akcja | Microsoft.Network/routeTables/read | Pobiera definicję tabeli tras |
> | Akcja | Microsoft.Network/routeTables/routes/delete | Usuwa definicję trasy |
> | Akcja | Microsoft.Network/routeTables/routes/read | Pobiera definicję trasy |
> | Akcja | Microsoft.Network/routeTables/routes/write | Tworzy trasę lub aktualizuje istniejącą trasę |
> | Akcja | Microsoft.Network/routeTables/write | Tworzy tabelę tras lub aktualizuje istniejącą tabelę tras |
> | Akcja | Microsoft.Network/securegateways/applicationRuleCollections/delete | Usuwa kolekcji reguł aplikacji bezpiecznego bramy |
> | Akcja | Microsoft.Network/securegateways/applicationRuleCollections/read | Pobierania kolekcję reguł aplikacji dla danej bramy Secure |
> | Akcja | Microsoft.Network/securegateways/applicationRuleCollections/write | Tworzy lub aktualizuje kolekcję reguł aplikacji bezpiecznego bramy |
> | Akcja | Microsoft.Network/securegateways/delete | Usuń bramę bezpieczne |
> | Akcja | Microsoft.Network/securegateways/networkRuleCollections/delete | Usuwa kolekcji reguł sieciowej bezpiecznej bramy |
> | Akcja | Microsoft.Network/securegateways/networkRuleCollections/read | Pobierania kolekcję reguł sieci dla danego bramy Secure |
> | Akcja | Microsoft.Network/securegateways/networkRuleCollections/write | Tworzy lub aktualizuje kolekcję reguł sieciowej Secure bramy |
> | Akcja | Microsoft.Network/securegateways/read | Pobierz bezpieczny bramy |
> | Akcja | Microsoft.Network/securegateways/write | Tworzy lub aktualizuje Secure bramy |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/delete | Usuwa zasady punktu końcowego usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/join/action | Tworzy sprzężenie zasad punktu końcowego usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Tworzy sprzężenie podsieci zasad punktu końcowego usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/read | Pobiera opis zasad punktu końcowego usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Usuwa definicję zasad punktu końcowego usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Pobiera wywieranych usługi punktu końcowego zasady definicji przez |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Tworzy zasady definicji punktu końcowego usługi lub aktualizuje istniejącą definicję zasad punktu końcowego usługi |
> | Akcja | Microsoft.Network/serviceEndpointPolicies/write | Tworzy zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi |
> | Akcja | Microsoft.Network/trafficManagerGeographicHierarchies/read | Pobiera zawierający regionów, które mogą być używane z metody routingu ruchu geograficznego hierarchii Geographic Menedżera ruchu |
> | Akcja | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Usuwa punktu końcowego platformy Azure z istniejącego profilu Menedżera ruchu. Menedżer ruchu przestanie routingu ruchu usunięto punkt końcowy usługi Azure. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Pobiera punktu końcowego platformy Azure, która należy do profilu Menedżera ruchu, w tym wszystkie właściwości tego punktu końcowego platformy Azure. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Dodaj nowy punkt końcowy Azure w istniejącego profilu Menedżera ruchu lub zaktualizować właściwości istniejącego punktu końcowego platformy Azure, w tym profilu usługi Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/delete | Usuń profil Menedżera ruchu. Wszystkie ustawienia skojarzone z profilem Menedżera ruchu zostaną utracone i profilu nie może służyć do kierowania ruchem. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Usuwa zewnętrznego punktu końcowego z istniejącego profilu Menedżera ruchu. Menedżer ruchu przestanie routingu ruchu do usuniętego zewnętrznego punktu końcowego. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Pobiera zewnętrznego punktu końcowego, który należy do profilu usługi Traffic Manager, w tym wszystkie właściwości tego zewnętrznego punktu końcowego. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Dodaj nowe zewnętrznego punktu końcowego w istniejącego profilu Menedżera ruchu lub zaktualizować właściwości istniejącego zewnętrznego punktu końcowego, w tym profilu usługi Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Pobiera Mapa cieplna Menedżera ruchu dla danego profilu usługi Traffic Manager zawierający dane liczby i opóźnienia zapytania według lokalizacji i źródła adresów IP. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Usuwa zagnieżdżone punkt końcowy z istniejącego profilu Menedżera ruchu. Menedżer ruchu przestanie routingu ruchu usunięto punkt końcowy zagnieżdżone. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Pobiera zagnieżdżone punktu końcowego, który należy do profilu usługi Traffic Manager, w tym wszystkie właściwości tego punktu końcowego zagnieżdżone. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Dodaj nowy punkt końcowy zagnieżdżone w istniejącego profilu Menedżera ruchu lub zaktualizować właściwości istniejący zagnieżdżone punkt końcowy w tym profilu usługi Traffic Manager. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyki Menedżera ruchu |
> | Akcja | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyki Menedżera ruchu, ta operacja jest uzupełniana przez dostawcę zasobów szczegółowych informacji. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | Pobiera zdarzenia dla Menedżera ruchu |
> | Akcja | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla Menedżera ruchu. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/read | Pobierz konfigurację profilu Menedżera ruchu. W tym ustawienia DNS, ustawienia kierowania ruchu, ustawienia monitorowania punktu końcowego i listę punktów końcowych kierowanych przez ten profil Menedżera ruchu. |
> | Akcja | Microsoft.Network/trafficManagerProfiles/write | Tworzenie profilu Menedżera ruchu lub zmodyfikować konfigurację istniejącego profilu Menedżera ruchu.<br>Dotyczy to również włączenia lub wyłączenia profilu i modyfikowanie ustawień DNS, ustawienia kierowania ruchu lub ustawień monitorowania dla punktu końcowego.<br>Punkty końcowe kierowane przez profil Menedżera ruchu można dodać, usunąć, włączone lub wyłączone. |
> | Akcja | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Usuwa używane do zbierania metryk użytkownika w czasie rzeczywistym klucza na poziomie subskrypcji. |
> | Akcja | Microsoft.Network/trafficManagerUserMetricsKeys/read | Pobiera klucz poziom subskrypcji używane do zbierania metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft.Network/trafficManagerUserMetricsKeys/write | Tworzy nowy klucz poziom subskrypcji do użycia dla kolekcji metryk użytkownika w czasie rzeczywistym. |
> | Akcja | Microsoft.Network/unregister/action | Wyrejestrowuje subskrypcji |
> | Akcja | Microsoft.Network/virtualHubs/delete | Usuwa koncentratora wirtualnego |
> | Akcja | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Usuwa HubVirtualNetworkConnection |
> | Akcja | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | Akcja | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Utwórz lub zaktualizuj HubVirtualNetworkConnection |
> | Akcja | Microsoft.Network/virtualHubs/read | Pobierz koncentratora wirtualnego |
> | Akcja | Microsoft.Network/virtualHubs/write | Utwórz lub zaktualizuj Centrum wirtualnego |
> | Akcja | Microsoft.Network/virtualnetworkgateways/Connections/Read | Get VirtualNetworkGatewayConnection |
> | Akcja | Microsoft.Network/virtualNetworkGateways/delete | Usuwa element virtualNetworkGateway |
> | Akcja | Microsoft.Network/virtualnetworkgateways/generatevpnclientpackage/Action | Generowanie pakietu klienta VPN dla virtualNetworkGateway |
> | Akcja | Microsoft.Network/virtualnetworkgateways/generatevpnprofile/Action | Generowanie pakietu VpnProfile VirtualNetworkGateway |
> | Akcja | Microsoft.Network/virtualnetworkgateways/getadvertisedroutes/Action | Pobiera virtualNetworkGateway anonsowane tras |
> | Akcja | Microsoft.Network/virtualnetworkgateways/getbgppeerstatus/Action | Pobiera stan elementu równorzędnego protokołu bgp virtualNetworkGateway |
> | Akcja | Microsoft.Network/virtualnetworkgateways/getlearnedroutes/Action | Pobiera trasy virtualnetworkgateway rozpoznane |
> | Akcja | Microsoft.Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Pobierz klienta VPN Ipsec parametrów VirtualNetworkGateway P2S klienta. |
> | Akcja | Microsoft.Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Pobiera adres URL profilu pakiet klienta vpn wstępnie wygenerowane |
> | Akcja | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyki bramy sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyki bramy sieci wirtualnej, ta operacja jest uzupełniana przez dostawcę zasobów szczegółowych informacji. |
> | Akcja | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Pobiera zdarzenia bramy sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla bramy sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworkGateways/read | Pobiera element VirtualNetworkGateway |
> | Akcja | Microsoft.Network/virtualnetworkgateways/reset/Action | Resetuje element virtualNetworkGateway |
> | Akcja | Microsoft.Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Ustaw parametry klienta VirtualNetworkGateway P2S Ipsec klienta VPN. |
> | Akcja | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Przedstawia obsługiwane urządzenia sieci Vpn |
> | Akcja | Microsoft.Network/virtualNetworkGateways/write | Tworzy lub aktualizuje element VirtualNetworkGateway |
> | Akcja | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Sprawdź, czy adres Ip jest dostępna w określonej sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/customViews/get/action | Pobieranie zawartości widok niestandardowy sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/customViews/read | Pobierz definicję widoku niestandardowego sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/delete | Usuwa sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/peer/action | Równorzędnymi użytkownikami sieci wirtualnej z inną siecią wirtualną |
> | Akcja | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz ustawienia diagnostyki sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | Utwórz lub zaktualizuj ustawienia diagnostyki sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | Pobierz definicje dziennika sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Usuwa serwer proxy sieci wirtualnej komunikacji równorzędnej |
> | Akcja | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Pobiera sieci wirtualnej komunikacji równorzędnej definicji serwera proxy |
> | Akcja | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Tworzy serwer proxy komunikacji równorzędnej sieci wirtualnej lub aktualizuje istniejące sieci wirtualnej komunikacji równorzędnej serwera proxy |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/delete | Usuwa podsieć sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/join/action | Tworzy sprzężenie sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Tworzy sprzężenie zasobów, takich jak konta magazynu lub bazy danych SQL do podsieci. |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | Usuwa zasób łącza nawigacji |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | Pobierz definicję zasobu łącza nawigacji |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | Tworzy łącze nawigacji zasobu lub aktualizuje istniejące łącze nawigacji zasobu |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | Usuwa Link skojarzenia usługi |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | Pobiera definicję szczegółów łącza skojarzenia usługi |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | Pobiera definicję łącza skojarzenia usługi |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | Weryfikuje łącza skojarzenia usługi |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | Tworzy łącze skojarzenia usługi lub aktualizuje istniejące łącze skojarzenia usługi |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w podsieci sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/subnets/write | Tworzy podsieć sieci wirtualnej lub aktualizuje istniejącą podsieć sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | Usuwa konsumenta oznakowanych ruchu |
> | Akcja | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | Pobierz definicję oznakowane konsumenta ruchu |
> | Akcja | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | Weryfikuje konsumenta oznakowanych ruchu |
> | Akcja | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | Tworzy konsumenta ruchu oznakowane lub aktualizuje istniejące konsumenta ruchu oznakowane |
> | Akcja | Microsoft.Network/virtualNetworks/usages/read | Pobierz użycia adresu IP dla każdej podsieci sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualMachines/read | Pobiera odwołania do wszystkich maszyn wirtualnych w sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Usuwa element równorzędny sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Pobiera definicję sieci wirtualnej komunikacji równorzędnej |
> | Akcja | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Tworzy element równorzędny sieci wirtualnej lub aktualizuje istniejące sieci wirtualnej komunikacji równorzędnej |
> | Akcja | Microsoft.Network/virtualNetworks/write | Tworzy sieć wirtualną lub aktualizuje istniejącą sieć wirtualną |
> | Akcja | Microsoft.Network/virtualNetworkTaps/delete | Usuń naciśnij sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworkTaps/join/action | Tworzy sprzężenie naciśnij sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworkTaps/read | Pobierz naciśnij sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualNetworkTaps/write | Utwórz lub zaktualizuj naciśnij sieci wirtualnej |
> | Akcja | Microsoft.Network/virtualWans/delete | Usuwa wirtualnych sieci Wan |
> | Akcja | Microsoft.Network/virtualWans/read | Get a wirtualnych sieci Wan |
> | Akcja | Microsoft.Network/virtualWans/virtualHubProxies/delete | Usuwa serwer proxy koncentratora wirtualnego |
> | Akcja | Microsoft.Network/virtualWans/virtualHubProxies/read | Pobiera definicję serwera proxy koncentratora wirtualnego |
> | Akcja | Microsoft.Network/virtualWans/virtualHubProxies/write | Tworzy serwer proxy koncentratora wirtualnej lub aktualizuje serwer proxy koncentratora wirtualnego |
> | Akcja | Microsoft.Network/virtualWans/virtualHubs/read | Pobiera wszystkie wirtualnego koncentratory, które odwołują się do wirtualnego Wan. |
> | Akcja | Microsoft.Network/virtualwans/vpnconfiguration/action | Pobiera konfigurację sieci Vpn |
> | Akcja | Microsoft.Network/virtualWans/vpnSiteProxies/delete | Usuwa serwer proxy w lokacji sieci Vpn |
> | Akcja | Microsoft.Network/virtualWans/vpnSiteProxies/read | Pobiera definicję serwera proxy w lokacji sieci Vpn |
> | Akcja | Microsoft.Network/virtualWans/vpnSiteProxies/write | Tworzy serwer proxy w lokacji sieci Vpn lub aktualizuje proxy witryny sieci Vpn |
> | Akcja | Microsoft.Network/virtualWans/vpnSites/read | Pobiera wszystkie lokacje sieci VPN, które odwołują się do wirtualnego Wan. |
> | Akcja | Microsoft.Network/virtualWans/write | Utwórz lub zaktualizuj wirtualnego Wan |
> | Akcja | Microsoft.Network/vpnGateways/read | Pobiera element VpnGateway. |
> | Akcja | microsoft.network/vpnGateways/vpnConnections/read | Pobiera VpnConnection. |
> | Akcja | microsoft.network/vpnGateways/vpnConnections/write | Umieszcza VpnConnection. |
> | Akcja | Microsoft.Network/vpnGateways/write | Umieszcza element VpnGateway. |
> | Akcja | Microsoft.Network/vpnsites/delete | Usuwa zasób lokacji sieci Vpn. |
> | Akcja | Microsoft.Network/vpnsites/read | Pobiera zasób lokacji sieci Vpn. |
> | Akcja | Microsoft.Network/vpnsites/write | Tworzy lub aktualizuje zasób lokacji sieci Vpn. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Sprawdza, czy dana nazwa zasobu przestrzeni nazw jest dostępna w usłudze NotificationHub. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć reguły autoryzacji Namespace domyślnej.  |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia z przestrzenią nazw |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Reguła autoryzacji przestrzeni nazw: wygeneruj ponownie klucz podstawowy/pomocniczy. Określ klucz do ponownego wygenerowania |
> | Akcja | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Tworzenie reguł autoryzacji z poziomu Namespace i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Sprawdza, czy dana nazwa usługi NotificationHub jest dostępna w przestrzeni nazw. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Usuń reguły autoryzacji centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Pobierz parametry połączenia z centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Pobierz listę reguł autoryzacji centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Reguła autoryzacji centrum powiadomień: wygeneruj ponownie klucz główny/pomocniczy. Określ klucz do ponownego wygenerowania |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Tworzenie reguł autoryzacji Centrum powiadomień i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Wyślij testowe powiadomienie wypychane. |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Usuń zasób centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Pobierz listę metryki Namespace opisów zasobów |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Pobierz wszystkie poświadczenia systemu powiadomień platformy Centrum powiadomień. Obejmuje to poświadczenia usługi WNS, MPNS, APNS, usługi GCM i Baidu |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Pobierz listę opisów zasobów centrum powiadomień |
> | Akcja | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Tworzenie Centrum powiadomień i aktualizowanie jej właściwości. Jego właściwości obejmują głównie poświadczeń systemu powiadomień platformy. Reguły autoryzacji i czas wygaśnięcia |
> | Akcja | Microsoft.NotificationHubs/Namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft.NotificationHubs/Namespaces/write | Utwórz zasób Namespace i zaktualizuj jego właściwości. Znaczniki i pojemności Namespace są właściwości, które mogą być aktualizowane. |
> | Akcja | Microsoft.NotificationHubs/register/action | Rejestruje subskrypcję dostawcy zasobów usługi NotifciationHubs i włącza funkcję tworzenia przestrzeni nazw i centrów NotificationHub |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.OperationalInsights/linkTargets/read | Wyświetla listę istniejących kont, które nie są skojarzone z subskrypcją platformy Azure. Aby połączyć tej subskrypcji platformy Azure do obszaru roboczego, użyj identyfikator klienta zwracane przez tę operację we właściwości klienta identyfikator operacji Utwórz obszar roboczy. |
> | Akcja | Microsoft.OperationalInsights/register/action | Zarejestruj subskrypcję dostawcy zasobów. |
> | Akcja | Microsoft.OperationalInsights/workspaces/analytics/query/action | Wyszukiwanie przy użyciu nowy aparat. |
> | Akcja | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Pobieranie schematu wyszukiwania V2. |
> | Akcja | Microsoft.OperationalInsights/workspaces/api/query/action | Wyszukiwanie przy użyciu nowy aparat. |
> | Akcja | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Pobieranie schematu wyszukiwania V2. |
> | Akcja | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Usuwanie konfiguracji zakresu |
> | Akcja | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Pobierz konfigurację zakresu |
> | Akcja | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Ustawianie zakresu konfiguracji |
> | Akcja | Microsoft.OperationalInsights/workspaces/datasources/delete | Usuwanie źródeł danych w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/datasources/read | Pobierz źródeł danych w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/datasources/write | Utwórz/Aktualizuj źródeł danych w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/delete | Usuwa obszaru roboczego. Jeśli obszar roboczy został połączony z istniejącym obszarem roboczym w czasie tworzenia obszaru roboczego, który był połączony z nie zostanie usunięta. |
> | Akcja | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Generuje certyfikatu rejestracji dla obszaru roboczego. Ten certyfikat służy do nawiązania połączenia obszaru roboczego programu Microsoft System Center Operations Manager. |
> | Akcja | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Wyłącza pakietu intelligence pack dla danego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Włącza pakiet analizy dla danego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Wyświetla listę wszystkich pakietów analizy, które są widoczne dla danego worksapce, a także wyświetla informację, czy pakiet jest włączone lub wyłączone dla tego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Usuń połączone usługi na podstawie danego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/linkedServices/read | Pobierz połączonej usługi w obszarze roboczym podane. |
> | Akcja | Microsoft.OperationalInsights/workspaces/linkedServices/write | Utwórz/zaktualizuj połączone usługi danego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/listKeys/action | Pobiera listę kluczy dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/listKeys/read | Pobiera listę kluczy dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/managementGroups/read | Pobiera nazwy i metadanych dla grupy zarządzania programu System Center Operations Manager podłączone do tego obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Pobierz definicje metryki w obszarze roboczym |
> | Akcja | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Usuń użytkownika ustawienia powiadomień w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Pobierz ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Określ ustawienia powiadomień użytkownika dla obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/purge/action | Usuń określone dane z obszaru roboczego |
> | Akcja | Microsoft.OperationalInsights/workspaces/read | Pobiera istniejący obszar roboczy |
> | Akcja | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Usuwa kwerendę zapisanego wyszukiwania |
> | Akcja | Microsoft.OperationalInsights/workspaces/savedSearches/read | Pobiera zapytanie zapisanego wyszukiwania |
> | Akcja | Microsoft.OperationalInsights/workspaces/savedSearches/write | Tworzy zapytanie zapisanego wyszukiwania |
> | Akcja | Microsoft.OperationalInsights/workspaces/schema/read | Pobiera schematu wyszukiwania dla obszaru roboczego.  Schematu wyszukiwania zawiera narażonych pól i ich typów. |
> | Akcja | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Akcja | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Pobiera klucze udostępnionego dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Pobiera klucze udostępnionego dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego. |
> | Akcja | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Usuwa konfiguracji magazynu. Przestanie Microsoft Operational Insights odczytywanie danych z konta magazynu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Pobiera konfiguracji magazynu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Tworzy nową konfigurację magazynu. Te konfiguracje są używane do pobierania danych z lokalizacji w istniejącego konta magazynu. |
> | Akcja | Microsoft.OperationalInsights/workspaces/usages/read | Pobiera dane użycia dla obszaru roboczego, w tym ilość danych do odczytu w obszarze roboczym. |
> | Akcja | Microsoft.OperationalInsights/workspaces/write | Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącym obszarem roboczym. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.OperationsManagement/managementAssociations/delete | Usuń istniejące skojarzenie zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementAssociations/read | Pobierz istniejące skojarzenie zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementAssociations/write | Utwórz nowe skojarzenie zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementConfigurations/delete | Usuń istniejące Configuratin zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementConfigurations/read | Pobierz istniejące konfiguracji zarządzania |
> | Akcja | Microsoft.OperationsManagement/managementConfigurations/write | Utwórz nową konfigurację zarządzania |
> | Akcja | Microsoft.OperationsManagement/register/action | Zarejestruj subskrypcję dostawcy zasobów. |
> | Akcja | Microsoft.OperationsManagement/solutions/delete | Usuń istniejące rozwiązanie OMS |
> | Akcja | Microsoft.OperationsManagement/solutions/read | Pobierz Kończenie rozwiązania OMS |
> | Akcja | Microsoft.OperationsManagement/solutions/write | Utwórz nowe rozwiązanie OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.PolicyInsights/policyEvents/queryResults/action | Wyślij zapytanie do informacji o zdarzeniach zasad. |
> | Akcja | Microsoft.PolicyInsights/policyStates/queryResults/action | Wyślij zapytanie do informacji na temat zasad stanów. |
> | Akcja | Microsoft.PolicyInsights/policyStates/summarize/action | Zapytanie informacje podsumowania stanów najnowsze zasady. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Portal/dashboards/delete | Usuwa pulpit nawigacyjny z subskrypcji. |
> | Akcja | Microsoft.Portal/dashboards/read | Odczytuje pulpity nawigacyjne z subskrypcji. |
> | Akcja | Microsoft.Portal/dashboards/write | Dodaj pulpit nawigacyjny do subskrypcji lub zmodyfikuj go. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Sprawdza, czy podany Power BI w wersji dedykowanej pojemności nazwa jest prawidłowa, a nie korzystać. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/delete | Usuwa usługi Power BI w wersji dedykowanej pojemności. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki o pojemności maksymalnej Power BI w wersji dedykowanej |
> | Akcja | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki Power BI w wersji dedykowanej pojemności. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/read | Pobiera informacje o pojemności określonego Power BI w wersji dedykowanej. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/resume/action | Wznawia pojemność. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/skus/read | Pobrać dostępne jednostki SKU informacji o wydajności |
> | Akcja | Microsoft.PowerBIDedicated/capacities/suspend/action | Wstrzymuje pojemność. |
> | Akcja | Microsoft.PowerBIDedicated/capacities/write | Tworzy lub aktualizuje określony Power BI w wersji dedykowanej pojemność. |
> | Akcja | Microsoft.PowerBIDedicated/locations/operationresults/read | Pobiera informacje o wyniku określonej operacji. |
> | Akcja | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Pobiera informacje o stanie określonej operacji. |
> | Akcja | Microsoft.PowerBIDedicated/operations/read | Pobiera informacje o operacji |
> | Akcja | Microsoft.PowerBIDedicated/register/action | Rejestruje dostawcę zasobów Power BI w wersji dedykowanej. |
> | Akcja | Microsoft.PowerBIDedicated/skus/read | Pobiera informacje jednostki SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | Akcja | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest używane przez usługę operacji wewnętrznych |
> | Akcja | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Akcja | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej dla Magazyny usług odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Sprawdź poprawność funkcji |
> | Akcja | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Akcja | Microsoft.RecoveryServices/register/action | Rejestruje subskrypcji danego dostawcy zasobów |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupconfig/read | Zwraca konfigurację Recovery usług magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupconfig/write | Konfigurowanie aktualizacji odzyskiwania usług magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Tworzenie kopii zapasowej opcje ochrony |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery chronione |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Usuwa kontener zarejestrowanych |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Wykonaj zapytanie dla obciążeń w kontenerze |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Wyświetlanie wszystkich elementów w kontenerze |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Wykonuje kopię zapasową elementu chronionego. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Usuwa chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu elemencie chroniony |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Odzyskiwanie elementów błyskawicznych udostępniania dla chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Przywróć punkty odzyskiwania dla elementów chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odwołanie elementu błyskawicznych odzyskiwania dla chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Tworzenie kopii zapasowej elementu chronione |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkich zarejestrowanych kontenerów |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Tworzy kontener zarejestrowanych |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenera |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Anulowanie zadania |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zadania. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadania |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportowanie zadań |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Zwraca wynik operacji eksportowania zadania. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Zwraca metadane zarządzania kopiami zapasowymi magazynu usług Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Zwraca wynik operacji tworzenia kopii zapasowej magazynu usług Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca operacji tworzenia kopii zapasowej odzyskiwania stanu usługi magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Usuń zasady ochrony |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Tworzy zasady ochrony |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Zwraca listę wszystkich elementów podlegających ochronie |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należących do subskrypcji |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Zwraca zabezpieczający numer PIN informacji odzyskiwania usług magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Zwraca konfigurację magazynu dla odzyskiwania usług magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Aktualizacje konfiguracji magazynu dla odzyskiwania usług magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dla chronionych serwerów i chronione elementy usług odzyskiwania. |
> | Akcja | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja aktualizacji zasobów certyfikatu aktualizuje certyfikat poświadczeń zasobów/magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/delete | Operacja usuwania magazynu usuwa określony zasób Azure typu "magazynu" |
> | Akcja | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alertów dla magazynu usług odzyskiwania. |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Usuwa alert. |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Pobiera konfigurację powiadomień magazynu usług odzyskiwania. |
> | Akcja | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfiguruje powiadomienia pocztą e-mail do magazynu usług odzyskiwania. |
> | Akcja | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Diagnostyka kopia zapasowa Azure |
> | Akcja | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Diagnostyka kopia zapasowa Azure |
> | Akcja | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Dzienniki kopii zapasowych Azure |
> | Akcja | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Metryki kopii zapasowej systemu Azure |
> | Akcja | Microsoft.RecoveryServices/Vaults/read | Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu" |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Operacja wyrejestrować kontenera można wyrejestrować kontenera. |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Wyniki operacji Get operacji można używać Pobierz stan operacji i wynik operacji asynchronicznie przesłane |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Kontenery pobrać operacji można używać Pobierz kontenery zarejestrowanych dla każdego zasobu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operacji zarejestrować kontenera usług można używać do rejestrowania kontener z usługą odzyskiwania. |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Wszystkie ustawienia alertów do odczytu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Utwórz lub zaktualizuj ustawienia alertów |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationEvents/read | Przeczytaj żadnych zdarzeń |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność sieci szkieletowej |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Usuń wszystkie sieci szkieletowe |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Wdróż obraz serwera przetwarzania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Żadnych sieci szkieletowych do odczytu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Ponownie Skojarz bramy |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Usunięcie sieci szkieletowej |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Odnów certyfikat sieci szkieletowej |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Przeczytaj żadnych sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Usuń wszelkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Przeczytaj wszelkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Utwórz lub zaktualizuj wszystkie mapowania sieci |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Odkryj element chronione |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Wszystkie kontenery ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Usunięcie kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Wszystkie chronione elementy do odczytu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Zastosuj punktu odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Usuń wszystkie chronione elementy |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Zatwierdzanie trybu failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowany tryb Failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Wszystkie chronione elementy do odczytu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Przeczytaj punktów odzyskiwania replikacji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Usuwanie chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Naprawa replikacji |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Włącz ponownie ochronę chronionego elementu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test pracy w trybie failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Wyczyszczenia testu pracy awaryjnej |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizacja usługi mobilności |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Utwórz lub zaktualizuj wszystkie chronione elementy. |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Usuwanie mapowań kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Przeczytaj mapowań kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Usuń mapowanie kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Utwórz lub zaktualizuj mapowań kontenera ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Kontener ochrony przełącznika |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Utwórz lub zaktualizuj kontenerach ochrony |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Usuwanie wszystkich dostawców usług odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Przeczytaj wszystkich dostawców usług odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Odśwież dostawcę |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Usuń dostawcę usług odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Utwórz lub zaktualizuj wszystkich dostawców usług odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Przeczytaj wszystkie klasyfikacje magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Usuń wszelkie mapowania klasyfikacji magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Wszelkie mapowania klasyfikacji magazynu do odczytu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Utwórz lub zaktualizuj wszystkie mapowania klasyfikacji magazynu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Usuń wszystkie zadania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Wszystkie zadania do odczytu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Utwórz lub zaktualizuj wszystkie zadania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Utwórz lub zaktualizuj żadnych sieci szkieletowych |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Anuluj zadanie |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/read | Wszystkie zadania do odczytu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Uruchom ponownie zadanie |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Wznów zadanie |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Usuń wszystkie zasady |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Wszystkie zasady do odczytu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Utwórz lub zaktualizuj wszystkie zasady |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Usuń wszystkie plany odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Zatwierdzanie trybu failover planu odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan odzyskiwania usługi planowanego trybu Failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Wszystkie plany odzyskiwania do odczytu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Włącz ponownie ochronę planu odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan odzyskiwania trybu Failover testu |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan odzyskiwania testu czyszczenia trybu Failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania trybu failover |
> | Akcja | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Utwórz lub zaktualizuj wszystkie plany odzyskiwania |
> | Akcja | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Zwraca token informacje magazynie usług odzyskiwania. |
> | Akcja | Microsoft.RecoveryServices/vaults/usages/read | Przeczytaj również użycia magazynu |
> | Akcja | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Akcja | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Magazyn Token operacji można uzyskać tokenu magazynu dla operacji poziomu wewnętrznej bazy danych magazynu. |
> | Akcja | Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Relay/checkNameAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.Relay/checkNamespaceAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailabiltiy. |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/action | Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest depricated. Można zaktualizować reguły autoryzacji Namespace zamiast tego użyj wywołania PUT. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć reguły autoryzacji Namespace domyślnej.  |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia z przestrzenią nazw |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft.Relay/namespaces/authorizationRules/write | Tworzenie reguł autoryzacji z poziomu Namespace i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze. |
> | Akcja | Microsoft.Relay/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobierz klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Usuwa konfiguracji odzyskiwania po awarii, skojarzone z przestrzenią nazw. Tę operację można wywołać tylko za pomocą głównej przestrzeni nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje tryb pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operacja aktualizowania HybridConnection. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operację można usunąć reguły autoryzacji HybridConnection |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Pobierz ciąg połączenia HybridConnection |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Pobierz listę reguł autoryzacji HybridConnection |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Tworzenie reguł autoryzacji HybridConnection i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia. |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/Delete | Operacja usunąć HybridConnection zasobu |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/read | Pobierz listę opisów zasobów HybridConnection |
> | Akcja | Microsoft.Relay/namespaces/HybridConnections/write | Tworzenie lub aktualizacja HybridConnection właściwości. |
> | Akcja | Microsoft.Relay/namespaces/messagingPlan/read | Pobiera Plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego).<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.Relay/namespaces/messagingPlan/write | Aktualizuje wiadomości Plan dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego).<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.Relay/namespaces/operationresults/read | Pobierz stan operacji przestrzeni nazw |
> | Akcja | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryki Namespace opisów zasobów |
> | Akcja | Microsoft.Relay/namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw usługi ACS |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operacja aktualizowania WcfRelay. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operację można usunąć reguły autoryzacji WcfRelay |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Pobierz ciąg połączenia WcfRelay |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Pobierz listę reguł autoryzacji WcfRelay |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Tworzenie reguł autoryzacji WcfRelay i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia. |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/Delete | Operacja usunąć WcfRelay zasobu |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/read | Pobierz listę opisów zasobów WcfRelay |
> | Akcja | Microsoft.Relay/namespaces/WcfRelays/write | Tworzenie lub aktualizacja WcfRelay właściwości. |
> | Akcja | Microsoft.Relay/namespaces/write | Utwórz zasób Namespace i zaktualizuj jego właściwości. Znaczniki i pojemności Namespace są właściwości, które mogą być aktualizowane. |
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
> | Akcja | Microsoft.ResourceHealth/register/action | Rejestruje subskrypcję na potrzeby usługi Microsoft Resource Health |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Resources/checkPolicyCompliance/action | Sprawdź stan zgodności danego zasobu wobec zasad zasobów. |
> | Akcja | Microsoft.Resources/checkResourceName/action | Sprawdź poprawność nazwy zasobu. |
> | Akcja | Microsoft.Resources/deployments/cancel/action | Anuluje wdrożenie. |
> | Akcja | Microsoft.Resources/deployments/delete | Usuwa wdrożenie. |
> | Akcja | Microsoft.Resources/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/deployments/read | Pobiera wdrożenia lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/deployments/validate/action | Weryfikuje wdrożenie. |
> | Akcja | Microsoft.Resources/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft.Resources/links/delete | Usuwa link do zasobu. |
> | Akcja | Microsoft.Resources/links/read | Pobiera linki do zasobów lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/links/write | Tworzy lub aktualizuje link do zasobu. |
> | Akcja | Microsoft.Resources/marketplace/purchase/action | Pozwala kupić zasób w portalu Marketplace. |
> | Akcja | Microsoft.Resources/providers/read | Pobiera listę dostawców. |
> | Akcja | Microsoft.Resources/resources/read | Pobiera listę zasobów zgodnie z filtrami. |
> | Akcja | Microsoft.Resources/subscriptions/locations/read | Pobiera listę obsługiwanych lokalizacji. |
> | Akcja | Microsoft.Resources/subscriptions/operationresults/read | Pobiera wyniki operacji subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/providers/read | Pobiera dostawców zasobów lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/subscriptions/read | Pobiera listę subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/delete | Usuwa grupę zasobów i wszystkie zasoby w tej grupie. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Pobiera stany operacji wdrażania lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Pobiera wdrożenia lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Przenosi zasoby z jednej grupy zasobów do innej. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Pobiera zasoby grupy zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Waliduj przeniesienie zasobów między grupami zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/resourceGroups/write | Tworzy lub aktualizuje grupę zasobów. |
> | Akcja | Microsoft.Resources/subscriptions/resources/read | Pobiera zasoby subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/delete | Usuwa tag subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/read | Pobiera tagi subskrypcji lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Usuwa wartość tagu subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Pobiera wartości tagów lub wyświetla ich listę. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Dodaje wartość tagu subskrypcji. |
> | Akcja | Microsoft.Resources/subscriptions/tagNames/write | Dodaje tag subskrypcji. |
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
> | Akcja | Microsoft.Search/operations/read | Wyświetla listę wszystkich dostępnych operacji Microsoft.Search dostawcy. |
> | Akcja | Microsoft.Search/register/action | Rejestruje subskrypcję dostawcy zasobów wyszukiwania i umożliwia tworzenie usługi wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/createQueryKey/action | Tworzy klucza zapytania. |
> | Akcja | Microsoft.Search/searchServices/delete | Usuwa usługę wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/deleteQueryKey/delete | Usuwa klucza zapytania. |
> | Akcja | Microsoft.Search/searchServices/diagnosticSettings/read | Pobiera diganostic ustawienie odczytu zasobu |
> | Akcja | Microsoft.Search/searchServices/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diganostic dla zasobu |
> | Akcja | Microsoft.Search/searchServices/listAdminKeys/action | Odczytuje kluczy administratora. |
> | Akcja | Microsoft.Search/searchServices/listQueryKeys/read | Zwraca listę kluczy zapytania interfejsu API dla danej usługi Azure Search. |
> | Akcja | Microsoft.Search/searchServices/logDefinitions/read | Pobiera dostępne dzienniki usługi wyszukiwania |
> | Akcja | Microsoft.Search/searchServices/metricDefinitions/read | Pobiera dostępne metryki dla usługi wyszukiwania |
> | Akcja | Microsoft.Search/searchServices/read | Odczytuje usługi wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/regenerateAdminKey/action | Generuje klucz administratora. |
> | Akcja | Microsoft.Search/searchServices/start/action | Uruchamia usługę wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/stop/action | Zatrzymuje usługę wyszukiwania. |
> | Akcja | Microsoft.Search/searchServices/write | Tworzy lub aktualizuje usługę wyszukiwania. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Security/alerts/read | Pobiera wszystkie alerty zabezpieczeń dostępne |
> | Akcja | Microsoft.Security/applicationWhitelistings/read | Pobiera whitelistings aplikacji |
> | Akcja | Microsoft.Security/applicationWhitelistings/write | Tworzy nowy listę dozwolonych aplikacji podobnej lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Security/complianceResults/read | Pobiera wyniki oceny zgodności dla zasobu |
> | Akcja | Microsoft.Security/locations/alerts/activate/action | Aktywuj alert zabezpieczeń |
> | Akcja | Microsoft.Security/locations/alerts/dismiss/action | Odrzuć alert zabezpieczeń |
> | Akcja | Microsoft.Security/locations/alerts/read | Pobiera wszystkie alerty zabezpieczeń dostępne |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicjuje zasady dostępu do sieci w czasie |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Pobiera zasady dostępu do sieci w czasie |
> | Akcja | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Tworzy nowe zasady dostępu do sieci w czasie, lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Security/locations/read | Pobiera lokalizację danych zabezpieczeń |
> | Akcja | Microsoft.Security/locations/tasks/activate/action | Aktywuj zalecana ze względów bezpieczeństwa |
> | Akcja | Microsoft.Security/locations/tasks/dismiss/action | Odrzuć zalecana ze względów bezpieczeństwa |
> | Akcja | Microsoft.Security/locations/tasks/read | Pobiera wszystkie zalecenia dotyczące zabezpieczeń dostępne |
> | Akcja | Microsoft.Security/locations/tasks/resolve/action | Rozwiąż zalecana ze względów bezpieczeństwa |
> | Akcja | Microsoft.Security/locations/tasks/start/action | Uruchom zalecana ze względów bezpieczeństwa |
> | Akcja | Microsoft.Security/policies/read | Pobiera zasady zabezpieczeń |
> | Akcja | Microsoft.Security/policies/write | Aktualizacje zasad zabezpieczeń |
> | Akcja | Microsoft.Security/pricings/delete | Usuwa cenową ustawienia dla zakresu |
> | Akcja | Microsoft.Security/pricings/read | Pobiera ustawienia cenową dla zakresu |
> | Akcja | Microsoft.Security/pricings/write | Zaktualizowanie ustawień cenową dla zakresu |
> | Akcja | Microsoft.Security/register/action | Rejestruje subskrypcję Centrum zabezpieczeń Azure |
> | Akcja | Microsoft.Security/securityContacts/delete | Usuwa kontaktu zabezpieczeń |
> | Akcja | Microsoft.Security/securityContacts/read | Pobiera kontaktu zabezpieczeń |
> | Akcja | Microsoft.Security/securityContacts/write | Aktualizacje zabezpieczeń skontaktuj się z |
> | Akcja | Microsoft.Security/securitySolutions/delete | Usuwa rozwiązania zabezpieczeń |
> | Akcja | Microsoft.Security/securitySolutions/read | Pobiera rozwiązań zabezpieczeń |
> | Akcja | Microsoft.Security/securitySolutions/write | Tworzy nowe rozwiązanie zabezpieczeń lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Security/securitySolutionsReferenceData/read | Pobiera dane referencyjne rozwiązań zabezpieczeń |
> | Akcja | Microsoft.Security/securityStatuses/read | Pobiera stany kondycji zabezpieczeń zasobów platformy Azure |
> | Akcja | Microsoft.Security/securityStatusesSummaries/read | Pobiera podsumowań stanów w zakresie zabezpieczeń |
> | Akcja | Microsoft.Security/tasks/read | Pobiera wszystkie zalecenia dotyczące zabezpieczeń dostępne |
> | Akcja | Microsoft.Security/webApplicationFirewalls/delete | Usuwa zapory aplikacji sieci web |
> | Akcja | Microsoft.Security/webApplicationFirewalls/read | Pobiera zapory aplikacji sieci web |
> | Akcja | Microsoft.Security/webApplicationFirewalls/write | Nowa Zapora aplikacji sieci web tworzy lub aktualizuje istniejący zestaw |
> | Akcja | Microsoft.Security/workspaceSettings/connect/action | Zmień ustawienia ponowne nawiązanie połączenia ustawień obszaru roboczego |
> | Akcja | Microsoft.Security/workspaceSettings/delete | Usuwa ustawienia obszaru roboczego |
> | Akcja | Microsoft.Security/workspaceSettings/read | Pobiera ustawienia obszaru roboczego |
> | Akcja | Microsoft.Security/workspaceSettings/write | Zaktualizowanie ustawień obszaru roboczego |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettings/delete | Usuwanie ustawienie diagnostyczne |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettings/read | Odczytywanie ustawienie diagnostyczne |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettings/write | Zapisywanie ustawień diagnostycznych |
> | Akcja | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Odczytywanie kategorii ustawienie diagnostyczne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.ServiceBus/checkNameAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.ServiceBus/checkNamespaceAvailability/action | Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailabiltiy. |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/action | Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest depricated. Można zaktualizować reguły autoryzacji Namespace zamiast tego użyj wywołania PUT. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Usuń regułę autoryzacji Namespace. Nie można usunąć reguły autoryzacji Namespace domyślnej.  |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Pobierz parametry połączenia z przestrzenią nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/read | Pobierz listę opisów reguł autoryzacji przestrzeni nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft.ServiceBus/namespaces/authorizationRules/write | Tworzenie reguł autoryzacji z poziomu Namespace i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze. |
> | Akcja | Microsoft.ServiceBus/namespaces/Delete | Usuń zasób przestrzeni nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Pobierz klucze reguł autoryzacji dla podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Pobierz reguły autoryzacji podstawowej przestrzeni nazw odzyskiwania po awarii |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Sprawdza dostępność aliasu przestrzeni nazw w ramach danej subskrypcji. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Usuwa konfiguracji odzyskiwania po awarii, skojarzone z przestrzenią nazw. Tę operację można wywołać tylko za pomocą głównej przestrzeni nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Wywołuje tryb pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Usuwa filtr usługi Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Pobiera filtr usługi Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Tworzy lub aktualizuje filtr usługi Event Grid skojarzony z przestrzenią nazw. |
> | Akcja | Microsoft.ServiceBus/namespaces/eventhubs/read | Pobierz listę opisów zasobów EventHub |
> | Akcja | Microsoft.ServiceBus/namespaces/messagingPlan/read | Pobiera Plan obsługi komunikatów dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego).<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.ServiceBus/namespaces/messagingPlan/write | Aktualizuje wiadomości Plan dla przestrzeni nazw.<br>Ten interfejs API jest przestarzały.<br>Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego).<br>Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. |
> | Akcja | Microsoft.ServiceBus/namespaces/migrate/action | Operacja migracji przestrzeni nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/operationresults/read | Pobierz stan operacji przestrzeni nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Pobierz listę Namespace ustawień diagnostycznych zasobu opisów |
> | Akcja | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Pobierz listę Namespace ustawień diagnostycznych zasobu opisów |
> | Akcja | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Pobierz listę dzienników Namespace opisów zasobów |
> | Akcja | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Pobierz listę metryki Namespace opisów zasobów |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operacja aktualizowania kolejki. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operację można usunąć reguły autoryzacji kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Pobrać parametry połączenia do kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Pobierz listę reguł autoryzacji kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Tworzenie reguł autoryzacji kolejki i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia. |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/Delete | Operację można usunąć kolejki zasobów |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/read | Pobierz listę opisów zasobów kolejki |
> | Akcja | Microsoft.ServiceBus/namespaces/queues/write | Utwórz lub właściwości kolejki aktualizacji. |
> | Akcja | Microsoft.ServiceBus/namespaces/read | Pobierz listę opisów zasobów przestrzeni nazw |
> | Akcja | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Usuń przestrzeń nazw usługi ACS |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operacja aktualizowania tematu. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji. |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operację można usunąć reguły autoryzacji w temacie |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Pobrać parametry połączenia do tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Pobierz listę reguł autoryzacji tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Tworzenie reguł autoryzacji tematu i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia. |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/Delete | Operację można usunąć zasobu tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/read | Pobierz listę opisów zasobów tematu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operacja usunąć TopicSubscription zasobu |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Pobierz listę opisów zasobów TopicSubscription |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operację można usunąć zasobu reguły |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Pobierz listę reguł opisów zasobów |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Tworzenie lub aktualizacja reguły właściwości. |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Tworzenie lub aktualizacja TopicSubscription właściwości. |
> | Akcja | Microsoft.ServiceBus/namespaces/topics/write | Tworzenie lub aktualizacja tematu właściwości. |
> | Akcja | Microsoft.ServiceBus/namespaces/write | Utwórz zasób Namespace i zaktualizuj jego właściwości. Znaczniki i pojemności Namespace są właściwości, które mogą być aktualizowane. |
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
> | Akcja | Microsoft.SignalRService/checknameavailability/action | Sprawdza, czy nazwa jest dostępna do użycia z nowej usługi SignalR |
> | Akcja | Microsoft.SignalRService/register/action | Rejestruje subskrypcję dostawcy zasobów "Microsoft.SignalRService" |
> | Akcja | Microsoft.SignalRService/SignalR/delete | Usuń całą SignalR |
> | Akcja | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla biblioteki SignalR |
> | Akcja | Microsoft.SignalRService/SignalR/read | Wyświetl ustawienia i konfiguracje SignalR, w portalu zarządzania lub za pośrednictwem interfejsu API |
> | Akcja | Microsoft.SignalRService/SignalR/write | Zmodyfikuj ustawienia i konfiguracje w portalu zarządzania lub za pośrednictwem interfejsu API SignalR |
> | Akcja | Microsoft.SignalRService/unregister/action | Wyrejestrowuje "Microsoft.SignalRService" dostawcy zasobów z subskrypcją |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Solutions/applicationDefinitions/delete | Usuwa definicję aplikacji. |
> | Akcja | Microsoft.Solutions/applicationDefinitions/read | Pobiera listę definicji aplikacji. |
> | Akcja | Microsoft.Solutions/applicationDefinitions/write | Dodaj lub zmodyfikuj definicję aplikacji. |
> | Akcja | Microsoft.Solutions/applications/delete | Usuwa aplikację. |
> | Akcja | Microsoft.Solutions/applications/read | Pobiera listę aplikacji. |
> | Akcja | Microsoft.Solutions/applications/write | Tworzy aplikację. |
> | Akcja | Microsoft.Solutions/locations/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | Akcja | Microsoft.Solutions/register/action | Zarezerwuj w rozwiązaniach. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Sql/checkNameAvailability/action | Sprawdź, czy nazwa danego serwera jest dostępne do alokowania na całym świecie, w ramach danej subskrypcji. |
> | Akcja | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Pobierz wynik obiektu blob serwera rozszerzonej inspekcji operacji zestawu zasad |
> | Akcja | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Pobieranie wyników inspekcji operacji zestawu zasad obiektu blob serwera |
> | Akcja | Microsoft.Sql/locations/capabilities/read | Pobiera możliwości dla tej subskrypcji w danej lokalizacji |
> | Akcja | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/locations/databaseOperationResults/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Pobiera w trakcie wykonywania operacji na serwerze o usuniętych |
> | Akcja | Microsoft.Sql/locations/deletedServerOperationResults/read | Pobiera w trakcie wykonywania operacji na serwerze o usuniętych |
> | Akcja | Microsoft.Sql/locations/deletedServers/read | Zwraca listę usuniętych serwerów lub pobiera właściwości dla określonego serwera usunięte. |
> | Akcja | Microsoft.Sql/locations/deletedServers/recover/action | Odzyskiwanie usuniętego serwera |
> | Akcja | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | Usuwa zasady sieci wirtualnej skojarzonych podsieci lub sieci wirtualnej |
> | Akcja | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Pobiera operacji asynchronicznej azure dla operacji asynchronicznej puli elastycznej |
> | Akcja | Microsoft.Sql/locations/elasticPoolOperationResults/read | Pobiera wynik operacji puli elastycznej. |
> | Akcja | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Pobierz wynik obiektu blob serwera rozszerzonej inspekcji operacji zestawu zasad |
> | Akcja | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Pobierz wynik obiektu blob serwera rozszerzonej inspekcji operacji zestawu zasad |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/delete | Usuwa istniejącą grupę trybu failover wystąpienia. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Wykonuje planowany tryb failover w istniejącej grupie trybu failover wystąpienia. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszenia pracy awaryjnej w istniejącej grupie trybu failover wystąpienia. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/read | Zwraca listę trybu failover wystąpienia grup lub pobiera właściwości dla określonego wystąpienia grupy pracy awaryjnej. |
> | Akcja | Microsoft.Sql/locations/instanceFailoverGroups/write | Tworzy wystąpienie grupę trybu failover z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego wystąpienia grupy pracy awaryjnej. |
> | Akcja | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Kończy operację przywracania zarządzanej bazie danych |
> | Akcja | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Pobiera w trakcie wykonywania operacji na zarządzanej bazie danych przezroczystego szyfrowania danych |
> | Akcja | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Pobiera w trakcie wykonywania operacji na zarządzanej bazie danych przezroczystego szyfrowania danych |
> | Akcja | Microsoft.Sql/locations/networkInterfaceAzureAsyncOperation/read | Zwraca szczegóły w konkretnym interfejsie sieciowym operacji asynchronicznej Azure |
> | Akcja | Microsoft.Sql/locations/networkInterfaceOperationResults/read | Zwraca szczegóły operacji interfejsu określonej sieci |
> | Akcja | Microsoft.Sql/locations/read | Pobiera dostępne lokalizacje w ramach danej subskrypcji |
> | Akcja | Microsoft.Sql/locations/syncAgentOperationResults/read | Pobierz wynik operacji zasobów agenta synchronizacji |
> | Akcja | Microsoft.Sql/locations/syncDatabaseIds/read | Pobierz identyfikatory bazy danych synchronizacji dla konkretnego regionu i subskrypcji |
> | Akcja | Microsoft.Sql/locations/syncGroupOperationResults/read | Pobierz wynik operacji synchronizacji grupy zasobów |
> | Akcja | Microsoft.Sql/locations/syncMemberOperationResults/read | Pobierz wynik operacji synchronizacji członek zasobów |
> | Akcja | Microsoft.Sql/locations/usages/read | Pobiera kolekcję metryki użycia dla tej subskrypcji w lokalizacji |
> | Akcja | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Zwraca szczegóły zasad sieci wirtualnej określonej operacji asynchronicznej azure  |
> | Akcja | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Zwraca szczegóły operacji reguły określonej sieci wirtualnej  |
> | Akcja | Microsoft.Sql/managedInstances/administrators/delete | Usuwa istniejącego administratora zarządzanego wystąpienia. |
> | Akcja | Microsoft.Sql/managedInstances/administrators/read | Pobiera listę zarządzanych wystąpienia administratorów. |
> | Akcja | Microsoft.Sql/managedInstances/administrators/write | Tworzy lub aktualizuje administratora zarządzanego wystąpienia z określonymi parametrami. |
> | Akcja | Microsoft.Sql/managedInstances/databases/delete | Usuwa istniejącą bazę danych zarządzanych |
> | Akcja | Microsoft.Sql/managedInstances/databases/read | Pobiera istniejące zarządzanej bazie danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Pobieranie szczegółów zasad wykrywania zagrożeń bazy danych skonfigurowanych dla danego zarządzanego bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń bazy danych dla danego zarządzanego bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/securityEvents/read | Pobiera zdarzenia zabezpieczeń zarządzanej bazie danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Pobieranie szczegółów przezroczystego szyfrowania danych bazy danych dla danego zarządzanego bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Zmień bazę danych przezroczystego szyfrowania danych dla danego zarządzanego bazy danych |
> | Akcja | Microsoft.Sql/managedInstances/databases/write | Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych. |
> | Akcja | Microsoft.Sql/managedInstances/delete | Usuwa istniejące wystąpienie zarządzanych. |
> | Akcja | Microsoft.Sql/managedInstances/metricDefinitions/read | Pobierz definicje metryki zarządzanego wystąpienia |
> | Akcja | Microsoft.Sql/managedInstances/metrics/read | Pobieraj metryki zarządzanego wystąpienia |
> | Akcja | Microsoft.Sql/managedInstances/read | Zwraca listę zarządzanych wystąpień lub pobiera właściwości dla określonego wystąpienia zarządzane. |
> | Akcja | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Pobieranie szczegółów skonfigurowanych na danym serwerze zarządzanym zasad wykrywania zagrożeń serwerów zarządzanych |
> | Akcja | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera zarządzanego dla danego serwera zarządzanego |
> | Akcja | Microsoft.Sql/managedInstances/write | Tworzy wystąpienie zarządzanego z określonymi parametrami lub zaktualizuj właściwości lub tagi dla określonego wystąpienia zarządzane. |
> | Akcja | Microsoft.Sql/operations/read | Pobiera dostępne operacje REST |
> | Akcja | Microsoft.Sql/register/action | Rejestruje subskrypcję dostawcy zasobów bazy danych Microsoft SQL i umożliwia tworzenie baz danych serwera SQL firmy Microsoft. |
> | Akcja | Microsoft.Sql/servers/administratorOperationResults/read | Pobiera w trakcie wykonywania operacji na administratorom serwerów |
> | Akcja | Microsoft.Sql/servers/administrators/delete | Usuwanie administratora serwera |
> | Akcja | Microsoft.Sql/servers/administrators/read | Pobieranie szczegółów administratora serwera |
> | Akcja | Microsoft.Sql/servers/administrators/write | Utwórz lub zaktualizuj administratora serwera |
> | Akcja | Microsoft.Sql/servers/advisors/read | Zwraca listę doradcy jest dostępna dla serwera |
> | Akcja | Microsoft.Sql/servers/advisors/recommendedActions/read | Zwraca listę zalecane działania advisor określonego serwera |
> | Akcja | Microsoft.Sql/servers/advisors/recommendedActions/write | Zastosuj akcję zalecaną na serwerze |
> | Akcja | Microsoft.Sql/servers/advisors/write | Aktualizacje automatyczne execute stanu usługi advisor na poziomie serwera. |
> | Akcja | Microsoft.Sql/servers/auditingPolicies/read | Pobieranie szczegółów tabeli serwera domyślne zasady skonfigurowane na danym serwerze inspekcji |
> | Akcja | Microsoft.Sql/servers/auditingPolicies/write | Zmień domyślny serwer tabeli inspekcji dla danego serwera |
> | Akcja | Microsoft.Sql/servers/auditingSettings/operationResults/read | Pobieranie wyników inspekcji operacji zestawu zasad obiektu blob serwera |
> | Akcja | Microsoft.Sql/servers/auditingSettings/read | Pobieranie szczegółów zasady inspekcji serwera obiektu blob skonfigurowane na danym serwerze |
> | Akcja | Microsoft.Sql/servers/auditingSettings/write | Zmienianie serwera Inspekcja obiektów blob dla danego serwera |
> | Akcja | Microsoft.Sql/servers/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania dla serwera |
> | Akcja | Microsoft.Sql/servers/automaticTuning/write | Aktualizuje ustawienia automatycznego dostrajania dla serwera i zwraca zaktualizowanymi ustawieniami |
> | Akcja | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Usuwa istniejące magazyn archiwizacji kopii zapasowych. |
> | Akcja | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Ta operacja jest używany do pobierania magazyn przechowywania długoterminowej kopii zapasowej. Zwraca informacje o magazynie zarejestrowane na tym serwerze |
> | Akcja | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ta operacja jest używana do rejestrowania przechowywania długoterminowej kopii zapasowej magazynu na serwerze |
> | Akcja | Microsoft.Sql/servers/communicationLinks/delete | Usuwa istniejące łącze komunikacji serwera. |
> | Akcja | Microsoft.Sql/servers/communicationLinks/read | Zwraca listę łączy komunikacji z określonego serwera. |
> | Akcja | Microsoft.Sql/servers/communicationLinks/write | Utwórz lub zaktualizuj łącza komunikacyjnego serwera. |
> | Akcja | Microsoft.Sql/servers/connectionPolicies/read | Zwraca listę zasad połączenia serwera z określonego serwera. |
> | Akcja | Microsoft.Sql/servers/connectionPolicies/write | Utwórz lub zaktualizuj zasady połączeń serwera. |
> | Akcja | Microsoft.Sql/servers/databases/advisors/read | Zwraca listę doradcy jest dostępna dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Zwraca listę zalecane działania advisor określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Zastosuj zalecaną akcję w bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/advisors/write | Aktualizacja automatycznego wykonywania stanu usługi advisor na poziomie bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/auditingPolicies/read | Pobieranie szczegółów tabeli zasad inspekcji skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/auditingPolicies/write | Zmień zasady inspekcji tabeli określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/auditingSettings/read | Pobieranie szczegółów obiektu blob zasad inspekcji skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/auditingSettings/write | Zmień zasady inspekcji obiektu blob określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji bazy danych obiektów blob |
> | Akcja | Microsoft.Sql/servers/databases/automaticTuning/read | Zwraca ustawienia automatycznego dostrajania dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualizuje ustawienia automatycznego dostrajania dla bazy danych i zwraca zaktualizowanymi ustawieniami |
> | Akcja | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Zwraca listę archiwizacji zasad tworzenia kopii zapasowych określonej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Utwórz lub zaktualizuj zasady archiwizacji kopii zapasowej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/connectionPolicies/read | Pobieranie szczegółów zasady połączeń skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/connectionPolicies/write | Zmień zasady połączenia dla danego bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Zwraca listę maskowania zasad danych bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Usuń dane maskowania reguły określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Pobieranie szczegółów danych maskowania regułę skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Zmień dane maskowania reguły określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Zmień zasady dla danych maskowania danych |
> | Akcja | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Zwraca informacje krok zapytań rozproszonych zapytania magazynu danych dla Identyfikatora wybrany krok |
> | Akcja | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Zwraca informacje zapytania dystrybucji magazynu danych dla Identyfikatora wybranego zapytania |
> | Akcja | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Pobiera aktywności użytkowników wystąpienia SQL Data Warehouse, która zawiera kwerendy działa i jest wstrzymany |
> | Akcja | Microsoft.Sql/servers/databases/delete | Usuwa istniejącą bazę danych. |
> | Akcja | Microsoft.Sql/servers/databases/export/action | Eksportowanie bazy danych Azure SQL |
> | Akcja | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Pobieranie szczegółów zasady inspekcji rozszerzonego obiektu blob skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Zmień zasady inspekcji rozszerzonego obiektu blob określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/extensions/read | Pobiera kolekcję rozszerzeń dla bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/extensions/write | Zmień rozszerzenie określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Pobieranie zasad tworzenia kopii zapasowych geograficznie określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Utwórz lub zaktualizuj zasady geobackup bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/importExportOperationResults/read | Pobiera w trakcie operacji importu/eksportu |
> | Akcja | Microsoft.Sql/servers/databases/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/metrics/read | Zwraca metryki dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/move/action | Zmień nazwę bazy danych Azure SQL |
> | Akcja | Microsoft.Sql/servers/databases/operationResults/read | Pobiera stan operacji bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/operations/cancel/action | Anuluje bazy danych SQL Azure oczekująca operacja asynchroniczna, która nie jest jeszcze zakończone. |
> | Akcja | Microsoft.Sql/servers/databases/operations/read | Zwraca listę operacji wykonywanych na bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/pause/action | Baza danych magazynu danych Azure SQL Wstrzymaj |
> | Akcja | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla baz danych |
> | Akcja | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Zwraca kolekcję teksty zapytania, które odpowiadają na określone parametry. |
> | Akcja | Microsoft.Sql/servers/databases/queryStore/read | Zwraca bieżące wartości ustawień magazyn zapytań dla bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/queryStore/write | Aktualizuje ustawienia magazynu zapytań dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/read | Zwraca listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/delete | Zakończenie relacji replikacji wymuszone i o utracie danych |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Trybu failover po zsynchronizowaniu wszystkich zmian z serwera podstawowego udostępniania tę bazę danych do relationship\u0027s replikacji głównej i wprowadzania zdalnego podstawowej do dodatkowej |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Trybu failover natychmiast o utracie danych, co ta baza danych do relationship\u0027s replikacji głównej i wprowadzania zdalnego podstawowej do dodatkowej |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/read | Zwracany szczegółów dotyczących łącz replikacji dla określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Zakończenie relacji replikacji wymuszone lub po synchronizacji z partnera |
> | Akcja | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Tryb aktualizacji replikacji dla łącza do trybu synchronicznego lub asynchronicznego |
> | Akcja | Microsoft.Sql/servers/databases/restorePoints/action | Tworzy nowy punkt przywracania |
> | Akcja | Microsoft.Sql/servers/databases/restorePoints/delete | Usuwa punkt przywracania bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/restorePoints/read | Zwraca przywrócić punkty dla bazy danych. |
> | Akcja | Microsoft.Sql/servers/databases/resume/action | Baza danych magazynu danych Azure SQL Wznów |
> | Akcja | Microsoft.Sql/servers/databases/schemas/read | Pobieranie listy schematów bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Pobieranie listy kolumn tabeli. |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Usuń etykietę czułości danej kolumnie |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Pobierz etykiety ważności danego kolumny |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Utwórz lub zaktualizuj etykiety ważności danego kolumny |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/read | Pobieranie listy tabel bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Pobieranie listy zalecenia dotyczące indeksu dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Zastosuj zalecenie dotyczące indeksu |
> | Akcja | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Pobieranie szczegółów zasady wykrywania zagrożeń skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/securityMetrics/read | Pobiera kolekcję metryki zabezpieczeń bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/sensitivityLabels/read | Lista czułości etykiety danego bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Zwraca sugestii dotyczących skalowania bazy danych w górę lub w dół w oparciu statystyk wykonywania zapytań do zwiększenia wydajności i zmniejszenie kosztów |
> | Akcja | Microsoft.Sql/servers/databases/skus/read | Pobiera kolekcję dostępne jednostki SKU dla bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Anulowanie synchronizacji grupy synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/delete | Usuwa istniejącą grupę synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Zwraca listę synchronizacji schematów bazy danych Centrum |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/logs/read | Zwraca listę dzienników grupy synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/read | Zwraca listę synchronizacji grupy lub pobiera właściwości dla określonego synchronizacji grupy. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Odśwież schemat bazy danych Centrum synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu Centrum synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Usuwa istniejącego elementu członkowskiego synchronizacji. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Zwraca listę elementów synchronizacji lub pobiera właściwości dla synchronizacji określonego elementu członkowskiego. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Odśwież schemat elementu członkowskiego synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Pobierz wynik operacji odświeżania schematu synchronizacji elementu członkowskiego |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Zwraca listę synchronizacji schematów bazy danych elementu członkowskiego |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Tworzy element członkowski synchronizacji z określonymi parametrami lub zaktualizować właściwości synchronizacji określonego elementu członkowskiego. |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Wyzwalacz synchronizacji grupy synchronizacji |
> | Akcja | Microsoft.Sql/servers/databases/syncGroups/write | Tworzy grupę synchronizacji z określonymi parametrami lub zaktualizować właściwości synchronizacji określonej grupy. |
> | Akcja | Microsoft.Sql/servers/databases/topQueries/queryText/action | Zwraca tekst języka Transact-SQL dla wybranego zapytania o identyfikatorze |
> | Akcja | Microsoft.Sql/servers/databases/topQueries/read | Zwraca zagregowane statystyk czasu wykonywania dla wybranego zapytania w wybranym okresie |
> | Akcja | Microsoft.Sql/servers/databases/topQueries/statistics/read | Zwraca zagregowane statystyk czasu wykonywania dla wybranego zapytania w wybranym okresie |
> | Akcja | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Pobiera w trakcie wykonywania operacji na przezroczystego szyfrowania danych |
> | Akcja | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Pobieranie stanu i szczegółowe informacje o funkcji zabezpieczeń szyfrowania danych przezroczysty określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Zmiany stanu szyfrowania danych przezroczyste |
> | Akcja | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Uaktualnianie bazy danych magazynu danych Azure SQL |
> | Akcja | Microsoft.Sql/servers/databases/usages/read | Pobiera dane użycia bazy danych SQL Azure |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Usuń oceny luk w zabezpieczeniach określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Pobieranie szczegółów oceny luk w zabezpieczeniach skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Usuń linię bazową reguły oceny luk w zabezpieczeniach określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Pobierz bazowej reguły oceny luk w zabezpieczeniach określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Zmiany planu bazowego reguły oceny luk w zabezpieczeniach określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Konwertowanie istniejących wyników skanowania na człowieka czytelnym formacie. Jeśli istnieje już nic się stanie, |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Wykonywanie skanowania bazy danych w celu oceny luk w zabezpieczeniach. |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Zwraca listę luki w zabezpieczeniach bazy danych rekordów skanowania oceny lub uzyskać rekordu skanowania skanowanie określonego identyfikatora. |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Zmień oceny luk w zabezpieczeniach określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Wykonywanie skanowania bazy danych w celu oceny luk w zabezpieczeniach. |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Pobieranie wyników skanowania oceny luk w zabezpieczeniach bazy danych operacji Execute |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Pobieranie szczegółów oceny luk w zabezpieczeniach skonfigurowany w danej bazie danych |
> | Akcja | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Zmień oceny luk w zabezpieczeniach określonej bazy danych |
> | Akcja | Microsoft.Sql/servers/databases/write | Tworzy bazy danych z określonymi parametrami lub aktualizuje właściwości bądź tagi określonej bazy danych. |
> | Akcja | Microsoft.Sql/servers/delete | Usuwa istniejącego serwera. |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Usuwa istniejące konfiguracjami odzyskiwania po awarii dla danego serwera |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Tryb failover DisasterRecoveryConfiguration |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Wymuszenie trybu Failover DisasterRecoveryConfiguration |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Pobiera kolekcję po awarii z konfiguracjami odzyskiwania, które obejmują tego serwera |
> | Akcja | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Konfiguracja odzyskiwania po awarii serwera zmiany |
> | Akcja | Microsoft.Sql/servers/elasticPoolEstimates/read | Zwraca listę już utworzone dla tego serwera oszacowania puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPoolEstimates/write | Tworzy nowego szacowania puli elastycznej dla listy baz danych, pod warunkiem |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/read | Zwraca listę doradcy jest dostępna dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Zwraca listę zalecane działania advisor określony dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Zastosuj akcję zalecaną dla elastycznej puli |
> | Akcja | Microsoft.Sql/servers/elasticPools/advisors/write | Aktualizacja automatycznego wykonywania stanu usługi advisor na poziomie puli elastycznej. |
> | Akcja | Microsoft.Sql/servers/elasticPools/databases/read | Pobiera listę baz danych dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/delete | Usuń istniejącej puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Pobieranie działań i szczegółowe informacje o puli elastycznej bazy danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Pobieranie działań i szczegóły dotyczące danego bazy danych, która jest częścią puli elastycznej bazy danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/metrics/read | Zwraca metryki dla elastycznych pul baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Anuluje puli elastycznej Azure SQL oczekująca operacja asynchroniczna, która nie jest jeszcze zakończone. |
> | Akcja | Microsoft.Sql/servers/elasticPools/operations/read | Zwraca listę operacji wykonywanych na puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla elastycznych pul baz danych |
> | Akcja | Microsoft.Sql/servers/elasticPools/read | Pobieranie szczegółów puli elastycznej na danym serwerze |
> | Akcja | Microsoft.Sql/servers/elasticPools/skus/read | Pobiera kolekcję dostępne jednostki SKU dla puli elastycznej |
> | Akcja | Microsoft.Sql/servers/elasticPools/write | Utwórz nową lub zmodyfikować właściwości istniejącej puli elastycznej |
> | Akcja | Microsoft.Sql/servers/encryptionProtector/read | Zwraca listę protectors szyfrowania serwera lub pobiera właściwości dla określonego serwera ochrony szyfrowania. |
> | Akcja | Microsoft.Sql/servers/encryptionProtector/write | Zaktualizuj właściwości dla określonego ochrony szyfrowania serwera. |
> | Akcja | Microsoft.Sql/servers/extendedAuditingSettings/read | Pobieranie szczegółów obiektu blob serwera rozszerzonej skonfigurowane na danym serwerze zasady inspekcji |
> | Akcja | Microsoft.Sql/servers/extendedAuditingSettings/write | Zmienianie serwera rozszerzonej Inspekcja obiektów blob dla danego serwera |
> | Akcja | Microsoft.Sql/servers/failoverGroups/delete | Usuwa istniejącą grupę trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/failover/action | Wykonuje planowany tryb failover w istniejącej grupie trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Wykonuje wymuszenia pracy awaryjnej w istniejącej grupie trybu failover. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/read | Zwraca listę trybu failover grupy lub pobiera właściwości dla określonego trybu failover grupy. |
> | Akcja | Microsoft.Sql/servers/failoverGroups/write | Tworzy grupę trybu failover z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego trybu failover grupy. |
> | Akcja | Microsoft.Sql/servers/firewallRules/delete | Usuwa istniejącą regułę zapory serwera. |
> | Akcja | Microsoft.Sql/servers/firewallRules/read | Zwraca listę Zapora serwera reguły lub pobiera właściwości dla określonego serwera reguły zapory. |
> | Akcja | Microsoft.Sql/servers/firewallRules/write | Powoduje utworzenie reguły zapory serwera z określonymi parametrami, należy zaktualizować właściwości określonej reguły lub zastąpienie wszystkich istniejących reguł nowych reguł zapory serwera. |
> | Akcja | Microsoft.Sql/servers/import/action | Utwórz nową bazę danych na serwerze i wdrażanie schemat i dane z pakietu DacPac |
> | Akcja | Microsoft.Sql/servers/importExportOperationResults/read | Pobiera w trakcie operacji importu/eksportu |
> | Akcja | Microsoft.Sql/servers/keys/delete | Usuwa istniejący klucz serwera. |
> | Akcja | Microsoft.Sql/servers/keys/read | Zwraca listę serwera kluczy lub pobiera właściwości dla klucza określonego serwera. |
> | Akcja | Microsoft.Sql/servers/keys/write | Tworzy klucz z określonymi parametrami lub aktualizuje właściwości bądź tagi dla klucza określonego serwera. |
> | Akcja | Microsoft.Sql/servers/networkInterfaces/read | Zwraca właściwości dla określonej sieci interfejsu |
> | Akcja | Microsoft.Sql/servers/networkInterfaces/write | Tworzy interfejs sieciowy z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonej sieci interfejsu |
> | Akcja | Microsoft.Sql/servers/operationResults/read | Pobiera w trakcie operacji serwera |
> | Akcja | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Zwracane typy metryk, które są dostępne dla serwerów |
> | Akcja | Microsoft.Sql/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Akcja | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Pobrać metryki dla zalecane elastyczne pule baz danych dla danego serwera |
> | Akcja | Microsoft.Sql/servers/recommendedElasticPools/read | Pobierz zalecenia dla elastycznych pul baz danych na zmniejszenie kosztów oraz pomagają w poprawieniu wydajności na podstawie wykorzystania zasobów historica |
> | Akcja | Microsoft.Sql/servers/recoverableDatabases/read | Ta operacja jest używana do odzyskiwania po awarii na żywo bazy danych, aby przywrócić ostatnią znaną dobrą punktu kopii zapasowej bazy danych. Zwraca informacje o ostatniej dobrej kopii zapasowej, ale doesn\u0027t faktycznie przywrócić bazę danych. |
> | Akcja | Microsoft.Sql/servers/restorableDroppedDatabases/read | Pobranie listy baz danych, które zostały usunięte na danym serwerze znajdujące się nadal w ramach zasad przechowywania. |
> | Akcja | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Pobierz wyniki serwerze operacji zapisu zasad wykrywania zagrożeń |
> | Akcja | Microsoft.Sql/servers/securityAlertPolicies/read | Pobieranie szczegółów serwera zasad wykrywania zagrożeń skonfigurowane na danym serwerze |
> | Akcja | Microsoft.Sql/servers/securityAlertPolicies/write | Zmień zasady wykrywania zagrożeń serwera dla danego serwera |
> | Akcja | Microsoft.Sql/servers/serviceObjectives/read | Pobieranie listy celów poziomu usługi (znanej także jako warstwy wydajności) dostępnych na danym serwerze |
> | Akcja | Microsoft.Sql/servers/syncAgents/delete | Usuwa istniejące agenta synchronizacji. |
> | Akcja | Microsoft.Sql/servers/syncAgents/generateKey/action | Generowanie klucza Rejestracja agenta synchronizacji |
> | Akcja | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Zwraca listę synchronizacji agenta połączone z baz danych |
> | Akcja | Microsoft.Sql/servers/syncAgents/read | Zwraca listę agentów synchronizacji lub pobiera właściwości dla agenta określonego synchronizacji. |
> | Akcja | Microsoft.Sql/servers/syncAgents/write | Tworzy agent synchronizacji z określonymi parametrami lub zaktualizować właściwości synchronizacji określonego agenta. |
> | Akcja | Microsoft.Sql/servers/usages/read | Zwraca limit przydziału jednostek dtu w warstwie serwera i bieżący consuption jednostek dtu w warstwie przez wszystkie bazy danych w ramach serwera |
> | Akcja | Microsoft.Sql/servers/virtualNetworkRules/delete | Usuwa istniejącą regułę sieci wirtualnej |
> | Akcja | Microsoft.Sql/servers/virtualNetworkRules/read | Zwraca listę sieci wirtualnej reguły lub pobiera właściwości dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.Sql/servers/virtualNetworkRules/write | Tworzy regułę sieci wirtualnej z określonymi parametrami lub zaktualizować właściwości bądź tagi dla reguły określonej sieci wirtualnej. |
> | Akcja | Microsoft.Sql/servers/write | Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera. |
> | Akcja | Microsoft.Sql/unregister/action | Wyrejestrowuje subskrypcji dla dostawcy zasobów bazy danych Microsoft SQL i umożliwia tworzenie baz danych serwera SQL firmy Microsoft. |
> | Akcja | Microsoft.Sql/virtualClusters/read | Zwraca listę klastrów wirtualnych lub pobiera właściwości dla określonego klastra wirtualnego. |
> | Akcja | Microsoft.Sql/virtualClusters/write | Aktualizuje tagi wirtualny klastra. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Storage/checknameavailability/read | Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana. |
> | Akcja | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Powiadamia element Microsoft.Storage o usuwaniu sieci wirtualnej lub podsieci |
> | Akcja | Microsoft.Storage/operations/read | Sonduje stan operacji asynchronicznej. |
> | Akcja | Microsoft.Storage/register/action | Rejestruje subskrypcję dostawcy zasobów magazynu i umożliwia tworzenie kont magazynu. |
> | Akcja | Microsoft.Storage/skus/read | Wyświetla listę jednostek SKU obsługiwanych przez zasób Microsoft.Storage. |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Usuń stan archiwizacji ze względów prawnych dla kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Zwraca wynik usunięcia kontenera |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Usuń zasady niezmienności kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Rozszerz zasady niezmienności kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Zablokuj zasady niezmienności kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Uzyskaj zasady niezmienności kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Dodaj zasady niezmienności kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/read | Zwraca kontener lub listę kontenerów |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ustaw stan archiwizacji ze względów prawnych dla kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/containers/write | Zwraca wynik umieszczania lub dzierżawienia kontenera obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | Uzyskaj listę definicji metryk magazynu firmy Microsoft. |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/read | Zwraca statystyki lub wartości usługi Blob Service |
> | Akcja | Microsoft.Storage/storageAccounts/blobServices/write | Zwraca wynik umieszczenia właściwości usługi obiektów blob |
> | Akcja | Microsoft.Storage/storageAccounts/delete | Usuwa istniejące konto magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | Uzyskaj listę definicji metryk magazynu firmy Microsoft. |
> | Akcja | Microsoft.Storage/storageAccounts/listAccountSas/action | Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/listServiceSas/action | Zwraca token sygnatury dostępu współdzielonego usługi dla określonego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | Uzyskaj listę definicji metryk magazynu firmy Microsoft. |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | Uzyskaj listę definicji metryk magazynu firmy Microsoft. |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Zwraca wynik usunięcia kolejki |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/queues/write | Zwraca wynik zapisania kolejki |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/read | Zwraca statystyki lub wartości usługi kolejki. |
> | Akcja | Microsoft.Storage/storageAccounts/queueServices/write | Zwraca wynik ustawienia właściwości usługi kolejki |
> | Akcja | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/regeneratekey/action | Generuje ponownie klucze dostępu dla podanego konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Utwórz lub zaktualizuj ustawienia diagnostyczne konta magazynu. |
> | Akcja | Microsoft.Storage/storageAccounts/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu. |
> | Akcja | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | Uzyskaj listę definicji metryk magazynu firmy Microsoft. |
> | Akcja | Microsoft.Storage/storageAccounts/write | Tworzy konto magazynu z podanymi parametrami lub aktualizuje właściwości bądź tagi albo dodaje niestandardową domenę dla podanego konta magazynu. |
> | Akcja | Microsoft.Storage/usages/read | Zwraca limit i bieżącą liczbę użyć dla zasobów w określonej subskrypcji |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | microsoft.storagesync/storageSyncServices/delete | Usuń wszystkie usługi synchronizacji magazynu |
> | Akcja | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla usługi synchronizacji magazynu |
> | Akcja | microsoft.storagesync/storageSyncServices/read | Wszystkie usługi synchronizacji magazynu do odczytu |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/delete | Usuń wszelkie zarejestrowanego serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/read | Przeczytaj żadnych zarejestrowanego serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/registeredServers/write | Utwórz lub zaktualizuj wszystkie zarejestrowanego serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Usuń wszystkie punkty końcowe chmury |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Interfejs api lokalizacji dla tworzenia kopii zapasowej wywołania asynchroniczne |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Wywołaj tę akcję po utworzeniu kopii zapasowej |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Wywołaj tę akcję po przywróceniu |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Wywołanie przed kopii zapasowej |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Wywołanie przed przywróceniem |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Przeczytaj żadnych punktów końcowych w chmurze |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Przywróć pulsu |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Utwórz lub zaktualizuj żadnych punktów końcowych w chmurze |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/delete | Usuń wszystkie grupy synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/read | Przeczytaj grupy synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Usuń wszystkie punkty końcowe serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Przeczytaj żadnych punktów końcowych serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Wywołaj tę akcję, aby odwołać pliki na serwerze |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Utwórz lub zaktualizuj wszystkie punkty końcowe serwera |
> | Akcja | microsoft.storagesync/storageSyncServices/syncGroups/write | Utwórz lub zaktualizuj wszystkie grupy synchronizacji |
> | Akcja | microsoft.storagesync/storageSyncServices/write | Utwórz lub zaktualizuj wszystkie usługi synchronizacji magazynu |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/delete | Usuwa rekordy kontroli dostępu |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/read | Wyświetla listę lub pobiera rekordy kontroli dostępu |
> | Akcja | Microsoft.StorSimple/managers/accessControlRecords/write | Utwórz lub zaktualizuj rekordy kontroli dostępu |
> | Akcja | Microsoft.StorSimple/managers/alerts/read | Wyświetla listę lub pobiera alerty |
> | Akcja | Microsoft.StorSimple/managers/bandwidthSettings/delete | Usuwa istniejące ustawienia przepustowości (8000 serii tylko) |
> | Akcja | Microsoft.StorSimple/managers/bandwidthSettings/read | Wyświetl listę ustawień przepustowości (tylko 8000 Series) |
> | Akcja | Microsoft.StorSimple/managers/bandwidthSettings/write | Tworzy nowy lub aktualizuje ustawienia przepustowości (8000 serii tylko) |
> | Akcja | Microsoft.StorSimple/Managers/certificates/write | Operacja aktualizacji zasobów certyfikatu aktualizuje certyfikat poświadczeń zasobów/magazynu. |
> | Akcja | Microsoft.StorSimple/managers/clearAlerts/action | Wyczyść wszystkie alerty skojarzonych z Menedżerem urządzeń. |
> | Akcja | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Lista urządzenia chmury obsługiwane konfiguracje |
> | Akcja | Microsoft.StorSimple/managers/configureDevice/action | Konfiguruje urządzenia |
> | Akcja | Microsoft.StorSimple/managers/delete | Usuwa menedżerów urządzeń |
> | Akcja | Microsoft.StorSimple/Managers/delete | Operacja usuwania magazynu usuwa określony zasób Azure typu "magazynu" |
> | Akcja | Microsoft.StorSimple/managers/devices/alertSettings/read | Wyświetla listę lub pobiera ustawienia alertu |
> | Akcja | Microsoft.StorSimple/managers/devices/alertSettings/write | Utwórz lub zaktualizuj ustawienia alertu |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Tworzenie kopii zapasowej ręcznie utworzyć na żądanie kopii zapasowych wszystkich woluminów, które są chronione przez zasady. |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Usuwa istniejące zasady tworzenia kopii zapasowej (8000 serii tylko) |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/read | Tworzenie kopii zapasowej listy zasad (tylko 8000 Series) |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Usuwa istniejących harmonogramów |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Lista harmonogramów |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Tworzy nowy lub aktualizuje harmonogramów |
> | Akcja | Microsoft.StorSimple/managers/devices/backupPolicies/write | Tworzy nowy lub aktualizuje zasady tworzenia kopii zapasowej (8000 serii tylko) |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/delete | Usuwa zestaw kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klonowanie udziale lub woluminie przy użyciu kopii zapasowej elementu. |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/read | Wyświetla listę lub pobiera zestawu kopii zapasowych |
> | Akcja | Microsoft.StorSimple/managers/devices/backups/restore/action | Przywróć wszystkie woluminy z zestawu kopii zapasowych. |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Usuwa harmonogram tworzenia kopii zapasowych grupy |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Wyświetla listę lub pobiera grup harmonogramu tworzenia kopii zapasowej |
> | Akcja | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Utwórz lub zaktualizuj grupy harmonogramu tworzenia kopii zapasowej |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/delete | Usuwa ustawienia protokołu Chap |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/read | Wyświetla listę lub pobiera ustawienia protokołu Chap |
> | Akcja | Microsoft.StorSimple/managers/devices/chapSettings/write | Utwórz lub zaktualizuj ustawienia protokołu Chap |
> | Akcja | Microsoft.StorSimple/managers/devices/deactivate/action | Dezaktywuje urządzenia. |
> | Akcja | Microsoft.StorSimple/managers/devices/delete | Usuwa urządzenia |
> | Akcja | Microsoft.StorSimple/managers/devices/download/action | Pobierania aktualizacji dla urządzenia. |
> | Akcja | Microsoft.StorSimple/managers/devices/failover/action | Tryb pracy awaryjnej urządzenia. |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Pobierz kopię zapasową serwera plików. |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/delete | Usuwa na serwerach plików |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Wyświetla listę lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Wyświetla listę lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/read | Wyświetla listę lub pobiera na serwerach plików |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Usuwa akcji |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Wyświetla listę lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Wyświetla listę lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Wyświetla listę lub pobiera akcji |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Utwórz lub zaktualizuj akcji |
> | Akcja | Microsoft.StorSimple/managers/devices/fileservers/write | Utwórz lub zaktualizuj na serwerach plików |
> | Akcja | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Zmiany stanu zasilania kontrolera grup składników sprzętowych |
> | Akcja | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Lista grup składników sprzętowych |
> | Akcja | Microsoft.StorSimple/managers/devices/install/action | Zainstaluj aktualizacje na urządzeniu. |
> | Akcja | Microsoft.StorSimple/managers/devices/installUpdates/action | Instaluje aktualizacje na urządzeniach |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Pobierz kopię zapasową serwera iSCSI. |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Usuwa iSCSI serwerów |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Usuwa dysków |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Wyświetla listę lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Wyświetla listę lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Wyświetla listę lub pobiera dysków |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Utwórz lub zaktualizuj dysków |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Wyświetla listę lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Wyświetla listę lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/read | Wyświetla listę lub pobiera iSCSI serwerów |
> | Akcja | Microsoft.StorSimple/managers/devices/iscsiservers/write | Utwórz lub zaktualizuj iSCSI serwerów |
> | Akcja | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Anuluj uruchomione zadania |
> | Akcja | Microsoft.StorSimple/managers/devices/jobs/read | Wyświetla listę lub pobiera zadania |
> | Akcja | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Lista zestawów trybu failover do istniejącego urządzenia. |
> | Akcja | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Cele trybu failover listy urządzeń |
> | Akcja | Microsoft.StorSimple/managers/devices/metrics/read | Wyświetla listę lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Wyświetla listę lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Potwierdza udanej migracji, a następnie przekazać go. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Pobierz stan potwierdzenia migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Pobierz stan zadania migracji szacowania. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Pobierz stan migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importowanie konfiguracji źródłowego do migracji |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Uruchom migrację za pomocą konfiguracji źródła |
> | Akcja | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Uruchom zadanie, aby oszacować w czasie trwania procesu migracji. |
> | Akcja | Microsoft.StorSimple/managers/devices/networkSettings/read | Wyświetla listę lub pobiera ustawienia sieci |
> | Akcja | Microsoft.StorSimple/managers/devices/networkSettings/write | Tworzy nowy lub aktualizuje ustawienia sieci |
> | Akcja | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Lista publicznego klucza szyfrowania Menedżera urządzeń |
> | Akcja | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publikowanie pakietu obsługi urządzenia do firmy Microsoft Support rozwiązywania problemów. |
> | Akcja | Microsoft.StorSimple/managers/devices/read | Wyświetla listę lub pobiera urządzenia |
> | Akcja | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Skanowanie w poszukiwaniu aktualizacji na urządzeniu. |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/read | Wyświetl listę ustawień zabezpieczeń |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synchronizuj certyfikat zdalnego zarządzania dla urządzenia. |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Zaktualizuj ustawienia zabezpieczeń. |
> | Akcja | Microsoft.StorSimple/managers/devices/securitySettings/write | Tworzy nowy lub aktualizuje ustawienia zabezpieczeń |
> | Akcja | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Wyślij testową wiadomość e-mail alertów do adresatów wiadomości e-mail skonfigurowany. |
> | Akcja | Microsoft.StorSimple/managers/devices/timeSettings/read | Wyświetla listę lub pobiera ustawienia czasu |
> | Akcja | Microsoft.StorSimple/managers/devices/timeSettings/write | Tworzy nowy lub aktualizuje ustawienia czasu |
> | Akcja | Microsoft.StorSimple/managers/devices/updateSummary/read | Wyświetla listę lub pobiera aktualizacji — podsumowanie |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Usuwa istniejące kontenery woluminów (8000 serii tylko) |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/listEncryptionKeys/action | Lista kluczy szyfrowania woluminu kontenerów |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Lista metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Lista definicji metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/read | Lista kontenery woluminów (tylko 8000 Series) |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/rolloverEncryptionKey/action | Klucze szyfrowania przerzucania kontenery woluminów |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Usuwa istniejące woluminy |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Lista metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Lista definicji metryk |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Wyświetl listę woluminów |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Tworzy nowy lub aktualizuje woluminów |
> | Akcja | Microsoft.StorSimple/managers/devices/volumeContainers/write | Tworzy nowy lub aktualizuje kontenery woluminów (8000 serii tylko) |
> | Akcja | Microsoft.StorSimple/managers/devices/write | Utwórz lub zaktualizuj urządzenia |
> | Akcja | Microsoft.StorSimple/managers/encryptionSettings/read | Wyświetla listę lub pobiera ustawienia szyfrowania |
> | Akcja | Microsoft.StorSimple/Managers/extendedInformation/delete | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.StorSimple/Managers/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.StorSimple/Managers/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Akcja | Microsoft.StorSimple/managers/getActivationKey/action | Menedżer urządzeń, należy pobrać klucza aktywacji. |
> | Akcja | Microsoft.StorSimple/managers/getEncryptionKey/action | Pobierz klucz szyfrowania dla Menedżera urządzeń. |
> | Akcja | Microsoft.StorSimple/managers/listActivationKey/action | Pobiera klucz aktywacji Menedżera urządzeń StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/listPrivateEncryptionKey/action | Pobiera klucz prywatny szyfrowania dla Menedżera urządzeń StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Lista kluczy szyfrowania publicznego z Menedżera urządzeń StorSimple. |
> | Akcja | Microsoft.StorSimple/managers/metrics/read | Wyświetla listę lub pobiera metryki |
> | Akcja | Microsoft.StorSimple/managers/metricsDefinitions/read | Wyświetla listę lub pobiera definicje metryk |
> | Akcja | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Utwórz nowe urządzenia chmury. |
> | Akcja | Microsoft.StorSimple/managers/read | Wyświetla listę lub pobiera menedżerów urządzeń |
> | Akcja | Microsoft.StorSimple/Managers/read | Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu" |
> | Akcja | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Ponowne generowanie certyfikatu rejestracji dla menedżerów urządzeń. |
> | Akcja | Microsoft.StorSimple/managers/regenerateActivationKey/action | Menedżer urządzeń, należy ponownie wygenerować klucza aktywacji. |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Usuwa poświadczenia konta magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/listAccessKey/action | Listy kluczy dostępu do poświadczeń konta magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/read | Wyświetla listę lub pobiera poświadczeń konta magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageAccountCredentials/write | Utwórz lub zaktualizuj poświadczenia konta magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageDomains/delete | Usuwa domen magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageDomains/read | Wyświetla listę lub pobiera domen magazynu |
> | Akcja | Microsoft.StorSimple/managers/storageDomains/write | Utwórz lub zaktualizuj domen magazynu |
> | Akcja | Microsoft.StorSimple/managers/write | Utwórz lub zaktualizuj Menedżera urządzeń |
> | Akcja | Microsoft.StorSimple/Managers/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.StreamAnalytics/locations/quotas/Read | Limit przydziału dla subskrypcji odczytu Stream Analytics |
> | Akcja | Microsoft.StreamAnalytics/operations/Read | Operacji analizy strumienia odczytu |
> | Akcja | Microsoft.StreamAnalytics/Register/action | Zarejestrować subskrypcji u dostawcy zasobów usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Delete | Usuwanie zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Usuń funkcję zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Wyniki operacji odczytu dla funkcji zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Funkcję zadania usługi analiza strumienia odczytu |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Pobieranie definicji domyślnej funkcji zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Funkcja zadanie analizy strumienia testu |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Write — funkcja zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Usuń dane wejściowe zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Wyniki operacji odczytu dla danych wejściowych zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Wejścia zadania usługi analiza strumienia odczytu |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Przykładowe Stream Analytics zadania w danych wejściowych |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Test Stream Analytics zadania w danych wejściowych |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Zapisu danych wejściowych zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Odczyt definicji metryk |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Wyniki operacji odczytu dla zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Usuń dane wyjściowe zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Wyniki operacji odczytu dla danych wyjściowych zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Odczytu Stream Analytics — dane wyjściowe zadania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Danych wyjściowych zadania analizy strumienia testu |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Zapisywania danych wyjściowych zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Ustawienie diagnostyczne dla odczytu. |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Zapis ustawienie diagnostyczne. |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Pobiera dostępne dzienniki dla streamingjobs |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla streamingjobs |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Read | Zadania usługi analiza strumienia odczytu |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Start/action | Uruchom zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Zatrzymanie zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Usuń przekształcenie zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Odczyt Stream Analytics zadania przekształcania |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Zapis przekształcenie zadania usługi analiza strumienia |
> | Akcja | Microsoft.StreamAnalytics/streamingjobs/Write | Zapis zadania usługi analiza strumienia |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Subscription/CreateSubscription/action | Tworzenie subskrypcji platformy Azure |
> | Akcja | Microsoft.Subscription/SubscriptionDefinitions/read | Pobierz definicję subskrypcji platformy Azure, w grupie zarządzania. |
> | Akcja | Microsoft.Subscription/SubscriptionDefinitions/write | Utwórz definicję subskrypcji platformy Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Support/register/action | Przeprowadza rejestrację u dostawcy zasobów pomocy technicznej |
> | Akcja | Microsoft.Support/supportTickets/read | Pobiera szczegóły biletu pomocy technicznej (w tym stan, ważność, szczegóły dotyczące kontaktu i wiadomości) lub pobiera listę biletów pomocy technicznej dla różnych subskrypcji. |
> | Akcja | Microsoft.Support/supportTickets/write | Tworzy lub aktualizuje bilet pomocy technicznej. Można utworzyć biletu pomocy technicznej dla technicznych, rozliczeń, limity przydziału lub zarządzaniem subskrypcjami problemy związane z. Możesz zaktualizować ważność, szczegóły dotyczące kontaktu i komunikacji dla istniejących biletów pomocy technicznej. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Usuwa zasady dostępu. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Pobierz właściwości zasad dostępu. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Tworzy nową zasadę dostępu dla środowiska, lub aktualizuje istniejące zasady dostępu. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/delete | Usuwa środowiska. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Usuwa źródło zdarzenia. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla eventsources |
> | Akcja | Microsoft.TimeSeriesInsights/environments/eventsources/read | Pobierz właściwości źródła zdarzenia. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/eventsources/write | Tworzy nowe źródło zdarzeń dla środowiska, lub aktualizuje istniejące źródło zdarzenia. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | Pobiera dostępne metryki dla środowisk |
> | Akcja | Microsoft.TimeSeriesInsights/environments/read | Pobierz właściwości środowiska. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Usuwa odwołanie do zestawu danych. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Pobierz właściwości zestawu danych odwołania. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Tworzy nowy zestaw danych odwołania dla środowiska, lub aktualizuje istniejący zestaw danych odwołania. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/status/read | Pobierz stan środowiska stanu jego skojarzony operacje, takie jak transfer danych przychodzących. |
> | Akcja | Microsoft.TimeSeriesInsights/environments/write | Tworzy nowe środowisko, lub aktualizuje istniejącego środowiska. |
> | Akcja | Microsoft.TimeSeriesInsights/register/action | Rejestruje subskrypcję dostawcy zasobów Insights serie czasu i umożliwia tworzenie środowisk Insights serii czasu. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.VisualStudio/Account/Delete | Usuń konto |
> | Akcja | Microsoft.VisualStudio/Account/Read | Konto do odczytu |
> | Akcja | Microsoft.VisualStudio/Account/Write | Ustaw konto |
> | Akcja | Microsoft.VisualStudio/Extension/Delete | Usuń rozszerzenie |
> | Akcja | Microsoft.VisualStudio/Extension/Read | Rozszerzenia do odczytu |
> | Akcja | Microsoft.VisualStudio/Extension/Write | Rozszerzenia zestawu |
> | Akcja | Microsoft.VisualStudio/Project/Delete | Usuwanie projektu |
> | Akcja | Microsoft.VisualStudio/Project/Read | Odczyt projektu |
> | Akcja | Microsoft.VisualStudio/Project/Write | Ustaw projekt |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.Web/apimanagementaccounts/apiacls/Read | Pobierz interfejs Api zarządzania kontami Apiacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/apiacls/DELETE | Usunięcie interfejsu Api zarządzania kontami interfejsów API Apiacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Read | Pobierz interfejs Api zarządzania kontami interfejsów API Apiacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Write | Aktualizowanie interfejsu Api zarządzania kontami interfejsów API Apiacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/connectionacls/Read | Pobierz interfejs Api zarządzania kontami interfejsów API Connectionacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/confirmconsentcode/Action | Potwierdź zgody kodu interfejsu Api zarządzania kontami interfejsów API połączeń. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/DELETE | Usunięcie interfejsu Api zarządzania kontami interfejsów API połączeń Connectionacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Read | Pobierz interfejs Api zarządzania kontami interfejsów API połączeń Connectionacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Write | Aktualizowanie interfejsu Api zarządzania kontami interfejsów API połączeń Connectionacls. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/DELETE | Usunięcie interfejsu Api zarządzania kontami interfejsów API połączeń. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/getconsentlinks/Action | Pobierz łącza zgody dla interfejsu Api zarządzania kontami interfejsów API połączeń. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/listconnectionkeys/Action | Lista kluczy interfejsu Api zarządzania kont interfejsów API połączenia. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/listsecrets/Action | Lista kluczy tajnych interfejsu Api zarządzania kont interfejsów API połączeń. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/Read | Pobierz interfejs Api zarządzania kontami interfejsów API połączeń. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Connections/Write | Aktualizowanie interfejsu Api zarządzania kontami interfejsów API połączeń. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/DELETE | Usuń interfejsów API usługi Api Management kont. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/DELETE | Usuń usługi Api Management API kont zlokalizowane definicje. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Read | Pobierz interfejs Api zarządzania interfejsów API kont zlokalizowane definicje. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Write | Aktualizacja interfejsu Api zarządzania kontami API zlokalizowane definicje. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Read | Pobierz interfejsów API usługi Api Management kont. |
> | Akcja | Microsoft.Web/apimanagementaccounts/APIs/Write | Zaktualizuj interfejsów API usługi Api Management kont. |
> | Akcja | Microsoft.Web/apimanagementaccounts/connectionacls/Read | Pobierz interfejs Api zarządzania kontami Connectionacls. |
> | Akcja | Microsoft.Web/availablestacks/Read | Pobierz stosy dostępne. |
> | Akcja | Microsoft.Web/billingmeters/Read | Pobierz listę liczników rozliczeń. |
> | Akcja | Microsoft.Web/certificates/Delete | Usuwanie istniejącego certyfikatu. |
> | Akcja | Microsoft.Web/certificates/Read | Pobierz listę certyfikatów. |
> | Akcja | Microsoft.Web/certificates/Write | Dodaj nowy certyfikat lub zaktualizuj istniejącą. |
> | Akcja | Microsoft.Web/checknameavailability/Read | Sprawdź, czy nazwa zasobu jest dostępna. |
> | Akcja | Microsoft.Web/classicmobileservices/Read | Pobierz klasycznego usług mobilnych. |
> | Akcja | Microsoft.Web/connectionGateways/Delete | Usuwa bramę połączenia. |
> | Akcja | Microsoft.Web/connectionGateways/Join/Action | Tworzy sprzężenie bramy połączenia. |
> | Akcja | Microsoft.Web/connectiongateways/liststatus/Action | Lista stanu połączenia bramy. |
> | Akcja | Microsoft.Web/connectionGateways/ListStatus/Action | Wyświetla stan połączenia bramy. |
> | Akcja | Microsoft.Web/connectionGateways/Move/Action | Przenosi bramy połączenia. |
> | Akcja | Microsoft.Web/connectionGateways/Read | Pobierz listę połączenia bramy. |
> | Akcja | Microsoft.Web/connectionGateways/Write | Tworzy lub aktualizuje bramy połączenia. |
> | Akcja | Microsoft.Web/Connections/confirmconsentcode/Action | Potwierdź kod zgody połączenia. |
> | Akcja | Microsoft.Web/connections/Delete | Usuwa połączenie. |
> | Akcja | Microsoft.Web/connections/Join/Action | Tworzy sprzężenie połączenia. |
> | Akcja | Microsoft.Web/Connections/listconsentlinks/Action | Lista łączy zgody dla połączeń. |
> | Akcja | Microsoft.Web/connections/Move/Action | Przenosi połączenie. |
> | Akcja | Microsoft.Web/connections/Read | Pobierz listę połączeń. |
> | Akcja | Microsoft.Web/connections/Write | Tworzy lub aktualizuje połączenia. |
> | Akcja | Microsoft.Web/customApis/Delete | Usuwa niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Pobiera definicję interfejsu API z WSDL. |
> | Akcja | Microsoft.Web/customApis/Join/Action | Tworzy sprzężenie niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/customApis/listWsdlInterfaces/Action | Zawiera listę WSDL interfejsów API niestandardowe. |
> | Akcja | Microsoft.Web/customApis/Move/Action | Przenosi niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/customApis/Read | Pobierz listę niestandardowego interfejsu API. |
> | Akcja | Microsoft.Web/customApis/Write | Tworzy lub aktualizuje API niestandardowe. |
> | Akcja | Microsoft.Web/deploymentlocations/Read | Uzyskanie lokalizacji wdrożenia. |
> | Akcja | Microsoft.Web/geoRegions/Read | Pobierz listę regionów geograficznych. |
> | Akcja | Microsoft.Web/hostingenvironments/capacities/Read | Pobierz Hosting możliwości środowiska. |
> | Akcja | Microsoft.Web/hostingEnvironments/Delete | Usuwanie środowiska usługi aplikacji |
> | Akcja | Microsoft.Web/hostingenvironments/Diagnostics/Read | Pobierz Hosting środowisk diagnostyki. |
> | Akcja | Microsoft.Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Pobierz punktów końcowych sieci wszystkie zależności dla ruchu przychodzącego. |
> | Akcja | Microsoft.Web/hostingenvironments/metricdefinitions/Read | Pobierz Hosting definicji metryk środowisk. |
> | Akcja | Microsoft.Web/hostingenvironments/multirolepools/metricdefinitions/Read | Pobierz Hosting definicji metryk pełniących wiele pul środowisk. |
> | Akcja | Microsoft.Web/hostingenvironments/multirolepools/Metrics/Read | Pobierz Hosting środowisk pełniących wiele pul metryki. |
> | Akcja | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | Pobiera dostępne metryki dla MultiRole środowiska usługi aplikacji |
> | Akcja | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Pobierz właściwości puli frontonu w środowisku usługi aplikacji |
> | Akcja | Microsoft.Web/hostingenvironments/multirolepools/skus/Read | Pobierz Hosting jednostki SKU pełniących wiele pul środowisk. |
> | Akcja | Microsoft.Web/hostingenvironments/multirolepools/Usages/Read | Pobierz Hosting środowisk pełniących wiele pul użycia. |
> | Akcja | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Tworzenie nowej puli frontonu w środowisku usługi aplikacji lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Web/hostingenvironments/Operations/Read | Pobierz Hosting operacji środowisk. |
> | Akcja | Microsoft.Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | Pobierz punktów końcowych sieci wszystkie zależności ruchu wychodzącego. |
> | Akcja | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Web/hostingEnvironments/Read | Pobierz właściwości środowiska usługi aplikacji |
> | Akcja | Microsoft.Web/hostingEnvironments/reboot/Action | Ponownie wszystkie maszyny w środowisku usługi aplikacji |
> | Akcja | Microsoft.Web/hostingenvironments/resume/Action | Wznów środowiskach hostingu. |
> | Akcja | Microsoft.Web/hostingenvironments/serverfarms/Read | Pobierz Hosting środowisk planów usługi aplikacji. |
> | Akcja | Microsoft.Web/hostingenvironments/Sites/Read | Pobierz aplikacje sieci Web środowiskach hostingu. |
> | Akcja | Microsoft.Web/hostingenvironments/suspend/Action | Wstrzymaj środowiskach hostingu. |
> | Akcja | Microsoft.Web/hostingenvironments/Usages/Read | Pobierz Hosting użycia środowiska. |
> | Akcja | Microsoft.Web/hostingenvironments/workerpools/metricdefinitions/Read | Pobierz Hosting definicje Metryka Workerpools środowisk. |
> | Akcja | Microsoft.Web/hostingenvironments/workerpools/Metrics/Read | Pobierz Hosting środowisk Workerpools metryki. |
> | Akcja | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | Pobiera dostępne metryki dla WorkerPool środowiska usługi aplikacji |
> | Akcja | Microsoft.Web/hostingEnvironments/workerPools/Read | Pobierz właściwości puli procesów roboczych w środowisku usługi aplikacji |
> | Akcja | Microsoft.Web/hostingenvironments/workerpools/skus/Read | Pobierz Hosting jednostki SKU Workerpools środowisk. |
> | Akcja | Microsoft.Web/hostingenvironments/workerpools/Usages/Read | Pobierz Hosting środowisk Workerpools użycia. |
> | Akcja | Microsoft.Web/hostingEnvironments/workerPools/Write | Tworzenie nowej puli procesów roboczych w środowisku usługi aplikacji lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Web/hostingEnvironments/Write | Tworzenie nowego środowiska usługi aplikacji lub zaktualizować istniejący |
> | Akcja | Microsoft.Web/ishostingenvironmentnameavailable/Read | GET, jeśli jest dostępna nazwa środowiska hostingu. |
> | Akcja | Microsoft.Web/ishostnameavailable/Read | Sprawdź, czy nazwa hosta jest dostępny. |
> | Akcja | Microsoft.Web/isusernameavailable/Read | Sprawdź, czy nazwa użytkownika jest dostępna. |
> | Akcja | Microsoft.Web/listSitesAssignedToHostName/Read | Pobierz nazwy lokacji przypisane do nazwy hosta. |
> | Akcja | Microsoft.Web/Locations/apioperations/Read | Pobierz operacje lokalizacje interfejsu API. |
> | Akcja | Microsoft.Web/Locations/connectiongatewayinstallations/Read | Pobierz lokalizacje połączenia bramy instalacji. |
> | Akcja | Microsoft.Web/Locations/extractapidefinitionfromwsdl/Action | Wyodrębnij definicji interfejsu Api z WSDL dla lokalizacji. |
> | Akcja | Microsoft.Web/Locations/listwsdlinterfaces/Action | Lista interfejsów WSDL dla lokalizacji. |
> | Akcja | Microsoft.Web/Locations/managedapis/apioperations/Read | Operacje zarządzanego interfejsu API lokalizacji pobierania. |
> | Akcja | Microsoft.Web/locations/managedapis/Join/Action | Tworzy sprzężenie zarządzanego interfejsu API. |
> | Akcja | Microsoft.Web/Locations/managedapis/Read | Pobierz lokalizacje zarządzanych interfejsów API. |
> | Akcja | Microsoft.Web/Operations/Read | Pobierz operacje. |
> | Akcja | Microsoft.Web/publishingusers/Read | Pobierz publikowanie użytkowników. |
> | Akcja | Microsoft.Web/publishingusers/Write | Aktualizacja publikowania użytkowników. |
> | Akcja | Microsoft.Web/recommendations/Read | Pobierz listę zaleceń dotyczących subskrypcji. |
> | Akcja | Microsoft.Web/Register/Action | Rejestrowanie dostawcy zasobów Microsoft.Web dla subskrypcji. |
> | Akcja | Microsoft.Web/resourcehealthmetadata/Read | Pobranie metadanych kondycji zasobów. |
> | Akcja | Microsoft.Web/serverfarms/Capabilities/Read | Skorzystaj z możliwości planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/Delete | Usuń istniejący Plan usługi App Service |
> | Akcja | Microsoft.Web/serverfarms/firstpartyapps/Settings/DELETE | Usuń ustawienia aplikacje firm pierwszy planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/firstpartyapps/Settings/Read | Pobierz ustawienia aplikacje firm pierwszy planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/firstpartyapps/Settings/Write | Zaktualizuj ustawienia aplikacje firm pierwszy planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/DELETE | Usuwanie aplikacji usługi planów hybrydowego połączenia przestrzenie nazw przekaźników. |
> | Akcja | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Read | Pobierz usługi aplikacji planów hybrydowego połączenia przestrzenie nazw przekaźników. |
> | Akcja | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Sites/Read | Pobierz hybrydowego planów usługi aplikacji połączenia aplikacji sieci Web przekaźników przestrzeni nazw. |
> | Akcja | Microsoft.Web/serverfarms/hybridconnectionplanlimits/Read | Pobiera limity planu połączeń hybrydowych planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/hybridconnectionrelays/Read | Pobierz przekaźników połączenia hybrydowe planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/metricdefinitions/Read | Pobierz definicje metryki planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/Metrics/Read | Pobieraj metryki planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/operationresults/Read | Pobierz wyniki operacji planów usługi aplikacji. |
> | Akcja | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | Pobiera dostępne metryki dla planu usługi App Service |
> | Akcja | Microsoft.Web/serverfarms/Read | Pobiera właściwości planu usługi App Service |
> | Akcja | Microsoft.Web/serverfarms/restartSites/Action | Uruchom ponownie wszystkie aplikacje sieci Web w planie usługi aplikacji |
> | Akcja | Microsoft.Web/serverfarms/Sites/Read | Pobierz aplikacje sieci Web planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/skus/Read | Pobierz jednostki SKU planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/Usages/Read | Pobierz użycia planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/virtualnetworkconnections/Gateways/Write | Zaktualizuj bramy połączenia wirtualnej sieci planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/virtualnetworkconnections/Read | Pobrać połączeń sieci wirtualnej planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/virtualnetworkconnections/Routes/DELETE | Usuń trasy połączenia wirtualnej sieci planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/virtualnetworkconnections/Routes/Read | Pobierz trasy połączenia wirtualnej sieci planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/virtualnetworkconnections/Routes/Write | Aktualizowanie tras połączenia wirtualnej sieci planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/workers/reboot/Action | Ponowny rozruch pracowników planów usługi aplikacji. |
> | Akcja | Microsoft.Web/serverfarms/Write | Tworzenie planu usługi aplikacji lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Web/Sites/analyzecustomhostname/Read | Przeanalizuj niestandardową nazwę hosta. |
> | Akcja | Microsoft.Web/sites/applySlotConfig/Action | Zastosuj konfigurację sieci web aplikacji gniazda z miejsca docelowego w bieżącej aplikacji sieci web |
> | Akcja | Microsoft.Web/sites/backup/Action | Utwórz nową kopię zapasową aplikacji sieci web |
> | Akcja | Microsoft.Web/Sites/Backup/Read | Pobierz kopii zapasowej aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Backup/Write | Aktualizacja kopii zapasowej aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Backups/DELETE | Usunąć kopie zapasowe aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Backups/list/Action | Lista kopie zapasowe aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/backups/Read | Pobierz właściwości kopii zapasowej aplikacji sieci web |
> | Akcja | Microsoft.Web/Sites/Backups/Restore/Action | Przywracać kopie zapasowe aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/config/DELETE | Usuwanie konfiguracji aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/config/list/Action | Wyświetl listę aplikacji sieci Web poufne ustawienia zabezpieczeń, takie jak publikowania poświadczeń, ustawienia aplikacji i parametry połączenia |
> | Akcja | Microsoft.Web/sites/config/Read | Pobierz ustawienia konfiguracji aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/config/Write | Zaktualizuj ustawienia konfiguracji aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/continuouswebjobs/DELETE | Usuń zadania ciągłego sieci Web aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/continuouswebjobs/Read | Pobierz zadania ciągłego sieci Web aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/continuouswebjobs/Start/Action | Rozpocznij zadania ciągłego sieci Web aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/continuouswebjobs/Stop/Action | Zatrzymanie zadania ciągłego sieci Web aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/Delete | Usuń istniejącą aplikację sieci Web |
> | Akcja | Microsoft.Web/Sites/Deployments/DELETE | Usunięcie wdrożenia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Deployments/log/Read | Pobierz dziennik wdrożenia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Deployments/Read | Pobierz wdrożenia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Deployments/Write | Aktualizacji wdrożenia aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/analyses/execute/Action | Uruchom analizę diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/analyses/Read | Pobierz analizy diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/aspnetcore/Read | Pobierz diagnostyki aplikacji sieci Web dla aplikacji platformy ASP.NET Core. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/autoheal/Read | Pobierz funkcja Autoheal diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/Deployment/Read | Pobierz wdrożenia diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/Deployments/Read | Pobierz wdrożeń diagnostyki aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/diagnostics/detectors/execute/Action | Uruchom detektora diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/detectors/Read | Pobierz detektora diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/failedrequestsperuri/Read | Pobierz żądań sieci Web diagnostyki aplikacji nie powiodło się dla identyfikatora Uri. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/frebanalysis/Read | Pobierz analizy FREB diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/loganalyzer/Read | Pobierz analizatora dziennika diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/Read | Pobierz kategorii diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/runtimeavailability/Read | Pobierz dostępności środowiska uruchomieniowego diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/servicehealth/Read | Pobierz kondycję usługi sieci Web diagnostyki aplikacji. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/sitecpuanalysis/Read | Pobierz analizy Procesora witryny diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/sitecrashes/Read | Pobierz awarii lokacji diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/sitelatency/Read | Pobierz czas oczekiwania witryny diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/sitememoryanalysis/Read | Pobierz analizy pamięci lokacji diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/siterestartsettingupdate/Read | Uzyskiwanie aktualizacji ustawienia ponownego uruchomienia witryny diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/siterestartuserinitiated/Read | Pobierz sieci Web diagnostyki aplikacji witryny inicjowane przez ponowne uruchomienie użytkownika. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/siteswap/Read | Pobierz wymiany lokacji diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/ThreadCount/Read | Pobierz liczbę wątków diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/workeravailability/Read | Pobierz Workeravailability diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Diagnostics/workerprocessrecycle/Read | Pobierz odtworzenia procesu roboczego diagnostyki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/domainownershipidentifiers/Read | Pobierz identyfikatory własność domeny aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/domainownershipidentifiers/Write | Zaktualizuj identyfikatory własność domeny aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/Action | Funkcje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/DELETE | Usuń funkcje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/listsecrets/Action | Listy kluczy tajnych funkcje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/masterkey/Read | Pobierz umożliwia funkcje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/Read | Pobierz funkcje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Functions/token/Read | Token funkcje aplikacji sieci Web GET. |
> | Akcja | Microsoft.Web/Sites/Functions/Write | Zaktualizuj funkcje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hostnamebindings/DELETE | Usuń powiązania nazwę hosta aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hostnamebindings/Read | Pobierz powiązania nazwę hosta aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hostnamebindings/Write | Zaktualizuj powiązania nazwę hosta aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnection/DELETE | Usuwanie połączenia hybrydowego aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnection/Read | Pobierz dane o połączeniu hybrydowych aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnection/Write | Zaktualizuj połączenie hybrydowe aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/DELETE | Usuń przekaźników przestrzenie nazw połączeń hybrydowych aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/listkeys/Action | Lista kluczy sieci Web aplikacji hybrydowych połączenia przestrzenie nazw przekaźników. |
> | Akcja | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Pobierz przekaźników przestrzenie nazw połączeń hybrydowych aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Write | Zaktualizuj przekaźników przestrzenie nazw połączeń hybrydowych aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/hybridconnectionrelays/Read | Pobierz przekaźników połączeń hybrydowych aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/Deployments/DELETE | Usunięcie wdrożeń wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/Deployments/Read | Pobierz wdrożeń wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/Extensions/log/Read | Pobierz dziennik rozszerzeń wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/Extensions/Read | Pobierz rozszerzenia wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/processes/DELETE | Usuń procesów wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/processes/Read | Pobierz procesów wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/instances/Read | Pobierz wystąpienia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/listsyncfunctiontriggerstatus/Action | Lista synchronizacji funkcja wyzwalacz stanu aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/metricdefinitions/Read | Pobierz definicje Metryka aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Metrics/Read | Pobieraj metryki aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/metricsdefinitions/Read | Pobierz definicje metryk aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/migratemysql/Action | Przeprowadź migrację aplikacji sieci Web MySql. |
> | Akcja | Microsoft.Web/Sites/migratemysql/Read | Get Web Apps Migrate MySql. |
> | Akcja | Microsoft.Web/Sites/networktrace/Action | Aplikacje sieci Web śledzenia sieci. |
> | Akcja | Microsoft.Web/Sites/newpassword/Action | Aplikacje sieci Web NoweHasło. |
> | Akcja | Microsoft.Web/Sites/operationresults/Read | Pobierz wyniki operacji aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Operations/Read | Pobierz operacje aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/perfcounters/Read | Pobierz liczniki wydajności aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/premieraddons/DELETE | Usuń dodatków Premier aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/premieraddons/Read | Pobierz dodatki Premier aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/premieraddons/Write | Aktualizowanie dodatków Premier aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/processes/Read | Pobierz procesów aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | Pobiera dostępne metryki dla aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/publiccertificates/DELETE | Usuń certyfikaty publiczne aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/publiccertificates/Read | Pobierz certyfikaty publiczne aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/publiccertificates/Write | Aktualizuj certyfikaty publiczne aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/publish/Action | Publikowanie aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/publishxml/Action | Pobierz publikowania format xml profilu dla aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/publishxml/Read | Pobierz aplikacje sieci Web publikowanie XML. |
> | Akcja | Microsoft.Web/sites/Read | Pobierz właściwości aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/recommendationhistory/Read | Pobierz historię zalecenie aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/recommendations/disable/Action | Wyłącz zalecenia dotyczące aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/recommendations/Read | Pobierz listę zaleceń dotyczących aplikacji sieci web. |
> | Akcja | Microsoft.Web/Sites/Recover/Action | Odzyskiwanie aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/resetSlotConfig/Action | Zresetuj konfigurację aplikacji sieci web |
> | Akcja | Microsoft.Web/Sites/resourcehealthmetadata/Read | Pobranie metadanych kondycji zasobów aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/restart/Action | Ponowne uruchomienie aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Restore/Read | Pobierz przywracania aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Restore/Write | Przywrócenie aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/restoresnapshot/Action | Przywracaj migawek aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/siteextensions/DELETE | Usuwanie rozszerzenia aplikacji witryny sieci Web. |
> | Akcja | Microsoft.Web/Sites/siteextensions/Read | Pobierz rozszerzenia aplikacji witryny sieci Web. |
> | Akcja | Microsoft.Web/Sites/siteextensions/Write | Należy zaktualizować rozszerzenia aplikacji witryny sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/analyzecustomhostname/Read | Pobierz Web miejsc aplikacji analizowanie niestandardową nazwę hosta. |
> | Akcja | Microsoft.Web/sites/slots/applySlotConfig/Action | Zastosuj konfigurację sieci web aplikacji gniazda z miejsca docelowego do bieżącego miejsca. |
> | Akcja | Microsoft.Web/sites/slots/backup/Action | Utwórz nową kopię zapasową miejsca aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Backup/Read | Pobierz kopię zapasową miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Backup/Write | Zaktualizuj kopii zapasowej miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Backups/DELETE | Usuwa miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Backups/list/Action | Lista sieci Web aplikacji miejsc wykonywanie kopii zapasowych. |
> | Akcja | Microsoft.Web/sites/slots/backups/Read | Pobierz właściwości kopii zapasowej miejsc aplikacji sieci web |
> | Akcja | Microsoft.Web/Sites/Slots/Backups/Restore/Action | Przywracanie kopii zapasowych miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/config/DELETE | Usuwanie konfiguracji miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/config/list/Action | Lista miejsca aplikacji sieci Web poufne ustawienia zabezpieczeń, takie jak publikowania poświadczeń, ustawienia aplikacji i parametry połączenia |
> | Akcja | Microsoft.Web/sites/slots/config/Read | Pobierz ustawienia konfiguracji miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/config/Write | Aktualizowanie ustawień konfiguracji miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/continuouswebjobs/DELETE | Usuń zadania ciągłego Web miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/continuouswebjobs/Read | Pobierz zadania ciągłego Web miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/continuouswebjobs/Start/Action | Uruchom zadania ciągłego Web miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/continuouswebjobs/Stop/Action | Zatrzymanie zadania ciągłego Web miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/Delete | Usuwanie istniejącego miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/Deployments/DELETE | Usunięcie wdrożeń miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Deployments/log/Read | Pobierz dziennik wdrożeń miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Deployments/Read | Pobierz wdrożeń miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Deployments/Write | Zaktualizuj wdrożeń miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Uruchom analizę diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/analyses/Read | Pobierz analizy diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/aspnetcore/Read | Pobierz diagnostyki miejsc aplikacji sieci Web dla aplikacji platformy ASP.NET Core. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/autoheal/Read | Pobierz funkcja Web Apps miejsc diagnostyki Autoheal. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/Deployment/Read | Pobierz wdrażaniem diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/Deployments/Read | Pobierz wdrożeń diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Uruchom detektora diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/detectors/Read | Pobierz detektora diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/frebanalysis/Read | Pobierz Web Apps miejsc diagnostyki FREB analizy. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/loganalyzer/Read | Pobierz sieci Web Apps miejsc diagnostyki dziennika analizatora. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/Read | Pobierz diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/runtimeavailability/Read | Pobierz sieci Web Apps miejsc diagnostyki środowiska uruchomieniowego dostępności. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/servicehealth/Read | Pobierz kondycja usługi diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/sitecpuanalysis/Read | Pobierz analizy Procesora witryny diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/sitecrashes/Read | Pobierz sieci Web Apps miejsc diagnostyki lokacji awarii. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/sitelatency/Read | Pobierz czas oczekiwania witryny diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/sitememoryanalysis/Read | Pobierz analizy pamięci lokacji diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/siterestartsettingupdate/Read | Pobierz miejsc aplikacji sieci Web diagnostyki aktualizacji ustawienie ponownego uruchomienia witryny. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/siterestartuserinitiated/Read | Pobierz inicjowane przez sieci Web aplikacji miejsc diagnostyki lokacji ponownego uruchomienia komputera użytkownika. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/siteswap/Read | Pobierz zamiany diagnostyki gniazda sieci Web aplikacji w lokacji. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/ThreadCount/Read | Pobierz liczbę wątków diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/workeravailability/Read | Pobierz Workeravailability diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Diagnostics/workerprocessrecycle/Read | Pobierz odtworzenia procesu roboczego diagnostyki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/domainownershipidentifiers/Read | Pobierz sieci Web Apps miejsc domeny własność identyfikatorów. |
> | Akcja | Microsoft.Web/Sites/Slots/hostnamebindings/DELETE | Usuń powiązań z nazwami hostów miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/hostnamebindings/Read | Pobierz powiązań z nazwami hostów miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/hostnamebindings/Write | Zaktualizować powiązań z nazwami hostów miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/hybridconnection/DELETE | Usuwanie połączenia hybrydowego miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/hybridconnection/Read | Pobierz połączenia hybrydowego miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/hybridconnection/Write | Zaktualizuj połączenie hybrydowe miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/hybridconnectionnamespaces/relays/DELETE | Usuwanie sieci Web Apps miejsc hybrydowego połączenia przestrzenie nazw przekaźników. |
> | Akcja | Microsoft.Web/Sites/Slots/hybridconnectionnamespaces/relays/Write | Zaktualizuj sieci Web Apps miejsc hybrydowego połączenia przestrzenie nazw przekaźników. |
> | Akcja | Microsoft.Web/Sites/Slots/hybridconnectionrelays/Read | Pobierz sieci Web Apps miejsc hybrydowego połączenia przekaźników. |
> | Akcja | Microsoft.Web/Sites/Slots/instances/Deployments/Read | Pobierz wdrożeń wystąpień miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/instances/processes/DELETE | Usuń procesów wystąpień miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/instances/processes/Read | Pobierz procesów wystąpień miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/instances/Read | Pobierz wystąpienia miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/metricdefinitions/Read | Pobierz definicje metryk miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Metrics/Read | Pobieraj metryki miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/migratemysql/Read | Pobierz Web miejsc aplikacji migracji MySql. |
> | Akcja | Microsoft.Web/Sites/Slots/networktrace/Action | Miejsc aplikacji sieci Web śledzenia sieci. |
> | Akcja | Microsoft.Web/Sites/Slots/newpassword/Action | Miejsc aplikacji sieci Web NoweHasło. |
> | Akcja | Microsoft.Web/Sites/Slots/operationresults/Read | Pobierz wyniki operacji miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Operations/Read | Pobierz operacje miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/perfcounters/Read | Pobierz liczniki wydajności miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/phplogging/Read | Pobierz Phplogging miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/premieraddons/DELETE | Usuń dodatków Premier miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/premieraddons/Read | Pobierz dodatki Premier miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/premieraddons/Write | Zaktualizuj dodatków Premier miejsc aplikacji sieci Web. |
> | Akcja | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | Pobiera ustawienie diagnostyczne dla zasobu |
> | Akcja | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu |
> | Akcja | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | Pobiera dostępne metryki dla miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/publiccertificates/Read | Pobierz certyfikaty publiczne miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/publiccertificates/Write | Utwórz lub zaktualizuj certyfikaty publiczne miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/publish/Action | Publikowanie miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/publishxml/Action | Pobierz publikowanie format xml profilu dla miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/Read | Pobierz właściwości miejsce wdrożenia aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/Recover/Action | Odzyskiwanie miejsca aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/resetSlotConfig/Action | Zresetuj konfigurację miejsca aplikacji sieci web |
> | Akcja | Microsoft.Web/Sites/Slots/resourcehealthmetadata/Read | Pobierz metadane kondycji zasobu miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/restart/Action | Uruchom ponownie miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/Restore/Read | Pobierz przywracania miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Restore/Write | Przywrócić miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/restoresnapshot/Action | Przywracaj migawek miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/siteextensions/DELETE | Usuwanie rozszerzenia lokacji miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/siteextensions/Read | Pobierz rozszerzenia lokacji miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/siteextensions/Write | Aktualizacja rozszerzeń witryny miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/slotsdiffs/Action | Pobierz różnic w konfiguracji aplikacji sieci web i gniazd |
> | Akcja | Microsoft.Web/sites/slots/slotsswap/Action | Zamienić miejsc wdrażania aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/snapshots/Read | Pobrać migawek miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/sourcecontrols/Read | Pobierz ustawienia konfiguracji miejsca aplikacji sieci Web do kontroli źródła |
> | Akcja | Microsoft.Web/sites/slots/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/start/Action | Uruchom miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/slots/stop/Action | Zatrzymaj miejsca aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Slots/Sync/Action | Synchronizacja miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/triggeredwebjobs/DELETE | Usuwanie zadań Webjob wyzwalanych miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/triggeredwebjobs/Read | Pobrania zadań Webjob wyzwalanych miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/triggeredwebjobs/Run/Action | Uruchamianie zadań Webjob wyzwalanych miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/Usages/Read | Pobierz użycia miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/virtualnetworkconnections/DELETE | Usuwanie połączenia wirtualnej sieci miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/virtualnetworkconnections/Gateways/Write | Zaktualizuj bramy połączeń sieci wirtualnej miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/virtualnetworkconnections/Read | Pobierz połączenia wirtualnej sieci miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/virtualnetworkconnections/Write | Zaktualizuj połączenia wirtualnych sieci miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Slots/webjobs/Read | Uzyskać zadań Webjob miejsc aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/slots/Write | Utwórz nowe miejsce aplikacji sieci Web lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Web/sites/slotsdiffs/Action | Pobierz różnic w konfiguracji aplikacji sieci web i gniazd |
> | Akcja | Microsoft.Web/sites/slotsswap/Action | Zamienić miejsc wdrażania aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/snapshots/Read | Pobrać migawek aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/sourcecontrols/Delete | Usuń ustawienia konfiguracji kontroli źródła dla aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/sourcecontrols/Read | Pobierz ustawienia konfiguracji aplikacji sieci Web do kontroli źródła |
> | Akcja | Microsoft.Web/sites/sourcecontrols/Write | Zaktualizuj ustawienia konfiguracji kontroli źródła dla aplikacji sieci Web |
> | Akcja | Microsoft.Web/sites/start/Action | Uruchamiają aplikację sieci Web |
> | Akcja | Microsoft.Web/sites/stop/Action | Zatrzymać aplikacji sieci Web |
> | Akcja | Microsoft.Web/Sites/Sync/Action | Aplikacje sieci Web synchronizacji. |
> | Akcja | Microsoft.Web/Sites/syncfunctiontriggers/Action | Wyzwalacze funkcja synchronizacji dla aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/triggeredwebjobs/DELETE | Usuń wyzwalanych zadań Webjob aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/triggeredwebjobs/history/Read | Pobierz historię zadań Webjob wyzwalanych aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/triggeredwebjobs/Read | Pobierz wyzwalanych zadań Webjob aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/triggeredwebjobs/Run/Action | Uruchom wyzwalanych zadań Webjob aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/Usages/Read | Pobierz użycia aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/virtualnetworkconnections/DELETE | Usuwanie połączenia wirtualnej sieci aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/virtualnetworkconnections/Gateways/Read | Pobierz bram połączeń sieci wirtualnej aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/virtualnetworkconnections/Gateways/Write | Zaktualizuj bramy połączenia wirtualnej sieci aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/virtualnetworkconnections/Read | Pobrać połączeń sieci wirtualnej aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/virtualnetworkconnections/Write | Zaktualizuj połączenia wirtualnej sieci aplikacji sieci Web. |
> | Akcja | Microsoft.Web/Sites/webjobs/Read | Pobrania zadań Webjob aplikacji sieci Web. |
> | Akcja | Microsoft.Web/sites/Write | Utwórz nową aplikację sieci Web lub zaktualizuj istniejącą |
> | Akcja | Microsoft.Web/skus/Read | Pobierz jednostki SKU. |
> | Akcja | Microsoft.Web/sourcecontrols/Read | Pobierz kontroli źródła. |
> | Akcja | Microsoft.Web/sourcecontrols/Write | Zaktualizuj kontrolki źródła. |
> | Akcja | Microsoft.Web/unregister/Action | Wyrejestruj Microsoft.Web dostawcy zasobów dla subskrypcji. |
> | Akcja | Microsoft.Web/Validate/Action | Sprawdzanie poprawności. |
> | Akcja | Microsoft.Web/verifyhostingenvironmentvnet/Action | Sprawdź Hosting sieci wirtualnej środowiska. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akcji | Operacja | Opis |
> | --- | --- | --- |
> | Akcja | Microsoft.WorkloadMonitor/components/read | Zasoby operacje odczytu |
> | Akcja | Microsoft.WorkloadMonitor/healthInstances/read | Zasoby operacje odczytu |
> | Akcja | Microsoft.WorkloadMonitor/Operations/read | Zasoby operacje odczytu |
> | Akcja | Microsoft.WorkloadMonitor/workloads/delete | Usuwa zasób obciążenia |
> | Akcja | Microsoft.WorkloadMonitor/workloads/read | Odczytuje zasób obciążenia |
> | Akcja | Microsoft.WorkloadMonitor/workloads/write | Zapisuje zasobów obciążenia |

## <a name="next-steps"></a>Kolejne kroki

- [Role niestandardowe](custom-roles.md)
- [Role wbudowane](built-in-roles.md)
