# 🖼️ NFT Minter JR - Contrato ERC-1155 personalizado

¡Bienvenidos! Este proyecto contiene un **contrato inteligente en Solidity** que permite **crear NFTs únicos** (tokens digitales) con **metadatos guardados en la blockchain**. Está pensado para ser simple, seguro y... ¡hasta tu tía lo puede entender! 👵✨

---

## 💡 ¿Qué hace este contrato?

Este contrato permite al "dueño" del contrato (es decir, quien lo despliega) **crear NFTs con datos personalizados**, como por ejemplo:

- 🏷️ **Título**
- 📝 **Descripción**
- 🧾 **Nombre**
- 📅 **Fecha**
- 🖼️ **URL de imagen**

Cada vez que se crea un NFT, se guarda esa información junto con un número de ID único (Token ID), y se envía a la dirección que el dueño indique. Es decir: ¡es como regalarle una postal digital única a alguien, pero grabada para siempre en la blockchain! 🎁🔐

---

## ⚙️ ¿Qué tecnologías usa?

- ✅ **Solidity 0.8.20**
- ✅ Contrato tipo **ERC-1155 personalizado**
- ✅ Los metadatos (título, descripción, etc.) se guardan directamente **en la blockchain**
- ✅ Usa un evento llamado `TransferSingle` para que otras apps puedan detectar cuándo se creó un NFT

---

## 🔐 Seguridad

- Solo el dueño del contrato puede crear NFTs (gracias a la función `onlyOwner`)
- Nadie más puede modificar lo que ya fue minteado
- La imagen es fija o se pasa como URL, ¡y se guarda de forma permanente junto al NFT!

---

## 👀 ¿Cómo se ve un NFT?

Una vez creado, se puede ver en la web que está conectada a este contrato. Cada tarjeta NFT muestra:



// SPDX-License-Identifier: MIT
//contact_Address= 0x2E14CD8D9ecfF34c941c69acE8FD9c17020Ef6Cb 
pragma solidity ^0.8.20;

contract CustomERC1155 {
    uint256 public currentTokenId = 0;
    address public owner;

    struct Metadata {
        string titulo;
        string descripcion;
        string nombre;
        string fecha;
        string imageUrl;
    }


    mapping(uint256 => Metadata) public metadatas;
    mapping(address => mapping(uint256 => uint256)) public balances;

    event TransferSingle(address indexed operator, address indexed from, address indexed to, uint256 id, uint256 value);

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Solo el owner puede ejecutar esta funcion");
        _;
    }

    function mintConMetadata(
        address to,
        string memory titulo,
        string memory descripcion,
        string memory nombre,
        string memory fecha,
        string memory imageUrl
    ) public onlyOwner {
        uint256 tokenId = currentTokenId;
        balances[to][tokenId] = 1;

        metadatas[tokenId] = Metadata({
            titulo: titulo,
            descripcion: descripcion,
            nombre: nombre,
            fecha: fecha,
            imageUrl: imageUrl
        });

        emit TransferSingle(msg.sender, address(0), to, tokenId, 1);
        currentTokenId++;
    }

    function balanceOf(address account, uint256 id) external view returns (uint256) {
        return balances[account][id];
    }

    // Returns metadata for a token
    function getMetadata(uint256 tokenId) public view returns (
        string memory titulo,
        string memory descripcion,
        string memory nombre,
        string memory fecha,
        string memory imageUrl
    ) {
        Metadata memory data = metadatas[tokenId];
        return (data.titulo, data.descripcion, data.nombre, data.fecha, data.imageUrl);
    }

    // Standard ERC1155 URI function (returns a dummy URI since we store metadata on-chain)
    function uri(uint256 tokenId) public view returns (string memory) {
        return string(abi.encodePacked(
            "data:application/json;utf8,{\"name\":\"NFT #", 
            toString(tokenId), 
            "\",\"description\":\"On-chain metadata NFT\",",
            "\"image\":\"", metadatas[tokenId].imageUrl, "\"}"
        ));
    }

    // Helper function to convert uint to string
    function toString(uint256 value) internal pure returns (string memory) {
        if (value == 0) return "0";
        uint256 temp = value;
        uint256 digits;
        while (temp != 0) {
            digits++;
            temp /= 10;
        }
        bytes memory buffer = new bytes(digits);
        while (value != 0) {
            digits -= 1;
            buffer[digits] = bytes1(uint8(48 + uint256(value % 10)));
            value /= 10;
        }
        return string(buffer);
    }
}



ABI:

[
	{
		"inputs": [
			{
				"internalType": "address",
				"name": "to",
				"type": "address"
			},
			{
				"internalType": "string",
				"name": "titulo",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "descripcion",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "nombre",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "fecha",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "imageUrl",
				"type": "string"
			}
		],
		"name": "mintConMetadata",
		"outputs": [],
		"stateMutability": "nonpayable",
		"type": "function"
	},
	{
		"inputs": [],
		"stateMutability": "nonpayable",
		"type": "constructor"
	},
	{
		"anonymous": false,
		"inputs": [
			{
				"indexed": true,
				"internalType": "address",
				"name": "operator",
				"type": "address"
			},
			{
				"indexed": true,
				"internalType": "address",
				"name": "from",
				"type": "address"
			},
			{
				"indexed": true,
				"internalType": "address",
				"name": "to",
				"type": "address"
			},
			{
				"indexed": false,
				"internalType": "uint256",
				"name": "id",
				"type": "uint256"
			},
			{
				"indexed": false,
				"internalType": "uint256",
				"name": "value",
				"type": "uint256"
			}
		],
		"name": "TransferSingle",
		"type": "event"
	},
	{
		"inputs": [
			{
				"internalType": "address",
				"name": "account",
				"type": "address"
			},
			{
				"internalType": "uint256",
				"name": "id",
				"type": "uint256"
			}
		],
		"name": "balanceOf",
		"outputs": [
			{
				"internalType": "uint256",
				"name": "",
				"type": "uint256"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "address",
				"name": "",
				"type": "address"
			},
			{
				"internalType": "uint256",
				"name": "",
				"type": "uint256"
			}
		],
		"name": "balances",
		"outputs": [
			{
				"internalType": "uint256",
				"name": "",
				"type": "uint256"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [],
		"name": "currentTokenId",
		"outputs": [
			{
				"internalType": "uint256",
				"name": "",
				"type": "uint256"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "uint256",
				"name": "tokenId",
				"type": "uint256"
			}
		],
		"name": "getMetadata",
		"outputs": [
			{
				"internalType": "string",
				"name": "titulo",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "descripcion",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "nombre",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "fecha",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "imageUrl",
				"type": "string"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "uint256",
				"name": "",
				"type": "uint256"
			}
		],
		"name": "metadatas",
		"outputs": [
			{
				"internalType": "string",
				"name": "titulo",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "descripcion",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "nombre",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "fecha",
				"type": "string"
			},
			{
				"internalType": "string",
				"name": "imageUrl",
				"type": "string"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [],
		"name": "owner",
		"outputs": [
			{
				"internalType": "address",
				"name": "",
				"type": "address"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "uint256",
				"name": "tokenId",
				"type": "uint256"
			}
		],
		"name": "uri",
		"outputs": [
			{
				"internalType": "string",
				"name": "",
				"type": "string"
			}
		],
		"stateMutability": "view",
		"type": "function"
	}
]
