proxies:
==============================（vless-reality-vision节点）
- name: vless-reality-vision节点               #可自定义名称
  type: vless
  server: 157.245.55.112                             #解析的域名或IP
  port: 3001                                 #自定义的端口
  uuid: ad0b383a-5aaf-44d9-a5e4-82f3f06ba533   #自定义的uuid
  network: tcp
  udp: true
  tls: true
  flow: xtls-rprx-vision
  servername: home.korea-ip.me                   #自定义的第三方域名
  reality-opts: 
    public-key: GQOcnUQGxWuNyXSwGnIXHp6eInwXh8RADieG83lMW2w    #自定义的public-key
    short-id: 93d6bb9d                       #自定义的short-id
  client-fingerprint: chrome                  #自定义的浏览器指纹

- name: hysteria2自签节点                             #可自定义名称
  type: hysteria2                                      
  server: 157.245.55.112                                     #服务器本地IP
  port: 3003                                         #自定义的端口，不支持多端口
  password: 123456                                    #自定义的密码
  alpn:
    - h3
  sni: www.bing.com                                    #自签证书
  skip-cert-verify: true
  fast-open: true



#（三区）==============================（代理分流）
#分流组可自行创建添加，proxies参数下的name节点名称，按需求自行增减，确保出现的name节点名称在二区代理协议中可查找
proxy-groups:
- name: 负载均衡
  type: load-balance
  url: https://cp.cloudflare.com/generate_204
  interval: 300
  strategy: round-robin
  proxies:
    - warp-wireguard节点                                  #自定义添加协议的name字段
    - vless-ws-tls节点
    - vless-ws节点
    - hysteria2自签节点
    - vless-reality-vision节点

- name: 自动选择
  type: url-test
  url: https://cp.cloudflare.com/generate_204
  interval: 300
  tolerance: 50
  proxies:
    - vless-reality-vision节点                            #自定义添加协议的name字段
    - vless-reality-grpc节点
    
- name: 🌍选择代理节点
  type: select
  proxies:
    - 负载均衡                                            #自定义添加协议的name字段
    - 自动选择
    - DIRECT
    - vless-reality-vision节点                                    
    - vless-reality-grpc节点
    - vless-xtls-rprx-vision节点
    - vless-ws-tls节点
    - vless-ws节点
    - vmess-ws-tls节点
    - vmess-ws节点
    - trojan-tcp-tls节点
    - hysteria1证书节点
    - hysteria1自签节点
    - hysteria2证书节点
    - hysteria2自签节点
    - tuic-V5证书节点
    - tuic-V5自签节点 
    - warp-wireguard节点
