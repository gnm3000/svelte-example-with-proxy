<script>
	import axios from 'axios';
	import {onMount} from 'svelte';
	import pkceChallenge from "pkce-challenge";
	export let name;
	let footprint;
	let quotes="";

	onMount(async () =>{
		
			let challenge = pkceChallenge(43);

			let client_id = "ffa22c88-174b-4d93-8dca-13511ef0ca31";
			let url = `http://localhost:3000/shopper/auth/v1/organizations/f_ecom_bdkj_s09/oauth2/authorize?redirect_uri=http://localhost:3000/callback&client_id=${client_id}&code_challenge=${challenge.code_challenge}&hint=guest`;
			
			const res = await axios.get(url, {
                headers: {
                    
                },
                params: {},
            });
            quotes = JSON.stringify(res.data) ;
			let code1=res.data.response.request.uri.query;
			let code_array = code1.split("&");
			let usid=(code_array[0].split("=")[1])
			let code=(code_array[1].split("=")[1])
			console.log(usid,code)
			const data_2 = {
			"Media-Type": "application/x-www-form-urlencoded",
			usid: usid,
			grant_type: "authorization_code_pkce",
			redirect_uri: "http://localhost:3000/callback",
			code_verifier: challenge.code_verifier,
			client_id: client_id,
			};
			const url_token = `http://localhost:3000/shopper/auth/v1/organizations/f_ecom_bdkj_s09/oauth2/token?grant_type=authorization_code_pkce&client_id=${client_id}&code_verifier=${challenge.code_verifier}&redirect_uri=http://localhost:3000/callback&code=${code}`;
			const res_token = await axios.post(url_token, {
                headers: {
                    "Content-Type": "application/x-www-form-urlencoded",
					"Media-Type": "application/x-www-form-urlencoded",
					"Access-Control-Allow-Origin": "*",
                },
                params: {},
				data:data_2
            });
			console.log("hey",JSON.parse(res_token.data.body).access_token);
			let access_token=JSON.parse(res_token.data.body).access_token;
			//let body = res_token.data.body; // body.access_token,body.usid
			//console.log("BODY access_token",body["access_token"]);
			const url_categories = `http://localhost:3000/product/shopper-products/v1/organizations/f_ecom_bdkj_s09/categories?siteId=SaksOff5th&ids=1408474395181058&levels=2&locale=default`;
			const data_categories = { "Media-Type": "application/json" };
			const response_categories = await axios.get(url_categories,{
				headers: {
          "Content-Type": "application/x-www-form-urlencoded",
          "Media-Type": "application/x-www-form-urlencoded",
          "Access-Control-Allow-Origin": "*",
          Authorization: `Bearer ${access_token}`,
        }
			});
			console.log("response_categories",response_categories)
			let categories_data = JSON.parse(response_categories.data.response.body)
			quotes=(JSON.stringify(categories_data.data));



			
		
		

	})

</script>

<main>
	<h1>Hello {name}!</h1>
	<p>{quotes}</p>
	<p>Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn how to build Svelte apps.</p>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>