<script setup lang="ts">
import { ref } from 'vue'

type DomainInfoResult = {
  domainInformation: object
  contactInformation: object
}

const domain = ref<string>('')
const loading = ref<boolean>(false)
const domainResult = ref<DomainInfoResult | null>(null)

const loadDomainInfo = async () => {
  loading.value = true

  fetch("http://localhost:5000/", {
    method: "POST",
    headers: {
      'Content-Type': "application/json",
      'Accept': "application/json",
    },
    body: JSON.stringify({
      domain: domain.value,
    })
  }).then(res => res.json())
    .then(data => {
      if(data.statusCode >= 400) {
        console.error(data.message)
        alert(data.message)
        return
      }
      domainResult.value = data
    })
    .then(() => loading.value = false)
}

</script>
<template>
  <h1>Whois Registry Search</h1>

  <div class="container my-5">
    <div class="mb-4">
      <label for="domainInput" class="form-label fw-bold">Domain:</label>
      <div class="input-group">
        <input
          id="domainInput"
          type="text"
          v-model="domain"
          class="form-control"
          placeholder="Enter domain name"
        />
        <button @click="loadDomainInfo" class="btn btn-primary">Search</button>
      </div>
    </div>

    <div v-if="loading" class="spinner-border" role="status"></div>

    <div v-if="domainResult" class="card mb-4 shadow-sm">
      <div class="card-header bg-primary text-white">
        <h5 class="mb-0">Domain Information</h5>
      </div>
      <div class="card-body">
        <table class="table table-striped table-bordered align-middle">
          <thead class="table-light">
            <tr>
              <th scope="col">Domain Name</th>
              <th scope="col">Registrar</th>
              <th scope="col">Registration Date</th>
              <th scope="col">Expiration Date</th>
              <th scope="col">Estimated Domain Age</th>
              <th scope="col">Hostnames</th>
            </tr>
          </thead>
          <tbody>
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

    <div v-if="domainResult" class="card shadow-sm">
      <div class="card-header bg-secondary text-white">
        <h5 class="mb-0">Contact Information</h5>
      </div>
      <div class="card-body">
        <table class="table table-striped table-bordered align-middle">
          <thead class="table-light">
            <tr>
              <th scope="col">Registrant Name</th>
              <th scope="col">Technical Contact Name</th>
              <th scope="col">Administrative Contact Name</th>
              <th scope="col">Contact Email</th>
            </tr>
          </thead>
          <tbody>
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
</template>
