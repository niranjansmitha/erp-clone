# erp-clone

CREATE TABLE employee_leave_balance (
    id CHAR(36) PRIMARY KEY,
    employee_id CHAR(36) NOT NULL,
    leave_type VARCHAR(50) NOT NULL,
    total_allotted INT NOT NULL DEFAULT 0,
    used_days INT NOT NULL DEFAULT 0,
    available_balance INT NOT NULL DEFAULT 0,
    year INT NOT NULL,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (employee_id) REFERENCES employee(id) ON DELETE CASCADE,
    UNIQUE KEY unique_employee_leave_year (employee_id, leave_type, year)
);											






USE peopleone_user_db;

-- Give Aarav Sharma (d0eebc99-9c0b-4ef8-bb6d-6bb9bd380a44) his balances for 2026
INSERT INTO employee_leave_balance (id, employee_id, leave_type, total_allotted, used_days, available_balance, year)
VALUES 
('f0eebc99-9c0b-4ef8-bb6d-6bb9bd380a66', 'd0eebc99-9c0b-4ef8-bb6d-6bb9bd380a44', 'SICK', 7, 0, 7, 2026),
('f1eebc99-9c0b-4ef8-bb6d-6bb9bd380a77', 'd0eebc99-9c0b-4ef8-bb6d-6bb9bd380a44', 'PAID', 20, 0, 20, 2026);

-- Give Ananya Verma (e0eebc99-9c0b-4ef8-bb6d-6bb9bd380a55) her balances for 2026
INSERT INTO employee_leave_balance (id, employee_id, leave_type, total_allotted, used_days, available_balance, year)
VALUES 
('f2eebc99-9c0b-4ef8-bb6d-6bb9bd380a88', 'e0eebc99-9c0b-4ef8-bb6d-6bb9bd380a55', 'SICK', 7, 0, 7, 2026),
('f3eebc99-9c0b-4ef8-bb6d-6bb9bd380a99', 'e0eebc99-9c0b-4ef8-bb6d-6bb9bd380a55', 'PAID', 20, 0, 20, 2026);
