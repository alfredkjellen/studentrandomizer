<script lang="ts" type="text/javascript">
  import { pricingPlans } from "./pricing_plans"
  import Pay from "$lib/components/Pay.svelte";

  // Module context
  export let highlightedPlanId: string = ""
  export let callToAction: string
  export let center = true;

import { onMount } from 'svelte';

onMount(() => {
  initializePaddle();
});

import { goto } from "$app/navigation";

import type { Paddle } from "@paddle/paddle-js";

export let priceLabel = "";
let billingCountry = "SE";

  let SELLER_ID = 195713;   
  let production = false;
  let sellerId = 0;
  let paddleEnv: any = "";

  if (production) {
    sellerId = Number(SELLER_ID);
    paddleEnv = "production";
  } else {
    sellerId = 20810;
    paddleEnv = "sandbox";
  }
  
    // Function to initialize Paddle
    export const initializePaddle = () => {  
        if (window.Paddle?.Initialize) {
        try {
          window.Paddle.Environment.set(paddleEnv);
          window.Paddle.Initialize({
            token: "test_6cac96937e2d114709715ba6135",
            checkout: {
              settings: {
                displayMode: "overlay",
                theme: "light",
                locale: "en",
                allowedPaymentMethods: [
                  "apple_pay",
                  "card",
                  "google_pay",
                  "paypal",
                ],
              },
            },
            eventCallback: function (event) {
              if (event.name == "checkout.completed") {
                handleCheckoutCompleted();
              }
            },
          });
          console.log("Paddle initialized successfully");


          getPrices(billingCountry);



        } catch (error) {
          console.error("Failed to initialize Paddle:", error);
        }
      } else {
        console.error("Paddle.Initialize is not available");
      }
  };

  const itemsList = [
    {
      priceId: "pri_01j1tps56znk8rp7a1spc35m63", // Ensure this is a valid sandbox price ID pri_01j3betg37s5k8vrcqnqkqk4w9
      quantity: 1,
    },
  ];

  export async function startCheckout() {
    try {
      const transaction = await window.Paddle?.Checkout.open({
        items: itemsList,
      });

      console.log("Checkout successful:", transaction);
    } catch (error: any) {
      console.error("Checkout error:", error);
      // You can add more specific error handling here
      if (error.code === "PADDLE_NETWORK_ERROR") {
        console.error("Network error. Please check your connection.");
      } else if (error.code === "PADDLE_INVALID_INPUT") {
        console.error(
          "Invalid input. Please check your price ID and other details.",
        );
      }
      // Add more specific error cases as needed
    }
  }

  export async function handleCheckoutCompleted() {
    try {
      await goto("/signup");
      console.log("Navigation successful");
    } catch (error) {
      console.error("Navigation error:", error);
    }
  }

  export function getPrices(billingCountry:string) {

    var request = {
      items: itemsList,
      address: {
        countryCode: billingCountry,
      },
    };

    window.Paddle?.PricePreview(request).then((result:any) => {
      result.data.details.lineItems.forEach((item:any) => {
        priceLabel = item.formattedTotals.total;
      });

    }).catch((error:string) => {
      console.error(error);
    });
  }




  class Currency{
    currency: string;
    country:string;

    constructor(currency: string, country: string) {
      this.currency = currency;
      this.country = country;
    }
  }

  let currencies = [new Currency("SEK", "SE"), new Currency("USD", "US")];





</script>

<div
  class="flex flex-col lg:flex-row gap-10 {center
    ? 'place-content-center'
    : ''} flex-wrap"
>
  {#each pricingPlans as plan}
    <div
      class="flex-none card card-bordered {plan.id === highlightedPlanId
        ? 'border-primary'
        : 'border-gray-200'} shadow-xl flex-1 flex-grow min-w-[260px] max-w-[370px] p-6"
    >
      <div class="flex flex-col h-full">
        <div class="text-xl font-bold">{plan.name}</div>
        <p class="mt-2 text-sm leading-relaxed">
          {plan.description}
        </p>
        <div class="mt-auto pt-4 text-sm">
          Includes every feature, such as:
          <ul class="list-disc list-inside mt-2 space-y-1">

            <ul></ul>
            <li >Randomize students in classrooms and groups </li>
            <li >Create customized classroom layouts</li>
            <li >Save and edit class lists</li>
            <li>Multiple teacher accounts</li>
          </ul>
        </div>

        <span class="text-primary text-2xl font-bold mt-5">1 month free <span class="text-sm text-gray-400">then</span> </span>
      

        <div class="mt-2">



          <span class="text-lg font-bold">799 kr</span>
          <span class="text-gray-400 font-bold">/ year</span>
          <div class="mt-6 pt-4 flex-1 flex flex-row items-center">
              
<a href="/signup" class="btn btn-primary w-[80%] mx-auto">Get started</a>
          </div>
          <div class="text-gray-400 text-sm flex justify-center mt-2">no credit card required</div>
        </div>
      </div>
    </div>
  {/each}
</div>
<div class="text-gray-400 text-sm flex justify-center mt-2">you can cancel anytime under the trial period</div>

<!-- 

<div class="flex justify-center">
  <select bind:value={billingCountry} on:change={() => getPrices(billingCountry)} class="select select-bordered w-full max-w-xs mt-5">
    <option disabled selected>Select Preferable Currency</option>
    {#each currencies as currency}
      <option value={currency.country}>{currency.currency}</option>
    {/each}
  </select>
</div>
 -->
