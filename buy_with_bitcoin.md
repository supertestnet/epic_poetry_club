---
layout: default
title: Buy with bitcoin
---

<script src="https://supertestnet.github.io/bitcoin-chess/js/qrcode.js"></script>

<div class="buy_msg">
    <p>Loading your <span class="filetype">loading...</span>:</p>
    <p><span class="filename">loading...</span> by venato_verba</p>
    <p><button class="buy_item">Buy this item</button></p>
</div>
<p class="error_msg hidden">Error, you did not select an item for purchase</p>

<style>
    .black-bg {
        width: 100%;
        position: fixed;
        top: 0;
        left: 0;
        background-color: black;
        opacity: .5;
        width: 100vw;
        height: 100vh;
    }
    .modal {
        position: fixed;
        box-sizing: border-box;
        top: 50%;
        left: 50%;
        transform: translate(-50%,-50%);
        width: 90%;
        max-width: 560px;
        background-color: white;
        border-radius: 1rem;
        padding: 20px;
        color: black;
        text-align: center;
        word-wrap: break-word;
    }
    .modal * {
        color: black;
    }
    .modal input, .modal textarea {
        max-width: 90%;
    }
    .hidden {
        display: none !important;
    }
</style>
<script>
    var modalVanish = () => {
        $( ".black-bg" ).classList.add( "hidden" );
        $( ".modal" ).classList.add( "hidden" );
    }
    var showModal = content => {
        $( ".modal" ).innerHTML = `<div class="x_modal" style="position: absolute;right: 1rem;top: 0.5rem;font-size: 2rem; cursor: pointer; color: black;" onclick="modalVanish()">&times;</div>`;
        $( ".modal" ).innerHTML += `<div style="overflow-y: auto; max-height: 80vh; margin-top: 1.5rem;">${content}</div>`;
        $( ".black-bg" ).classList.remove( "hidden" );
        $( ".modal" ).classList.remove( "hidden" );
    }
</script>

<style>
    .copy_box {
        display: flex;
        justify-content: space-between;
        align-items: center;
        height: 2.2rem;
    }
    .copy_box * {
        box-sizing: border-box;
    }
    .copy_addy {
        width: 100%;
        max-width: 78%;
        word-wrap: break-word;
        background-color: #cccccc;
        border: 1px solid black;
        padding: .3rem;
        height: 100%;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        display: block;
        user-select: none;
    }
    .copy_btn {
        width: 100%;
        max-width: 18%;
        text-align: center;
        background-color: #cccccc;
        border: 1px solid black;
        padding: .3rem;
        height: 100%;
        padding-top: 3%;
        cursor: pointer;
    }
    .toast {
        box-sizing: border-box;
        visibility: hidden;
        width: 250px;
        margin-left: -125px;
        background-color: rgb(97, 235, 52);
        color: white;
        text-align: center;
        border-radius: 2px;
        padding: 16px;
        position: fixed;
        z-index: 1;
        left: 50%;
        bottom: 30px;
    }

    .toast.show {
        visibility: visible;
        -webkit-animation: fadein 0.5s, fadeout 0.5s 2.5s;
        animation: fadein 0.5s, fadeout 0.5s 2.5s;
    }

    @-webkit-keyframes fadein {
        from {bottom: 0; opacity: 0;}
        to {bottom: 30px; opacity: 1;}
    }

    @keyframes fadein {
        from {bottom: 0; opacity: 0;}
        to {bottom: 30px; opacity: 1;}
    }

    @-webkit-keyframes fadeout {
        from {bottom: 30px; opacity: 1;}
        to {bottom: 0; opacity: 0;}
    }

    @keyframes fadeout {
        from {bottom: 30px; opacity: 1;}
        to {bottom: 0; opacity: 0;}
    }
</style>

<script>
    var $ = document.querySelector.bind( document );
    var $$ = document.querySelectorAll.bind( document );
    var hash_arr = window.location.href.substring( window.location.href.indexOf( "#" ) ).split( "#" );
    hash_arr.splice( 0, 1 );
    var $_HASH = {}
    hash_arr.forEach( item => {
        var vals = item.split( "=" );
        $_HASH[ vals[ 0 ] ] = vals[ 1 ];
    });

    //set global variables
    var getUsername = ( filename, filetype ) => {
        if ( filename === "perseus_and_medusa" && filetype === "epub" ) return "perseusandmedusaebook";
    }
    $( '.filetype' ).innerText = $_HASH[ "filetype" ];
    $( '.filename' ).innerText = $_HASH[ "filename" ];
    $( '.buy_item' ).onclick = () => {buyItem()};
    if ( !$_HASH[ "filename" ] || !$_HASH[ "filetype" ] ) {
        $( '.buy_msg' ).remove();
        $( '.error_msg' ).classList.remove( "hidden" );
    }

    //prepare dependencies
    var hexToBytes = hex => Uint8Array.from( hex.match( /.{1,2}/g ).map( byte => parseInt( byte, 16 ) ) );
    var bytesToHex = bytes => bytes.reduce( ( str, byte ) => str + byte.toString( 16 ).padStart( 2, "0" ), "" );
    var waitSomeTime = num => new Promise( resolve => setTimeout( resolve, num ) );
    var sha256 = async s => {
        if ( typeof s == "string" ) s = new TextEncoder().encode( s );
        var arr = await crypto.subtle.digest( 'SHA-256', s );
        return bytesToHex( new Uint8Array( arr ) );
    }
    var showToast = content => {
        $( '.toast' ).innerHTML = content;
        $( '.toast' ).classList.add( "show" );
        setTimeout( () => $( '.toast' ).classList.remove( "show" ), 3000 );
    }
    var createQR = content => {
        var dataUriPngImage = document.createElement( "img" ),
        s = QRCode.generatePNG( content, {
            ecclevel: "M",
            format: "html",
            fillcolor: "#FFFFFF",
            textcolor: "#000000",
            margin: 4,
            modulesize: 8,
        });
        dataUriPngImage.src = s;
        dataUriPngImage.className = "qr_code";
        dataUriPngImage.style.width = "100%";
        return dataUriPngImage;
    }

    //prepare other functions
    var buyItem = async () => {
        showModal( `<p>Loading...</p>` );
        var username = getUsername( $_HASH[ "filename" ], $_HASH[ "filetype" ] );
        var invoice_data = await getInvoiceData( username );
        var bolt11_id = invoice_data[ "id" ];
        var bolt11 = invoice_data[ "text" ];

        //display invoice
        var url = "lightning:" + bolt11;
        var a = document.createElement( "a" );
        a.href = url;
        a.target = "_blank";
        a.append( createQR( bolt11.toUpperCase() ) );
        var prep_div = document.createElement( "div" );
        prep_div.append( a );
        var div_html = prep_div.innerHTML;
        showModal( `<p>Use a bitcoin wallet that supports the lightning network</p><div style="max-width: 15rem; margin: auto;">${div_html}<div class="copy_box"><input class="copy_addy" value="${bolt11}" disabled=""> <span>&nbsp;</span><div class="copy_btn">⎘</div></div></div>` );
        $( '.copy_btn' ).onclick = () => {
            var copytext = $( '.copy_addy' );
            copytext.select();
            copytext.setSelectionRange( 0, 99999 );
            navigator.clipboard.writeText( copytext.value );
            showToast( 'copied' );
        }

        //check if it is paid
        var awaitPreimage = async bolt11_id => {
            console.log( 'looping...' );
            var settlement_data = await fetch( `https://coinos.io/api/invoice/${bolt11_id}` );
            settlement_data = await settlement_data.json();
            if ( "preimage" in settlement_data ) return settlement_data[ "preimage" ];
            await waitSomeTime( 5_000 );
            return awaitPreimage( bolt11_id );
        }
        var preimage = await awaitPreimage( bolt11_id );
        getPlink( preimage );
    }
    var getInvoiceData = async username => {
        try {
            var headers = {'Content-Type': 'application/json'};
            var body = JSON.stringify({
                invoice: {
                    usePreimage: true,
                },
                user: {
                    username,
                },
            });
            var data = await fetch('https://coinos.io/api/invoice', {
                method: 'POST',
                headers,
                body,
            });
            var json = await data.json();
            return json;
        } catch ( e ) {
            return 'could not get invoice data';
        }
    }
    var getPlink = async preimage => {
        // var data = await fetch( `/assets/public_values/public_values_for_perseus_and_medusa_ebook.txt` );
        var data = await fetch( `https://venatoverba.com/assets/public_values/public_values_for_perseus_and_medusa_ebook.txt` );
        var json = await data.json();
        var hash = await sha256( hexToBytes( preimage ) );
        var public_value = json[ hash ];
        var decryption_key = ( BigInt( `0x${preimage}` ) + BigInt( `0x${public_value}` ) ).toString( 16 ).padStart( "0", 64 );
        window.location.href = `https://venatoverba.com/download_page.html#plink=${decryption_key}`;
    }
</script>

<div class="black-bg hidden" onclick="modalVanish();"></div>
<div class="modal hidden"></div>
<div class="toast"></div>
