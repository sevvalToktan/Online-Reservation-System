document.addEventListener('DOMContentLoaded', () => {
    const userForm = document.getElementById('userForm');
    const seatingChart = document.getElementById('seatingChart');
    const reservationInfo = document.getElementById('reservationInfo');
    const rowsInput = document.getElementById('rows');
    const colsInput = document.getElementById('cols');
    const setSeatingButton = document.getElementById('setSeating');
    const confirmReservationButton = document.getElementById('confirmReservation');
    const adminInterface = document.querySelector('.admin-interface');
    const userInterface = document.querySelector('.user-interface');
    const selectedSeatsDisplay = document.getElementById('selectedSeatsDisplay');
    const confirmButtonContainer = document.getElementById('confirmButtonContainer');

    let seats = [];
    let userAge = 0;
    let selectedSeats = [];

    function getSeatPrice(age) {
        if (age < 18) return 10;
        if (age < 26) return 15;
        if (age < 65) return 25;
        return 10;
    }

    function renderSeatingChart() {
        seatingChart.innerHTML = '';
        seatingChart.style.gridTemplateColumns = `repeat(${colsInput.value}, 1fr)`;
        seats.forEach(seat => {
            const seatElement = document.createElement('div');
            seatElement.classList.add('seat');
            seatElement.textContent = seat.label;
            if (seat.reserved) {
                seatElement.classList.add('reserved');
            } else if (seat.selected) {
                seatElement.classList.add('selected');
            }
            const tooltip = document.createElement('div');
            tooltip.classList.add('tooltip');
            tooltip.textContent = `$${getSeatPrice(userAge)}`;
            seatElement.appendChild(tooltip);
            seatElement.addEventListener('click', () => {
                if (!seat.reserved) {
                    seat.selected = !seat.selected;
                    if (seat.selected) {
                        selectedSeats.push(seat.label);
                    } else {
                        selectedSeats = selectedSeats.filter(selectedSeat => selectedSeat !== seat.label);
                    }
                    renderSeatingChart();
                    updateSelectedSeatsDisplay();
                }
            });
            seatingChart.appendChild(seatElement);
        });
    }

    function generateSeats(rows, cols) {
        seats = [];
        const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
        for (let r = 0; r < rows; r++) {
            for (let c = 0; c < cols; c++) {
                const label = alphabet[c] + (r + 1);
                seats.push({
                    id: r * cols + c,
                    label: label,
                    reserved: false,
                    selected: false,
                });
            }
        }
        renderSeatingChart();
    }

    function updateSelectedSeatsDisplay() {
        selectedSeatsDisplay.textContent = `Selected Seats: ${selectedSeats.join(', ')}`;
    }

    userForm.addEventListener('submit', (e) => {
        e.preventDefault();
        
        const user = {
            firstName: document.getElementById('firstName').value,
            lastName: document.getElementById('lastName').value,
            email: document.getElementById('email').value,
            phone: document.getElementById('phone').value,
            age: document.getElementById('age').value,
            role: 'user'
        };
        
        userAge = parseInt(user.age, 10);

        if (user.email === 'admin@admin.com') {
            user.role = 'admin';
            adminInterface.style.display = 'block'; 
        } else {
            adminInterface.style.display = 'none'; 
            userInterface.style.display = 'block'; 
        }
        
        reservationInfo.innerHTML = `
            <p>Name: ${user.firstName}</p>
            <p>Surname: ${user.lastName}</p>
            <p>E-mail: ${user.email}</p>
            <p>Phone: ${user.phone}</p>
            <p>Age: ${user.age}</p>
            <p>User: ${user.role}</p>
        `;
    });

    setSeatingButton.addEventListener('click', () => {
        const rows = parseInt(rowsInput.value, 10);
        const cols = parseInt(colsInput.value, 10);
        if (rows > 0 && cols > 0) {
            generateSeats(rows, cols);
        }
    });

    confirmReservationButton.addEventListener('click', () => {
        const user = {
            firstName: document.getElementById('firstName').value,
            lastName: document.getElementById('lastName').value,
            email: document.getElementById('email').value,
            phone: document.getElementById('phone').value,
            age: document.getElementById('age').value,
            role: 'user'
        };

        const totalPrice = selectedSeats.length * getSeatPrice(parseInt(user.age, 10));

        const confirmationMessage = `
            Name: ${user.firstName}
            Surname: ${user.lastName}
            E-mail: ${user.email}
            Phone: ${user.phone}
            Age: ${user.age}
            Selected Seats: ${selectedSeats.join(', ')}
            Total Price: $${totalPrice}
        `;

        const confirmation = confirm(`${confirmationMessage}\n\nConfirm your reservation?`);
        if (confirmation) {
            const confirmationParagraph = document.createElement('p');
            confirmationParagraph.textContent = "Reservation completed.";
            confirmButtonContainer.appendChild(confirmationParagraph);
        }
    });
});
