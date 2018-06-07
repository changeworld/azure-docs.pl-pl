---
title: Skonfigurować bezpiecznego protokołu LDAP (LDAPS) w usługach domenowych Azure AD | Dokumentacja firmy Microsoft
description: Skonfiguruj zabezpieczenia protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: cc2f8105c87b80f012a7e39e2f544c730dc41067
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34586923"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD
W tym artykule opisano, jak można włączyć bezpieczny Lightweight Directory dostępu protokołu (LDAPS) dla domeny zarządzanej usług domenowych Azure AD. Bezpiecznego protokołu LDAP jest także znana jako "dostępu protokołu LDAP (Lightweight Directory) za pośrednictwem Secure Sockets Layer (SSL) / zabezpieczeń TLS (Transport Layer)".

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:

1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).
4. A **certyfikat użyty do włączenia bezpiecznego protokołu LDAP**.

   * **Zalecane** -uzyskać certyfikat od zaufanego publicznego urzędu certyfikacji. Ta opcja konfiguracji jest bardziej bezpieczne.
   * Alternatywnie można również wybrać opcję [Utwórz certyfikat z podpisem własnym](#task-1---obtain-a-certificate-for-secure-ldap) jak pokazano w dalszej części tego artykułu.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Wymagania dotyczące bezpiecznego certyfikatu LDAP
Przed włączeniem bezpiecznego protokołu LDAP, należy uzyskać prawidłowy certyfikat dla następujących wytycznych. Wystąpią błędy, jeśli próbujesz włączyć bezpiecznego protokołu LDAP do domeny zarządzanej za pomocą certyfikatu nieprawidłowa lub być niepoprawne.

1. **Zaufanych wystawców** — certyfikat musi być wystawiony przez urząd certyfikacji zaufany przez komputery łączenie domeny zarządzanej przy użyciu bezpiecznego protokołu LDAP. Ten urząd może być publicznego urzędu certyfikacji (CA) lub urząd certyfikacji przedsiębiorstwa uważany za zaufany przez te komputery.
2. **Okres istnienia** — certyfikat musi być prawidłowy, przez co najmniej dalej 3 – 6 miesięcy. Bezpieczny dostęp LDAP do domeny zarządzanej jest zakłócona po wygaśnięciu certyfikatu.
3. **Nazwa podmiotu** — nazwa podmiotu certyfikatu musi być symbolem wieloznacznym dla domeny zarządzanej. Na przykład, jeśli domena ma nazwę "contoso100.com", nazwa podmiotu certyfikatu musi być "*. contoso100.com". Ustaw nazwę DNS (alternatywnej nazwy podmiotu) ta nazwa symbolu wieloznacznego.
4. **Użycie klucza** — certyfikat musi być skonfigurowany dla poniżej używa - podpisy cyfrowe i szyfrowanie klucza.
5. **Cel certyfikatu** -musi to być prawidłowy do uwierzytelniania serwera SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Zadanie 1 — Uzyskaj certyfikat dla bezpiecznego protokołu LDAP
Pierwszym zadaniem obejmuje uzyskiwanie certyfikatu używany do bezpiecznego dostępu LDAP do domeny zarządzanej. Dostępne są dwie opcje:

* Uzyskaj certyfikat z publicznego urzędu certyfikacji lub urzędu certyfikacji przedsiębiorstwa.
* Tworzenie certyfikatu z podpisem własnym.

> [!NOTE]
> Komputery klienckie, które łączą się do domeny zarządzanej przy użyciu bezpiecznego protokołu LDAP muszą ufać wystawcy certyfikatu bezpiecznego LDAP.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opcja (zalecane) — Uzyskaj certyfikat bezpiecznego LDAP z urzędu certyfikacji
Jeśli Twoja organizacja otrzymuje swoje certyfikaty z publicznego urzędu certyfikacji, należy uzyskać bezpiecznego certyfikatu LDAP z publicznego urzędu certyfikacji. W przypadku wdrożenia urzędu certyfikacji przedsiębiorstwa, należy uzyskać bezpiecznego certyfikatu LDAP z urzędu certyfikacji przedsiębiorstwa.

> [!TIP]
> **Certyfikaty z podpisem własnym na użytek domen zarządzanych z ". onmicrosoft.com" sufiksy domen.**
> Jeśli nazwa domeny DNS domeny zarządzanej kończy się ". onmicrosoft.com", nie można uzyskać certyfikatu bezpiecznego LDAP z publicznego urzędu certyfikacji. Ponieważ Microsoft ma domenę "onmicrosoft.com", odmówić wystawić certyfikat LDAP bezpieczny do użytkownika w domenie z ten sufiks publicznych urzędów certyfikacji. W tym scenariuszu należy utworzyć certyfikat z podpisem własnym i używać, aby skonfigurować bezpiecznego protokołu LDAP.
>

Upewnij się, można uzyskać od publicznego urzędu certyfikacji certyfikat spełnia wszystkie wymagania opisane w temacie [wymagania dotyczące bezpiecznego certyfikatu LDAP](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opcja B - Tworzenie certyfikatu z podpisem własnym dla bezpiecznego protokołu LDAP
Jeśli nie planujesz używać certyfikatów z publicznego urzędu certyfikacji, można utworzyć certyfikatu z podpisem własnym dla bezpiecznego protokołu LDAP. Wybierz tę opcję, jeśli nazwa domeny DNS domeny zarządzanej kończy się ". onmicrosoft.com".

**Tworzenie certyfikatu z podpisem własnym za pomocą programu PowerShell**

Na komputerze z systemem Windows, Otwórz nowe okno programu PowerShell jako **administratora** i wpisz następujące polecenia, aby utworzyć nowy certyfikat z podpisem własnym.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject *.contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com
```

W poprzednim przykładzie Zastąp "*. contoso100.com" z nazwą domeny DNS domeny zarządzanej. For example, jeśli utworzono o nazwie "contoso100.onmicrosoft.com" domeny zarządzanej, zastąp "*. contoso100.com" w poprzednim skryptu "*. contoso100.onmicrosoft.com").

![Wybieranie katalogu usługi Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Nowo utworzony certyfikat z podpisem własnym znajduje się w magazynie certyfikatów komputera lokalnego.


## <a name="next-step"></a>Następny krok
[Zadanie 2 — wyeksportowany certyfikat bezpiecznego LDAP. Plik PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
