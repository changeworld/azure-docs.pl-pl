# [Dokumentacja usługi Azure Active Directory](index.md)

# Omówienie
## [Co to jest usługa Azure Active Directory?](fundamentals/active-directory-whatis.md)
## [Informacje o zarządzaniu tożsamościami na platformie Azure](fundamentals/identity-fundamentals.md)
## [Omówienie rozwiązań do obsługi tożsamości na platformie Azure](fundamentals/understand-azure-identity-solutions.md)
## [Kojarzenie subskrypcji usługi Azure](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Zagadnienia dotyczące rezydencji i danych](fundamentals/active-directory-data-storage-eu.md)
## [Często zadawane pytania](fundamentals/active-directory-faq.md)
## [Co nowego](fundamentals/whats-new.md)


# Rozpoczęcie pracy
## [Tworzenie konta w usłudze Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Dodawanie niestandardowej nazwy domeny](fundamentals/add-custom-domain.md)
## [Configure company branding (Konfigurowanie oznaczenia marką firmy)](fundamentals/customize-branding.md)
## [Dodawanie użytkowników do usługi Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Assign licenses to users (Przypisywanie licencji do użytkowników)](fundamentals/license-users-groups.md)
## [Configure Self-service password reset (Konfigurowanie samoobsługowego resetowania haseł)](authentication/quickstart-sspr.md)
## [Dodawanie informacji o ochronie prywatności w organizacji do usługi Azure AD](active-directory-properties-area.md)
## [Uzyskiwanie dostępu do usługi Azure Active Directory w celu utworzenia nowej dzierżawy](fundamentals/active-directory-access-create-new-tenant.md)


# Instrukcje
## Planowanie i projektowanie
### [Omówienie architektury usługi Azure AD](fundamentals/active-directory-architecture.md)
### [Mapowanie oświadczeń w usłudze Azure Active Directory](develop/active-directory-claims-mapping.md)
### [Wdrażanie rozwiązania tożsamości hybrydowej](hybrid/plan-hybrid-identity-design-considerations-overview.md)
#### Określanie wymagań
##### [Tożsamość](hybrid/plan-hybrid-identity-design-considerations-business-needs.md)
##### [Synchronizacja katalogów](hybrid/plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Uwierzytelnianie wieloskładnikowe](hybrid/plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategia cyklu życia tożsamości](hybrid/plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planowanie zabezpieczeń danych](hybrid/plan-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Ochrona danych](hybrid/plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Zarządzanie zawartością](hybrid/plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Kontrola dostępu](hybrid/plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Reagowanie na zdarzenia](hybrid/plan-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planowanie cyklu życia tożsamości
##### [Zadania](hybrid/plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Strategia wdrażania](hybrid/plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Następne kroki](hybrid/plan-hybrid-identity-design-considerations-nextsteps.md)
#### [Porównanie narzędzi](hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md)

## Zarządzanie użytkownikami
### [Dodawanie nowych użytkowników do usługi Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Zarządzanie profilami użytkowników](fundamentals/active-directory-users-profile-azure-portal.md)
### [Resetowanie haseł użytkowników](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Przypisywanie użytkowników do ról administratorów](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Dodawanie użytkowników-gości z innego katalogu (B2B)](b2b/what-is-b2b.md)
#### [Administratorzy dodający użytkowników B2B](b2b/add-users-administrator.md)
#### [Pracownicy przetwarzający informacje dodający użytkowników B2B](b2b/add-users-information-worker.md)
#### [Interfejs API i dostosowywanie](b2b/customize-invitation-api.md)
#### [Federacja z firmą Google](b2b/google-federation.md)
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
### [Zarządzanie grupami](fundamentals/active-directory-groups-create-azure-portal.md)
### [Usuwanie grupy i jej członków](fundamentals/active-directory-groups-delete-group.md)
### [Zarządzanie ustawieniami grup](fundamentals/active-directory-groups-settings-azure-portal.md)
## [Zarządzanie raportami](reports-monitoring/overview-reports.md)
### [Działania logowania](reports-monitoring/concept-sign-ins.md)
### [Działania inspekcji](reports-monitoring/concept-audit-logs.md)
### [Narażeni użytkownicy](reports-monitoring/concept-user-at-risk.md)
### [Ryzykowne logowania](reports-monitoring/concept-risky-sign-ins.md)
### [Zdarzenia ryzyka](reports-monitoring/concept-risk-events.md)
### [Monitorowanie dzienników przy użyciu usługi Azure Monitor](reports-monitoring/concept-activity-logs-in-azure-monitor.md)
### [Często zadawane pytania](reports-monitoring/reports-faq.md)

### Zadania
#### [Pobieranie raportu logowań](reports-monitoring/quickstart-download-sign-in-report.md)
#### [Pobieranie raportu inspekcji](reports-monitoring/quickstart-download-audit-report.md)
#### [Konfigurowanie nazwanych lokalizacji](reports-monitoring/quickstart-configure-named-locations.md)
#### [Znajdowanie raportów aktywności](reports-monitoring/howto-find-activity-reports.md)
#### [Używanie pakietu zawartości Azure AD Power BI](reports-monitoring/howto-power-bi-content-pack.md)
#### [Korygowanie użytkowników oflagowanych w związku z ryzykiem](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Kierowanie dzienników aktywności do Centrum zdarzeń platformy Azure](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Archiwizowanie dzienników aktywności na koncie usługi Azure Storage](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Integrowanie dzienników aktywności z rozwiązaniem Splunk przy użyciu usługi Azure Monitor](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Integrowanie dzienników aktywności z rozwiązaniem SumoLogic przy użyciu usługi Azure Monitor](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)
#### [Integrowanie dzienników aktywności z usługą Log Analytics przy użyciu usługi Azure Monitor](reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

### Dokumentacja
#### [Przechowywanie](reports-monitoring/reference-reports-data-retention.md)
#### [Czasy oczekiwania](reports-monitoring/reference-reports-latencies.md)
#### [Dokumentacja działań inspekcji](reports-monitoring/reference-audit-activities.md)
#### [Sign-in activity error codes (Kody błędów działań logowania)](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Interpretowanie schematu dziennika inspekcji w usłudze Azure Monitor](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [Interpretowanie schematu dziennika logowania w usłudze Azure Monitor](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### Rozwiązywanie problemów
#### [Brak danych w dziennikach aktywności usługi Azure AD](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [Brak danych do pobrania](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Błędy pakietu zawartości dzienników aktywności usługi Azure AD](reports-monitoring/troubleshoot-content-pack.md)
#### [Błędy interfejsu API raportowania usługi Azure AD](reports-monitoring/troubleshoot-graph-api.md)

### [Dostęp programowy](reports-monitoring/concept-reporting-api.md)
#### [Wymagania wstępne](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [Używanie certyfikatów](reports-monitoring/tutorial-access-api-with-certificates.md)

## [Zarządzanie hasłami](authentication/concept-sspr-howitworks.md)

## Zarządzanie aplikacjami
### [Omówienie](manage-apps/what-is-application-management.md)
### [Rozpoczęcie pracy](manage-apps/plan-an-application-integration.md)
### [Samouczki integracji aplikacji SaaS](saas-apps/tutorial-list.md)

### [Aprowizacja i anulowanie aprowizacji użytkowników w aplikacjach SaaS](manage-apps/user-provisioning.md) 
#### [Samouczki integracji aplikacji](saas-apps/tutorial-list.md) 
#### [Automatyzowanie aprowizacji w aplikacjach obsługujących standard SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Dostosowywanie mapowań atrybutów](manage-apps/customize-application-attributes.md) 
#### [Pisanie wyrażeń do mapowania atrybutów](manage-apps/functions-for-customizing-application-data.md) 
#### [Korzystanie z filtrów określania zakresu](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [Raportowanie na temat automatycznej aprowizacji użytkowników](manage-apps/check-status-user-account-provisioning.md) 
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
##### [Symbole wieloznaczne](manage-apps/application-proxy-wildcard.md)
##### [Usuwanie danych osobowych](manage-apps/application-proxy-remove-personal-data.md)


#### Przewodniki dotyczące publikowania
##### [Pulpit zdalny](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
#### [Program PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Rozwiązywanie problemów](manage-apps/application-proxy-troubleshoot.md)

### Zarządzanie aplikacjami w przedsiębiorstwie
#### [Dodawanie aplikacji](manage-apps/add-application-portal.md)
#### [Wyświetlanie aplikacji w dzierżawie](manage-apps/view-applications-portal.md)
#### [Konfigurowanie logowania jednokrotnego](manage-apps/configure-single-sign-on-portal.md)
#### [Przypisywanie użytkowników](manage-apps/assign-user-or-group-access-portal.md)
#### [Dostosowywanie oznaczania marką firmy](manage-apps/change-name-or-logo-portal.md)
#### [Wyłączanie logowania użytkownika](manage-apps/disable-user-sign-in-portal.md)
#### [Usuwanie użytkowników](manage-apps/remove-user-or-group-access-portal.md)

#### [Zarządzanie aprowizacją konta użytkownika](manage-apps/configure-automatic-user-provisioning-portal.md)

#### [Zaawansowane podpisywanie certyfikatów dla aplikacji SAML](manage-apps/certificate-signing-options.md)
#### [Ukrywanie aplikacji w środowisku użytkownika](manage-apps/hide-application-from-user-portal.md)
### [Konfigurowanie automatycznego przyspieszenia logowania za pomocą zasad HRD](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrate AD FS apps to Azure AD (Migracja aplikacji AD FS do usługi Azure AD)](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Zarządzanie dostępem do aplikacji](manage-apps/what-is-access-management.md)
#### [Dostęp z użyciem logowania jednokrotnego](manage-apps/what-is-single-sign-on.md)
#### [Certyfikaty rejestracji jednokrotnej](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Ograniczenia dzierżawy](manage-apps/tenant-restrictions.md)
#### [Aprowizacja użytkowników przy użyciu standardu SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Opis środowisk zgody dla aplikacji usługi Azure AD](develop/application-consent-experience.md)

### Rozwiązywanie problemów



#### Panel dostępu
##### [Aplikacja nie jest wyświetlana](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [Nieoczekiwane wyświetlanie aplikacji](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [Nie można się zalogować](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [Błąd podczas instalacji rozszerzenia przeglądarki](manage-apps/access-panel-extension-problem-installing.md)
##### [Jak używać dostępu do aplikacji samoobsługowej](manage-apps/access-panel-manage-self-service-access.md)
##### [Błąd podczas używania dostępu do aplikacji samoobsługowej](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### Dodawanie aplikacji
##### [Wybieranie typu aplikacji](manage-apps/choose-application-type.md)
##### [Typowe problemy — aplikacje z galerii](manage-apps/adding-gallery-app-common-problems.md)
##### [Typowe problemy — aplikacje spoza galerii](manage-apps/adding-non-gallery-app-common-problems.md)

#### Serwer proxy aplikacji
##### [Problem z wyświetlaniem strony aplikacji](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [Ładowanie aplikacji trwa zbyt długo](manage-apps/application-proxy-page-load-speed-problem.md)
##### [Linki na stronie aplikacji nie działają](manage-apps/application-proxy-page-links-broken-problem.md)
##### [Jakie porty należy otworzyć dla mojej aplikacji](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [Brak działającego łącznika w grupie łączników mojej aplikacji](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [Konfigurowanie w portalu administracyjnym](manage-apps/application-proxy-config-how-to.md)
##### [Konfigurowanie logowania jednokrotnego w mojej aplikacji](manage-apps/application-proxy-config-sso-how-to.md)
##### [Problem podczas tworzenia aplikacji w portalu administracyjnym](manage-apps/application-proxy-config-problem.md)
##### [Konfigurowanie ograniczonego delegowania protokołu Kerberos](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Konfigurowanie przy użyciu usługi PingAccess](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### [Błąd „Can't Access this Corporate Application” (Nie można uzyskać dostępu do tej aplikacji firmowej)](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Problem z instalacją łącznika agenta serwera proxy aplikacji](manage-apps/application-proxy-connector-installation-problem.md)


#### Rejestracja aplikacji
##### [Wprowadzanie pól obiektu aplikacji](develop/registration-config-specific-application-property-how-to.md)
##### [Zmienianie ustawień domyślnych okresu istnienia tokenu](develop/registration-config-change-token-lifetime-how-to.md)

#### Authentication
##### [Konfigurowanie punktów końcowych](develop/registration-config-how-to.md)

#### Dostęp warunkowy
##### [Klient nie spełnił wymagań wstępnych dotyczących rejestracji urządzeń](active-directory-conditional-access.md)
##### [Jak i kiedy działają reguły poza siecią firmową?](https://aka.ms/calocation)
##### [Jak zwiększyć liczbę urządzeń, które użytkownik może zarejestrować w usłudze Azure AD?](active-directory-azureadjoin-setup.md)
##### [Jak skonfigurować dostęp warunkowy dla usługi Exchange Online?](https://aka.ms/csforexchange)
##### [Jak skonfigurować dostęp warunkowy dla urządzeń z systemem Windows 7?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Aplikacje obsługujące dostęp warunkowy](active-directory-conditional-access-supported-apps.md)

#### Znajdowanie interfejsu API
##### [Znajdowanie interfejsu API](develop/api-find-an-api-how-to.md)

#### Zarządzanie dostępem
##### [Przypisywanie użytkowników i grup do aplikacji](manage-apps/methods-for-assigning-users-and-groups.md)
##### [Usuwanie dostępu użytkowników do aplikacji](manage-apps/methods-for-removing-user-access.md)
##### [Konfigurowanie samoobsługowego przypisania aplikacji](manage-apps/manage-self-service-access.md)
##### [Przypisano nieoczekiwanego użytkownika](manage-apps/ways-users-get-assigned-to-applications.md)
##### [Nieoczekiwana aplikacja na liście aplikacji](manage-apps/application-types.md)

#### Aplikacje z wieloma dzierżawami
##### [Konfigurowanie nowej aplikacji](develop/setup-multi-tenant-app.md)
##### [Dodawanie do galerii aplikacji](develop/registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Uprawnienia
##### [Wybieranie uprawnień dla interfejsu API](develop/perms-for-given-api.md)
##### [Uprawnienia delegowane a uprawnienia aplikacji](develop/delegated-and-app-perms.md)
##### [Zgoda dla aplikacji](develop/consent-framework.md)

#### Inicjowanie obsługi
##### [Czas trwania](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Długie godziny — aplikacja z galerii](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [Konfigurowanie aprowizacji użytkowników — aplikacja z galerii](manage-apps/application-provisioning-config-how-to.md)
##### [Problem podczas konfigurowania aprowizacji użytkowników — aplikacja z galerii](manage-apps/application-provisioning-config-problem.md)
##### [Problem podczas zapisywania poświadczeń administratora podczas konfigurowania aplikacji z galerii do aprowizacji użytkowników](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [Użytkownicy nie są aprowizowani — aplikacja z galerii](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [Aprowizowano niewłaściwych użytkowników — aplikacja z galerii](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)

#### Logowanie jednokrotne
##### [Wybieranie metody](manage-apps/single-sign-on-modes.md)
##### [Konfigurowanie](develop/registration-config-sso-how-to.md)
##### [Konfigurowanie federacji — aplikacje z galerii](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [Typowe problemy podczas konfigurowania federacji — aplikacje z galerii](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [Konfigurowanie federacji — aplikacje spoza galerii](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [Typowe problemy podczas konfigurowania federacji — aplikacje spoza galerii](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [Konfigurowanie hasła — aplikacje z galerii](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [Typowe problemy podczas konfigurowania hasła — aplikacje z galerii](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [Konfigurowanie hasła — aplikacje spoza galerii](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [Typowe problemy podczas konfigurowania hasła — aplikacje spoza galerii](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### Problemy z logowaniem użytkowników
##### [Nieoczekiwany monit o wyrażenie zgody](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [Błąd zgody użytkownika](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [Problemy podczas logowania się z poziomu niestandardowego portalu](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [Problemy podczas logowania się z poziomu panelu dostępu](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [Błąd na stronie logowania aplikacji](manage-apps/application-sign-in-problem-application-error.md)
##### [Problem z logowaniem jednokrotnym hasła — aplikacja spoza galerii](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [Problem z logowaniem jednokrotnym hasła — aplikacja z galerii](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Problem podczas logowania się do aplikacji firmy Microsoft](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [Problem z federacyjnym logowaniem jednokrotnym — aplikacja spoza galerii](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [Problem z federacyjnym logowaniem jednokrotnym — aplikacja z galerii](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [Problem z aplikacją niestandardową](manage-apps/application-sign-in-problem-custom-dev.md)
##### [Problem z aplikacją lokalną — serwer proxy aplikacji](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)


## Zarządzanie swoim katalogiem
### [Program Azure AD Connect](hybrid/whatis-hybrid-identity.md)
### Niestandardowe nazwy domen
#### [Szybki start](fundamentals/add-custom-domain.md)
### [Administrowanie własnym katalogiem](fundamentals/active-directory-administer.md)
### [Integrowanie tożsamości lokalnych za pomocą usługi Azure AD Connect](hybrid/whatis-hybrid-identity.md)

### [Konfigurowanie okresów istnienia tokenu](develop/active-directory-configurable-token-lifetimes.md)

## Zabezpieczanie tożsamości

### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)



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
## [Interfejs API programu .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)

# Powiązane
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Program Azure AD Connect](hybrid/whatis-hybrid-identity.md)
## [Azure AD Connect Health](hybrid/whatis-hybrid-identity-health.md)
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
## [Filmy wideo](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
