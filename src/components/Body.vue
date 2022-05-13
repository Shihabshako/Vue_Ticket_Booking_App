<template>
  <div class="h-full flex items-center justify-center">
    <div
      class="
        flex flex-col
        w-[31rem]
        h-[28rem]
        shadow-md
        border-solid border-2
        p-4
        rounded-lg
      "
    >
      <!-- /top container details of seats -->
      <div class="flex justify-center space-x-6">
        <div
          v-for="(seat, index) in seatStates"
          :key="index"
          class="flex items-center space-x-2"
        >
          <div
            class="w-7 h-4 rounded-md border-solid border-[1px] border-gray-900"
            :style="{ backgroundColor: seat.color }"
          ></div>
          <span>{{ seat.text }}</span>
        </div>
      </div>

      <!-- body bottom -->
      <div class="grid grid-cols-5 mt-6">
        <!-- left -->
        <div
          class="
            col-span-2
            flex flex-col
            justify-between
            border-solid border-2 border-gray-500
            p-2
            rounded-md
          "
        >
          <!-- bus top -->
          <div class="flex justify-between">
            <!-- door -->
            <div
              class="
                border-solid border-2 border-gray-500
                w-20
                h-10
                flex
                items-center
                justify-center
                rounded-md
              "
            >
              <span>Door</span>
            </div>

            <!-- driver -->
            <div
              class="
                border-solid border-2 border-gray-500
                w-20
                h-10
                flex
                items-center
                justify-center
                rounded-md
              "
            >
              <span>Driver</span>
            </div>
          </div>

          <!-- all seats -->
          <div class="grid grid-cols-4 mt-4 gap-2">
            <div
              class="
                flex
                justify-center
                items-center
                text-sm
                w-8
                h-8
                border-solid border-2
                cursor-pointer
                hover:font-bold
                duration-200
                rounded-md
              "
              v-for="(seat, index) in seats"
              :key="seat.name"
              :class="assignClassName(seat.type)"
              @click="selectSeat(index)"
            >
              <span> {{ seat.name }} </span>
            </div>
          </div>
        </div>

        <!-- right -->
        <div class="col-span-3 ml-4">
          <!-- show error message if no seat is selected  -->
          <h2
            v-if="selectedSeats.length == 0"
            class="
              h-full
              flex
              items-center
              justify-center
              text-lg text-red-500
              font-bold
              text-center
            "
          >
            No seat selected <br />
            Please select some seat first
          </h2>

          <div v-else class="space-y-4">
            <h3 class="text-lg font-semibold text-center">Selected Seats</h3>
            <table class="border-collapse border table-fixed w-full">
              <thead>
                <tr>
                  <th class="border border-slate-300 ...">Seat</th>
                  <th class="border border-slate-300 ...">Price</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(seat, index) in selectedSeats" :key="index">
                  <td class="text-center border border-slate-300">
                    {{ seat.name }}
                  </td>
                  <td class="text-center border border-slate-300">
                    {{ seat.price }}
                  </td>
                </tr>

                <tr v-if="appliedCoupon">
                  <td class="text-center font-bold border border-slate-300">
                    Discount
                  </td>
                  <td class="text-center font-bold border border-slate-300">
                    {{ -appliedCoupon.price }}
                  </td>
                </tr>

                <tr>
                  <td class="text-center font-bold border border-slate-300">
                    Total Price
                  </td>
                  <td class="text-center font-bold border border-slate-300">
                    {{ totalPrice }}
                  </td>
                </tr>
              </tbody>
            </table>

            <!-- coupon -->
            <div
              class="flex items-center justify-between"
              v-if="!appliedCoupon"
            >
              <input
                type="text"
                class="w-44 border-solid border-2 px-2 py-1 rounded-lg"
                placeholder="Coupon Code"
                v-model="couponCode"
              />
              <button
                class="bg-purple-500 text-white px-3 py-1 rounded-lg"
                @click="applyCoupon"
              >
                Apply
              </button>
            </div>
            <p v-else class="text-center">
              {{ appliedCoupon.price }} TK Coupon applied
            </p>

            <!-- information -->
            <form @submit.prevent="handleForm">
              <div class="flex flex-col">
                <div class="flex items-center justify-between">
                  <input
                    type="text"
                    class="w-32 border-solid border-2 px-2 py-1 rounded-lg"
                    placeholder="Name"
                    v-model="name"
                    required
                  />
                  <input
                    type="text"
                    class="w-32 border-solid border-2 px-2 py-1 rounded-lg"
                    placeholder="Mobile"
                    v-model="mobile"
                    required
                  />
                </div>

                <button
                  type="submit"
                  class="
                    flex
                    items-center
                    justify-center
                    bg-blue-400
                    text-white
                    h-8
                    mt-4
                    rounded-lg
                    cursor-pointer
                  "
                >
                  Submit
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>

    <div
      class="
        absolute
        flex flex-col
        w-[31rem]
        h-[28rem]
        shadow-md
        border-solid border-2
        p-10
        rounded-lg
        bg-black/70
        justify-center
        items-center
        space-y-10
      "
      v-if="confirmed"
    >
      <h2
        class="
          text-lg
          font-semibold
          text-green-500
          pb-2
          border-solid border-b-2
          self-stretch
          text-center
        "
      >
        Ticket Confirmed!!!
      </h2>
      <table class="border-collapse border table-fixed w-80">
        <tbody>
          <tr>
            <td class="text-center border border-slate-300 text-white">Passenger Name</td>
            <td class="text-center border border-slate-300 text-white">{{ name }}</td>
          </tr>
          <tr>
            <td class="text-center border border-slate-300 text-white">
              Passenger Contact
            </td>
            <td class="text-center border border-slate-300 text-white">{{ mobile }}</td>
          </tr>
          <tr>
             <td class="text-center border border-slate-300 text-white">
              Seats
            </td>
            <td class="text-center border border-slate-300 text-white flex flex-col">
              <span v-for="seat in selectedSeats" :key="seat.name"> {{ seat.name }} </span>
            </td>
          </tr>
          <tr v-if="appliedCoupon">
            <td class="text-center border border-slate-300 text-white">
              Discount
            </td>
            <td class="text-center border border-slate-300 text-white">{{ -appliedCoupon.price }}</td>
          </tr>
          <tr>
            <td class="text-center border border-slate-300 text-white">
              Total Cost
            </td>
            <td class="text-center border border-slate-300 text-white">TK {{ totalPrice }}</td>
          </tr>
        </tbody>
      </table>
      <button class="bg-red-400 self-stretch p-1 text-gray-300 rounded-lg" @click="resetData">Book again</button>
    </div>
  </div>
</template>

<script>
export default {
  props: ["seatStates", "seats"],
  data() {
    return {
      confirmed: false,
      name: "",
      mobile: "",
      appliedCoupon: null,
      couponCode: "",
      coupons: [
        {
          code: "100TAKAOFF",
          discount: 100,
        },
        {
          code: "200TAKAOFF",
          discount: 200,
        },
      ],
    };
  },
  methods: {
    assignClassName(value) {
      if (value == "sold") {
        return "bg-[#ff0000] text-white";
      } else if (value == "booked") {
        return "bg-gray-400";
      } else if (value == "selected") {
        return "bg-[#00ff00]";
      }
    },
    selectSeat(index) {
      let selectedSeat = this.seats[index];

      if (selectedSeat.type == "sold" || selectedSeat.type == "booked") {
        alert("You cannot select this seat");
        return;
      }
      if (this.selectedSeats.length > 2 && selectedSeat.type != "selected") {
        alert("You cannot select more than 3 seats");
        return;
      }

      if (this.selectedSeats.length == 0) {
        this.appliedCoupon = null;
        this.couponCode = "";
      }

      this.seats[index].type =
        selectedSeat.type == "selected" ? "available" : "selected";
    },
    applyCoupon() {
      let coupon = this.coupons.filter((item) => item.code == this.couponCode);
      if (coupon.length == 1) {
        this.appliedCoupon = {
          name: "Discount",
          price: coupon[0].discount,
        };
      }
    },
    handleForm() {
      this.confirmed = true
    },
    resetData(){
       this.confirmed = false;
       this.name = ''
       this.mobile = ''
       this.appliedCoupon = null
       this.selectedSeats.map(item => item.type = 'sold')
    }
  },
  computed: {
    selectedSeats() {
      let seatCount = this.seats.filter((item) => item.type == "selected");
      return seatCount;
    },
    totalPrice() {
      let sum = 0;
      this.selectedSeats.forEach((item) => (sum += item.price));
      if (this.appliedCoupon) {
        sum -= this.appliedCoupon.price;
      }
      return sum;
    },
  },
};
</script>

<style>
</style>