import java.io.*;
import java.util.*;

class Patient {
    private String patientId;
    private String name;
    private int age;

    public Patient(String patientId, String name, int age) {
        this.patientId = patientId;
        this.name = name;
        this.age = age;
    }

    public String getPatientId() {
        return patientId;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public String getDetails() {
        return "ID: " + patientId + ", Name: " + name + ", Age: " + age;
    }
}

abstract class PatientDecorator extends Patient {
    protected Patient decoratedPatient;

    public PatientDecorator(Patient decoratedPatient) {
        super(decoratedPatient.getPatientId(), decoratedPatient.getName(), decoratedPatient.getAge());
        this.decoratedPatient = decoratedPatient;
    }

    public String getDetails() {
        return decoratedPatient.getDetails();
    }
}

class InsuranceDecorator extends PatientDecorator {
    private String insuranceDetails;

    public InsuranceDecorator(Patient decoratedPatient, String insuranceDetails) {
        super(decoratedPatient);
        this.insuranceDetails = insuranceDetails;
    }

    public String getDetails() {
        return super.getDetails() + ", Insurance: " + insuranceDetails;
    }
}

class EmergencyContactDecorator extends PatientDecorator {
    private String emergencyContact;

    public EmergencyContactDecorator(Patient decoratedPatient, String emergencyContact) {
        super(decoratedPatient);
        this.emergencyContact = emergencyContact;
    }

    public String getDetails() {
        return super.getDetails() + ", Emergency Contact: " + emergencyContact;
    }
}

class Doctor {
    private String doctorId;
    private String name;
    private String specialty;

    public Doctor(String doctorId, String name, String specialty) {
        this.doctorId = doctorId;
        this.name = name;
        this.specialty = specialty;
    }

    public String getDetails() {
        return "ID: " + doctorId + ", Name: " + name + ", Specialty: " + specialty;
    }
}

class Appointment {
    private String appointmentId;
    private Patient patient;
    private Doctor doctor;
    private String date;

    public Appointment(String appointmentId, Patient patient, Doctor doctor, String date) {
        this.appointmentId = appointmentId;
        this.patient = patient;
        this.doctor = doctor;
        this.date = date;
    }

    public String getDetails() {
        return "Appointment ID: " + appointmentId + ", Patient: " + patient.getDetails() + ", Doctor: " + doctor.getDetails() + ", Date: " + date;
    }
}

class Prescription {
    private String prescriptionId;
    private Patient patient;
    private Doctor doctor;
    private String medication;

    public Prescription(String prescriptionId, Patient patient, Doctor doctor, String medication) {
        this.prescriptionId = prescriptionId;
        this.patient = patient;
        this.doctor = doctor;
        this.medication = medication;
    }

    public String getDetails() {
        return "Prescription ID: " + prescriptionId + ", Patient: " + patient.getDetails() + ", Doctor: " + doctor.getDetails() + ", Medication: " + medication;
    }
}

class MedicalRecord {
    private String recordId;
    private Patient patient;
    private String details;

    public MedicalRecord(String recordId, Patient patient, String details) {
        this.recordId = recordId;
        this.patient = patient;
        this.details = details;
    }

    public String getDetails() {
        return "Record ID: " + recordId + ", Patient: " + patient.getDetails() + ", Details: " + details;
    }
}

abstract class RequestHandler {
    protected RequestHandler nextHandler;

    public void setNextHandler(RequestHandler nextHandler) {
        this.nextHandler = nextHandler;
    }

    public abstract void handleRequest(String requestType);
}

class AppointmentHandler extends RequestHandler {
    public void handleRequest(String requestType) {
        if (requestType.equals("Appointment")) {
            System.out.println("Handling appointment request.");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(requestType);
        }
    }
}

class PrescriptionHandler extends RequestHandler {
    public void handleRequest(String requestType) {
        if (requestType.equals("Prescription")) {
            System.out.println("Handling prescription request.");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(requestType);
        }
    }
}

interface Command {
    void execute();
}

class ScheduleAppointmentCommand implements Command {
    private Appointment appointment;

    public ScheduleAppointmentCommand(Appointment appointment) {
        this.appointment = appointment;
    }

    public void execute() {
        System.out.println("Scheduling appointment: " + appointment.getDetails());
    }
}

class GenerateBillCommand implements Command {
    private Patient patient;

    public GenerateBillCommand(Patient patient) {
        this.patient = patient;
    }

    public void execute() {
        System.out.println("Generating bill for patient: " + patient.getDetails());
    }
}

interface MedicalRecordAccess {
    void accessMedicalRecord();
}

class RealMedicalRecordAccess implements MedicalRecordAccess {
    private MedicalRecord medicalRecord;

    public RealMedicalRecordAccess(MedicalRecord medicalRecord) {
        this.medicalRecord = medicalRecord;
    }

    public void accessMedicalRecord() {
        System.out.println("Accessing medical record: " + medicalRecord.getDetails());
    }
}

class ProxyMedicalRecordAccess implements MedicalRecordAccess {
    private RealMedicalRecordAccess realAccess;
    private String userRole;

    public ProxyMedicalRecordAccess(MedicalRecord medicalRecord, String userRole) {
        this.realAccess = new RealMedicalRecordAccess(medicalRecord);
        this.userRole = userRole;
    }

    public void accessMedicalRecord() {
        if (userRole.equals("Doctor") || userRole.equals("Admin")) {
            realAccess.accessMedicalRecord();
        } else {
            System.out.println("Access denied. Insufficient permissions.");
        }
    }
}

public class HospitalManagementSystem {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        // Dynamic input for patient details
        System.out.print("Enter patient ID: ");
        String patientId = scanner.nextLine();
        System.out.print("Enter patient name: ");
        String patientName = scanner.nextLine();
        System.out.print("Enter patient age: ");
        int patientAge = scanner.nextInt();
        scanner.nextLine(); // Consume newline left-over

        Patient patient = new Patient(patientId, patientName, patientAge);

        // Dynamic input for doctor details
        System.out.print("Enter doctor ID: ");
        String doctorId = scanner.nextLine();
        System.out.print("Enter doctor name: ");
        String doctorName = scanner.nextLine();
        System.out.print("Enter doctor specialty: ");
        String doctorSpecialty = scanner.nextLine();

        Doctor doctor = new Doctor(doctorId, doctorName, doctorSpecialty);

        // Dynamic input for insurance details
        System.out.print("Enter insurance details: ");
        String insuranceDetails = scanner.nextLine();

        // Dynamic input for emergency contact
        System.out.print("Enter emergency contact: ");
        String emergencyContact = scanner.nextLine();

        // Decorate patient with additional details
        Patient decoratedPatient = new EmergencyContactDecorator(new InsuranceDecorator(patient, insuranceDetails), emergencyContact);

        // Dynamic input for appointment details
        System.out.print("Enter appointment ID: ");
        String appointmentId = scanner.nextLine();
        System.out.print("Enter appointment date: ");
        String appointmentDate = scanner.nextLine();

        Appointment appointment = new Appointment(appointmentId, decoratedPatient, doctor, appointmentDate);

        // Dynamic input for prescription details
        System.out.print("Enter prescription ID: ");
        String prescriptionId = scanner.nextLine();
        System.out.print("Enter medication: ");
        String medication = scanner.nextLine();

        Prescription prescription = new Prescription(prescriptionId, decoratedPatient, doctor, medication);

        // Command pattern: schedule appointment and generate bill
        Command scheduleAppointment = new ScheduleAppointmentCommand(appointment);
        Command generateBill = new GenerateBillCommand(decoratedPatient);
        scheduleAppointment.execute();
        generateBill.execute();

        // Chain of Responsibility pattern: handle requests
        RequestHandler appointmentHandler = new AppointmentHandler();
        RequestHandler prescriptionHandler = new PrescriptionHandler();
        appointmentHandler.setNextHandler(prescriptionHandler);
        appointmentHandler.handleRequest("Appointment");
        appointmentHandler.handleRequest("Prescription");

        // Proxy pattern: access medical record
        System.out.print("Enter medical record ID: ");
        String recordId = scanner.nextLine();
        System.out.print("Enter medical record details: ");
        String recordDetails = scanner.nextLine();

        MedicalRecord medicalRecord = new MedicalRecord(recordId, decoratedPatient, recordDetails);
        MedicalRecordAccess proxyAccess = new ProxyMedicalRecordAccess(medicalRecord, "Doctor");
        proxyAccess.accessMedicalRecord();

        scanner.close();
    }
}
