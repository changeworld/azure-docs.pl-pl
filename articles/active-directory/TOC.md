# [Dokumentacja usługi Azure Active Directory](index.md)

# Omówienie
## [Co to jest usługa Azure Active Directory?](fundamentals/active-directory-whatis.md)
## [Informacje o zarządzaniu tożsamościami na platformie Azure](fundamentals/identity-fundamentals.md)
## [Omówienie rozwiązań do obsługi tożsamości na platformie Azure](fundamentals/understand-azure-identity-solutions.md)
## [Wybieranie rozwiązania tożsamości hybrydowej](choose-hybrid-identity-solution.md)
## [Kojarzenie subskrypcji usługi Azure](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Zagadnienia dotyczące rezydencji i danych](fundamentals/active-directory-data-storage-eu.md)
## [Często zadawane pytania](fundamentals/active-directory-faq.md)
## [Co nowego](fundamentals/whats-new.md)


# Rozpoczęcie pracy
## [Rozpoczynanie pracy z usługą Azure AD](fundamentals/get-started-azure-ad.md)
## [Tworzenie konta w usłudze Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Dodawanie niestandardowej nazwy domeny](fundamentals/add-custom-domain.md)
## [Configure company branding (Konfigurowanie oznaczenia marką firmy)](fundamentals/customize-branding.md)
## [Dodawanie użytkowników do usługi Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Assign licenses to users (Przypisywanie licencji do użytkowników)](fundamentals/license-users-groups.md)
## [Configure Self-service password reset (Konfigurowanie samoobsługowego resetowania haseł)](authentication/quickstart-sspr.md)
## [Dodawanie informacji o ochronie prywatności w organizacji do usługi Azure AD](active-directory-properties-area.md)


# Instrukcje
## Planowanie i projektowanie
### [Omówienie architektury usługi Azure AD](fundamentals/active-directory-architecture.md)
### [Mapowanie oświadczeń w usłudze Azure Active Directory](active-directory-claims-mapping.md)
### [Wdrażanie rozwiązania tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-overview.md)
#### Określanie wymagań
##### [Tożsamość](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Synchronizacja katalogów](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Uwierzytelnianie wieloskładnikowe](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategia cyklu życia tożsamości](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planowanie zabezpieczeń danych](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Ochrona danych](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Zarządzanie zawartością](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Kontrola dostępu](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Reagowanie na zdarzenia](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planowanie cyklu życia tożsamości
##### [Zadania](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Strategia wdrażania](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Następne kroki](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Porównanie narzędzi](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Zarządzanie użytkownikami
### [Dodawanie nowych użytkowników do usługi Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Zarządzanie profilami użytkowników](fundamentals/active-directory-users-profile-azure-portal.md)
### [Resetowanie haseł użytkowników](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Udostępnianie kont](active-directory-sharing-accounts.md)
### [Przypisywanie użytkowników do ról administratorów](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Przywracanie usuniętego użytkownika](fundamentals/active-directory-users-restore.md)
### [Dodawanie użytkowników-gości z innego katalogu (B2B)](b2b/what-is-b2b.md)
#### [Administratorzy dodający użytkowników B2B](b2b/add-users-administrator.md)
#### [Pracownicy przetwarzający informacje dodający użytkowników B2B](b2b/add-users-information-worker.md)
#### [Interfejs API i dostosowywanie](b2b/customize-invitation-api.md)
#### [Przykłady kodu i programu Azure PowerShell](b2b/code-samples.md)
#### [Przykład portalu do samodzielnej rejestracji](b2b/self-service-portal.md)
#### [Wiadomość e-mail z zaproszeniem](b2b/invitation-email-elements.md)
#### [Realizacja zaproszenia](b2b/redemption-experience.md)
#### [Add B2B users without an invitation (Dodawanie użytkowników B2B bez zaproszenia)](b2b/add-user-without-invite.md)
#### [Zezwalanie na zaproszenia lub blokowanie ich](b2b/allow-deny-list.md)
#### [Dostęp warunkowy dla modelu B2B](b2b/conditional-access.md)
#### [Zasady udostępniania B2B](b2b/delegate-invitations.md)
#### [Dodawanie użytkownika B2B do roli](b2b/add-guest-to-role.md)
#### [Grupy dynamiczne i użytkownicy B2B](b2b/use-dynamic-groups.md)
#### [Opuszczanie organizacji](b2b/leave-the-organization.md)
#### [Inspekcja i raporty](b2b/auditing-and-reporting.md)
#### [B2B dla organizacji hybrydowych](b2b/hybrid-organizations.md)
##### [Udzielanie użytkownikom B2B dostępu do aplikacji lokalnych](b2b/hybrid-cloud-to-on-premises.md)
##### [Udzielanie użytkownikom lokalnym dostępu do aplikacji w chmurze](b2b/hybrid-on-premises-to-cloud.md)
#### [B2B i udostępnianie zewnętrzne w usłudze Office 365](b2b/o365-external-user.md)
#### [Licencjonowanie B2B](b2b/licensing-guidance.md)
#### [Bieżące ograniczenia](b2b/current-limitations.md)
#### [Często zadawane pytania](b2b/faq.md)
#### [Rozwiązywanie problemów dotyczących B2B](b2b/troubleshoot.md)
#### [Informacje o użytkowniku B2B](b2b/user-properties.md)
#### [Token użytkownika B2B](b2b/user-token.md)
#### [Aplikacje zintegrowane B2B dla usługi Azure AD](b2b/configure-saas-apps.md)
#### [Mapowanie oświadczeń użytkowników B2B](b2b/claims-mapping.md)
#### [Porównanie współpracy B2B do modelu B2C](b2b/compare-with-b2c.md)
#### [Uzyskiwanie pomocy technicznej dla modelu B2B](b2b/get-support.md)

## [Zarządzanie grupami i członkami](fundamentals/active-directory-manage-groups.md)
### Zarządzanie grupami
#### [Azure Portal](fundamentals/active-directory-groups-create-azure-portal.md)
#### [Moduł Azure AD PowerShell dla programu Graph (wersja )](users-groups-roles/groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](users-groups-roles/groups-settings-cmdlets.md)
### [Zarządzanie członkami grup](fundamentals/active-directory-groups-members-azure-portal.md)
### [Zarządzenie właścicielami grup](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [Zarządzanie członkostwem w grupie](fundamentals/active-directory-groups-membership-azure-portal.md)
### [Przypisywanie licencji za pomocą grup](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [Przypisywanie licencji do grupy](users-groups-roles/licensing-groups-assign.md)
#### [Wykrywanie i rozwiązywanie problemów z licencją w grupie](users-groups-roles/licensing-groups-resolve-problems.md)
#### [Migrowanie użytkowników z licencjami indywidualnymi do licencji opartych na grupach](users-groups-roles/licensing-groups-migrate-users.md)
#### [Migrowanie użytkowników między licencjami produktów](users-groups-roles/licensing-groups-change-licenses.md)
#### [Dodatkowe scenariusze dotyczące licencjonowania opartego na grupach](users-groups-roles/licensing-group-advanced.md)
#### [Przykłady programu Azure PowerShell dla licencjonowania opartego na grupach](users-groups-roles/licensing-ps-examples.md)
#### [Dokumentacja produktów i planów usług w usłudze Azure AD](users-groups-roles/licensing-service-plan-reference.md)
### [Ustawianie wygasania grup usługi Office 365](users-groups-roles/groups-lifecycle.md)
### [Enforce a naming policy for groups (Wymuszanie zasad nazewnictwa dla grup)](users-groups-roles/groups-naming-policy.md)
### [Wyświetlanie wszystkich grup](fundamentals/active-directory-groups-view-azure-portal.md)
### [Dodawanie dostępu grupowego do aplikacji SaaS](users-groups-roles/groups-saasapps.md)
### [Przywracanie usuniętej grupy usługi Office 365](fundamentals/active-directory-groups-restore-azure-portal.md)
### [Zarządzanie ustawieniami grup](fundamentals/active-directory-groups-settings-azure-portal.md)
### [Konfigurowanie grup samoobsługi](users-groups-roles/groups-self-service-management.md)
### Grupy dynamiczne
#### [Tworzenie grupy dynamicznej](users-groups-roles/groups-create-rule.md)
#### [Właściwości i składnia reguły](users-groups-roles/groups-dynamic-membership.md)
#### [Zmienianie typu członkostwa grupy](users-groups-roles/groups-change-type.md)
#### [Rozwiązywanie problemów](users-groups-roles/groups-troubleshooting.md)

## [Zarządzanie raportami](active-directory-reporting-azure-portal.md)
### [Działania logowania](active-directory-reporting-activity-sign-ins.md)
### [Działania inspekcji](active-directory-reporting-activity-audit-logs.md)
### [Narażeni użytkownicy](active-directory-reporting-security-user-at-risk.md)
### [Ryzykowne logowania](active-directory-reporting-security-risky-sign-ins.md)
### [Zdarzenia ryzyka](active-directory-reporting-risk-events.md)
### [Monitorowanie dzienników przy użyciu usługi Azure Monitor](reporting-azure-monitor-diagnostics-overview.md)
### [Często zadawane pytania](active-directory-reporting-faq.md)

### Zadania
#### [Konfigurowanie nazwanych lokalizacji](active-directory-named-locations.md)
#### [Znajdowanie raportów aktywności](active-directory-reporting-migration.md)
#### [Używanie pakietu zawartości Azure AD Power BI](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Korygowanie użytkowników oflagowanych w związku z ryzykiem](active-directory-report-security-user-at-risk-remediation.md)
#### [Kierowanie dzienników aktywności do Centrum zdarzeń platformy Azure](reporting-azure-monitor-diagnostics-azure-event-hub.md)
#### [Archiwizowanie dzienników aktywności na koncie usługi Azure Storage](reporting-azure-monitor-diagnostics-azure-storage-account.md)
#### [Integrowanie dzienników aktywności z rozwiązaniem Splunk przy użyciu usługi Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md)

### Dokumentacja
#### [Przechowywanie](active-directory-reporting-retention.md)
#### [Czasy oczekiwania](active-directory-reporting-latencies-azure-portal.md)
#### [Dokumentacja działań inspekcji](active-directory-reporting-activity-audit-reference.md)
#### [Sign-in activity error codes (Kody błędów działań logowania)](active-directory-reporting-activity-sign-ins-errors.md)
#### [Interpretowanie schematu dziennika inspekcji w usłudze Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
#### [Interpretowanie schematu dziennika logowania w usłudze Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)

### Rozwiązywanie problemów
#### [Brak danych inspekcji](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Brak danych do pobrania](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Błędy pakietu zawartości dzienników aktywności usługi Azure AD](active-directory-reporting-troubleshoot-content-pack.md)
#### [Błędy interfejsu API raportowania usługi Azure AD](active-directory-reporting-troubleshoot-graph-api.md)

### [Dostęp programowy](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Wymagania wstępne](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Przykłady inspekcji](active-directory-reporting-api-audit-samples.md)
#### [Przykłady logowania](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Używanie certyfikatów](active-directory-reporting-api-with-certificates.md)

## [Zarządzanie hasłami](authentication/concept-sspr-howitworks.md)
### Dokumenty użytkowników
#### [Resetowanie lub zmienianie hasła](user-help/active-directory-passwords-update-your-own-password.md)
#### [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](user-help/active-directory-passwords-reset-register.md)


## Zarządzanie urządzeniami
### [Omówienie](devices/overview.md)

### Przewodniki Szybki start
#### [Konfigurowanie urządzeń z systemem Windows 10 zarejestrowanych w usłudze Azure AD](user-help/device-management-azuread-registered-devices-windows10-setup.md)
#### [Konfigurowanie urządzeń przyłączonych do usługi Azure AD](user-help/device-management-azuread-joined-devices-setup.md)

### Samouczki
#### [Konfigurowanie przyłączania do hybrydowej usługi Azure AD dla domen zarządzanych](devices/hybrid-azuread-join-managed-domains.md)
#### [Konfigurowanie przyłączania do hybrydowej usługi Azure AD dla domen federacyjnych](devices/hybrid-azuread-join-federated-domains.md)
#### [Ręczne konfigurowanie przyłączania do hybrydowej usługi Azure AD](devices/hybrid-azuread-join-manual-steps.md)
#### [Konfigurowanie przyłączania do usługi Azure AD podczas pierwszego uruchomienia systemu Windows 10](devices/azuread-joined-devices-frx.md)

### Podręczniki z instrukcjami
#### [Planowanie przyłączenia do usługi Azure AD](devices/azureadjoin-plan.md)
#### [Planowanie implementacji przyłączania do hybrydowej usługi Azure AD](devices/hybrid-azuread-join-plan.md)
#### [Kontrolowanie przyłączania Twoich urządzeń do hybrydowej usługi Azure AD](devices/hybrid-azuread-join-control.md)
#### [Przypisywanie administratorów lokalnych do urządzeń przyłączonych do usługi Azure AD](devices/assign-local-admin.md)
#### [Rozwiązywanie problemów z urządzeniami z bieżącym systemem Windows przyłączonymi do hybrydowej usługi Azure AD](devices/troubleshoot-hybrid-join-windows-current.md)
#### [Rozwiązywanie problemów z urządzeniami ze starszym systemem Windows przyłączonymi do hybrydowej usługi Azure AD](devices/troubleshoot-hybrid-join-windows-legacy.md)

### Pojęcia
#### [Manage devices using the Azure portal (Zarządzanie urządzeniami przy użyciu witryny Azure Portal)](devices/device-management-azure-portal.md)
#### [Często zadawane pytania](devices/faq.md)

## Zarządzanie aplikacjami
### [Omówienie](manage-apps/what-is-application-management.md)
### [Rozpoczęcie pracy](manage-apps/plan-an-application-integration.md)
### [Samouczki integracji aplikacji SaaS](saas-apps/tutorial-list.md)

### [Aprowizacja i anulowanie aprowizacji użytkowników w aplikacjach SaaS](active-directory-saas-app-provisioning.md) 
#### [Samouczki integracji aplikacji](saas-apps/tutorial-list.md) 
#### [Automatyzowanie aprowizacji w aplikacjach obsługujących standard SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Dostosowywanie mapowań atrybutów](active-directory-saas-customizing-attribute-mappings.md) 
#### [Pisanie wyrażeń do mapowania atrybutów](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Korzystanie z filtrów określania zakresu](active-directory-saas-scoping-filters.md) 
#### [Raportowanie na temat automatycznej aprowizacji użytkowników](active-directory-saas-provisioning-reporting.md) 
#### [Rozwiązywanie problemów z aprowizowaniem użytkowników](active-directory-application-provisioning-content-map.md) 

### [Zdalny dostęp do aplikacji za pomocą serwera proxy aplikacji](manage-apps/application-proxy.md)
#### Rozpoczęcie pracy
##### [Włączanie serwera proxy aplikacji](manage-apps/application-proxy-enable.md)
##### [Publikowanie aplikacji](manage-apps/application-proxy-publish-azure-portal.md)
##### [Niestandardowe domeny](manage-apps/application-proxy-configure-custom-domain.md)
#### [Logowanie jednokrotne](manage-apps/application-proxy-single-sign-on.md)
##### [Logowanie jednokrotne z delegowaniem KCD](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [Logowanie jednokrotne z nagłówkami](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [Logowanie jednokrotne z przechowywaniem haseł w magazynie](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Pojęcia
##### [Łączniki](manage-apps/application-proxy-connectors.md)
##### [Bezpieczeństwo](manage-apps/application-proxy-security.md)
##### [Sieci](manage-apps/application-proxy-network-topology.md)


##### [Uaktualnienie z serwera TMG lub UAG](manage-apps/application-proxy-migration.md)

#### Konfiguracje zaawansowane
##### [Publikowanie w oddzielnych sieciach](manage-apps/application-proxy-connector-groups.md)
##### [Serwery proxy](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [Aplikacje obsługujące oświadczenia](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [Aplikacje klienta natywnego](manage-apps/application-proxy-configure-native-client-application.md)
##### [Instalacja dyskretna](manage-apps/application-proxy-register-connector-powershell.md)
##### [Niestandardowa strona główna](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Tłumaczenie linków w tekście](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [Symbole wieloznaczne](active-directory-application-proxy-wildcard.md)
##### [Usuwanie danych osobowych](manage-apps/application-proxy-remove-personal-data.md)


#### Przewodniki dotyczące publikowania
##### [Pulpit zdalny](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [Program PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Rozwiązywanie problemów](manage-apps/application-proxy-troubleshoot.md)

### Zarządzanie aplikacjami w przedsiębiorstwie
#### [Dodawanie aplikacji](manage-apps/add-application-portal.md)
#### [Wyświetlanie aplikacji w dzierżawie](manage-apps/view-applications-portal.md)
#### [Przypisywanie użytkowników](manage-apps/assign-user-or-group-access-portal.md)
#### [Dostosowywanie oznaczania marką firmy](manage-apps/change-name-or-logo-portal.md)
#### [Wyłączanie logowania użytkownika](manage-apps/disable-user-sign-in-portal.md)
#### [Usuwanie użytkowników](manage-apps/remove-user-or-group-access-portal.md)

#### [Zarządzanie aprowizacją konta użytkownika](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [Zarządzanie logowaniem jednokrotnym dla aplikacji przedsiębiorstwa](manage-apps/configure-single-sign-on-portal.md)
#### [Zaawansowane podpisywanie certyfikatów dla aplikacji SAML](manage-apps/certificate-signing-options.md)
#### [Ukrywanie aplikacji w środowisku użytkownika](manage-apps/hide-application-from-user-portal.md)
### [Konfigurowanie automatycznego przyspieszenia logowania za pomocą zasad HRD](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrate AD FS apps to Azure AD (Migracja aplikacji AD FS do usługi Azure AD)](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Zarządzanie dostępem do aplikacji](manage-apps/what-is-access-management.md)
#### [Dostęp z użyciem logowania jednokrotnego](manage-apps/what-is-single-sign-on.md)
#### [Certyfikaty rejestracji jednokrotnej](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Ograniczenia dzierżawy](manage-apps/tenant-restrictions.md)
#### [Aprowizacja użytkowników przy użyciu standardu SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)


### Rozwiązywanie problemów



#### Panel dostępu
##### [Aplikacja nie jest wyświetlana](application-access-panel-unexpected-application-not-appearing.md)
##### [Nieoczekiwane wyświetlanie aplikacji](application-access-panel-unexpected-application-appears.md)
##### [Nie można się zalogować](application-access-panel-web-sign-in-problem.md)
##### [Błąd podczas instalacji rozszerzenia przeglądarki](application-access-panel-extension-problem-installing.md)
##### [Jak używać dostępu do aplikacji samoobsługowej](application-access-panel-self-service-applications-how-to.md)
##### [Błąd podczas używania dostępu do aplikacji samoobsługowej](application-access-panel-self-service-applications-problem.md)

#### Dodawanie aplikacji
##### [Wybieranie typu aplikacji](application-config-add-app-problem-how-to-choose-application-type.md)
##### [Typowe problemy — aplikacje z galerii](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [Typowe problemy — aplikacje spoza galerii](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### Serwer proxy aplikacji
##### [Problem z wyświetlaniem strony aplikacji](application-proxy-page-appearance-broken-problem.md)
##### [Ładowanie aplikacji trwa zbyt długo](application-proxy-page-load-speed-problem.md)
##### [Linki na stronie aplikacji nie działają](application-proxy-page-links-broken-problem.md)
##### [Jakie porty należy otworzyć dla mojej aplikacji](application-proxy-connectivity-ports-how-to.md)
##### [Brak działającego łącznika w grupie łączników mojej aplikacji](application-proxy-connectivity-no-working-connector.md)
##### [Konfigurowanie w portalu administracyjnym](application-proxy-config-how-to.md)
##### [Konfigurowanie logowania jednokrotnego w mojej aplikacji](application-proxy-config-sso-how-to.md)
##### [Problem podczas tworzenia aplikacji w portalu administracyjnym](application-proxy-config-problem.md)
##### [Konfigurowanie ograniczonego delegowania protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Konfigurowanie przy użyciu usługi PingAccess](application-proxy-back-end-ping-access-how-to.md)
##### [Błąd „Can't Access this Corporate Application” (Nie można uzyskać dostępu do tej aplikacji firmowej)](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Problem z instalacją łącznika agenta serwera proxy aplikacji](application-proxy-connector-installation-problem.md)


#### Rejestracja aplikacji
##### [Wprowadzanie pól obiektu aplikacji](application-dev-registration-config-specific-application-property-how-to.md)
##### [Zmienianie ustawień domyślnych okresu istnienia tokenu](application-dev-registration-config-change-token-lifetime-how-to.md)

#### Authentication
##### [Konfigurowanie punktów końcowych](application-dev-registration-config-how-to.md)

#### Dostęp warunkowy
##### [Klient nie spełnił wymagań wstępnych dotyczących rejestracji urządzeń](active-directory-conditional-access.md)
##### [Dzierżawa jest blokowana z powodu niepoprawnego ustawienia zasad dostępu warunkowego](active-directory-conditional-access-device-remediation.md)
##### [Jak i kiedy działają reguły poza siecią firmową?](https://aka.ms/calocation)
##### [Jak zwiększyć liczbę urządzeń, które użytkownik może zarejestrować w usłudze Azure AD?](active-directory-azureadjoin-setup.md)
##### [Jak skonfigurować dostęp warunkowy dla usługi Exchange Online?](https://aka.ms/csforexchange)
##### [Jak skonfigurować dostęp warunkowy dla urządzeń z systemem Windows 7?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Aplikacje obsługujące dostęp warunkowy](active-directory-conditional-access-supported-apps.md)

#### Znajdowanie interfejsu API
##### [Znajdowanie interfejsu API](application-dev-api-find-an-api-how-to.md)

#### Zarządzanie dostępem
##### [Przypisywanie użytkowników i grup do aplikacji](application-access-assignment-how-to-add-assignment.md)
##### [Usuwanie dostępu użytkowników do aplikacji](application-access-assignment-how-to-remove-assignment.md)
##### [Konfigurowanie samoobsługowego przypisania aplikacji](application-access-self-service-how-to.md)
##### [Przypisano nieoczekiwanego użytkownika](application-access-unexpected-user-assignment.md)
##### [Nieoczekiwana aplikacja na liście aplikacji](application-access-unexpected-application.md)

#### Aplikacje z wieloma dzierżawami
##### [Konfigurowanie nowej aplikacji](application-dev-setup-multi-tenant-app.md)
##### [Dodawanie do galerii aplikacji](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Uprawnienia
##### [Wybieranie uprawnień dla interfejsu API](application-dev-perms-for-given-api.md)
##### [Udzielanie uprawnień dla mojej aplikacji](application-dev-registration-config-grant-permissions-how-to.md)
##### [Uprawnienia delegowane a uprawnienia aplikacji](application-dev-delegated-and-app-perms.md)
##### [Zgoda dla aplikacji](application-dev-consent-framework.md)

#### Inicjowanie obsługi
##### [Czas trwania](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Długie godziny — aplikacja z galerii](application-provisioning-when-will-provisioning-finish.md)
##### [Konfigurowanie aprowizacji użytkowników — aplikacja z galerii](application-provisioning-config-how-to.md)
##### [Problem podczas konfigurowania aprowizacji użytkowników — aplikacja z galerii](application-provisioning-config-problem.md)
##### [Problem podczas zapisywania poświadczeń administratora podczas konfigurowania aplikacji z galerii do aprowizacji użytkowników](application-provisioning-config-problem-storage-limit.md)
##### [Użytkownicy nie są aprowizowani — aplikacja z galerii](application-provisioning-config-problem-no-users-provisioned.md)
##### [Aprowizowano niewłaściwych użytkowników — aplikacja z galerii](application-provisioning-config-problem-wrong-users-provisioned.md)

#### Logowanie jednokrotne
##### [Wybieranie metody](application-config-sso-how-to-choose-sign-on-method.md)
##### [Konfigurowanie](application-dev-registration-config-sso-how-to.md)
##### [Konfigurowanie federacji — aplikacje z galerii](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [Typowe problemy podczas konfigurowania federacji — aplikacje z galerii](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [Konfigurowanie federacji — aplikacje spoza galerii](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [Typowe problemy podczas konfigurowania federacji — aplikacje spoza galerii](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [Konfigurowanie hasła — aplikacje z galerii](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [Typowe problemy podczas konfigurowania hasła — aplikacje z galerii](application-config-sso-problem-configure-password-sso-gallery.md)
##### [Konfigurowanie hasła — aplikacje spoza galerii](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [Typowe problemy podczas konfigurowania hasła — aplikacje spoza galerii](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### Problemy z logowaniem użytkowników
##### [Nieoczekiwany monit o wyrażenie zgody](application-sign-in-unexpected-user-consent-prompt.md)
##### [Błąd zgody użytkownika](application-sign-in-unexpected-user-consent-error.md)
##### [Problemy podczas logowania się z poziomu niestandardowego portalu](application-sign-in-other-problem-deeplink.md)
##### [Problemy podczas logowania się z poziomu panelu dostępu](application-sign-in-other-problem-access-panel.md)
##### [Błąd na stronie logowania aplikacji](application-sign-in-problem-application-error.md)
##### [Problem z logowaniem jednokrotnym hasła — aplikacja spoza galerii](application-sign-in-problem-password-sso-non-gallery.md)
##### [Problem z logowaniem jednokrotnym hasła — aplikacja z galerii](application-sign-in-problem-password-sso-gallery.md)
##### [Problem podczas logowania się do aplikacji firmy Microsoft](application-sign-in-problem-first-party-microsoft.md)
##### [Problem z federacyjnym logowaniem jednokrotnym — aplikacja spoza galerii](application-sign-in-problem-federated-sso-non-gallery.md)
##### [Problem z federacyjnym logowaniem jednokrotnym — aplikacja z galerii](application-sign-in-problem-federated-sso-gallery.md)
##### [Problem z aplikacją niestandardową](application-sign-in-problem-custom-dev.md)
##### [Problem z aplikacją lokalną — serwer proxy aplikacji](application-sign-in-problem-on-premises-application-proxy.md)

### [Programowanie aplikacji](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Biblioteka dokumentów](active-directory-apps-index.md)

## Zarządzanie swoim katalogiem
### [Program Azure AD Connect](./connect/active-directory-aadconnect.md)
### Niestandardowe nazwy domen
#### [Szybki start](fundamentals/add-custom-domain.md)
#### [Dodawanie niestandardowych nazw domen](users-groups-roles/domains-manage.md)
### [Administrowanie własnym katalogiem](fundamentals/active-directory-administer.md)
### [Usuwanie katalogu](users-groups-roles/directory-delete-howto.md)
### [Wiele katalogów](users-groups-roles/licensing-directory-independence.md)
### [Rejestracja samoobsługowa](users-groups-roles/directory-self-service-signup.md)
### [Przejmowanie katalogu niezarządzanego](users-groups-roles/domains-admin-takeover.md)
### [Roaming stanu dla przedsiębiorstw](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Włączanie](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Ustawienia zasad grupy](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Ustawienia systemu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Często zadawane pytania](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Rozwiązywanie problemów](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrowanie tożsamości lokalnych za pomocą usługi Azure AD Connect](./connect/active-directory-aadconnect.md)

## Delegowanie dostępu do zasobów
### [Role administratorów](users-groups-roles/directory-assign-admin-roles.md)
#### [Wyświetlanie członków roli administratora](users-groups-roles//directory-manage-roles-portal.md)
#### [Przypisywanie użytkownikowi roli administratora](fundamentals/active-directory-users-assign-role-azure-portal.md)
#### [Porównanie uprawnień członka i gościa](fundamentals/users-default-permissions.md)
### [Zabezpieczenia roli administratora](users-groups-roles/directory-admin-roles-secure.md)  
#### [Tworzenie kont administratorów z dostępem awaryjnym](users-groups-roles/directory-emergency-access.md)
### [Jednostki administracyjne](users-groups-roles/directory-administrative-units.md)
### [Konfigurowanie okresów istnienia tokenu](active-directory-configurable-token-lifetimes.md)

## Przeglądy dostępu
### [Omówienie przeglądów dostępu](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md)
### [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md)
### [Wykonywanie przeglądu dostępu](active-directory-azure-ad-controls-perform-access-review.md)
### [Jak przejrzeć swój dostęp](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Dostęp gościa i przeglądy dostępu](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Zarządzanie dostępem użytkowników i przeglądy](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Zarządzanie programami i kontrolami](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Pobieranie wyników przeglądu dostępu](active-directory-azure-ad-controls-retrieve-access-review.md)

## Zabezpieczanie tożsamości
### [Dostęp warunkowy](active-directory-conditional-access-azure-portal.md)
#### [Wprowadzenie](active-directory-conditional-access-azure-portal-get-started.md)
#### Przewodniki Szybki start
##### [Konfigurowanie usługi MFA na aplikację w chmurze](conditional-access/app-based-mfa.md)
##### [Wymaganie akceptacji warunków użytkowania](conditional-access/require-tou.md)
##### [Blokowanie dostępu po wykryciu ryzykownej sesji](conditional-access/app-sign-in-risk.md)
#### Samouczki
##### [Migrowanie klasycznych zasad MFA](conditional-access/policy-migration-mfa.md)
#### Pojęcia
##### [Ochrona punktu odniesienia](conditional-access/baseline-protection.md)
##### [Warunki](conditional-access/conditions.md)
##### [Warunki lokalizacji](conditional-access/location-condition.md)
##### [Kontrolki](conditional-access/controls.md)
##### [Narzędzie analizy warunkowej](conditional-access/what-if-tool.md)
##### [Zrozumienie zasad dotyczących urządzeń dla usług Office 365](active-directory-conditional-access-device-policies.md)
#### Przewodniki z instrukcjami
##### [Najlepsze praktyki](conditional-access/best-practices.md)
##### [Konfigurowanie zasad dostępu warunkowego dla prób dostępu z niezaufanych sieci](active-directory-conditional-access-untrusted-networks.md)
##### [Konfigurowanie dostępu warunkowego opartego na urządzeniach](active-directory-conditional-access-policy-connected-applications.md)
##### [Konfigurowanie dostępu warunkowego opartego na aplikacji](active-directory-conditional-access-mam.md)
##### [Podawanie warunków użytkowania dla użytkowników i aplikacji](active-directory-tou.md)
##### [Migrowanie zasad klasycznych](conditional-access/policy-migration.md)
##### [Konfigurowanie połączeń sieci VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [Konfigurowanie usług SharePoint i Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Korygowanie](active-directory-conditional-access-device-remediation.md)
#### [Dokumentacja techniczna](active-directory-conditional-access-technical-reference.md)
#### [Często zadawane pytania](conditional-access/faqs.md)

### Uwierzytelnianie oparte na certyfikatach
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Wprowadzenie](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Włączanie](active-directory-identityprotection-enable.md)
#### [Wykrywanie luk w zabezpieczeniach](active-directory-identityprotection-vulnerabilities.md)
#### [Zdarzenia ryzyka](active-directory-identity-protection-risk-events.md)
#### [Powiadomienia](active-directory-identityprotection-notifications.md)
#### [Środowisko logowania](active-directory-identityprotection-flows.md)
#### [Symulowanie ryzykownych zdarzeń](active-directory-identityprotection-playbook.md)
#### [Odblokowywanie użytkowników](active-directory-identityprotection-unblock-howto.md)
#### [Często zadawane pytania](active-directory-identity-protection-faqs.md)
#### [Słownik](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## Integrowanie innych usług z usługą Azure AD 
### [Integracja usługi LinkedIn z usługą Azure AD](users-groups-roles/linkedin-integration.md)

## [Wdrażanie usług AD FS na platformie Azure](active-directory-aadconnect-azure-adfs.md)
### [Wysoka dostępność](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Zmiana algorytmu wyznaczania wartości skrótu podpisu](active-directory-federation-sha256-guidance.md)

## [Rozwiązywanie problemów](fundamentals/active-directory-troubleshooting-support-howto.md)

## Wdrażanie prototypu usługi Azure AD
### [Podręcznik prototypu: wprowadzenie](active-directory-playbook-intro.md)
### [Podręcznik prototypu: składniki](active-directory-playbook-ingredients.md)
### [Podręcznik prototypu: implementacja](active-directory-playbook-implementation.md)
### [Podręcznik prototypu: bloki konstrukcyjne](active-directory-playbook-building-blocks.md)


# Dokumentacja
## [Przykłady kodu](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Polecenia cmdlet programu Azure PowerShell](/powershell/azure/overview)
## [Dokumentacja interfejsu API języka Java](/java/api)
## [Interfejs API .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Ograniczenia i limity usługi](users-groups-roles/directory-service-limits-restrictions.md)

# Powiązane
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Program Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Program Azure AD dla programistów](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# Zasoby
## [Plany wdrożenia usługi Azure AD](./fundamentals/active-directory-deployment-plans.md)
## [Forum z opiniami na temat platformy Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Harmonogram działania dla platformy Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Cennik](https://azure.microsoft.com/pricing/details/active-directory/)
## [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizacje usług](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Wideo](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
