# Omówienie
## [Co to jest program Azure AD Connect?](active-directory-aadconnect.md)
## [Co to jest program Azure AD Connect Sync?](active-directory-aadconnectsync-whatis.md)
### [Użytkownicy i kontakty](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [Architektura](active-directory-aadconnectsync-understanding-architecture.md)
### [Aprowizacja deklaratywna](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [Wyrażenia związane z aprowizacją deklaratywną](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [Konfiguracja domyślna](active-directory-aadconnectsync-understanding-default-configuration.md)
## [Co to jest program Azure AD Connect i Federation?](active-directory-aadconnectfed-whatis.md)


# Rozpoczęcie pracy
## [Wymagania wstępne](active-directory-aadconnect-prerequisites.md)
## [Instalowanie programu Azure AD Connect](active-directory-aadconnect-select-installation.md)
### [Ustawienia ekspresowe](active-directory-aadconnect-get-started-express.md)
### [Ustawienia niestandardowe](active-directory-aadconnect-get-started-custom.md)
### [Uaktualnianie z narzędzia DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [Uaktualnianie z poprzedniej wersji](active-directory-aadconnect-upgrade-previous-version.md)
### [Instalowanie przy użyciu istniejącej bazy danych ADSync](active-directory-aadconnect-existing-database.md)
### [Install using SQL delegated administrator permissions (Instalowanie przy użyciu delegowanych uprawnień administratora programu SQL)](active-directory-aadconnect-sql-delegation.md)

# Instrukcje
## Planowanie i projektowanie
### [Zagadnienia dotyczące projektowania](active-directory-aadconnect-design-concepts.md)
### [Topologie obsługiwane w programie Azure AD Connect](active-directory-aadconnect-topologies.md)
### [Usługi Active Directory Federation Services na platformie Azure](active-directory-aadconnect-azure-adfs.md)
### [Specjalne uwagi dotyczące wystąpień](active-directory-aadconnect-instances.md)
### [Jeśli masz już usługę Azure AD](active-directory-aadconnect-existing-tenant.md)
## [Zarządzanie programem Azure AD Connect](active-directory-aadconnect-whats-next.md)
### [Odnawianie certyfikatów dla usług O365 i Azure AD](active-directory-aadconnect-o365-certs.md)
### [Aktualizowanie certyfikatu SSL dla farmy usług Active Directory Federation Services (AD FS)](active-directory-aadconnectfed-ssl-update.md)
### [Włączanie zapisywania zwrotnego urządzeń](active-directory-aadconnect-feature-device-writeback.md)
### [Opcje logowania użytkowników](active-directory-aadconnect-user-signin.md)
#### [Bezproblemowe logowanie jednokrotne](active-directory-aadconnect-sso.md)
##### [Szybki start](active-directory-aadconnect-sso-quick-start.md)
##### [Jak to działa?](active-directory-aadconnect-sso-how-it-works.md)
##### [Często zadawane pytania](active-directory-aadconnect-sso-faq.md)
##### [Rozwiązywanie problemów](active-directory-aadconnect-troubleshoot-sso.md)
##### [Prywatność użytkownika i bezproblemowe logowanie jednokrotne do usługi Azure AD](active-directory-aadconnect-sso-gdpr.md)
#### [Uwierzytelnianie przekazywane](active-directory-aadconnect-pass-through-authentication.md)
##### [Szybki start](active-directory-aadconnect-pass-through-authentication-quick-start.md)
##### [Bieżące ograniczenia](active-directory-aadconnect-pass-through-authentication-current-limitations.md)
##### [Jak to działa?](active-directory-aadconnect-pass-through-authentication-how-it-works.md)
##### [Uaktualnianie agentów w wersji zapoznawczej](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)
##### [Smart Lockout (Inteligentna blokada)](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)
##### [Często zadawane pytania](active-directory-aadconnect-pass-through-authentication-faq.md)
##### [Rozwiązywanie problemów](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)
##### [Szczegółowe informacje o zabezpieczeniach](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)
##### [Prywatność użytkownika i uwierzytelnianie przekazywane usługi Azure Active Directory](active-directory-aadconnect-pass-through-authentication-gdpr.md)
### [Obsługa wielu domen do federowania](active-directory-aadconnect-multiple-domains.md)
### [Automatycznie uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md)
### [Korzystanie z dostawcy tożsamości SAML 2.0 na potrzeby logowania jednokrotnego](active-directory-aadconnect-federation-saml-idp.md)
### [Synchronizowanie kont użytkowników-gości logujących się za pomocą adresu e-mail](active-directory-aadconnect-guest-sync.md)


## Zarządzanie programem Azure AD Connect Sync
### [Prywatność użytkownika i program Azure AD Connect](active-directory-aadconnect-gdpr.md)
### [Preferowana lokalizacja danych dla zasobów usługi O365](active-directory-aadconnectsync-feature-preferreddatalocation.md)
### [Zapobieganie przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [Synchronizacja skrótów haseł](active-directory-aadconnectsync-implement-password-hash-synchronization.md)
### [Konto usługi Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md)
### [Kreator instalacji](active-directory-aadconnectsync-installation-wizard.md)
### [Sposób wypełniania nazwy UserPrincipalName](active-directory-aadconnect-userprincipalname.md)
### [Zmienianie konfiguracji domyślnej](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [Konfigurowanie filtrowania](active-directory-aadconnectsync-configure-filtering.md)
### [Scheduler](active-directory-aadconnectsync-feature-scheduler.md)
### [Rozszerzenia katalogów](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Zmienianie hasła konta usługi Azure AD Sync](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [Zmienianie hasła konta usług AD DS](active-directory-aadconnectsync-change-addsacct-pass.md)
### [Włączanie Kosza usługi AD](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [Operacje](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [Łączniki](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Projektant magazynu metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Wyszukiwanie magazynu metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## Zarządzanie usługami Federation Services
### [Zarządzanie i dostosowywanie](active-directory-aadconnect-federation-management.md)
### [Federowanie wielu wystąpień usługi Azure AD przy użyciu jednego wystąpienia usług AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## Rozwiązywanie problemów
### [Łączność z usługą Azure AD za pośrednictwem programu Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md)
### [Łączność z serwerem SQL](active-directory-aadconnect-tshoot-sql-connectivity.md)
### [Błędy występujące podczas synchronizacji](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [Obiekt nie jest zsynchronizowany](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [Object sync using the troubleshooting task (Synchronizacja obiektów za pomocą zadania rozwiązywania problemów)](active-directory-aadconnect-troubleshoot-objectsync.md)
### [Synchronizacja skrótów haseł](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md)
### [Błąd LargeObject spowodowany przez element userCertificate](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [Jak usunąć limit 10 GB dla lokalnej bazy danych](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# Dokumentacja
## [Przykłady kodu](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Synchronizacja tożsamości i odporność na zduplikowane atrybuty](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [Wymagane porty i protokoły tożsamości hybrydowej](active-directory-aadconnect-ports.md)
## [Funkcje w wersji zapoznawczej](active-directory-aadconnect-feature-preview.md)
## [Historia wersji](active-directory-aadconnect-version-history.md)
## [Konta i uprawnienia](active-directory-aadconnect-accounts-permissions.md)

## Azure AD Connect Sync
### [Atrybuty synchronizowane z usługą Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md)
### [Historia wersji łącznika](active-directory-aadconnectsync-connector-version-history.md)
### [Informacje ogólne o funkcjach](active-directory-aadconnectsync-functions-reference.md)
### [Zagadnienia i zadania operacyjne](active-directory-aadconnectsync-operations.md)
### [Lista zgodności usługi Azure AD z usługami federacyjnymi](active-directory-aadconnect-federation-compatibility.md)
### [Zagadnienia techniczne](active-directory-aadconnectsync-technical-concepts.md)
### [Funkcje usługi](active-directory-aadconnectsyncservice-features.md)




# Powiązane
## [Monitorowanie lokalnej infrastruktury do obsługi tożsamości i usług synchronizacji w chmurze](../connect-health/active-directory-aadconnect-health.md)
## [Podręcznik projektowania tożsamości hybrydowej](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# Zasoby
## [Harmonogram działania dla platformy Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
##[Azure AD Connect — Często zadawane pytania](active-directory-aadconnect-faq.md)
##[Zakończenie świadczenia usługi DirSync](active-directory-aadconnect-dirsync-deprecated.md)
## [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)

