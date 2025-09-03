<script setup lang="ts">
import { ref } from 'vue'

type DomainInfoResult = {
  domainInformation: Record<string, string>
  contactInformation: Record<string, string>
}

const apiUrl = import.meta.env.VITE_API_URL || 'http://localhost:5000/'

const domain = ref<string>('')
const loading = ref<boolean>(false)
const domainResult = ref<DomainInfoResult | null>(null)

const loadDomainInfo = async () => {
  if(loading.value || !domain.value) {
    return false
  }

  loading.value = true

  fetch(apiUrl, {
    method: "POST",
    headers: {
      'Content-Type': "application/json",
      'Accept': "application/json",
    },
    body: JSON.stringify({ domain: domain.value })
  })
    .then(res => res.json())
    .then(data => {
      if (data.statusCode >= 400) {
        console.error(data)
        alert(data.message)
        return
      }
      domainResult.value = data
    })
    .finally(() => {
      loading.value = false
    })
}
</script>

<template>
  <div class="container-sm my-4">
    <h2 class="text-center mb-4">Whois Registry Search</h2>

    <div class="mb-3">
      <label for="domainInput" class="form-label fw-semibold small">Domain:</label>
      <div class="input-group input-group-sm">
        <input
          id="domainInput"
          type="text"
          v-model="domain"
          @keyup.enter="loadDomainInfo"
          class="form-control"
          placeholder="Enter domain name"
        />
        <button @click="loadDomainInfo" class="btn btn-primary">Search</button>
      </div>
    </div>

    <div v-if="loading" class="d-flex justify-content-center my-3">
      <div class="spinner-border spinner-border-sm text-primary" role="status"></div>
    </div>

    <div v-if="domainResult" class="card mb-3 shadow-sm">
      <div class="card-header bg-primary text-white py-2">
        <h6 class="mb-0">Domain Information</h6>
      </div>
      <div class="card-body p-2">
        <div class="table-responsive">
          <table class="table table-sm table-striped table-bordered align-middle mb-0">
            <thead class="table-light small">
              <tr>
                <th>Domain Name</th>
                <th>Registrar</th>
                <th>Registration Date</th>
                <th>Expiration Date</th>
                <th>Estimated Domain Age</th>
                <th>Hostnames</th>
              </tr>
            </thead>
            <tbody class="small">
              <tr>
                <td>{{ domainResult.domainInformation?.domainName ?? 'N/A' }}</td>
                <td>{{ domainResult.domainInformation?.registrar ?? 'N/A' }}</td>
                <td>{{ domainResult.domainInformation?.registrationDate ?? 'N/A' }}</td>
                <td>{{ domainResult.domainInformation?.expirationDate ?? 'N/A' }}</td>
                <td>{{ domainResult.domainInformation?.estimatedDomainAge ?? 'N/A' }}</td>
                <td>{{ domainResult.domainInformation?.hostnames ?? 'N/A' }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <div v-if="domainResult" class="card shadow-sm">
      <div class="card-header bg-secondary text-white py-2">
        <h6 class="mb-0">Contact Information</h6>
      </div>
      <div class="card-body p-2">
        <div class="table-responsive">
          <table class="table table-sm table-striped table-bordered align-middle mb-0">
            <thead class="table-light small">
              <tr>
                <th>Registrant Name</th>
                <th>Technical Contact Name</th>
                <th>Administrative Contact Name</th>
                <th>Contact Email</th>
              </tr>
            </thead>
            <tbody class="small">
              <tr>
                <td>{{ domainResult.contactInformation?.registrantName ?? 'N/A' }}</td>
                <td>{{ domainResult.contactInformation?.technicalContactName ?? 'N/A' }}</td>
                <td>{{ domainResult.contactInformation?.administrativeContactName ?? 'N/A' }}</td>
                <td>{{ domainResult.contactInformation?.contactEmail ?? 'N/A' }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>