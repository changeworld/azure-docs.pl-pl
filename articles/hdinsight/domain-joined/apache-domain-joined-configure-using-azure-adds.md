---
title: Pakiet Enterprise Security konfiguracji za pomocą usługi Azure Active Directory Domain Services — Azure HDInsight
description: Dowiedz się, jak instalowanie i konfigurowanie klastra HDInsight Enterprise Security pakietu przy użyciu usługi Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 03/26/2019
ms.custom: seodec18
ms.openlocfilehash: 2b7364a2bb32f2d38f5cf9ddeddd5e4e1f928e01
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519803"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurowanie klastra usługi HDInsight z pakietem Enterprise Security przy użyciu usług Azure Active Directory Domain Services

Klastry Enterprise Security pakietu (ESP) zapewniają dostęp wielu użytkowników w klastrach usługi Azure HDInsight. Klastrami HDInsight przy użyciu ESP są podłączone do domeny, dzięki czemu użytkownicy domeny mogą używać swoich poświadczeń domeny przy użyciu klastrów i uruchamiać zadania obsługi danych big data. 

W tym artykule dowiesz się, jak skonfigurować klaster HDInsight przy użyciu ESP przy użyciu Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]  
>ESP jest w wersji ogólnie dostępnej w usłudze HDI 3.6 dla platformy Apache Spark, interaktywny i Apache Hadoop. Jest ESP dla typów klastrów Apache HBase i Apache Kafka w wersji zapoznawczej.

## <a name="enable-azure-ad-ds"></a>Włączanie usługi Azure AD DS

> [!NOTE]  
> Tylko Administratorzy dzierżawy mają uprawnienia do włączenia usług AD DS Azure. Czy za pomocą magazynu klastra usługi Azure Data Lake Storage (ADLS) Gen1 lub Gen2 tylko dla użytkowników, którzy chcą uzyskać dostęp do klastra przy użyciu uwierzytelnienia Kerberose podstawowe, należy wyłączyć uwierzytelnianie wieloskładnikowe (MFA). Możesz użyć [zaufane adresy IP](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#trusted-ips) lub [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) wyłączania uwierzytelnianie wieloskładnikowe dla określonych użytkowników tylko wtedy gdy uzyskują dostęp do klastra HDInsight zakres adresów IP w sieci Wirtualnej. Jeśli używasz warunkowego dostępu upewnij się, ten punkt końcowy usługi AD w włączone w sieci Wirtualnej HDInsight.
>
>Jeśli magazyn klastra usługi Azure Blob Storage (WASB), nie należy wyłączać usługi MFA.



Włączanie usługi Azure AD DS jest wymaganiem wstępnym, przed utworzeniem klastra HDInsight z ESP. Aby uzyskać więcej informacji, zobacz [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Po włączeniu usług AD DS Azure wszystkich użytkowników i obiekty rozpocząć synchronizowanie z usługi Azure Active Directory (AAD) do usługi Azure AD — DS domyślnie. Długość operacji synchronizacji jest zależna od liczby obiektów w usłudze Azure AD. Synchronizacja może potrwać kilka dni w setkach tysięcy obiektów. 

Klienci mogą wybrać opcję Synchronizuj tylko grupy, którzy potrzebują dostępu do klastrów HDInsight. Tej opcji tylko określone grupy synchronizacji jest nazywany *zakresu synchronizacji*. Zobacz [konfigurowania zakresu synchronizacji z usługi Azure AD do domeny zarządzanej](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) instrukcje.

Podczas włączania bezpiecznego protokołu LDAP, umieść nazwę domeny w polu Nazwa podmiotu i alternatywnej nazwy podmiotu w certyfikacie. Na przykład, jeśli nazwa domeny to *contoso100.onmicrosoft.com*, upewnij się, że takiej samej nazwie istnieje w nazwie podmiotu certyfikatu i alternatywnej nazwy podmiotu. Aby uzyskać więcej informacji, zobacz [domeny zarządzanej przez Konfigurowanie bezpiecznego protokołu LDAP dla usługi Azure AD — DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md). Poniżej przedstawiono przykład tworzenia certyfikatu z podpisem własnym i nazwy domeny (*contoso100.onmicrosoft.com*) w nazwie podmiotu i DnsName (alternatywnej nazwy podmiotu):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
``` 

## <a name="check-azure-ad-ds-health-status"></a>Sprawdź stan kondycji usług AD DS platformy Azure
Wyświetlanie stanu kondycji usługi Azure Active Directory Domain Services, wybierając **kondycji** w obszarze **Zarządzaj** kategorii. Upewnij się, że jest w stanie usługi Azure AD — DS kolor zielony (uruchomione), a synchronizacja została zakończona.

![Kondycja usługi Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Tworzenie i autoryzować tożsamości zarządzanej

A **przypisanych przez użytkownika z tożsamości zarządzanej** umożliwia prosty i bezpieczny operacji usługi domeny. Po przypisaniu roli współautora usługi domeny HDInsight do tożsamości zarządzanej ją odczytać, tworzenia, modyfikacji i Usuń operacje usługi domeny. Niektóre operacje, takie jak tworzenie jednostek organizacyjnych usług domain services i zasad usługi są wymagane przez pakiet HDInsight Enterprise Security. W każdej subskrypcji można utworzyć zarządzanych tożsamości. Aby uzyskać więcej informacji o zarządzanych tożsamości ogólnie rzecz biorąc, zobacz [zarządzanych tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). Aby uzyskać więcej informacji na temat zarządzanych pracy tożsamości w usłudze Azure HDInsight, zobacz [zarządzanych tożsamości w usłudze Azure HDInsight](../hdinsight-managed-identities.md).

Aby skonfigurować klastry ESP, Utwórz tożsamości zarządzanej przypisanych przez użytkownika, jeśli nie masz jeszcze takiego. Zobacz [Utwórz, listy, usuwania lub przypisać rolę do przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) instrukcje. Następnie przypisz **Współautor usługi domeny HDInsight** roli tożsamość zarządzaną w kontroli dostępu do usługi Azure AD DS (wymagane dokonanie tego przypisania roli są uprawnienia administratora DS usługi AAD).

![Usługa Azure Active Directory Domain Services dostępu formantu](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Przypisywanie **Współautor usługi domeny HDInsight** roli gwarantuje, że ta tożsamość ma poprawne (w imieniu) dostęp do wykonywania operacji usług domeny, takich jak tworzenie jednostek organizacyjnych, usuwanie jednostek organizacyjnych, itp., w domenie usługi Katalogowej usługi AAD.

Po utworzeniu tożsamości zarządzanej i biorąc pod uwagę odpowiednią rolę, administrator usługi AAD DS można zdefiniować kto może korzystać z tej tożsamości zarządzanej. Aby skonfigurować użytkowników na potrzeby tożsamości zarządzanej, administrator powinien wybrać tożsamość zarządzaną w portalu, a następnie kliknij **kontrola dostępu (IAM)** w obszarze **Przegląd**. Następnie po prawej stronie, Przypisz **Operator tożsamości zarządzanych** ról do użytkowników lub grup, które chcesz utworzyć klastry HDInsight ESP. Na przykład administrator usługi AAD DS można przypisać tę rolę do grupy "MarketingTeam" tożsamość "sjmsi" zarządzanych, jak pokazano na poniższej ilustracji. Pozwoli to zagwarantować, że właściwym osobom w organizacji mają dostęp do użycia tej tożsamości zarządzanej na potrzeby tworzenia klastrów ESP.

![HDInsight zarządzane przypisania roli Operator tożsamości](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Zagadnienia dotyczące pracy w sieci

> [!NOTE]  
> Azure AD DS musi zostać wdrożony w sieci wirtualnej na podstawie usługi Azure Resource Manager (ARM). Klasyczne sieci wirtualne nie są obsługiwane dla usługi Azure AD — DS. Zapoznaj się [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-network) Aby uzyskać więcej informacji.

Po włączeniu usług AD DS platformy Azure, lokalnego serwera usługi nazw domen (DNS, Domain Name System) działa na maszynach wirtualnych (VM) AD. Konfigurowanie usługi Azure usług AD DS Virtual Network (VNET) do użycia tych niestandardowych serwerów DNS. Aby znaleźć odpowiednie adresy IP, wybierz **właściwości** w obszarze **Zarządzaj** kategorii i spójrz na adresy IP na liście poniżej **adresu IP w sieci wirtualnej**.

![Znajdź adresy IP serwerów DNS lokalnym](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Zmień konfigurację serwerów DNS w sieci Wirtualnej usług AD DS platformy Azure, aby użyć tych niestandardowych adresów IP, wybierając **serwerów DNS** w obszarze **ustawienia** kategorii. Następnie kliknij przycisk radiowy obok pola **niestandardowe**, wprowadź adres IP pierwszego w poniższym polu tekstowym i kliknij przycisk **Zapisz**. Dodaj dodatkowe adresy IP, korzystając z tej samej procedury.

![Trwa aktualizowanie konfiguracji DNS sieci Wirtualnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Łatwiej można umieścić wystąpienia usług AD DS Azure wraz z klastrów HDInsight w tej samej sieci wirtualnej platformy Azure. Jeśli planujesz używać różnych sieciach wirtualnych, aby kontroler domeny jest widoczny dla maszyn wirtualnych w usłudze HDI musi komunikacji równorzędnej tych sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [komunikacja równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md). 

Po nawiązaniu komunikacji równorzędnej między sieciami wirtualnymi, należy skonfigurować HDInsight sieci Wirtualnej do użycia niestandardowego serwera DNS i wprowadzić prywatnych adresów IP usług AD DS Azure jako adresy serwera DNS. Obie sieci wirtualne, używając tych samych serwerów DNS, niestandardową nazwę domeny zostanie rozwiązany do prawego adresu IP i będzie dostępny z HDInsight. Na przykład jeśli nazwa domeny "contoso.com" następnie po wykonaniu tego kroku pingowanie "contoso.com" powinna być rozpoznawana adresów IP usług AD DS platformy Azure po prawej stronie. 

![Konfigurowanie serwerów DNS niestandardowe dla wirtualnej sieci równorzędnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Jeśli używasz reguł sieciowych grup zabezpieczeń w podsieci usługi HDInsight, należy umożliwić [wymaganych adresów IP](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) dla ruchu przychodzącego i ruchu wychodzącego. 

**Aby przetestować** Jeśli sieci są poprawnie skonfigurowane, Dołącz do HDInsight sieci Wirtualnej/podsieci maszyny Wirtualnej systemu windows i zbadaj nazwy domeny (jego powinien rozwiązać adres IP), a następnie uruchom **ldp.exe** uzyskać dostępu do domeny usług AD DS platformy Azure. Następnie **przyłączanie maszyny Wirtualnej systemu windows do domeny, aby potwierdzić** się wszystkie wymagane wywołania RPC sukcesem między klientem i serwerem. Można również użyć **nslookup** o potwierdzenie sieci dostępu do konta magazynu lub dowolnego zewnętrzne bazy danych można użyć (na przykład zewnętrzny Magazyn metadanych Hive lub Ranger bazy danych).
Upewnij się, że wszystkie [wymagane porty](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) dozwolonych elementów znajdują się w podsieci usługi AAD DS reguły sieciowej grupy zabezpieczeń, jeśli DS usługi AAD jest zabezpieczony przez sieciową grupę zabezpieczeń. Jeśli maszyna wirtualna przyłączenie do domeny systemu windows to się powiedzie, można przejść do następnego kroku i utworzyć klastry z ESP.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Tworzenie klastra HDInsight z ESP

Po skonfigurowaniu poprawnie w poprzednich krokach, następnym krokiem jest tworzenie klastra HDInsight z usługą ESP włączone. Podczas tworzenia klastra usługi HDInsight, możesz włączyć pakiet Enterprise Security w **niestandardowe** kartę. Jeśli wolisz skorzystać z szablonu usługi Azure Resource Manager dla wdrożenia, użyj środowisko pracy w portalu po i Pobierz szablon wstępnie wypełnione na ostatniej stronie "Podsumowanie" w celu wykorzystania w przyszłości.

> [!NOTE]  
> Pierwsze sześć znaków nazwy klastrów ESP musi być unikatowa w danym środowisku. Na przykład jeśli masz wiele klastrów ESP w różnych sieciach wirtualnych, należy wybrać convension nazewnictwa, która gwarantuje, że pierwsze sześć znaków na nazwy klastra są unikatowe.


![Weryfikacja domeny pakietu zabezpieczeń Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Po włączeniu ESP, typowych błędów konfiguracji związane z usługi Azure AD — DS będzie można automatycznie wykryte i sprawdzone. Po rozwiązaniu tych problemów można przejść do następnego kroku: 

![Pakiet zabezpieczeń w usłudze Azure HDInsight przedsiębiorstwa nie powiodła się weryfikacja domeny](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Po utworzeniu klastra HDInsight z ESP, musisz podać następujące parametry:

- **Administrator klastra**: Wybierz administratora klastra z usługi zsynchronizowanych Azure AD — DS. To konto domeny musi być już zsynchronizowane i dostępne w usłudze Azure AD-DS.

- **Klaster grup dostępu**: Grupy zabezpieczeń użytkowników, których chcesz synchronizować i mieć dostęp do klastra powinien być dostępny w usłudze Azure AD — DS. Na przykład HiveUsers grupy. Aby uzyskać więcej informacji, zobacz [tworzyć grupy i dodawać członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **ADRES URL PROTOKOŁU LDAPS**: Przykładem jest ldaps://contoso.com:636.

Poniższy zrzut ekranu przedstawia Konfiguracja zakończyła się pomyślnie, w witrynie Azure portal:

![Konfiguracja usługi Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Tożsamość zarządzaną, który został utworzony można wybrać w z listy rozwijanej przypisanych przez użytkownika z tożsamości zarządzanej podczas tworzenia nowego klastra.

![Konfiguracja usługi Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Kolejne kroki
* Konfigurowanie zasad usługi Hive i uruchamiania zapytań programu Hive, zobacz [zasady skonfiguruj Apache Hive HDInsight klastrów przy użyciu ESP](apache-domain-joined-run-hive.md).
* Aby połączyć się z klastrami HDInsight przy użyciu ESP, przy użyciu protokołu SSH, zobacz [używanie protokołu SSH z opartą na systemie Linux platformą Apache Hadoop w HDInsight z systemów Linux, Unix lub OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
