<script setup lang="ts">
import { ref } from "vue";
import { PDFDocument } from "pdf-lib";

// PDF metadata
type AttachmentType = {
  title: string;
  attachment_link: string;
};

const attachments = ref<AttachmentType[]>([
  { title: "Delivery_Note.pdf", attachment_link: "/sample2.pdf" },
  { title: "Purchase_order.pdf", attachment_link: "/sample2.pdf" },
  { title: "Invoices_order.pdf", attachment_link: "https://sds-dev-api.innotechcambodia.com/static/images/PurchasePayment/3125831e-9aa8-431a-8f4d-abda54960aa3.pdf" },
]);

// PDF preview modal state
const pdfPreview = ref<{ show: boolean; url: string }>({
  show: false,
  url: "",
});

// Convert merged ArrayBuffer to blob URL
function arrayBufferToBlobUrl(buffer: ArrayBuffer, type = "application/pdf") {
  const blob = new Blob([buffer], { type });
  return URL.createObjectURL(blob);
}

// Merge multiple PDFs and preview
async function mergePdfs() {
  console.log("Merging PDFs...");
  const mergedPdf = await PDFDocument.create();

  for (const file of attachments.value) {
    const existingPdfBytes = await fetch(file.attachment_link).then(res => res.arrayBuffer());
    const pdf = await PDFDocument.load(existingPdfBytes);
    const copiedPages = await mergedPdf.copyPages(pdf, pdf.getPageIndices());
    copiedPages.forEach(page => mergedPdf.addPage(page));
  }

  const mergedPdfBytes = await mergedPdf.save();
  const blobUrl = arrayBufferToBlobUrl(mergedPdfBytes);

  pdfPreview.value = {
    show: true,
    url: blobUrl,
  };
}
</script>

<template>
  <div class="min-h-screen flex flex-col">
    <div class="flex-1 p-6">
      <section>
        <h1 class="text-lg font-semibold mb-5">* Attachment</h1>

        <div class="flex gap-5 mb-6">
          <button
            class="flex items-center gap-2 bg-blue-500 text-white hover:bg-blue-600 px-4 py-2 rounded"
            @click="mergePdfs"
          >
            <span class="i-lucide-file-plus"></span>
            Merge & View
          </button>

          <button class="flex items-center gap-2 bg-gray-100 hover:bg-gray-200 px-4 py-2 rounded">
            <span class="i-lucide-download"></span>
            Download
          </button>
        </div>

        <!-- Grid with one card -->
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
          <div
            class="flex flex-col items-center gap-2 cursor-pointer bg-gray-100 rounded-xl w-full h-[180px] hover:bg-gray-200 p-4 attachment-card"
            @click="mergePdfs"
          >
            <svg width="40" height="40" viewBox="0 0 40 40" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path
                d="M21.6641 14.9997H30.8307L21.6641 5.83301V14.9997ZM9.9974 3.33301H23.3307L33.3307 13.333V33.333C33.3307 34.2171 32.9795 35.0649 32.3544 35.69C31.7293 36.3152 30.8815 36.6663 29.9974 36.6663H9.9974C9.11334 36.6663 8.26549 36.3152 7.64037 35.69C7.01525 35.0649 6.66406 34.2171 6.66406 33.333V6.66634C6.66406 5.78229 7.01525 4.93444 7.64037 4.30932C8.26549 3.6842 9.11334 3.33301 9.9974 3.33301ZM18.2141 20.733C18.8974 22.233 19.7641 23.4663 20.7641 24.3163L21.4474 24.8497C19.9974 25.1163 17.9974 25.583 15.8807 26.3997L15.6974 26.4663L16.5307 24.733C17.2807 23.283 17.8307 21.9663 18.2141 20.733ZM29.0141 27.083C29.3141 26.783 29.4641 26.3997 29.4807 25.983C29.5307 25.6497 29.4474 25.333 29.2807 25.0663C28.7974 24.283 27.5474 23.9163 25.4807 23.9163L23.3307 24.033L21.8807 23.0663C20.8307 22.1997 19.8807 20.683 19.2141 18.7997L19.2807 18.5663C19.8307 16.3497 20.3474 13.6663 19.2474 12.5663C19.1128 12.4357 18.9537 12.3331 18.7792 12.2644C18.6047 12.1957 18.4182 12.1624 18.2307 12.1663H17.8307C17.2141 12.1663 16.6641 12.8163 16.5141 13.4497C15.8974 15.6663 16.2641 16.883 16.8807 18.8997V18.9163C16.4641 20.383 15.9307 22.083 15.0807 23.7997L13.4807 26.7997L11.9974 27.6163C9.9974 28.8663 9.0474 30.2663 8.86406 31.1497C8.7974 31.4663 8.83073 31.7497 8.9474 32.0497L8.9974 32.133L9.7974 32.6497L10.5307 32.833C11.8807 32.833 13.4141 31.2497 15.4807 27.7163L15.7807 27.5997C17.4974 27.0497 19.6307 26.6663 22.4974 26.3497C24.2141 27.1997 26.2307 27.583 27.4974 27.583C28.2307 27.583 28.7307 27.3997 29.0141 27.083Z"
                fill="#EF5350"
              />
            </svg>

            <p class="text-sm font-medium text-gray-700 text-center">View All Merged PDFs</p>
          </div>
        </div>
      </section>
    </div>

    <!-- PDF Modal -->
    <div v-if="pdfPreview.show" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg shadow-xl w-full max-w-4xl max-h-[90vh] flex flex-col">
        <!-- Header -->
        <div class="flex items-center justify-between p-4 border-b">
          <h3 class="font-semibold">PDF Preview</h3>
          <button @click="pdfPreview.show = false" class="text-gray-500 hover:text-gray-700 text-xl">
            &times;
          </button>
        </div>

        <!-- PDF Viewer -->
        <div class="flex-1 overflow-auto p-4">
          <iframe
            v-if="pdfPreview.url"
            :src="pdfPreview.url"
            class="w-full h-full min-h-[70vh]"
            frameborder="0"
          />
        </div>

        <!-- Footer -->
        <div class="flex items-center justify-end p-4 border-t">
          <button @click="pdfPreview.show = false" class="px-4 py-2 bg-gray-200 rounded hover:bg-gray-300">
            Close
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
