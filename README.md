<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>VSS Lead Manager</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    .hidden { display: none; }
    .btn { padding: 8px 12px; margin: 5px; cursor: pointer; background: #007bff; color: #fff; border: none; border-radius: 5px; }
    .btn:hover { background: #0056b3; }
    #dashboard table { width: 100%; border-collapse: collapse; margin-top: 20px; }
    #dashboard th, #dashboard td { border: 1px solid #ccc; padding: 8px; text-align: left; }
    .toast { position: fixed; top: 10px; right: 10px; background: #333; color: #fff; padding: 10px 15px; border-radius: 5px; opacity: 0.9; }
  </style>
</head>
<body>

<h2>VSS Lead Inventory Manager</h2>

<!-- Role Selection -->
<div>
  <label>Select Role: </label>
  <select id="roleSelect">
    <option value="Admin">Admin</option>
    <option value="FieldInspector">Field Inspector</option>
    <option value="ReportMaker">Report Maker</option>
  </select>
</div>

<!-- Add new lead button -->
<button id="addLeadBtn" class="btn">+ New Lead</button>

<!-- Form to add lead -->
<div id="leadForm" class="hidden">
  <h3>Create / Update Lead</h3>
  <form id="leadFormContent">
    <label>Name of Applicant:</label><br>
    <textarea id="applicant"></textarea><br>

    <label>Bank:</label><br>
    <textarea id="bank"></textarea><br>

    <label>Branch:</label><br>
    <textarea id="branch"></textarea><br>

    <label>Brief Address:</label><br>
    <textarea id="address"></textarea><br>

    <label>Contact Number:</label><br>
    <textarea id="contact"></textarea><br>

    <label>Status:</label><br>
    <select id="status">
      <option>Escalated</option>
      <option>Pending</option>
      <option>Customer Not Responded</option>
      <option>Customer Accepted</option>
      <option>Documents Insufficient</option>
      <option>Draft Report Prepared</option>
      <option>Data Insufficient</option>
      <option>Required Clarification</option>
      <option>Completed</option>
    </select><br><br>

    <label>Documents Submitted:</label><br>
    <textarea id="documents"></textarea><br>

    <label>Remarks:</label><br>
    <textarea id="remarks"></textarea><br>

    <button type="submit" class="btn">Submit</button>
  </form>
</div>

<!-- Dashboard -->
<div id="dashboard">
  <h3>Dashboard</h3>
  <table>
    <thead>
      <tr>
        <th>Applicant</th><th>Bank</th><th>Branch</th><th>Address</th><th>Contact</th>
        <th>Status</th><th>Documents</th><th>Remarks</th><th>Action</th>
      </tr>
    </thead>
    <tbody id="leadTableBody"></tbody>
  </table>
</div>

<!-- Notification container -->
<div id="toastContainer"></div>

<script>
let leads = [];
let editingIndex = null;

const roleSelect = document.getElementById("roleSelect");
const addLeadBtn = document.getElementById("addLeadBtn");
const leadForm = document.getElementById("leadForm");
const leadFormContent = document.getElementById("leadFormContent");
const tableBody = document.getElementById("leadTableBody");
const toastContainer = document.getElementById("toastContainer");

addLeadBtn.onclick = () => {
  leadForm.classList.remove("hidden");
  editingIndex = null;
  leadFormContent.reset();
};

leadFormContent.onsubmit = (e) => {
  e.preventDefault();
  const role = roleSelect.value;

  let lead = {
    applicant: document.getElementById("applicant").value,
    bank: document.getElementById("bank").value,
    branch: document.getElementById("branch").value,
    address: document.getElementById("address").value,
    contact: document.getElementById("contact").value,
    status: document.getElementById("status").value,
    documents: document.getElementById("documents").value,
    remarks: document.getElementById("remarks").value
  };

  if (editingIndex !== null) {
    leads[editingIndex] = lead;
  } else {
    leads.push(lead);
  }

  showNotification(lead.applicant, lead.status);
  renderTable();
  leadForm.classList.add("hidden");
};

function renderTable() {
  tableBody.innerHTML = "";
  leads.forEach((lead, index) => {
    let row = `<tr>
      <td>${lead.applicant}</td>
      <td>${lead.bank}</td>
      <td>${lead.branch}</td>
      <td>${lead.address}</td>
      <td>${lead.contact}</td>
      <td>${lead.status}</td>
      <td>${lead.documents}</td>
      <td>${lead.remarks}</td>
      <td><button class="btn" onclick="editLead(${index})">Edit</button></td>
    </tr>`;
    tableBody.innerHTML += row;
  });
}

function editLead(index) {
  const role = roleSelect.value;
  editingIndex = index;
  const lead = leads[index];

  // Restrict editing for non-admin roles
  document.getElementById("applicant").disabled = role !== "Admin";
  document.getElementById("bank").disabled = role !== "Admin";
  document.getElementById("branch").disabled = role !== "Admin";
  document.getElementById("address").disabled = role !== "Admin";
  document.getElementById("contact").disabled = role !== "Admin";

  document.getElementById("applicant").value = lead.applicant;
  document.getElementById("bank").value = lead.bank;
  document.getElementById("branch").value = lead.branch;
  document.getElementById("address").value = lead.address;
  document.getElementById("contact").value = lead.contact;
  document.getElementById("status").value = lead.status;
  document.getElementById("documents").value = lead.documents;
  document.getElementById("remarks").value = lead.remarks;

  leadForm.classList.remove("hidden");
}

function showNotification(applicant, status) {
  let toast = document.createElement("div");
  toast.className = "toast";
  toast.innerText = `${applicant} – ${status}`;
  toastContainer.appendChild(toast);
  setTimeout(() => toast.remove(), 3000);
}
</script>

</body>
</html>
