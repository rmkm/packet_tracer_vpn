# packet_tracer_vpn
## About
Cisco Packet Tracer を使って IPsec VPN を構築するチュートリアルです。

## 準備
Cisco Packet Tracer 6.3 以上 (2019年6月14日現在の最新は7.2.1)

## VPN
### VPNの概要
参考 https://www.infraexpert.com/study/study10.html  
VPN（Virtual Private Nework）とは、仮想的なプライベートネットワーク接続のことです。VPNにより
インターネットなどの公衆網を利用する場合でも、IPsec等の高度なセキュリティを実装させられるので、
安全に企業の拠点間通信を実現できます。また、安価なFTTHの広帯域な回線をWANとして利用できます。
### VPNの種類
VPNはインターネットVPNとIP-VPNの大きく2つに分類できます。インターネットVPNはインターネット
などの公衆網を利用したVPNのことです。IP-VPNとは、通信事業者が提供するクローズド（閉じれらた）
IPネットワークを利用したVPNのこと。インターネットVPNは正確には2種類があります。セキュリティ
プロトコルにIPsecを使用したIPsec-VPNと、セキュリティプロトコルにSSLを使用した SSL-VPN です。
IP-VPNは、MPLS-VPN のことであり、プライベートIP網内の経路情報の探索にMPLSを採用しています。

| IPsecプロトコル | 役割 | プロトコル種別 |
| ---- | ---- | ---- |
| インターネットVPN | IPsec-VPN | セキュリティプロトコルにIPsecを使用したインターネットを利用したVPNのこと |
| インターネットVPN | SSL-VPN | セキュリティプロトコルにSSLを使用したインターネットを利用したVPNのこと |
| IP-VPN | MPLS-VPN | 通信事業者のプライベートIP網内で経路情報の探索にMPLSを採用したVPNのこと |

### VPNを支える技術
VPN接続は、鍵管理、暗号化、ハッシュ関数、認証によって実現されます。
1. **鍵管理方式**  
IPsecでは Diffie-Hellman (DH) を採用しています。これは暗号鍵を安全に手渡すために使用します。
1. **暗号化方式**  
共通鍵暗号と公開鍵暗号の2つがあります。データを暗号化するために使用します。  
共通鍵暗号(RC4, DES, 3DES, AES)  
公開鍵暗号(RSA)  
チュートリアルではAESを使用します。
1. **ハッシュ関数**  
MD5とSHAなどがあります。認証技術と併用して使用します。  
チュートリアルではSHAを使用します。
1. **認証方式**  
HMAC（ Keyed-Hashing for Message Authentication Code ）とデジタル署名があり，HMACは共通鍵を使用し，デジタル署名は公開鍵を使用します。HMACとはハッシュ関数と共通鍵を組み合わせて計算するMACのことです。  
チュートリアルではSHAを使用したHMACを使用します。

## Security Architecture for IP (IPsec)
### IPsecの概要
IPsecは、暗号化システムの技術によりネットワーク層にて、データのセキュリティを保護するのに使用されるプロトコルです。 IPsecには  
- Authentication Header (AH)
- Encapsulated Security Payload (ESP)  
の２つのプロトコルがあります。

| VPNの分類 | 役割 | プロトコル種別 |
| ---- | ---- | ---- |
| AH | パケットが改ざんされていないかどうか認証を行う。(HMAC)<br>パケットの暗号化はできない。 | IPプロトコル番号 51 |
| ESP | パケットが改ざんされていないかどうか認証を行う。(HMAC)<br>パケットのペイロード部の暗号化 ( DES or 3DES or AES ) を行う。 | IPプロトコル番号 50 |

RFC2406とRFC4303形式のESPには認証トレーラ機能があるので，通常はAHを併用せず，ESPのみを使用します。

### IPsecの通信モード
IPsecにおける通信モードには、トランスポートモードとトンネルモードの2つのモードがあります。
- トランスポートモード: IPsecが実装されたホスト間でのIPsec-VPN
- トンネルモード: IPsecが実装されたルータ間でのIPsec-VPN  
IPsecによる通信はトンネルモードを利用していることが多いと言えます。トンネルモードにおけるIPsecが実装されたルータのことをVPNゲートウェイと呼びます。

