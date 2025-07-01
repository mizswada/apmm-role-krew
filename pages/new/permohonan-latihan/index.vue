<template>
    <div class="p-6 space-y-6">
        <div class="flex justify-between items-center">
            <h1 class="text-2xl font-semibold mb-6">Senarai Latihan Tersedia</h1>
        </div>
        <rs-card>
            <div class="p-4">           
                <rs-table
                    :data="tableData"
                    :field="['bil', 'namaLatihan', 'penerangan', 'tempoh', 'lokasi', 'tarikhMula', 'status', 'tindakan']"
                    :options="{
                        variant: 'default',
                        striped: true,
                        borderless: true,
                        hover: true
                    }"
                    :options-advanced="{
                        sortable: true,
                        responsive: true,
                        filterable: true
                    }"
                    advanced
                >
                    <template v-slot:bil="{ text }">
                        {{ text }}
                    </template>
                    <template v-slot:namaLatihan="{ text }">
                        <span class="font-medium">{{ text }}</span>
                    </template>
                    <template v-slot:penerangan="{ text }">
                        <span class="text-sm text-gray-600">{{ text }}</span>
                    </template>
                    <template v-slot:tempoh="{ text }">
                        <span class="text-sm">{{ text }}</span>
                    </template>
                    <template v-slot:lokasi="{ text }">
                        <span class="text-sm">{{ text }}</span>
                    </template>
                    <template v-slot:tarikhMula="{ text }">
                        <span class="text-sm">{{ text }}</span>
                    </template>
                    <template v-slot:status="{ text }">
                        <rs-badge :variant="getBadgeVariant(text)">
                            {{ text }}
                        </rs-badge>
                    </template>
                    <template v-slot:tindakan="{ value }">
                        <rs-button
                            variant="primary"
                            size="sm"
                            @click="openApplyModal(value)"
                            :disabled="value.status === 'Tutup'"
                        >
                            {{ value.status === 'Tutup' ? 'Tutup' : 'Mohon' }}
                        </rs-button>
                    </template>
                </rs-table>
            </div>
        </rs-card>

        <!-- Apply Modal -->
        <rs-modal v-model="isApplyModalOpen" size="lg">
            <template #header>
                <h3 class="text-lg font-semibold">Mohon Latihan</h3>
            </template>
            <template #body>
                <div class="space-y-4">
                    <div class="grid grid-cols-1 gap-4">
                        <div>
                            <label class="font-medium">Nama Latihan:</label>
                            <p>{{ selectedTraining?.namaLatihan }}</p>
                        </div>
                        <div>
                            <label class="font-medium">Penerangan:</label>
                            <p>{{ selectedTraining?.penerangan }}</p>
                        </div>
                        <div>
                            <label class="font-medium">Tempoh:</label>
                            <p>{{ selectedTraining?.tempoh }}</p>
                        </div>
                        <div>
                            <label class="font-medium">Lokasi:</label>
                            <p>{{ selectedTraining?.lokasi }}</p>
                        </div>
                        <div>
                            <label class="font-medium">Tarikh Mula:</label>
                            <p>{{ selectedTraining?.tarikhMula }}</p>
                        </div>
                        <!-- <div>
                            <label class="font-medium">Alasan Permohonan:</label>
                            <FormKit 
                                type="textarea" 
                                v-model="applicationForm.alasan"
                                placeholder="Nyatakan alasan anda untuk menyertai latihan ini"
                                rows="4"
                                class="w-full"
                            />
                        </div>
                        <div>
                            <label class="font-medium">Lampiran Sokongan:</label>
                            <FormKit
                                type="file"
                                @change="handleFileUpload"
                                accept=".pdf,.doc,.docx"
                                class="w-full"
                            />
                        </div> -->
                    </div>
                </div>
            </template>
            <template #footer>
                <div class="flex justify-end gap-2">
                    <rs-button
                        variant="secondary"
                        @click="isApplyModalOpen = false"
                    >
                        Batal
                    </rs-button>
                    <rs-button
                        variant="primary"
                        @click="handleApply"
                    >
                        Memohon
                    </rs-button>
                </div>
            </template>
        </rs-modal>
    </div>    
</template>

<script setup>
const tableData = ref([
    {
        bil: 1,
        namaLatihan: 'Kursus Pengurusan Projek Maritim',
        penerangan: 'Kursus ini memberi pendedahan kepada peserta tentang asas pengurusan projek dalam konteks maritim.',
        tempoh: '5 hari',
        lokasi: 'Akademi Tentera Laut Lumut',
        tarikhMula: '15 Januari 2024',
        status: 'Terbuka'
    },
    {
        bil: 2,
        namaLatihan: 'Latihan Keselamatan Maritim',
        penerangan: 'Program latihan keselamatan dan pencegahan kemalangan di laut.',
        tempoh: '3 hari',
        lokasi: 'Pangkalan Tentera Laut Kota Kinabalu',
        tarikhMula: '20 Januari 2024',
        status: 'Terbuka'
    },
    {
        bil: 3,
        namaLatihan: 'Kursus Teknologi Navigasi Moden',
        penerangan: 'Latihan penggunaan sistem navigasi dan teknologi terkini dalam operasi maritim.',
        tempoh: '7 hari',
        lokasi: 'Akademi Tentera Laut Lumut',
        tarikhMula: '25 Januari 2024',
        status: 'Terbuka'
    },
    {
        bil: 4,
        namaLatihan: 'Program Kepimpinan Pasukan',
        penerangan: 'Latihan kemahiran kepimpinan dan pengurusan pasukan dalam operasi maritim.',
        tempoh: '4 hari',
        lokasi: 'Pangkalan Tentera Laut Sandakan',
        tarikhMula: '30 Januari 2024',
        status: 'Tutup'
    },
    {
        bil: 5,
        namaLatihan: 'Kursus Penyelenggaraan Kapal',
        penerangan: 'Latihan teknikal penyelenggaraan dan pembaikan sistem kapal.',
        tempoh: '6 hari',
        lokasi: 'Dok Tentera Laut Lumut',
        tarikhMula: '5 Februari 2024',
        status: 'Terbuka'
    }
]);

const isApplyModalOpen = ref(false);
const selectedTraining = ref(null);

const applicationForm = ref({
    alasan: '',
    attachment: null
});

const getBadgeVariant = (status) => {
    switch (status.toLowerCase()) {
        case 'terbuka':
            return 'success';
        case 'tutup':
            return 'danger';
        default:
            return 'primary';
    }
};

const openApplyModal = (training) => {
    selectedTraining.value = training;
    applicationForm.value = {
        alasan: '',
        attachment: null
    };
    isApplyModalOpen.value = true;
};

const handleFileUpload = (event) => {
    applicationForm.value.attachment = event.target.files[0];
};

const handleApply = () => {
    // Here you would typically send the application to the backend
    console.log('Applying for training:', selectedTraining.value.namaLatihan);
    console.log('Application form:', applicationForm.value);
    
    // Show success message (you can use your preferred notification system)
    alert('Permohonan latihan telah dihantar dengan jayanya!');
    
    isApplyModalOpen.value = false;
    
    // Reset form
    applicationForm.value = {
        alasan: '',
        attachment: null
    };
};
</script>

<style lang="scss" scoped>
.rs-card {
    @apply bg-white rounded-lg shadow;
}
</style>