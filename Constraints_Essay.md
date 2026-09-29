Nate Heath, Elliot Warner, William Thomas

CS5001

9/17/26

# Project Constraints Essay
## Open-Source Deskmate Display Device and Development Library
### Economic
Economic constraints are important because the physical product must be affordable for the general public (a budget of $150 is deemed as reasonable). The product requires obtaining an Orange Pi Zero 3 and a 64x32 LED display. The team must prioritize these components and avoid unnecessary hardware so that the device remains affordable to build and reproduce. This limits the complexity of the physical device and encourages us to perform application processing directly on the Orange Pi device rather than deferring computing to more expensive hardware.

### Professional
Professional constraints are important because the project is being developed by a three-person team, so the software must be maintainable and understandable by developers beyond the original team. The open-source application library should hide boilerplate code behind a simple API so that users can focus on creating applications themselves, rather than how to communicate with the display board and Orange Pi. This creates a conflict between Professional and Economic constraints because creating a well-documented, reusable library requires additional development time. We therefore need to limit the initial library to a manageable set of core features while leaving the architecture extensible for future contributors.

### Legal
Legal constraints factor into this project because the application will be open-source and may eventually include code or dependencies created by other developers. The team must therefore select compatible open-source licenses for our own code and follow the license requirements of any third-party libraries used to control the Orange Pi or LED display.