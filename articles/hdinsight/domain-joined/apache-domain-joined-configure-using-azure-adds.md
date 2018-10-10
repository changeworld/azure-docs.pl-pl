---
title: Konfigurowanie klastra HDInsight z pakietem Enterprise Security za pomocą usług AD DS platformy Azure
description: Dowiedz się, jak instalowanie i konfigurowanie klastra HDInsight Enterprise Security pakietu przy użyciu usługi Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/9/2018
ms.openlocfilehash: c56158a5e8df2e8781ec8e4431c75beadd154297
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901655"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurowanie klastra HDInsight z pakietem Enterprise Security za pomocą usługi Azure Active Directory Domain Services

Klastry Enterprise Security pakietu (ESP) zapewniają dostęp wielu użytkowników w klastrach usługi Azure HDInsight. Klastrami HDInsight przy użyciu ESP są podłączone do domeny, dzięki czemu użytkownicy domeny mogą używać swoich poświadczeń domeny przy użyciu klastrów i uruchamiać zadania obsługi danych big data. 

W tym artykule dowiesz się, jak skonfigurować klaster HDInsight przy użyciu ESP przy użyciu Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]
>ESP jest ogólnie w HDI 3.6 dla platformy Spark, interaktywny i Hadoop. ESP dla typów klastrów HBase i Kafka jest w wersji zapoznawczej.

## <a name="enable-azure-ad-ds"></a>Włączanie usługi Azure AD DS

> [!NOTE]
> Tylko Administratorzy dzierżawy mają uprawnienia do tworzenia wystąpienia usług AD DS platformy Azure. Czy za pomocą magazynu klastra usługi Azure Data Lake Store (ADLS) Gen1 lub Gen2, Wyłącz uwierzytelnianie wieloskładnikowe (MFA) tylko dla użytkowników, którzy będą uzyskiwać dostęp do klastra. Jeśli magazyn klastra usługi Azure Blob Storage (WASB), nie należy wyłączać usługi MFA.

Włączanie usługi Azure AD DS jest wymaganiem wstępnym, przed utworzeniem klastra HDInsight z ESP. Aby uzyskać więcej informacji, zobacz [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Po włączeniu usług AD DS Azure wszystkich użytkowników i obiekty rozpocząć synchronizowanie z usługi Azure Active Directory do usługi Azure AD — DS domyślnie. Długość operacji synchronizacji jest zależna od liczby obiektów w usłudze Azure AD. Synchronizacja może potrwać kilka dni w setkach tysięcy obiektów. 

Klienci mogą wybrać opcję Synchronizuj tylko grupy, którzy potrzebują dostępu do klastrów HDInsight. Tej opcji tylko określone grupy synchronizacji jest nazywany *zakresu synchronizacji*. Zobacz [konfigurowania zakresu synchronizacji z usługi Azure AD do domeny zarządzanej](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) instrukcje.

Po włączeniu usług AD DS platformy Azure, lokalnego serwera usługi nazw domen (DNS, Domain Name System) działa na maszynach wirtualnych (VM) AD. Konfigurowanie usługi Azure usług AD DS Virtual Network (VNET) do użycia tych niestandardowych serwerów DNS. Aby znaleźć odpowiednie adresy IP, wybierz **właściwości** w obszarze **Zarządzaj** kategorii i spójrz na adresy IP na liście poniżej **adresu IP w sieci wirtualnej**.

![Znajdź adresy IP serwerów DNS lokalnym](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Zmień konfigurację serwerów DNS w sieci Wirtualnej usług AD DS platformy Azure, aby użyć tych niestandardowych adresów IP, wybierając **serwerów DNS** w obszarze **ustawienia** kategorii. Następnie kliknij przycisk radiowy obok pola **niestandardowe**, wprowadź adres IP pierwszego w poniższym polu tekstowym i kliknij przycisk **Zapisz**. Dodaj dodatkowe adresy IP, korzystając z tej samej procedury.

![Trwa aktualizowanie konfiguracji DNS sieci Wirtualnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)



Podczas włączania bezpiecznego protokołu LDAP, umieść nazwę domeny do nazwy podmiotu lub alternatywną nazwę podmiotu w certyfikacie. Na przykład, jeśli nazwa domeny to *contoso.com*, upewnij się, takiej samej nazwie istnieje w nazwie podmiotu certyfikatu lub alternatywnej nazwy podmiotu. Aby uzyskać więcej informacji, zobacz [domeny zarządzanej przez Konfigurowanie bezpiecznego protokołu LDAP dla usługi Azure AD — DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-azure-ad-ds-health-status"></a>Sprawdź stan kondycji usług AD DS platformy Azure

Wyświetlanie stanu kondycji usługi Azure Active Directory Domain Services, wybierając **kondycji** w obszarze **Zarządzaj** kategorii. Upewnij się, że jest w stanie usługi Azure AD — DS kolor zielony (uruchomione), a synchronizacja została zakończona.

![Kondycja usługi Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Dodaj tożsamość zarządzaną

Tworzenie tożsamości zarządzanych przypisanych przez użytkownika, jeśli nie masz jeszcze takiego. Zobacz [Utwórz, listy, usuwania lub przypisać rolę do przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) instrukcje. 

Tożsamość zarządzana jest używany do upraszczają operacje usługi domeny. Ta tożsamość ma dostęp do odczytu, tworzenie, modyfikowanie i usuwanie operacji usług domeny, które są wymagane przez pakiet zabezpieczeń przedsiębiorstwa HDInsight, takie jak tworzenie jednostek organizacyjnych i zasad usługi.

Po włączeniu usług AD DS platformy Azure, tworzenie zarządzanych tożsamości przypisanych przez użytkownika i przypisz ją do **Współautor usługi domeny HDInsight** roli kontroli dostępu usług AD DS w platformie Azure.

![Usługa Azure Active Directory Domain Services dostępu formantu](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Przypisywanie zarządzanych tożsamości w celu **Współautor usługi domeny HDInsight** roli gwarantuje, że tożsamość ma prawidłowy dostęp do wykonywania pewnych operacji usług domeny w domenie usług AD DS platformy Azure. Aby uzyskać więcej informacji, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Tworzenie klastra HDInsight z ESP

Następnym krokiem jest do utworzenia klastra HDInsight z ESP włączone za pomocą usług AD DS platformy Azure.

Łatwiej można umieścić wystąpienia usług AD DS Azure wraz z klastrów HDInsight w tej samej sieci wirtualnej platformy Azure. Jeśli są one w różnych sieciach wirtualnych, tak aby maszyny wirtualne HDInsight są widoczne na kontrolerze domeny i mogą być dodawane do domeny musi komunikacji równorzędnej tych sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [komunikacja równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md). Aby sprawdzić, czy komunikacja równorzędna jest prawidłowa, dołączyć Maszynę wirtualną do HDInsight sieci Wirtualnej/podsieci i wysłać polecenie ping nazwa domeny lub uruchom **ldp.exe** uzyskać dostępu do domeny usług AD DS platformy Azure.

Po nawiązaniu komunikacji równorzędnej między sieciami wirtualnymi, należy skonfigurować HDInsight sieci Wirtualnej do użycia niestandardowego serwera DNS i wprowadzić prywatnych adresów IP usług AD DS Azure jako adresy serwera DNS. Obie sieci wirtualne, używając tych samych serwerów DNS, niestandardową nazwę domeny zostanie rozwiązany do prawego adresu IP i będzie dostępny z HDInsight. Na przykład jeśli nazwa domeny "contoso.com" następnie po wykonaniu tego kroku pingowanie "contoso.com" powinna być rozpoznawana adresów IP usług AD DS platformy Azure po prawej stronie. Następnie możesz dołączyć Maszynę wirtualną do tej domeny.

![Konfigurowanie serwerów DNS niestandardowe dla wirtualnej sieci równorzędnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Podczas tworzenia klastra usługi HDInsight, aby umożliwić pakiet Enterprise Security na niestandardowej karcie.

![Zabezpieczenia usługi Azure HDInsight i sieci](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Po włączeniu ESP, typowych błędów konfiguracji związane z usługi Azure AD — DS będzie można automatycznie wykryte i sprawdzone.

![Weryfikacja domeny pakietu zabezpieczeń Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Wczesne wykrywanie pozwala zaoszczędzić czas, co pozwala naprawić błędy przed utworzeniem klastra.

![Pakiet zabezpieczeń w usłudze Azure HDInsight przedsiębiorstwa nie powiodła się weryfikacja domeny](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Po utworzeniu klastra HDInsight z ESP, musisz podać następujące parametry:

- **Administrator klastra**: Wybierz z usługi zsynchronizowanych Azure AD — DS Administrator dla klastra. To konto musi być już zsynchronizowane i dostępne w usłudze Azure AD — DS.

- **Klaster grup dostępu**: grupy zabezpieczeń, w której użytkownicy mają być synchronizowane w klastrze powinna być dostępna w usłudze Azure AD-DS. Na przykład HiveUsers grupy. Aby uzyskać więcej informacji, zobacz [tworzyć grupy i dodawać członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **Adres URL protokołu LDAPS**: są to na przykład ldaps://contoso.com:636.

Poniższy zrzut ekranu przedstawia Konfiguracja zakończyła się pomyślnie, w witrynie Azure portal:

![Konfiguracja usługi Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Tożsamość zarządzaną, który został utworzony można wybrać w z listy rozwijanej przypisanych przez użytkownika z tożsamości zarządzanej podczas tworzenia nowego klastra.

![Konfiguracja usługi Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Kolejne kroki
* Konfigurowanie zasad usługi Hive i uruchamiania zapytań programu Hive, zobacz [Konfigurowanie zasad usługi Hive dla HDInsight klastrów przy użyciu ESP](apache-domain-joined-run-hive.md).
* Aby połączyć się z klastrami HDInsight przy użyciu ESP, przy użyciu protokołu SSH, zobacz [używanie protokołu SSH z opartą na systemie Linux platformą Hadoop w HDInsight z systemów Linux, Unix lub OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
