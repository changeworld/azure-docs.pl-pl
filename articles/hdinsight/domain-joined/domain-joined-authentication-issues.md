---
title: Problemy z uwierzytelnianiem w usłudze Azure HDInsight
description: Problemy z uwierzytelnianiem w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896139"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problemy z uwierzytelnianiem w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

W przypadku bezpiecznych klastrów wspieranych przez usługę Azure Data Lake (Gen1 lub Gen2), gdy użytkownicy domeny logują się do usług klastrowania za pośrednictwem bramy HDI (na przykład logując się do portalu Apache Ambari), usługa HDI Gateway spróbuje najpierw uzyskać token OAuth z usługi Azure Active Directory (Azure AD) , a następnie pobierz bilet protokołu Kerberos z usługi Azure AD DS. Uwierzytelnianie może zakończyć się niepowodzeniem w jednym z tych etapów. Ten artykuł ma na celu debugowanie niektórych z tych problemów.

Gdy uwierzytelnianie nie powiedzie się, zostanie wyświetlony monit o poświadczenia. Jeśli anulujesz to okno dialogowe, zostanie wydrukowany komunikat o błędzie. Oto niektóre z typowych komunikatów o błędach:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant lub unauthorized_client, 50126

### <a name="issue"></a>Problem

Logowanie nie powiedzie się dla użytkowników federowanych z kodem błędu 50126 (zalogowanie się powiedzie się dla użytkowników chmury). Komunikat o błędzie jest podobny do:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Przyczyna

Kod błędu usługi Azure AD 50126 oznacza, `AllowCloudPasswordValidation` że zasady nie zostały ustawione przez dzierżawcę.

### <a name="resolution"></a>Rozwiązanie

Administrator firmy dzierżawy usługi Azure AD powinien włączyć usługę Azure AD do używania skrótów haseł dla użytkowników wspieranych przez usługę ADFS.  Zastosuj `AllowCloudPasswordValidationPolicy` jak pokazano w artykule [Użyj pakietu zabezpieczeń przedsiębiorstwa w hdinsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant lub unauthorized_client, 50034

### <a name="issue"></a>Problem

Logowanie nie powiedzie się z kodem błędu 50034. Komunikat o błędzie jest podobny do:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Przyczyna

Nazwa użytkownika jest niepoprawna (nie istnieje). Użytkownik nie używa tej samej nazwy użytkownika, która jest używana w witrynie Azure portal.

### <a name="resolution"></a>Rozwiązanie

Użyj tej samej nazwy użytkownika, która działa w tym portalu.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant lub unauthorized_client, 50053

### <a name="issue"></a>Problem

Konto użytkownika jest zablokowane, kod błędu 50053. Komunikat o błędzie jest podobny do:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Przyczyna

Zbyt wiele prób logowania przy za pomocą nieprawidłowego hasła.

### <a name="resolution"></a>Rozwiązanie

Poczekaj około 30 minut, zatrzymaj wszystkie aplikacje, które mogą próbować uwierzytelnić.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant lub unauthorized_client, 50053

### <a name="issue"></a>Problem

Hasło wygasło, kod błędu 50053. Komunikat o błędzie jest podobny do:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Przyczyna

Hasło wygasło.

### <a name="resolution"></a>Rozwiązanie

Zmień hasło w witrynie Azure portal (w systemie lokalnym), a następnie poczekaj 30 minut na synchronizację, aby nadrobić zaległości.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problem

Otrzymuj komunikat `interaction_required`o błędzie .

### <a name="cause"></a>Przyczyna

Zasady dostępu warunkowego lub uwierzytelniania wieloskładnikowego są stosowane do użytkownika. Ponieważ uwierzytelnianie interakcyjne nie jest jeszcze obsługiwane, użytkownika lub klaster należy wykluczyć z uwierzytelniania wieloskładnikowego lub dostępu warunkowego. Jeśli zdecydujesz się zwolnić klaster (zasady zwolnienia oparte na `ServiceEndpoints` adresie IP), upewnij się, że usługa AD jest włączona dla tej sieci wirtualnej.

### <a name="resolution"></a>Rozwiązanie

Użyj zasad dostępu warunkowego i wyłącz klastry HDInisght z usługi MFA, jak pokazano w [temacie Konfigurowanie klastra HDInsight z pakietem zabezpieczeń przedsiębiorstwa przy użyciu usług domenowych Active Directory platformy Azure](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Odmowa logowania

### <a name="issue"></a>Problem

Logowanie jest odrzucane.

### <a name="cause"></a>Przyczyna

Aby przejść do tego etapu, uwierzytelnianie OAuth nie jest problemem, ale uwierzytelnianie Kerberos jest. Jeśli ten klaster jest wspierany przez usługi ADLS, logowanie OAuth powiodło się przed podjęciem próby umowy Kerberos auth. W klastrach WASB logowania OAuth nie jest podejmowana próba. Może istnieć wiele przyczyn niepowodzenia protokołu Kerberos — takie jak skróty haseł są niezsynchronizowane, konto użytkownika zablokowane w usługach Azure AD DS i tak dalej. Skróty haseł są synchronizowane tylko wtedy, gdy użytkownik zmieni hasło. Podczas tworzenia wystąpienia usług Azure AD DS rozpocznie synchronizację haseł, które są zmieniane po utworzeniu. Nie będzie synchronizować z mocą wsteczną haseł, które zostały ustawione przed jego powstaniem.

### <a name="resolution"></a>Rozwiązanie

Jeśli uważasz, że hasła mogą nie być zsynchronizowane, spróbuj zmienić hasło i poczekać kilka minut na synchronizację.

Spróbuj SSH do Musisz spróbować uwierzytelnić (kinit) przy użyciu tych samych poświadczeń użytkownika, z komputera, który jest przyłączony do domeny. SSH do węzła head / edge z użytkownikiem lokalnym, a następnie uruchomić kinit.

---

## <a name="kinit-fails"></a>kinit nie powiedzie się

### <a name="issue"></a>Problem

Kinit zawodzi.

### <a name="cause"></a>Przyczyna

Różni się.

### <a name="resolution"></a>Rozwiązanie

Aby kinit się powiódł, `sAMAccountName` musisz znać swoje (jest to krótka nazwa konta bez obszaru). `sAMAccountName`jest zwykle prefiksem konta `bob@contoso.com`(jak bob w ). Dla niektórych użytkowników może być inaczej. Będziesz potrzebował możliwości przeglądania / wyszukiwania katalogu, aby `sAMAccountName`dowiedzieć się .

Sposoby `sAMAccountName`na znalezienie :

* Jeśli możesz zalogować się do Ambari za pomocą lokalnego administratora Ambari, spójrz na listę użytkowników.

* Jeśli masz [komputer z systemem Windows przyłączony do domeny,](../../active-directory-domain-services/manage-domain.md)możesz korzystać ze standardowych narzędzi usługi Windows AD do przeglądania. Wymaga to działającego konta w domenie.

* W węźle głównym można używać poleceń SAMBA do wyszukiwania. Wymaga to prawidłowej sesji Protokołu Kerberos (pomyślnego kinitu). wyszukiwanie reklam netto "(userPrincipalName=bob*)"

    Wyniki wyszukiwania / przeglądania powinny `sAMAccountName` wyświetlać atrybut. Ponadto, można spojrzeć na `pwdLastSet` `badPasswordTime`inne `userPrincipalName` atrybuty, takie jak , itp., aby zobaczyć, czy te właściwości pasują do tego, czego oczekujesz.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit kończy się niepowodzeniem z niepowodzeniem preauthentication

### <a name="issue"></a>Problem

Kinit kończy `Preauthentication` się niepowodzeniem.

### <a name="cause"></a>Przyczyna

Nieprawidłowa nazwa użytkownika lub hasło.

### <a name="resolution"></a>Rozwiązanie

Sprawdź swoją nazwę użytkownika i hasło. Sprawdź również inne właściwości opisane powyżej. Aby włączyć pełne debugowanie, `export KRB5_TRACE=/tmp/krb.log` uruchom z sesji przed wypróbowaniem kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Zadanie / HDFS polecenie nie powiedzie się z powodu TokenNotFoundException

### <a name="issue"></a>Problem

Polecenie Job / HDFS `TokenNotFoundException`kończy się niepowodzeniem z powodu .

### <a name="cause"></a>Przyczyna

Nie znaleziono wymaganego tokenu dostępu OAuth, aby zadanie/polecenie zakończyło się pomyślnie. Sterownik ADLS / ABFS spróbuje pobrać token dostępu OAuth z usługi poświadczeń przed dokonaniem żądań magazynu. Ten token zostanie zarejestrowany po zalogowaniu się do portalu Ambari przy użyciu tego samego użytkownika.

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że pomyślnie zalogowano się do portalu Ambari raz za pośrednictwem nazwy użytkownika, którego tożsamość jest używana do uruchamiania zadania.

---

## <a name="error-fetching-access-token"></a>Błąd podczas pobierania tokenu dostępu

### <a name="issue"></a>Problem

Użytkownik otrzymuje `Error fetching access token`komunikat o błędzie .

### <a name="cause"></a>Przyczyna

Ten błąd występuje sporadycznie, gdy użytkownicy próbują uzyskać dostęp do ADLS Gen2 przy użyciu list ACL i tokenu Kerberos wygasła.

### <a name="resolution"></a>Rozwiązanie

* W przypadku usługi Azure Data Lake Storage Gen1 posprzątaj pamięć podręczną przeglądarki i zaloguj się ponownie do ambari.

* W przypadku usługi Azure Data `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` Lake Storage Gen2 uruchom dla użytkownika, który użytkownik próbuje się zalogować

---

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
