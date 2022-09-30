<template>
  <main class="min-h-screen mt-4 bg-base-100">
    <div
      class="max-w-xl px-4 mx-auto grid items-center"
      v-if="current_currency != null"
    >
      <div class="border rounded p-4">
        <div class="flex justify-between items-center border rounded mb-4">
          <div class="flex flex-1 justify-between">
            <div class="flex items-center">
              <select
                class="select"
                name=""
                id=""
                v-if="country != null"
                @change="updateCurrentCurrency"
              >
                <optgroup v-for="(c, k) in country" :key="k" :label="k">
                  <option
                    v-for="(cc, kk) in c"
                    :key="kk"
                    :selected="kk == current_currency"
                  >
                    {{ kk }}
                  </option>
                </optgroup>
              </select>
              <label v-else class="btn loading"></label>
            </div>
            <div>
              <input
                type="number"
                id="default"
                class="input text-right max-w-xs"
                value="1"
                @keyup="updateValue"
              />
            </div>
          </div>
        </div>

        <div class="border-b my-4"></div>

        <div v-if="currency.length != 0">
          <div
            class="flex justify-between items-center border rounded mb-4"
            v-for="(x, l) in currency"
            :key="l"
            :id="x"
          >
            <div class="flex flex-1 justify-between px-2">
              <div class="flex items-center">{{ x }}</div>
              <input type="number" class="input text-right" />
            </div>
            <div class="border-l">
              <button class="btn" @click="deleteCurrency(x)">X</button>
            </div>
          </div>
        </div>

        <div class="w-full" v-else>
          <div class="btn btn-ghost w-full loading"></div>
        </div>

        <div class="flex justify-between mt-4">
          <label
            class="btn btn-sm rounded modal-button"
            for="show_country"
            @click="getCountry"
          >
            + Add Currency
          </label>
          <button class="btn btn-sm rounded" id="refresh" @click="getConvert">
            <!-- refresh svg -->
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              class="inline-block w-5 h-5 stroke-current"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M15 12a3 3 0 11-6 0 3 3 0 016 0zm-3 3a3 3 0 100-6 3 3 0 000 6zm9-3a9 9 0 11-18 0 9 9 0 0118 0z"
              ></path>
            </svg>
          </button>
        </div>
      </div>
    </div>
    <div class="max-w-xl px-4 mx-auto grid items-center" v-else>
      <label for="" class="btn loading"></label>
    </div>
    <!-- search country -->
    <input type="checkbox" id="show_country" class="modal-toggle" />
    <div class="modal modal-bottom sm:modal-middle cursor-pointer">
      <div class="modal-box min-h-screen">
        <div class="sticky top-0 bg-base-100">
          <div class="flex justify-between">
            <div class="flex-1">
              <label class="btn btn-sm rounded" for="show_country">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  fill="none"
                  viewBox="0 0 24 24"
                  class="inline-block w-5 h-5 stroke-current"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M15 19l-7-7 7-7"
                  ></path>
                </svg>
              </label>
            </div>
            <div class="flex-1 text-center flex items-center">
              <h1 class="font-bold text-sm">Search Country</h1>
            </div>
            <div class="flex-1 text-right"></div>
          </div>
          <div class="mt-2">
            <input
              type="text"
              class="input w-full"
              placeholder="Search"
              id="search"
              autocomplete="off"
              @keyup="search"
            />
          </div>
        </div>
        <div v-if="country != null" class="mt-4">
          <div v-for="(c, k) in country" :key="k" class="border-b mb-2">
            <div class="flex items-center font-bold text-lg">{{ k }}</div>
            <div v-for="(cc, kk) in c" :key="kk" class="p-2 currency_parent">
              <label
                v-if="currency.includes(kk)"
                class="flex justify-between font-bold currency"
                :id="kk"
              >
                <span class="mr-2">{{ kk }}</span>
                <span>-</span>
                <span class="flex-1 ml-2">{{ cc }}</span>
              </label>

              <label
                for="show_country"
                class="flex justify-between currency"
                :id="kk"
                @click="addCurrency(kk)"
                v-else
              >
                <span class="mr-2">{{ kk }}</span>
                <span>-</span>
                <span class="flex-1 ml-2">{{ cc }}</span>
              </label>
            </div>
          </div>
        </div>
        <div v-else class="w-full text-center">
          <label for="" class="btn loading"></label>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  head() {
    return {
      title: "List View",
    };
  },
  data() {
    return {
      currency: [],
      value: 0,
      country: null,
      current_currency: null,
    };
  },
  mounted() {
    const check_value = new Promise((resolve, reject) => {
      if (localStorage.getItem("current_currency") != null) {
        resolve(localStorage.getItem("current_currency"));
      } else {
        reject("No currency");
      }
    });

    if (localStorage.getItem("current_currency") != null) {
      check_value
        .then((res) => {
          this.current_currency = res;
          this.getCountry();
        })
        .then(() => {
          if (localStorage.getItem("value") != null) {
            this.value = JSON.parse(localStorage.getItem("value"));
          } else {
            this.value = 1;
            localStorage.setItem("value", this.value);
          }

          if (localStorage.getItem("currency") != null) {
            this.currency = JSON.parse(localStorage.getItem("currency"));
            this.getConvert();
          }
        })
        .catch((err) => {
          console.log(err);
        });
    } else {
      this.getCurrentCountry();
    }
  },
  methods: {
    async getCountry() {
      const requestOptions = {
        method: "GET",
        headers: {
          "X-RapidAPI-Key": process.env.RAPID_API,
          "X-RapidAPI-Host":
            "currency-conversion-and-exchange-rates.p.rapidapi.com",
        },
      };
      const res = await fetch(
        "https://currency-conversion-and-exchange-rates.p.rapidapi.com/symbols",
        requestOptions
      );
      const data = await res.json();
      var arr = [];
      Object.entries(data.symbols).map(([key, value]) => {
        if (arr[value.charAt(0)] != null) {
          arr[value.charAt(0)][key] = value;
        } else {
          arr[value.charAt(0)] = {};
          arr[value.charAt(0)][key] = value;
        }
        arr[value.charAt(0)] = Object.fromEntries(
          Object.entries(arr[value.charAt(0)]).sort()
        );
      });
      this.country = Object.fromEntries(Object.entries(arr).sort());
    },

    async search(value) {
      const currency = document.querySelectorAll(".currency");
      Array.prototype.forEach.call(currency, function (el) {
        if (el.innerText.toLowerCase().indexOf(value.target.value) > -1) {
          el.parentElement.style.opacity = "1";
          el.parentElement.style.display = "";
        } else {
          el.parentElement.style.opacity = "0";
          setTimeout(() => {
            el.parentElement.style.display = "none";
          }, 500);
        }
      });
    },

    addCurrency(e) {
      this.currency.push(e);

      localStorage.setItem("currency", JSON.stringify(this.currency));

      this.getConvert();
    },

    deleteCurrency(e) {
      this.currency.splice(this.currency.indexOf(e), 1);

      localStorage.setItem("currency", JSON.stringify(this.currency));

      this.getConvert();
    },

    updateCurrentCurrency(e) {
      this.current_currency = e.target.value;
      localStorage.setItem("current_currency", e.target.value);
    },

    updateValue(e) {
      this.value = e.target.value;

      localStorage.setItem("value", this.value);

      this.getConvert();
    },

    async getCurrentCountry() {
      await fetch("https://ipapi.co/currency/")
        .then((response) => {
          return response.text();
        })
        .then((data) => {
          this.current_currency = data;
          localStorage.setItem("current_currency", data);
        })
        .catch(function (error) {
          console.log(error);
        });
    },

    async getConvert() {
      if (this.currency.length == 0) {
        alert("Please select currency");
        return;
      }

      this.currency = [];

      const all_currencies = JSON.parse(localStorage.getItem("currency"));

      const def = document.getElementById("default");

      all_currencies.forEach((e) => {
        const requestOptions = {
          method: "GET",
          headers: {
            "X-RapidAPI-Key": process.env.RAPID_API,
            "X-RapidAPI-Host":
              "currency-conversion-and-exchange-rates.p.rapidapi.com",
          },
        };

        fetch(
          "https://currency-conversion-and-exchange-rates.p.rapidapi.com/convert?from=" +
            this.current_currency +
            "&to=" +
            e +
            "&amount=" +
            def.value,
          requestOptions
        )
          .then((response) => {
            this.currency = all_currencies;
            return response.json();
          })
          .then((data) => {
            console.log(data);
            document.getElementById(e).children[0].children[1].value =
              data.result;
          })
          .catch(function (error) {
            console.log(error);
          });
      });
    },
  },
};
</script>

<style>
.currency_parent {
  transition: all 0.25s ease-in-out;
}
</style>
