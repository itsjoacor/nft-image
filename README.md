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
