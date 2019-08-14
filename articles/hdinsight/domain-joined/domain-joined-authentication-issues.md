---
title: Problemy z uwierzytelnianiem w usłudze Azure HDInsight
description: Problemy z uwierzytelnianiem w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/09/2019
ms.openlocfilehash: 9167757625347ba1857dc0d666ffdc6d25291a61
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990847"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problemy z uwierzytelnianiem w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

W przypadku bezpiecznych klastrów objętych usługą Azure Data Lake (Gen1 lub Gen2), gdy użytkownicy domeny logują się do usług klastra za pośrednictwem bramy HDI (na przykład zalogowanie się do portalu Apache Ambari), program HDI Gateway spróbuje najpierw uzyskać token OAuth od Azure Active Directory (Azure AD) , a następnie Pobierz bilet protokołu Kerberos z usługi Azure AD DS. Uwierzytelnianie może zakończyć się niepowodzeniem w żadnym z tych etapów. Ten artykuł ma na celu debugowanie niektórych z tych problemów.

Gdy uwierzytelnianie nie powiedzie się, zostanie wyświetlony monit o podanie poświadczeń. Jeśli anulujesz to okno dialogowe, zostanie wydrukowany komunikat o błędzie. Poniżej przedstawiono niektóre typowe komunikaty o błędach:

## <a name="issue-invalid_grant-or-unauthorized_client-50126"></a>Problem: invalid_grant lub unauthorized_client, 50126

Logowanie użytkowników federacyjnych kończy się niepowodzeniem z kodem błędu 50126 (logowanie dla użytkowników w chmurze kończy się niepowodzeniem). Komunikat o błędzie jest podobny do:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Przyczyna

Kod błędu usługi Azure AD 50126 oznacza `AllowCloudPasswordValidation` , że zasady nie zostały ustawione przez dzierżawcę.

### <a name="resolution"></a>Rozwiązanie

Administrator firmy w dzierżawie usługi Azure AD powinien zezwolić usłudze Azure AD na używanie skrótów haseł dla użytkowników z kopiami zapasowymi usług ADFS.  Zastosuj, `AllowCloudPasswordValidationPolicy` jak pokazano w artykule, [Użyj pakiet Enterprise Security w usłudze HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="issue-invalid_grant-or-unauthorized_client-50034"></a>Problem: invalid_grant lub unauthorized_client, 50034

Logowanie kończy się niepowodzeniem z kodem błędu 50034. Komunikat o błędzie jest podobny do:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Przyczyna

Nazwa użytkownika jest niepoprawna (nie istnieje). Użytkownik nie korzysta z tej samej nazwy użytkownika, która jest używana w Azure Portal.

### <a name="resolution"></a>Rozwiązanie

Użyj tej samej nazwy użytkownika, która działa w tym portalu.

---




## <a name="issue-invalid_grant-or-unauthorized_client-50053"></a>Problem: invalid_grant lub unauthorized_client, 50053

Konto użytkownika jest zablokowane, kod błędu 50053. Komunikat o błędzie jest podobny do:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Przyczyna

Zbyt wiele prób logowania przy użyciu niepoprawnego hasła.

### <a name="resolution"></a>Rozwiązanie

Zaczekaj 30 minut lub tak, Zatrzymaj wszystkie aplikacje, które mogą próbować uwierzytelniać.

---

## <a name="issue-invalid_grant-or-unauthorized_client-50053"></a>Problem: invalid_grant lub unauthorized_client, 50053

Hasło wygasło, kod błędu 50053. Komunikat o błędzie jest podobny do:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Przyczyna

Hasło wygasło.

### <a name="resolution"></a>Rozwiązanie

Zmień hasło w Azure Portal (w systemie lokalnym), a następnie poczekaj 30 minut na przechwycenie synchronizacji.

---

## <a name="issue-interaction_required"></a>Problem: interaction_required

Odbieranie komunikatu `interaction_required`o błędzie.

### <a name="cause"></a>Przyczyna

Zasady dostępu warunkowego lub uwierzytelnianie wieloskładnikowe są stosowane do użytkownika. Ponieważ uwierzytelnianie interaktywne nie jest jeszcze obsługiwane, użytkownik lub klaster muszą zostać wykluczone z usługi MFA/dostępu warunkowego. Jeśli zdecydujesz się na wykluczenie klastra (Zasady wykluczania oparte na adresach IP), upewnij `ServiceEndpoints` się, że Usługa AD jest włączona dla tej sieci wirtualnej.

### <a name="resolution"></a>Rozwiązanie

Użyj zasad dostępu warunkowego i Wyklucz klastry HDInisght z usługi MFA, jak pokazano w temacie [Konfigurowanie klastra usługi HDInsight z pakiet Enterprise Security przy użyciu Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="issue-sign-in-denied"></a>Problem: Odmowa logowania

Odmowa logowania.

### <a name="cause"></a>Przyczyna

Aby przejść do tego etapu, uwierzytelnianie OAuth nie jest problemem, ale uwierzytelnianie Kerberos to. Jeśli ten klaster jest objęty ADLS, logowanie OAuth zakończyło się pomyślnie przed podjęciem próby uwierzytelnienia Kerberos. W klastrach WASB nie podjęto próby logowania przy użyciu protokołu OAuth. Może istnieć wiele powodów użycia protokołu Kerberos skróty haseł, które nie są zsynchronizowane, konto użytkownika zostało zablokowane na platformie Azure AD DS i tak dalej. Skróty haseł synchronizują się tylko wtedy, gdy użytkownik zmienia hasło. Podczas tworzenia wystąpienia usługi Azure AD DS rozpocznie się Synchronizowanie haseł, które zostały zmienione po utworzeniu. Nie synchronizuje wstecz hasła, które zostały ustawione przed jego rozpoczęciem.

### <a name="resolution"></a>Rozwiązanie

Jeśli uważasz, że hasła mogą nie być zsynchronizowane, spróbuj zmienić hasło i poczekaj kilka minut na synchronizację.

Spróbuj użyć protokołu SSH w celu uwierzytelnienia (Narzędzie kinit) przy użyciu tych samych poświadczeń użytkownika, z komputera, który jest przyłączony do domeny. Użyj protokołu SSH do węzła głowy/krawędzi z użytkownikiem lokalnym, a następnie uruchom narzędzie kinit.

---

## <a name="issue-kinit-fails"></a>Problem: Narzędzie kinit kończy się niepowodzeniem

### <a name="cause"></a>Przyczyna

Różni się.

### <a name="resolution"></a>Rozwiązanie

Aby narzędzie kinit się powieść, musisz znać `sAMAccountName` (to jest krótka nazwa konta bez obszaru). `sAMAccountName`jest zazwyczaj prefiksem konta (na przykład Roberta `bob@contoso.com`in). W przypadku niektórych użytkowników może się to różnić. Będziesz potrzebować możliwości przeglądania i przeszukiwania katalogu w celu uzyskania informacji `sAMAccountName`.

Sposoby znajdowania `sAMAccountName`:

* Jeśli możesz zalogować się do usługi Ambari przy użyciu lokalnego administratora usługi Ambari, zapoznaj się z listą użytkowników.

* Jeśli masz przyłączoną do [domeny maszynę z systemem Windows](../../active-directory-domain-services/manage-domain.md), możesz użyć standardowych narzędzi Windows AD do przeglądania. Wymaga to konta działającego w domenie.

* W węźle głównym można wyszukiwać za pomocą poleceń SAMBA. Wymaga to prawidłowej sesji protokołu Kerberos (pomyślne narzędzie kinit). Wyszukiwanie w usłudze net AD "(userPrincipalName = Robert *)"

    W wynikach wyszukiwania/przeglądania powinna być wyświetlana wartość `sAMAccountName` atrybutu. Ponadto można przeglądać inne atrybuty `pwdLastSet` `userPrincipalName` , takie jak, itp `badPasswordTime`., aby sprawdzić, czy te właściwości są zgodne z oczekiwaniami.

---

## <a name="issue-kinit-fails-with-preauthentication-failure"></a>Problem: Narzędzie kinit kończy się niepowodzeniem z błędem uwierzytelniania wstępnego

### <a name="cause"></a>Przyczyna

Nieprawidłowa nazwa użytkownika lub hasło.

### <a name="resolution"></a>Rozwiązanie

Sprawdź nazwę użytkownika i hasło. Sprawdź również inne opisane powyżej właściwości. Aby włączyć pełne debugowanie, uruchom `export KRB5_TRACE=/tmp/krb.log` polecenie z sesji przed podjęciem próby narzędzie kinit.

---

## <a name="issue-job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Problem: Polecenie Job/HDFS nie powiodło się z powodu TokenNotFoundException

### <a name="cause"></a>Przyczyna

Nie znaleziono wymaganego tokenu dostępu OAuth, aby zadanie/polecenie zakończyło się pomyślnie. Sterownik ADLS/ABFS podejmie próbę pobrania tokenu dostępu OAuth z usługi Credential przed przekazaniem żądań magazynu. Ten token jest rejestrowany po zalogowaniu się do portalu Ambari przy użyciu tego samego użytkownika.

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że po pomyślnym zalogowaniu się do portalu Ambari za pomocą nazwy użytkownika, której tożsamość jest używana do uruchomienia zadania.

---

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
