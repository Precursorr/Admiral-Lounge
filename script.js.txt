// Admiral Lounge - Centralized JavaScript

// ===== INDEX PAGE FUNCTIONS =====
function showOptions() {
    const optionsSection = document.getElementById('options');
    const beginButton = document.querySelector('.begin-button');
    
    if (optionsSection && beginButton) {
        // Hide the begin button
        beginButton.style.display = 'none';
        
        // Show the options with animation
        optionsSection.style.display = 'block';
        setTimeout(() => {
            optionsSection.classList.add('show');
        }, 50);
    }
}

function selectOption(option) {
    // Navigate to the appropriate page
    const pageMap = {
        'core-info': 'core-info.html',
        'fleet-builds': 'fleet-builds.html',
        'advanced-tips': 'advanced-tips.html',
        'tactical-guide': 'tactical-warfare.html'
    };
    
    if (pageMap[option]) {
        window.location.href = pageMap[option];
    }
}

// ===== FLEET BUILDER FUNCTIONS =====
let currentFleet = 1;
let fleetAdmirals = {}; // Store admiral selections for each fleet

// Load saved data from localStorage on page load
function loadSavedData() {
    const savedFleetAdmirals = localStorage.getItem('fleetAdmirals');
    const savedCurrentFleet = localStorage.getItem('currentFleet');
    
    if (savedFleetAdmirals) {
        fleetAdmirals = JSON.parse(savedFleetAdmirals);
    }
    
    if (savedCurrentFleet) {
        currentFleet = parseInt(savedCurrentFleet);
        // Update UI to show the saved current fleet
        const currentFleetButton = document.querySelector(`[data-fleet="${currentFleet}"]`);
        const currentFleetSection = document.getElementById(`fleet-${currentFleet}`);
        
        if (currentFleetButton && currentFleetSection) {
            currentFleetButton.classList.add('active');
            currentFleetSection.classList.add('active');
            
            // Remove active from fleet 1 if it's not the saved current fleet
            if (currentFleet !== 1) {
                const fleet1Button = document.querySelector(`[data-fleet="1"]`);
                const fleet1Section = document.getElementById(`fleet-1`);
                if (fleet1Button) fleet1Button.classList.remove('active');
                if (fleet1Section) fleet1Section.classList.remove('active');
            }
        }
    }
    
    updateAdmiralDisplay();
}

// Save data to localStorage
function saveData() {
    localStorage.setItem('fleetAdmirals', JSON.stringify(fleetAdmirals));
    localStorage.setItem('currentFleet', currentFleet.toString());
}

function selectFleet(fleetNumber) {
    // Hide current fleet section
    const currentFleetSection = document.getElementById(`fleet-${currentFleet}`);
    const currentFleetButton = document.querySelector(`[data-fleet="${currentFleet}"]`);
    
    if (currentFleetSection) currentFleetSection.classList.remove('active');
    if (currentFleetButton) currentFleetButton.classList.remove('active');
    
    // Show selected fleet section
    const selectedFleetSection = document.getElementById(`fleet-${fleetNumber}`);
    const selectedFleetButton = document.querySelector(`[data-fleet="${fleetNumber}"]`);
    
    if (selectedFleetSection) selectedFleetSection.classList.add('active');
    if (selectedFleetButton) selectedFleetButton.classList.add('active');
    
    currentFleet = fleetNumber;
    
    // Update admiral display for the selected fleet
    updateAdmiralDisplay();
    
    // Save the current state
    saveData();
}

function updateAdmiralDisplay() {
    const admiralDisplay = document.getElementById('selected-admiral');
    const admiralImage = document.getElementById('admiral-image');

    if (admiralDisplay && admiralImage) {
        if (fleetAdmirals[currentFleet]) {
            // Show the admiral for the current fleet
            admiralImage.src = fleetAdmirals[currentFleet].imagePath;
            admiralImage.alt = fleetAdmirals[currentFleet].name;
            admiralDisplay.style.display = 'block';

            // Show Sho Wu configuration if Sho Wu is selected
            updateShoWuConfiguration();
        } else {
            // Hide admiral display if no admiral selected for this fleet
            admiralDisplay.style.display = 'none';

            // Hide Sho Wu configuration
            hideShoWuConfiguration();
        }
    }
}

function updateShoWuConfiguration() {
    const shoWuConfig = document.getElementById('sho-wu-config');

    if (shoWuConfig && fleetAdmirals[currentFleet] && fleetAdmirals[currentFleet].name === 'Sho Wu') {
        shoWuConfig.style.display = 'block';
    } else {
        hideShoWuConfiguration();
    }
}

function hideShoWuConfiguration() {
    const shoWuConfig = document.getElementById('sho-wu-config');
    if (shoWuConfig) {
        shoWuConfig.style.display = 'none';
    }
}

function addShip(plusIcon) {
    // Handle ship addition - placeholder for now
    console.log('Add ship clicked:', plusIcon);

    // Add visual feedback
    plusIcon.style.transform = 'scale(1.3)';
    plusIcon.style.color = 'var(--primary-orange)';

    // Reset after animation
    setTimeout(() => {
        plusIcon.style.transform = '';
        plusIcon.style.color = '';
    }, 200);

    // TODO: Add ship selection modal or functionality here
}

function openAdmiralModal() {
    const modal = document.getElementById('admiral-modal');
    if (modal) {
        modal.style.display = 'flex';
        updateAdmiralAvailability();
    }
}

function closeAdmiralModal() {
    const modal = document.getElementById('admiral-modal');
    if (modal) {
        modal.style.display = 'none';
    }
}

function updateAdmiralAvailability() {
    // Get all taken admirals (excluding current fleet)
    const takenAdmirals = [];
    for (let fleet in fleetAdmirals) {
        if (fleet != currentFleet) {
            takenAdmirals.push(fleetAdmirals[fleet].imagePath);
        }
    }
    
    // Update admiral options in the modal
    const admiralOptions = document.querySelectorAll('.admiral-option');
    admiralOptions.forEach((option, index) => {
        const admiralPath = `assets/images/admiral${index + 1}.jpg`;
        
        if (takenAdmirals.includes(admiralPath)) {
            // Admiral is taken by another fleet
            option.classList.add('taken');
            option.onclick = null; // Disable clicking
        } else {
            // Admiral is available
            option.classList.remove('taken');
            // Re-enable clicking with the original function
            const admiralNames = [
                'Sho Wu', 'Admiral Nova', 'Admiral Orion', 'Admiral Stellar',
                'Admiral Cosmos', 'Admiral Galaxy', 'Admiral Nebula', 'Admiral Quasar',
                'Admiral Pulsar', 'Admiral Comet', 'Admiral Meteor', 'Admiral Astro'
            ];
            const admiralName = admiralNames[index] || option.querySelector('.admiral-placeholder').textContent;
            option.onclick = function() {
                selectAdmiral(`admiral${index + 1}.jpg`, admiralName);
            };
        }
    });
}

function selectAdmiral(imagePath, admiralName) {
    // Store the admiral selection for the current fleet
    fleetAdmirals[currentFleet] = {
        imagePath: `assets/images/${imagePath}`,
        name: admiralName
    };
    
    // Update the display
    updateAdmiralDisplay();
    
    // Save the current state
    saveData();
    
    // Close the modal
    closeAdmiralModal();
}

// ===== GLOBAL EVENT LISTENERS =====

// Close modal when clicking outside of it
window.onclick = function(event) {
    const modal = document.getElementById('admiral-modal');
    if (modal && event.target === modal) {
        closeAdmiralModal();
    }
}

// Initialize the appropriate page functionality when DOM is loaded
document.addEventListener('DOMContentLoaded', function() {
    // Check which page we're on and initialize accordingly
    const currentPage = window.location.pathname.split('/').pop();
    
    if (currentPage === 'fleet-builds.html') {
        // Initialize fleet builder
        loadSavedData();
    }
    
    // Initialize any other page-specific functionality here
});

// Legacy support for window.onload (keeping for compatibility)
window.addEventListener('load', function() {
    const currentPage = window.location.pathname.split('/').pop();
    
    if (currentPage === 'fleet-builds.html') {
        loadSavedData();
    }
});
