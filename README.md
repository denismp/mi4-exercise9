# mi4-exercise9
Create Document Registry App

Exercises: Create Document Registry App
In this exercise, we will deploy a simple Smart Contract using Remix IDE in Ganache CLI – command version of Ganache, a personal Ethereum blockchain perfect for our development purposes. Then we will create a simple JavaScript app, which will interact with the contract using Web3 API and MetaMask. The DApp is a simple registry for documents in which only the contract creator can add, but anyone can see them. For the example, we will add images of the documents not in the contract, but in IPFS. In the contract, we will only store the hash of the images.
 
You need http server for this project, because metamask doesn’t inject web3 to html pages.
Install http-server with
npm install –g http-server
After that go to the project and run in terminal 
http-server
Problem 1.	Setup the Project
1.	Create a folder called Document-Registry. Then create the following files document-registry.css, document-registry.js and index.html
2.	Create a folder lib/ and import the two libraries from the resources given in the site. 
 
3.	Copy the following css code and paste it into document-registry.css
document-registry.css
@import url('https://fonts.googleapis.com/css?family=Lato:300,400');

body {
    font-family: Lato;
    font-weight: 300;
}

#menu {
    font-weight: 400;
    background: #DDD;
    text-align: center;
    padding: 5px;
    line-height: 1.5;
    border-radius: 3px;
    overflow: auto;
}

#menu a {
    text-decoration: none;
    padding: 5px 10px;
    border-radius: 5px;
}

#menu a:hover {
    background: #BBB;
}

main > section {
    display: none;
    padding: 20px 5px;
}

section#viewHome {
    display: block;
}

section h1 {
    margin: 10px 0px;
    font-size: 1.2em;
}

#infoBox, #errorBox, #loadingBox {
    width: 80%;
    margin: 10px auto;
    color: white;
    text-align: center;
    padding: 5px;
    border-radius: 3px;
}

#infoBox>header, #errorBox>header, #loadingBox>header {
    float: right;
    margin-right: 5px;
}

#infoBox>header:hover, #errorBox>header:hover, #loadingBox>header:hover {
    transition: all 0.2s;
    font-weight: bold;
    cursor: pointer;
}

#loadingBox {
    background: #7CB3E9;
}
#infoBox {
    background: #393;
}
#errorBox {
    background: #F50;
}

footer {
    background: #DDD;
    padding: 5px 10px;
    font-size: 0.8em;
    text-align: center;
    border-radius: 3px;
}

div {
    border: 1px solid #DDD;
    border-radius: 4px;
    padding: 5px;
    max-width: 100%;
    height: auto;
    margin: 10px;
}

Problem 2.	Create HTML
index.html
<!DOCTYPE html>

<html>

<head>
    <meta charset="utf-8" />
    <title>Document Registry DApp</title>
    <link rel="stylesheet" type="text/css" href="document-registry.css" />
    <script src="lib/jquery-3.3.1.min.js"></script>
    <script src="lib/sha256.min.js"></script>
    <script src="document-registry.js"></script>
</head>

<body>
<header id="menu">
    <a href="#" id="linkHome">Home</a>
    <a href="#" id="linkSubmitDocument">Submit Document</a>
    <a href="#" id="linkGetDocuments">View Documents</a>
</header>

<main>
    <section id="loadingBox">Loading ...</section>

    <section id="infoBox"><header>x</header><p>Info</p></section>

    <section id="errorBox"><header>x</header><p>Error</p></section>

    <section id="viewHome">
        <h1>Document Registry</h1>
        Welcome to the "Document Registry" DApp. This distributed app runs on the Ethereum blockchain network and holds a registry of documents as Solidity smart contract.
        <ul>
            <li>The registry keeps images of documents along with their publish date.</li>
            <li><b>Contract owner</b> can submit new documents to be stored on the blockchain.</li>
            <li><b>Users</b> can view the existence of certain documents in the registry.</li>
        </ul>
    </section>

    <section id="viewSubmitDocument">
        <h1>Submit a Document</h1>
        <p>Contract owners can register (upload) new documents to the "Document Registry" smart contract on the Ethereum blockchain decentralized network.</p>
        <input type="file" id="documentForUpload" /><br/>
        <input type="button" id="documentUploadButton" value="Submit" />
    </section>

    <section id="viewGetDocuments">
        <h1>View Documents</h1>
        <p>Blockchain users can view documents in the registry on the Ethereum network.</p>
    </section>
</main>

<footer>Document Registry - JavaScript & Ethereum DApp</footer>
</body>
</html>
Problem 3.	Create the Smart Contract
1.	Create initial contract and define the version:
 
2.	Create a simple structure called Document, which stores the string hash of the document in IPFS and the date it has been added. The contract will have an array of documents. Last but not least, an address which will store the owner’s address:
 
3.	A simple modifier onlyOwner which makes sure the caller of the method is the contract owner:
 
4.	Create the constructor, which will make the executor owner of the contract:
 
5.	Create an add function, which adds a document in the array by giving its hash and the current time (block.timestamp) in the structure. The function returns the date of the added document:
 
6.	Finally, create getDocumentsCount () and getDocument (uint index) functions, which returns the count of the documents and a document by a given index.
 
Problem 4.	Create JavaScript Front-end
1.	Create the jQuery function which will run as soon as the pages DOM become safe to manipulate:
 
2.	In the function, insert the constants of the contract address and contract ABI, which will get after deploying our contract after a while:
 
3.	Now it is time to add IPFS API to our project. Go to index.html and add in the script:
<script src="https://unpkg.com/ipfs-api/dist/index.js"></script>
 
Initialize the IPFS:
 
4.	Insert the code below for the buttons to the functions and visibility:
document-registry.js
$('#linkHome').click(function () {
    showView("viewHome")
});
$('#linkSubmitDocument').click(function () {
    showView("viewSubmitDocument")
});
$('#linkGetDocuments').click(function () {
    $('#viewGetDocuments div').remove();
    showView("viewGetDocuments");
    viewGetDocuments();
});
$('#documentUploadButton').click(uploadDocument);

// Attach AJAX "loading" event listener
$(document).on({
    ajaxStart: function () {
        $("#loadingBox").show()
    },
    ajaxStop: function () {
        $("#loadingBox").hide()
    }
});

function showView(viewName) {
    // Hide all views and show the selected view only
    $('main > section').hide();
    $('#' + viewName).show();
}

function showInfo(message) {
    $('#infoBox>p').html(message);
    $('#infoBox').show();
    $('#infoBox>header').click(function () {
        $('#infoBox').hide();
    });
}

function showError(errorMsg) {
    $('#errorBox>p').html("Error: " + errorMsg);
    $('#errorBox').show();
    $('#errorBox>header').click(function () {
        $('#errorBox').hide();
    });
}
5.	Create a function for uploading the document:
 
6.	Check if there is any file to upload, if not, show error message:
 
7.	Create a file reader to read the given document as an array buffer:
 
8.	In the onload property check whether web3 is included. Then use Buffer to get a buffer from the file reader result. 
 
9.	Then use IPFS to add the document. After it is uploaded, call the contract by its ABI and address and add the document.
 
10.	Now the function showing the documents. First, it will check if web3 is defined:
 
11.	Then call the contract and get the count of the documents. After the documents count is returned, we will make a simple for cycle iterating through the index and get each document. For each document, from the received image hash create a URL with “https://ipfs.io/ipfs/{hash}” and add it as a source of an <img> tag. Beautify the received date and place it in a simple <p> tag. Then append them both in a <div> and append it to viewGetElements.
 
Problem 5.	Play with the Document Registry
1.	Install ganache by the command:
npm install –g ganache-cli
2.	Start ganache-cli by typing on the command line:
ganache-cli
3.	Go to remix.ethereum.org and paste the smart contract code.
4.	Compile and choose the Web3 Provider to connect to ganache.
5.	Click [Run] and [Create] the contract.
 
6.	After the creation, the contract will appear beneath. Copy the contract address:
 
7.	To get the ABI, go back to [Compile] and then [Details]:
 
 
8.	Go back to the project and paste the contract address and the ABI for the variables we previously wrote:
 
9.	Start the IPFS daemon:
 
10.	Do not forget to log in MetaMask and switch to Local Network:
 
 
11.	Import at least one of the private keys from ganache-cli (contract creator address and one more random).
 
12.	Open index.html:
 
13.	Upload any kind of a file in the [Submit Document] section. Click [Submit]. MetaMask will appear, Click [Submit]:
 
 
14.	A message will appear:
 
In Ganache:
 


15.	Go to [View Documents]:
 

What to Submit?
Submit a zip file (e.g. your-username-document-registry.zip) with the source code, the smart contract and at least two screenshots with a successful submission of a document and one with of the “View Documents” section.

NOTE:  This code does not work!

