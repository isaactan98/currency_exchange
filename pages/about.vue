<template>
  <main class="container mx-auto px-8 min-h-screen mt-8">
    <!-- generate about page for exchange currency -->
    <div class="flex flex-col items-center justify-center">
      <h1 class="text-4xl font-bold text-center">About</h1>
      <p class=" mt-8 text-justify">
        This is a simple currency exchange app. You can exchange your currency
        with the current exchange rate. Exchange Rates API began as a simple REST API to allow developers to add
        currency exchange rate data to their applications. The API has been a trusted data source for millions of
        developers around the world. ExchangeRatesApi.io is now proudly part of apilayer, a company that provides
        developers with powerful data and conversion APIs.
      </p>
    </div>

    <div class="mt-8">
      <h2 class="text-2xl text-center font-bold">Exchange Rate</h2>
      <p class="mt-4 text-justify">
        The exchange rate is updated every 30 minutes. The exchange rate is
        provided by
        <a href="https://exchangeratesapi.io/" target="_blank" rel="noopener noreferrer"
          class="text-blue-500">exchangeratesapi.io</a>.
        The exchange rate is based on the European Central Bank reference rates. Exchange Rates API provides access to
        170 global currencies and over 14,000 exchange rate conversion pairs. Both historical and real-time exchange
        rates are available with a data refresh rate as often as 60 seconds. Time-series and fluctuation data are also
        available based on your specific needs.
      </p>
    </div>
  </main>
</template>

<script>
export default {
  head() {
    return {
      title: "About",
    };
  },
};
</script>

<style>

</style>
