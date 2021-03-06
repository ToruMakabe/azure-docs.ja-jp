仮想ネットワーク ゲートウェイは、"ゲートウェイ サブネット" と呼ばれる特定のサブネットを使用します。 VPN ゲートウェイ接続を作成する場合は、常にゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットに指定する IP アドレスは、仮想ネットワークの構成時に指定した仮想ネットワーク IP アドレス範囲に含まれます。 VPN ゲートウェイで使用されるリソースとサービスの一部では、IP アドレスが必要です。 ゲートウェイ サブネット IP アドレスは、こういったゲートウェイ サービスに割り当てられます。 そのため、ゲートウェイ サブネットには VM やその他のリソースを直接デプロイしないでください。

ゲートウェイ サブネットを作成するときに、そのゲートウェイ サブネットに含まれる IP アドレスの数を指定します。 指定したゲートウェイ サブネットのサイズは、作成する VPN ゲートウェイの構成によって異なります。 一部の構成では、他の構成よりも多くの IP アドレスを割り当てる必要があります。 この記事の手順では、特定のサイズのゲートウェイ サブネットが必要な場合に指定します。 /29 と同程度の小規模なゲートウェイ サブネットを作成することは技術的に可能ですが、一般的には /28 または /27 を選択してさらに多くのアドレスが含まれる大規模なサブネットを作成することをお勧めします。 大規模なゲートウェイ サブネットを使用すると、将来の構成に対応するのに十分な IP アドレスをゲートウェイ サービスに割り当てることができます。

ゲートウェイ サブネットを作成する際は、名前を "GatewaySubnet " にする必要があります。 サブネットに "GatewaySubnet" という名前を付けると、Azure にゲートウェイ サービスの作成場所が通知されます。 サブネットをこれ以外の名前にすると、VPN ゲートウェイ構成は失敗します。
