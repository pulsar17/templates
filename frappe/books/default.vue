<main class="h-full bg-white text-xs" :style="{ 'font-family': print.font }">
  <!-- Invoice Header -->
  <header class="bg-gray-100 px-12 pt-2">
    <div class="text-center font-semibold" v-if="doc.salutation">{{ doc.salutation }}</div>
    <div class="my-2 text-center font-semibold underline">{{ doc.entryType === 'SalesInvoice' ? 'Tax Invoice' : 'Purchase Invoice' }}</div>

    <!-- Company Details -->
    <section class="my-2 text-center font-semibold">
      <img v-if="print.displayLogo" class="m-auto my-4 h-16 object-contain" :src="print.logo" />
      <div>
        <p class="text-xl font-semibold" :style="{ color: print.color }">{{ print.companyName.toUpperCase() }}</p>
        <p class="text-gray-800" v-if="print.address">{{ print.links.address.addressDisplay.toUpperCase() }}</p>
        <div class="mx-auto grid w-9/12 grid-cols-3">
          <p class="text-gray-800" v-if="print.gstin">GSTIN: {{ print.gstin }}</p>
          <p class="text-gray-800" v-if="print.phone">PAN: {{ doc.panNo }}</p>
          <p class="text-gray-800" v-if="print.phone">Phone: {{ print.phone }}</p>
        </div>
      </div>
    </section>

    <!-- Invoice Details -->
    <section class="mx-auto mb-2 mt-4 grid w-2/3 grid-cols-4" style="grid-template-columns: 1fr 3fr 1fr 1fr">
      <h3 class="justify-self-start font-semibold">{{ doc.entryType === 'SalesInvoice' ? 'Invoice No. :' : 'Bill' }}</h3>
      <p class="font-semibold">{{ doc.name }}</p>

      <h3 class="justify-self-start font-semibold">Date :</h3>
      <p class="font-semibold text-gray-800">{{ doc.date }}</p>
    </section>

    <!-- PO and Party Details -->
    <section class="mx-auto grid w-2/3 grid-cols-4 gap-y-2 pb-4" style="grid-template-columns: 1fr 3fr 1fr 1fr">
      <h3 v-if="doc.pono" class="justify-self-start font-semibold">P.O. No. :</h3>
      <p v-if="doc.pono" class="text-gray-800">{{ doc.pono }}</p>
      <h3 v-if="doc.poDate" class="justify-self-start font-semibold">P.O. Date :</h3>
      <p v-if="doc.poDate" class="text-gray-800">{{ doc.poDate }}</p>

      <!-- Party Details -->
      <h3 class="justify-self-start font-semibold">{{ doc.entryType === 'SalesInvoice' ? 'Bill / Ship to :' : 'Supplier' }}</h3>
      <div class="col-span-3 text-gray-800" v-if="doc.party">
        <p class="font-semibold">{{ doc.party }}</p>
        <p v-if="doc.links.party.address">{{ doc.links.party.links.address.addressDisplay.toUpperCase() }}</p>
        <p v-if="doc.links.party.gstin"><span class="font-semibold">GSTIN: </span>{{ doc.links.party.gstin }}</p>
      </div>

      <!-- State Info -->
      <h3 class="font-semibold">{{ t`State :` }}</h3>
      <p>{{ doc.links.party.links.address.state }}</p>
      <h3 class="font-semibold">{{ t`State Code :`}}</h3>
      <p class="text-gray-800">{{ doc.links.party.gstin.substr(0, 2) }}</p>
    </section>
  </header>

  <!-- Items Table -->
  <section class="divide-y divide-dashed px-6 pt-4">
    <!-- Heading Row -->
    <section class="mb-1 flex font-semibold">
      <div class="w-5/12 text-left">{{ t`Item` }}</div>
      <!--div class="w-3/12 text-left">{{ t`Description` }}</div-->
      <div class="w-2/12 text-right">{{ t`HSN` }}</div>
      <div class="w-2/12 text-right">{{ t`Qty.` }}</div>
      <div class="w-1/12 text-right">{{ t`Unit`}}</div>
      <div class="w-3/12 text-right">{{ t`Rate` }}</div>
      <div class="w-3/12 text-right">{{ t`Amount`}}</div>
    </section>

    <!-- Body Rows -->
    <section class="flex py-1 text-gray-800" v-for="row in doc.items" :key="row.name">
      <div class="w-5/12 text-left">{{ row.item.toUpperCase() }}</div>
      <!--div class="w-3/12 text-left">{{ row.description }}</div-->
      <div class="w-2/12 text-right">{{ row.hsnCode }}</div>
      <div :style="[row.links.item.showQuantity === 'true' ? '' : { 'visibility': 'hidden' }]" class="w-2/12 text-right">{{ row.quantity }}</div>
      <div :style="[row.links.item.showUnit === 'true' ? '' : { 'visibility': 'hidden' }]" class="w-1/12 text-right">{{ row.unit }}</div>
      <div :style="[row.links.item.showRate === 'true' ? '' : { 'visibility': 'hidden' }]" class="w-3/12 text-right">{{ row.rate }}</div>
      <div class="w-3/12 text-right">{{ row.amount }}</div>
    </section>

    <section v-if="doc.customInvoiceNotes" class="flex pt-4 italic">{{ doc.customInvoiceNotes }}</section>
  </section>

  <!-- Invoice Footer -->
  <footer class="mt-4 px-6">
    <!-- Totaled Amounts -->
    <section class="ml-auto w-5/12 break-inside-avoid divide-y divide-dashed rounded-lg border-2 border-solid px-2">
      <!-- Subtotal -->
      <div class="flex justify-between py-1 pl-2">
        <h3>{{ t`Subtotal` }}</h3>
        <p>{{ doc.netTotal }}</p>
      </div>

      <!-- Discount (if applied before tax) -->
      <div class="flex justify-between py-1 pl-2" v-if="doc.totalDiscount && !doc.discountAfterTax">
        <h3>{{ t`Discount` }}</h3>
        <p>{{ doc.totalDiscount }}</p>
      </div>

      <!-- Tax Breakdown -->
      <div class="grid grid-cols-3 justify-between py-1 pl-2" v-for="tax in doc.taxes" :key="tax.name">
        <h3>{{ tax.account }}</h3>
        <p class="justify-left w-8/12">@ {{ tax.rate }} %</p>
        <p class="justify-self-end">{{ tax.amount }}</p>
      </div>

      <!-- Discount (if applied after tax) -->
      <div class="flex justify-between py-1 pl-2" v-if="doc.totalDiscount && doc.discountAfterTax">
        <h3>{{ t`Discount` }}</h3>
        <p>{{ doc.totalDiscount }}</p>
      </div>

      <!-- Grand Total -->
      <div class="flex justify-between py-1 pl-2 font-semibold" :style="{ color: print.color }">
        <h3>{{ t`Grand Total` }}</h3>
        <p>{{ doc.grandTotal }}</p>
      </div>
    </section>

    <!-- Terms and Authorised Signatory -->
    <section class="mt-12 flex">
      <!-- Invoice Terms -->
      <div class="w-8/12" v-if="doc.terms">
        <h3 class="font-semibold">Terms of Sale</h3>
        <p class="mt-4 whitespace-pre-line">{{ doc.terms }}</p>
      </div>
      <!-- Authorised Signatory -->
      <div class="ml-auto w-4/12 break-inside-avoid text-right">
        <h3 class="font-semibold">{{t`FOR ${print.companyName.toUpperCase()}`}}</h3>
        <p class="mt-20">{{ t`Authorised Signatory` }}</p>
      </div>
    </section>
  </footer>
</main>
