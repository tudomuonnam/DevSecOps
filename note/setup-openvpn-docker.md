https://medium.com/@gurayy/set-up-a-vpn-server-with-docker-in-5-minutes-a66184882c45
https://github.com/kylemanna/docker-openvpn
cd ..
mkdir vpn-data && touch vpn-data/vars
docker run -v $PWD/vpn-data:/etc/openvpn --rm myownvpn ovpn_genconfig -u udp://IP_ADDRESS:3000
docker run -v $PWD/vpn-data:/etc/openvpn --rm -it numcakevpn ovpn_initpki
key: numcake
docker run -v $PWD/vpn-data:/etc/openvpn -d -p 3001:1194/udp --cap-add=NET_ADMIN numcakevpn

docker run -v $PWD/vpn-data:/etc/openvpn --rm -it numcakevpn easyrsa build-client-full user_vpn nopass

docker run -v $PWD/vpn-data:/etc/openvpn --rm numcakevpn ovpn_getclient user_vpn > user1.ovpn
