---
title: Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) w usługach domenowych Azure AD | Dokumentacja firmy Microsoft
description: Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: iainfou
ms.openlocfilehash: 1cb9ff70b41b63f4a3e3d45d744037195fdf28a7
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473772"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD
W tym artykule pokazano, jak włączyć bezpieczny Lightweight Directory dostępu protokołu (LDAPS) dla domeny zarządzanej usług domenowych Azure AD. Protokół Secure LDAP jest także znana jako "LDAP Lightweight Directory Access Protocol () za pośrednictwem Secure Sockets Layer (SSL) / zabezpieczeń TLS (Transport Layer)".

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:

1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](create-instance.md).
4. A **certyfikat służący do włączenia bezpiecznego protokołu LDAP**.

   * **Zaleca się** — uzyskać certyfikat od zaufanego publicznego urzędu certyfikacji. Ta opcja konfiguracji jest bardziej bezpieczne.
   * Alternatywnie można też do [Utwórz certyfikat z podpisem własnym](#task-1---obtain-a-certificate-for-secure-ldap) przedstawiony w dalszej części tego artykułu.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Wymagania dotyczące certyfikat secure LDAP
Przed włączeniem bezpiecznego protokołu LDAP, należy uzyskać prawidłowy certyfikat na poniższe wskazówki. Wystąpią błędy, jeśli zostanie podjęta próba włączenia protokołu secure LDAP dla domeny zarządzanej za pomocą nieważnych/nieprawidłowych certyfikatu.

1. **Zaufanych wystawców** — certyfikat musi być wystawiony przez urząd certyfikacji zaufany przez komputery, na nawiązywanie połączeń z domeny zarządzanej przy użyciu protokołu secure LDAP. Ten urząd może być publiczny urząd certyfikacji (CA) lub urzędu certyfikacji przedsiębiorstwa ufa tych komputerów.
2. **Okres istnienia** — certyfikat musi być ważny przez co najmniej ciągu następnych 3 – 6 miesięcy. Dostęp do Twojej domeny zarządzanej Secure LDAP jest zakłócone. po wygaśnięciu certyfikatu.
3. **Nazwa podmiotu** -nazwa podmiotu certyfikatu musi być Twojej domeny zarządzanej. Na przykład w nazwie domeny "contoso100.com" Nazwa podmiotu certyfikatu musi być "contoso100.com". Ustaw nazwę DNS (alternatywnej nazwy podmiotu) na nazwy z symbolami wieloznacznymi dla domeny zarządzanej.
4. **Użycie klucza** — certyfikat musi być skonfigurowany do następujących zastosowań - podpisów cyfrowych i szyfrowanie klucza.
5. **Cel certyfikatu** — musi to być prawidłowy do uwierzytelniania serwera SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Zadanie 1 — uzyskiwanie certyfikatu dla protokołu secure LDAP
Pierwsze zadanie polega na uzyskaniu certyfikat używany do uzyskiwania dostępu secure LDAP do domeny zarządzanej. Dostępne są dwie opcje:

* Uzyskanie certyfikatu z publicznego urzędu certyfikacji lub urzędu certyfikacji przedsiębiorstwa.
* Utwórz certyfikat z podpisem własnym.

> [!NOTE]
> Komputery klienckie, które muszą połączyć się z domeny zarządzanej przy użyciu protokołu secure LDAP, muszą ufać wystawca certyfikat secure LDAP.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opcja (zalecane) — uzyskiwanie bezpiecznego certyfikatu LDAP z urzędu certyfikacji
Jeśli Twoja organizacja uzyska jego certyfikatów z publicznego urzędu certyfikacji, należy uzyskać certyfikat secure LDAP z publicznego urzędu certyfikacji. W przypadku wdrożenia urzędu certyfikacji przedsiębiorstwa, należy uzyskać certyfikat secure LDAP z urzędu certyfikacji przedsiębiorstwa.

> [!TIP]
> **Certyfikaty z podpisem własnym na użytek domen zarządzanych za pomocą ". onmicrosoft.com" sufiksy domeny.**
> Jeśli nazwa domeny DNS z domeny zarządzanej kończy się na ". onmicrosoft.com", nie można uzyskać certyfikat secure LDAP z publicznego urzędu certyfikacji. Ponieważ firma Microsoft posiada domeny "onmicrosoft.com", odmówić problem certyfikat secure LDAP dla użytkownika domeny z ten sufiks publicznych urzędów certyfikacji. W tym scenariuszu należy utworzyć certyfikat z podpisem własnym i używać go do skonfigurowania protokołu secure LDAP.
>

Upewnij się, certyfikat można uzyskać od publicznego urzędu certyfikacji spełnia wszystkie wymagania opisane w temacie [wymagania dotyczące bezpiecznego certyfikatu LDAP](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opcja B — Utwórz certyfikat z podpisem własnym dla protokołu secure LDAP
Jeśli nie planujesz użyć certyfikatu z publicznego urzędu certyfikacji, można utworzyć certyfikatu z podpisem własnym dla protokołu secure LDAP. Wybierz tę opcję, jeśli nazwa domeny DNS z domeny zarządzanej, który kończy się na ". onmicrosoft.com".

**Utwórz certyfikat z podpisem własnym za pomocą programu PowerShell**

Na komputerze z systemem Windows otwórz nowe okno programu PowerShell jako **administratora** i wpisz następujące polecenia, aby utworzyć nowy certyfikat z podpisem własnym.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com, contoso100.com
```

W poprzednim przykładzie "contoso100.com" należy zastąpić nazwę domeny DNS z domeny zarządzanej. Na przykład, jeśli utworzono domeny zarządzanej, o nazwie "contoso100.onmicrosoft.com", Zamień "contoso100.com" w atrybucie podmiotu "contoso100.onmicrosoft.com" i " *. contoso100.com" w atrybucie DnsName za pomocą "* . contoso100.onmicrosoft.com ").

![Wybieranie katalogu usługi Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Nowo utworzony certyfikat z podpisem własnym zostanie umieszczony w magazynie certyfikatów komputera lokalnego.


## <a name="next-step"></a>Następny krok
[Zadanie 2 — eksportowanie do certyfikat secure LDAP. Plik PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
