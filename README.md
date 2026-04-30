# Freight Guard Validation Pipeline 🛡️

### **Scalable Shipment Validation Engine using the Chain of Responsibility Pattern**

## 📖 Overview
In enterprise logistics, data integrity is the difference between a successful delivery and a legal liability. I built Freight Guard to move away from fragile, "spaghetti" validation logic. This project implements a **Chain of Responsibility** to process shipments through a sequence of independent, specialized validation rules before they are committed to the system.

## 🛠 Senior Tech Stack
* **Language:** Java 21 (Records, Optional, Pattern Matching)
* **Design Pattern:** Chain of Responsibility (Functional Implementation)
* **Framework:** Spring Boot 3.x
* **Testing:** JUnit 5 (isolating each handler for unit testing)

## 🚀 Key Engineering Features
* **Chain of Responsibility:** Each validation rule (e.g., `HazmatValidator`, `WeightCapacityValidator`) is a standalone component. This makes the system "Open/Closed"—you can add a "Global Trade Compliance" check without touching existing code.
* **Modern Idioms:**
    * **Records:** Used for immutable `Shipment` and `ValidationResult` objects.
    * **Optional:** Replaced null-checks with `Optional` to handle potentially missing data in a clean, functional way.
    * **Pattern Matching:** Used in the final triage to decide whether to `REJECT`, `WARN`, or `APPROVE` based on the validation outcome.
* **Fail-Fast vs. Accumulative:** Configurable logic to either stop at the first error or collect all errors for a comprehensive report.

## 💻 Code Highlight: The "Modern" Chain
Instead of the old-school "Next Handler" pointer approach, I use a functional list-based chain:

```java
public record ValidationResult(boolean isValid, String reason) {}

public interface ValidationHandler {
    ValidationResult validate(Shipment shipment);
}

// In the Orchestrator
public List<ValidationResult> runPipeline(Shipment shipment) {
    return List.of(new HazmatHandler(), new WeightHandler(), new InsuranceHandler())
        .stream()
        .map(handler -> handler.validate(shipment))
        .filter(result -> !result.isValid())
        .toList();
}
